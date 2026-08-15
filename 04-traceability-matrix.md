# 04 · Requirements Traceability Matrix

**Project:** Patient No-Show Risk & Smart Scheduling
**Author:** David Shalkhakov · Business Systems Analyst

Every business requirement is traced forward through functional requirement → user story → the demo feature that implements it → the acceptance check that verifies it. This is the artifact that proves nothing was built without a reason and nothing required was left unbuilt.

---

| Business req | Functional req | User story | Demo feature (in `demo/index.html`) | Acceptance check | Status |
|--------------|----------------|-----------|-------------------------------------|------------------|:------:|
| BR-1 Identify at-risk appointments early | FR-1 Compute score · FR-2 Assign tier | US-1 See risk at a glance | Score + tier badge on every appointment row | Each row shows a 0–100 score and Low/Med/High tier | ✅ |
| BR-2 Act with a proportionate intervention | FR-4 Recommend action by tier | US-3 Get a recommended action | "Recommended action" column, one action per tier | Action matches tier per §4 of context spec | ✅ |
| BR-3 Staff keep decision authority | FR-5 Accept/override/dismiss | US-4 Stay in control | Accept / Override / Dismiss control per appointment | Choice recorded; no auto-action on patient | ✅ |
| BR-4 Assessments are explainable | FR-3 Show factor contributions · FR-8 Configurable | US-2 Understand why · US-6 Tune the model | Factor breakdown panel on selection; adjustable weights/thresholds | Displayed factors sum to the score; edits re-score | ✅ |
| BR-5 Measure impact | FR-6 Day summary · FR-7 Log recommendations & actions | US-5 See the day at a glance | Day-summary bar; action log capture | Summary reflects tiers + actions; actions timestamped | ✅ |

## Non-functional coverage

| NFR | Where enforced | Verified by |
|-----|----------------|-------------|
| NFR-1 Explainability | Context spec §2 (no hidden factors); demo factor panel | Factor contributions shown and sum to score |
| NFR-2 Privacy | Synthetic data only; no network calls in demo | Data inspection; single-file, offline demo |
| NFR-3 Fairness | Context spec §1 exclusions; oversight log OL-03 | Excluded attributes absent from data model & scoring |
| NFR-4 Usability | Single-view scheduler design | Risk, reason, action readable without training |
| NFR-5 Auditability | FR-7 logging; factor storage | Each recommendation stores its inputs; actions logged |
| NFR-6 Performance | Client-side additive scoring | Day re-scores instantly on change |

**Legend:** ✅ implemented and demonstrated in the v1 demo.
