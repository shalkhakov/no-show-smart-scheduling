# 01 · Business Intent Brief

**Project:** Patient No-Show Risk & Smart Scheduling
**Author:** David Shalkhakov · Business Systems Analyst
**Status:** v1 scope, portfolio (synthetic data)

---

## Problem

Outpatient clinics lose revenue and clinical capacity when patients miss scheduled appointments. Typical no-show rates run 15–30%. An empty slot discovered at the appointment time cannot be refilled, provider and staff time is wasted, and a patient elsewhere who needed that slot doesn't get it. Today, scheduling staff have no forward-looking signal — every appointment is treated the same until the patient either shows up or doesn't.

## Stakeholders

| Stakeholder | Interest |
|-------------|----------|
| Clinic operations / practice manager | Utilization, revenue, staff efficiency |
| Front-desk schedulers | A clear, trustworthy signal and a next action they can take |
| Providers | Full, predictable schedules; fewer idle gaps |
| Patients | Timely access to care; helpful reminders, not spam |
| Finance | Recovered revenue from reduced no-shows |
| Compliance / privacy officer | PHI handling, fairness, auditability |

## Goals

Reduce the no-show rate and recover lost capacity by:

1. **Flagging** appointments that are at elevated risk of no-show *ahead of time*.
2. **Recommending** a proportionate action per appointment — enhanced reminder, waitlist backfill, or controlled overbook — while keeping the final decision with a human scheduler.

## Success metrics

| Metric | Direction | Why it matters |
|--------|-----------|----------------|
| No-show rate | ↓ | Primary outcome |
| Slot utilization | ↑ | Recovered capacity |
| Recovered revenue (backfilled/kept slots) | ↑ | Financial impact |
| Staff time per recovered slot | ↓ | Efficiency of the intervention |
| Scheduler action-acceptance rate | monitored | Whether the tool's recommendations are trusted and used |

## Scope — v1

**In scope:** single clinic; one appointment type family; a transparent risk score and a recommended action per appointment; a scheduler-facing view (table + day summary); a manual "action taken" capture so outcomes can be measured later.

**Out of scope (v1):** live EHR/EMR integration; actually sending reminders or messages; multi-site rollout; a machine-learning predictive model; patient-facing UI.

## Governance & guardrails

- **Data:** synthetic data only. No real PHI is used, stored, or transmitted. Any production version would treat all fields as PHI under HIPAA.
- **Human-in-the-loop:** the tool *recommends*; a scheduler *decides*. No automated action is taken on a patient without human confirmation.
- **Explainability over accuracy:** risk is computed by a transparent, weighted rule set (see `03`), not a black-box model, so every score can be explained to staff and audited.
- **Fairness:** scoring inputs are limited to operational/behavioral factors (lead time, prior attendance, reminder status, appointment type, travel distance). Protected or proxy attributes (e.g., age band, ZIP as a demographic proxy, insurance type) are deliberately excluded — see oversight log entry OL-03.
- **Auditability:** every recommendation records the factors that produced it; every scheduler action is logged.

## Assumptions

- Historical attendance and basic appointment metadata are available for scoring inputs.
- A waitlist of patients willing to take earlier slots exists or can be maintained.
- Overbooking is permitted under clinic policy within defined limits.
