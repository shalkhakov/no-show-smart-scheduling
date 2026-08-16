# no-show-smart-scheduling
AI-assisted business systems analysis — full requirements lifecycle with traceability and a human-oversight governance layer, on a patient no-show use case.

**▶ Live demo:** https://shalkhakov.github.io/no-show-smart-scheduling/demo/

# Patient No-Show Risk & Smart Scheduling

**A business-systems-analysis portfolio project** demonstrating end-to-end requirements delivery — from business intent through structured requirements, a context specification, a working demo, and full traceability — with an explicit human-oversight layer over AI-assisted work.

**Author:** David Shalkhakov · Business Systems Analyst
**Type:** Portfolio project (synthetic data only — not affiliated with any healthcare organization)

---

## Why this project exists

Most analyst portfolios show a dashboard. This one shows the *work behind the solution*: how a fuzzy business problem is turned into requirements a team can build against, how those requirements stay traceable through to a working artifact, and how AI is used to accelerate the work **without** handing over judgment.

The method follows an AI-assisted, "context-over-code" delivery model: the analyst defines the structured context (requirements, rules, data model, acceptance criteria), AI generates drafts against that context, and every AI output passes through a documented human review. The differentiators are the **traceability matrix** and the **human-oversight log** — they show requirements discipline and responsible AI use, not just an output.

## The business problem

Outpatient clinics lose revenue and capacity to no-shows (industry range ~15–30%). A missed slot is hard to backfill last-minute, staff time is wasted, and a patient who needed care doesn't get it. This project scopes a v1 tool that (a) flags at-risk appointments ahead of time and (b) recommends a targeted action — enhanced reminder, waitlist backfill, or controlled overbook — leaving the final decision with a human scheduler.

## Repository contents

| File | What it is |
|------|-----------|
| [`01-business-intent-brief.md`](01-business-intent-brief.md) | One-page framing: problem, stakeholders, goals, success metrics, scope, governance |
| [`02-structured-requirements.md`](02-structured-requirements.md) | Business, functional, and non-functional requirements; user stories with acceptance criteria; Gherkin scenarios |
| [`03-context-specification.md`](03-context-specification.md) | Data model, transparent risk-scoring rules, action-mapping logic, edge cases, assumptions |
| [`04-traceability-matrix.md`](04-traceability-matrix.md) | Every business requirement traced through functional requirement → user story → demo feature → acceptance check |
| [`05-human-oversight-log.md`](05-human-oversight-log.md) | Each AI-assisted step: what was proposed, what was reviewed, what was changed, and why |
| [`demo/index.html`](https://shalkhakov.github.io/no-show-smart-scheduling/demo/) | Working demo — a scheduler view that scores a day of synthetic appointments and recommends actions |

## Running the demo

Open `demo/index.html` in any browser — it's a single self-contained file, no build step, no data leaves the page. All appointment data is synthetic. Or host it via GitHub Pages (Settings → Pages → deploy from branch, `/demo` as source).

## How to read this as a hiring manager

- Want to see requirements craft? → `02` and `04`.
- Want to see responsible-AI / governance judgment? → `05`.
- Want to see it work? → `demo/index.html`.

## Scope and honesty notes

This is a demonstration built on **synthetic data**. The risk scoring is a transparent, rule-based model chosen deliberately for explainability — it is not a validated clinical or predictive model and is not intended for real patient use. No real PHI is used, stored, or transmitted.
