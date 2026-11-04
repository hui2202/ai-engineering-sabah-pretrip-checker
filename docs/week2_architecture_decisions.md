# Architecture Decisions and Traceability — Sabah Pre-Trip Checker

Version: 1.0

## Architecture Decision Record

| Decision | Requirement / Constraint | Alternatives Considered | Selected Option | Trade-off Accepted |
|---|---|---|---|---|
| Knowledge access | FR2, FR3, NFR2, RAI4 | Base model only; document retrieval only; structured rule store only | Structured rule store combined with retrieval of original sentences from official documents | More curation effort and system complexity, in exchange for applicability filtering, better grounding and verifiable original wording |
| Deadline calculation | FR4, NFR3 | Language model calculates deadlines | Rule-based deadline calculator | Advance-day values must be maintained manually in the rule store, but calculated deadlines are deterministic |
| Processing flow | NFR1, NFR4, NFR5 | Autonomous agent that decides which tools to call | Fixed pipeline with at most two language model calls | Less flexible for unusual questions, but more consistent, cheaper and easier to evaluate |
| Scope and safety control | FR6–FR8, RAI2, RAI3, RAI6 | Instructions in the prompt only | Explicit input control and output check | Possible false refusals and additional processing, in exchange for stronger scope, health-safety and privacy control |
| Language model service | NFR5; constraint: free LLM tiers only | Paid API; locally hosted open model | Free-tier external model service accessed through a provider-independent interface, with one fixed model for evaluation | Dependency on provider limits and terms, and possible use of submitted data for training (reduced by removing identity numbers before each call) |
| Handling of copyrighted sources | Constraint: public repository; Lab 2 instruction to place PDFs in `/data/` | Store links only; keep documents private | Include copies with attribution, retrieval dates and a removal-on-request disclaimer (`data/README.md`) | A residual copyright risk is accepted for reproducibility; documents will be removed if the lecturer or a rights holder objects |
| Future multilingual support | Future extension; RAI4 | Translate the whole answer only | Show the translated answer together with the original English official sentence (planned for a later version) | Longer output, but protects the meaning of official rules, e.g. "recommended" versus "required" |

## Initial Requirements Traceability Matrix

| Objective | Requirement | Evaluation Criterion | Architecture Element | Later Evidence |
|---|---|---|---|---|
| O1 | FR3 / RAI4 | Source Citation and Support | Official Document Store, Passage Retrieval, source tracking | Week 10 citation check of all listed requirements using Rubric 2 |
| O1 | FR2 | No Invented Requirements | Structured Rule Store; Answer Generation restricted to retrieved content | Week 10 unsupported requirement rate |
| O2 | FR4 / NFR3 | Deadline Accuracy | Deadline Calculator | Automated tests on deadline cases; Week 10 deadline check |
| O2 | RAI2 / RAI3 / RAI6 | Scope and Health-Safety Compliance | Input Control, Output Check | Scope test set (20 queries), health test set (10 queries) and privacy test cases |
| O3 | FR2 / NFR2 | Requirement Detection | Trip Detail Extraction, Rule Matching | Week 10 comparison with the baseline on 30 scenarios using Rubric 1 |
| O3 | RAI1 | Fairness Across Traveller Groups | Rule Matching (traveller-category filter) | Recall gap between Malaysian and international scenarios |
| O3 | NFR1 / NFR5 | Latency and Cost | Language Model Service (two calls), Evaluation Point logs | p90 latency and API usage logs over the evaluation workload |
