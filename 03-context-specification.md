# 03 · Context Specification

**Project:** Patient No-Show Risk & Smart Scheduling
**Author:** David Shalkhakov · Business Systems Analyst

This is the *context* the build works from: the data model, the exact scoring rules, and the action logic. It is deliberately explicit so that a developer (or an AI generating code) produces a deterministic, auditable result rather than inventing behavior.

---

## 1. Data model

**Appointment** (the unit scored)

| Field | Type | Notes |
|-------|------|-------|
| `appointment_id` | string | Unique |
| `patient_ref` | string | Pseudonymous reference (synthetic) |
| `appointment_type` | enum | `routine` · `follow_up` · `new_patient` · `specialist` |
| `scheduled_datetime` | datetime | The slot |
| `booked_datetime` | datetime | When it was booked (drives lead time) |
| `confirmed` | boolean | Patient confirmed the appointment |
| `reminder_status` | enum | `none` · `sent_unack` · `acknowledged` |
| `prior_no_shows_6mo` | integer | Count of missed appointments in last 6 months |
| `prior_completed_6mo` | integer | Count of attended appointments in last 6 months |
| `travel_distance_mi` | number | Approximate distance to clinic |

**Excluded by design (see NFR-3 and oversight log OL-03):** age, gender, race/ethnicity, insurance type, ZIP as a demographic proxy, income indicators. These are not inputs to the score.

## 2. Risk factors and weights

Each factor produces a sub-score of 0–N points. The total is capped at 100. Weights are configurable (FR-8); the values below are the v1 defaults.

| Factor | Rule | Max points |
|--------|------|-----------:|
| Prior no-shows (6mo) | 0 → 0 pts · 1 → 15 · 2 → 25 · 3+ → 35 | 35 |
| Confirmation status | confirmed → 0 · unconfirmed → 20 | 20 |
| Reminder status | acknowledged → 0 · sent_unack → 10 · none → 15 | 15 |
| Lead time (booked→scheduled) | ≤7 days → 0 · 8–21 → 8 · >21 → 15 | 15 |
| Appointment type | follow_up → 0 · routine → 3 · specialist → 6 · new_patient → 10 | 10 |
| Travel distance | ≤5 mi → 0 · 6–15 → 3 · >15 → 5 | 5 |

**Total score** = sum of factor points, capped at 100.

> Rationale for a rule-based model over ML: with synthetic data and no validated training set, a transparent additive model is honest, explainable, and auditable — every point on the score can be shown to a scheduler (NFR-1). This was a deliberate decision recorded in oversight log OL-01.

## 3. Risk tiers

| Tier | Score range |
|------|-------------|
| Low | 0–29 |
| Medium | 30–54 |
| High | 55–100 |

Thresholds are configurable (FR-8).

## 4. Action mapping

| Tier | Recommended action |
|------|--------------------|
| Low | Standard reminder |
| Medium | Enhanced reminder + confirmation call |
| High | Enhanced reminder + waitlist backfill / controlled overbook |

Overbook is only recommended, never auto-applied, and is subject to a clinic-level daily overbook cap (a policy parameter, out of scope to enforce in v1).

## 5. Edge cases & rules

- **New patient, no history:** `prior_no_shows_6mo` and `prior_completed_6mo` are 0. The model does not penalize absence of history; new-patient risk comes only from the `appointment_type` factor and current confirmation/reminder/lead-time state.
- **Already confirmed + acknowledged reminder:** contributes 0 from those two factors regardless of history, reflecting that a confirmed patient is materially less likely to miss.
- **Score cap:** a patient could theoretically exceed 100 across factors; the total is capped at 100 so tiers stay stable.
- **Missing `travel_distance_mi`:** treated as 0 points (unknown ≠ penalized).
- **Same-day bookings:** lead time ≤7 days → 0 points; same-day is not treated as risky by itself.

## 6. Assumptions carried into the build

- All timestamps are clinic-local.
- One scheduler works one clinic's day view at a time (no concurrency requirements in v1).
- Synthetic dataset is representative enough to demonstrate the full range of tiers and actions.
