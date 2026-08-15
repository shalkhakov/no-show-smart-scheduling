# 02 · Structured Requirements

**Project:** Patient No-Show Risk & Smart Scheduling
**Author:** David Shalkhakov · Business Systems Analyst

Requirements are numbered and referenced from the traceability matrix (`04`). IDs: **BR** business requirement, **FR** functional requirement, **NFR** non-functional requirement, **US** user story.

---

## 1. Business requirements

| ID | Requirement |
|----|-------------|
| BR-1 | The clinic must be able to identify, ahead of the appointment date, which appointments are at elevated risk of no-show. |
| BR-2 | The clinic must be able to act on at-risk appointments with a proportionate, recommended intervention. |
| BR-3 | Scheduling staff must retain final decision authority over any action taken on a patient. |
| BR-4 | Every risk assessment must be explainable — staff must be able to see why an appointment was flagged. |
| BR-5 | The clinic must be able to measure whether interventions reduce no-shows and recover capacity. |

## 2. Functional requirements

| ID | Requirement | Supports |
|----|-------------|----------|
| FR-1 | The system computes a no-show risk score (0–100) for each scheduled appointment from defined input factors. | BR-1 |
| FR-2 | The system assigns each appointment a risk tier — Low / Medium / High — from configurable thresholds. | BR-1 |
| FR-3 | The system displays, for each appointment, the factors and their contribution to the score. | BR-4 |
| FR-4 | The system recommends an action per risk tier: Low → standard reminder; Medium → enhanced reminder + confirmation call; High → enhanced reminder + waitlist backfill / controlled overbook. | BR-2 |
| FR-5 | A scheduler can accept, override, or dismiss the recommended action; the choice is recorded. | BR-3 |
| FR-6 | The system presents a day-level summary: appointment count, risk distribution, count of recommended actions. | BR-1, BR-5 |
| FR-7 | The system records each recommendation and each scheduler action with a timestamp for later outcome analysis. | BR-5 |
| FR-8 | Risk-scoring weights and tier thresholds are configurable without code changes. | BR-4 |

## 3. Non-functional requirements

| ID | Requirement |
|----|-------------|
| NFR-1 | **Explainability** — no factor may contribute to a score without being displayable to the user. Black-box scoring is prohibited. |
| NFR-2 | **Privacy** — the demo uses synthetic data only; a production version treats all patient fields as PHI and no data leaves the clinic boundary without controls. |
| NFR-3 | **Fairness** — scoring inputs are restricted to operational/behavioral factors; protected or proxy demographic attributes are excluded. |
| NFR-4 | **Usability** — a scheduler can read an appointment's risk, reason, and recommended action without training, in a single view. |
| NFR-5 | **Auditability** — every recommendation stores the inputs that produced it; every action is logged with time and user. |
| NFR-6 | **Performance** — scoring a full day of appointments is effectively instantaneous from the scheduler's perspective. |

## 4. User stories & acceptance criteria

**US-1 — See risk at a glance**
*As a scheduler, I want each appointment on the day to show a risk tier and score, so I can prioritize where to spend effort.*
Acceptance:
- Each appointment row shows a numeric score (0–100) and a tier (Low/Medium/High).
- Rows are sortable/visually distinguishable by risk.
- Supports FR-1, FR-2.

**US-2 — Understand *why***
*As a scheduler, I want to see the reasons behind a risk score, so I can trust it and explain it.*
Acceptance:
- Selecting an appointment reveals the contributing factors and each factor's contribution.
- No factor appears in the score that is not shown to the user.
- Supports FR-3, NFR-1.

**US-3 — Get a recommended action**
*As a scheduler, I want a recommended next action for each at-risk appointment, so I know what to do without guessing.*
Acceptance:
- Each appointment shows exactly one recommended action mapped from its tier per FR-4.
- The mapping is consistent and configurable.
- Supports FR-4.

**US-4 — Stay in control**
*As a scheduler, I want to accept, override, or dismiss a recommendation, so the final decision stays with me.*
Acceptance:
- Each recommendation offers accept / override / dismiss.
- The chosen outcome is recorded with a timestamp.
- No action is executed on the patient automatically.
- Supports FR-5, BR-3, NFR-5.

**US-5 — See the day at a glance**
*As a practice manager, I want a day-level summary of risk and recommended actions, so I can staff and plan the day.*
Acceptance:
- A summary shows total appointments, count by tier, and count of each recommended action.
- Updates as scheduler actions are recorded.
- Supports FR-6.

**US-6 — Tune the model**
*As a practice manager, I want to adjust scoring weights and tier thresholds, so the tool matches our clinic's reality.*
Acceptance:
- Weights and thresholds are editable without code changes.
- Changes re-score the current day.
- Supports FR-8, BR-4.

## 5. Gherkin scenarios (key behaviors)

```gherkin
Feature: No-show risk scoring and recommendation

  Scenario: A high-risk appointment surfaces a backfill recommendation
    Given a patient with two prior no-shows in the last 6 months
      And an unconfirmed appointment booked 21 days ago
      And no reminder has been acknowledged
    When the scheduler opens today's schedule
    Then the appointment is scored in the High tier
      And the recommended action is "Enhanced reminder + waitlist backfill / overbook"
      And the contributing factors are shown with their weights

  Scenario: Scheduler overrides a recommendation
    Given an appointment recommended for waitlist backfill
    When the scheduler selects "Override" and chooses "Standard reminder only"
    Then the override is recorded with a timestamp
      And no automated action is taken on the patient

  Scenario: Explainability is enforced
    Given any appointment with a computed risk score
    When the scheduler views the score detail
    Then every factor contributing to the score is displayed
      And the displayed contributions sum to the total score
```
