# 05 · Human Oversight Log

**Project:** Patient No-Show Risk & Smart Scheduling
**Author / reviewer:** David Shalkhakov · Business Systems Analyst

This project used AI to accelerate drafting of requirements, the context specification, and the demo. This log records each point where AI output was reviewed and where human judgment changed the result. It is the governance record: it shows AI was used as a drafting accelerant under analyst control, not as an unreviewed author.

**How to read an entry:** what was being produced → what the AI proposed → what I reviewed / changed → why → decision.

---

### OL-01 · Scoring approach: ML model vs. transparent rules
- **Artifact:** Context specification, §2
- **AI proposed:** A machine-learning classifier trained on historical attendance to predict no-show probability.
- **Reviewed / changed:** Rejected the ML approach for v1. Replaced with a transparent additive rule set with visible per-factor weights.
- **Why:** There is no validated training data here (synthetic set), and a black-box probability would violate the explainability requirement (NFR-1) and be impossible for a scheduler to trust or an auditor to check. An additive model can be explained line by line.
- **Decision:** Rule-based model adopted; ML noted as a possible future phase *only* with real, governed, validated data.

### OL-02 · Action mapping: automated messaging vs. human-in-the-loop
- **Artifact:** Functional requirements, FR-5; action mapping §4
- **AI proposed:** Auto-send reminders and auto-place high-risk patients on the waitlist to "save clicks."
- **Reviewed / changed:** Removed all automated patient-facing actions. The tool recommends; the scheduler accepts/overrides/dismisses. Added FR-5 and BR-3 to make human authority an explicit requirement.
- **Why:** Automated action on a patient without human confirmation is the wrong default in a care setting and undermines accountability.
- **Decision:** Human-in-the-loop made a hard requirement, not an option.

### OL-03 · Scoring inputs: demographic factors excluded
- **Artifact:** Data model §1; NFR-3
- **AI proposed:** Include patient age band, insurance type, and ZIP code as predictive features ("they improve accuracy").
- **Reviewed / changed:** Excluded all of them. Restricted inputs to operational/behavioral factors (prior attendance, confirmation, reminder status, lead time, appointment type, distance). Documented the exclusion explicitly.
- **Why:** Age, insurance, and ZIP act as protected-class or proxy attributes; using them to decide who gets extra outreach risks discriminatory impact and is indefensible in a compliance review. Marginal accuracy is not worth that.
- **Decision:** Fairness constraint written into the data model and NFR-3; distance kept only as a coarse operational factor, capped low.

### OL-04 · Scope: EHR integration pulled out of v1
- **Artifact:** Business intent brief, scope section
- **AI proposed:** A v1 that reads live appointments from the EHR and writes actions back.
- **Reviewed / changed:** Moved live EHR/EMR integration out of v1 scope; v1 works on a provided/synthetic day.
- **Why:** EHR integration carries the heaviest security, privacy, and vendor-coordination cost and would dominate the effort while adding nothing to the thing being demonstrated (the requirements-to-solution method). Classic scope discipline.
- **Decision:** Integration deferred to a later phase with its own security review.

### OL-05 · Requirements wording: vague acceptance criteria tightened
- **Artifact:** User stories, US-2 and US-4
- **AI proposed:** Acceptance criteria like "the score should be understandable" and "the user can manage the recommendation."
- **Reviewed / changed:** Rewrote to testable form — "every factor contributing to the score is displayed and the displayed contributions sum to the total," and "accept/override/dismiss is recorded with a timestamp; no automated action is taken."
- **Why:** "Understandable" and "manage" aren't verifiable. Acceptance criteria have to be checkable or they can't gate delivery.
- **Decision:** All acceptance criteria rewritten to observable, testable conditions.

### OL-06 · Edge cases: new-patient penalty corrected
- **Artifact:** Context specification, §5
- **AI proposed:** Treat a missing attendance history as elevated risk (no history → assume risky).
- **Reviewed / changed:** Changed so absence of history is *not* penalized; new-patient risk comes only from the appointment-type factor and current state.
- **Why:** Penalizing patients simply for being new is both unfair and operationally wrong — it would flood outreach toward first-time patients regardless of actual behavior.
- **Decision:** "Unknown ≠ penalized" adopted as a general rule (also applied to missing travel distance).

---

## Summary of human interventions

| # | Area | Effect of human review |
|---|------|------------------------|
| OL-01 | Model choice | Black-box ML → transparent, auditable rules |
| OL-02 | Automation | Auto-actions → human-in-the-loop requirement |
| OL-03 | Fairness | Demographic inputs removed |
| OL-04 | Scope | EHR integration deferred; effort focused |
| OL-05 | Requirements quality | Vague criteria → testable criteria |
| OL-06 | Edge-case logic | Corrected unfair new-patient penalty |

**Takeaway:** AI drafted quickly; the analyst decided what was correct, fair, in-scope, and testable. Six of the AI's initial choices were changed — each change is a documented judgment call.
