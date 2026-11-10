# Requirements Specification — Sabah Pre-Trip Checker

Version: 1.0

## Section 0 — Project Framing & Application Scope Checkpoint

| Item | Details |
|---|---|
| **Problem Evidence** | **Evidence 1:** In exploratory tests (October 2026), ChatGPT and Google AI were asked how to prepare for a Mount Kinabalu climb in March 2027. Neither mentioned the online self check-in, which becomes available five days before the climb and is needed to prepare the indemnity form required at registration. The two tools also gave contradictory advice on 2027 bookings, while the official Sabah Parks page stated that 2027 bookings had not opened ([test log](evidence/ai_tool_tests.md); [Sabah Parks Mount Kinabalu Climbing guide](https://sabahparks.org.my/services/mount-kinabalu-climbing)).<br>**Evidence 2:** Online sources contradict each other: reported daily permit limits range from 135 to 185 for Mount Kinabalu and between 120 and 250 for Sipadan Island ([week1_project_scope.md](week1_project_scope.md)).<br>**Explanation:** Requirements that can cause a trip to fail are stated in official sources, but travellers and general AI tools miss them or rely on inconsistent unofficial information. |
| **Existing Approaches** | **1. TravelPlanner benchmark** (Xie et al., 2024): addresses multi-constraint itinerary planning with language agents, showing that agents struggle to satisfy constraints; does not address official procedural requirements or deadlines.<br>**2. General-purpose AI assistants** (ChatGPT, Google AI): address general travel questions and well-known risks such as the monsoon; miss procedural requirements in official documents and vary in source quality.<br>**3. Sabah Parks website and its AI assistant TAVI**: address official, cited answers to specific step-by-step questions; could not answer a trip-level preparation question, do not calculate dated deadlines and cover only Sabah Parks content. |
| **Project / Engineering Gap** | General-purpose AI assistants do not systematically check procedural requirements against official documents and vary in source quality, while the official site-specific assistant only answers step-by-step questions for a single agency. Neither calculates requirement deadlines from the traveller's dates. The gap is therefore one of grounding, source traceability and proactive, date-aware coverage. |
| **Objective O1** | To design a document-grounded pre-trip requirement assistant for travel in Sabah based on official sources. |
| **Objective O2** | To develop the assistant with retrieval, source citation, rule-based deadline calculation and scope guardrails. |
| **Objective O3** | To evaluate the assistant against a general-purpose LLM baseline in terms of requirement detection rate, citation correctness, deadline accuracy and out-of-scope refusal. |
| **Intended Users** | Malaysian and international travellers planning independent trips to regulated sites in Sabah. |
| **Supported Tasks** | (1) Describe a planned trip in free text (destinations, travel dates, activities, traveller category); (2) receive a checklist of applicable pre-trip requirements, each with an official source and last-checked date; (3) see the date by which each time-bound requirement must be completed; (4) receive seasonal risk reminders (northeast monsoon, haze) with links to official real-time sources; (5) ask follow-up questions about listed requirements; (6) export deadlines to a calendar (if time allows). |
| **Information / Knowledge Domain** | Official procedural requirements (permits, quotas, booking windows, registration, fees, documents, health restrictions) for three sites in V1: Mount Kinabalu, Sipadan Island and Tunku Abdul Rahman Marine Park; plus entry documents for travel to East Malaysia and seasonal risks (northeast monsoon, haze). |
| **Operating Context** | Used through a web interface on a computer or phone, weeks to months before departure, while the traveller is planning the trip. English only in V1. |
| **Important Constraints** | Free LLM usage tiers only (rate limits, variable latency, provider terms may change); rules must be curated and verified manually from official sources; official information changes over time; copyrighted source content must not be published in the public repository; official web pages must be converted to PDF or TXT before processing; one student, one semester. |
| **Explicit Out-of-Scope Tasks** | Making bookings or payments; planning full itineraries; deciding visa or entry eligibility; providing personalised medical advice (no diagnosis, no specific medicines or dosages, no judgement of fitness to travel); guaranteeing that information is current or complete; covering destinations outside the three V1 sites; languages other than English in V1. |
| **Evaluation Focus** | Whether the system, compared with a general-purpose LLM baseline, detects more applicable requirements, cites correct official sources, calculates deadlines accurately and refuses out-of-scope requests; and whether performance is consistent between Malaysian and international traveller scenarios. |

## Section 1 — Functional Requirements

| # | Requirement — The system SHALL | Scope Boundary — SHALL NOT (where relevant) |
|---|---|---|
| FR1 | Extract trip details (destinations, travel dates, activities and traveller category: Malaysian or international) from a free-text trip description, and ask for any missing detail needed to determine requirements. | Assume missing travel dates or traveller category. |
| FR2 | List all pre-trip requirements that apply to the in-scope destinations, activities and traveller category described. | Include requirements that are not supported by the source documents. |
| FR3 | Provide, for each requirement, the original supporting sentence from the official source, the source reference and its last-checked date, and label secondary sources separately. | Present unofficial or unsupported information as official. |
| FR4 | Calculate the date by which each time-bound requirement must be completed based on the travel dates, and warn when a deadline has passed or bookings have not yet opened. | State a deadline for a requirement whose timing is not specified in the source documents. |
| FR5 | Warn about seasonal risks (northeast monsoon, haze) for the destination and travel month, with a link to the official real-time information source. | Forecast weather or air quality for specific travel dates. |
| FR6 | State clearly when a destination or topic is not covered, and decline out-of-scope requests (bookings, full itineraries, destinations outside scope) with an appropriate redirect. | Invent rules for destinations or topics that are not covered. |
| FR7 | Provide general health-preparation reminders and official health restrictions with sources, and refer users to a doctor or pharmacist. | Diagnose conditions, name specific medicines or dosages, or judge whether a user is fit to travel. |
| FR8 | Direct international travellers to official immigration sources for entry requirements. | Decide whether a traveller is eligible for a visa or entry. |
| FR9 *(Should)* | Allow users to export requirement deadlines as a calendar file. | - |

## Section 2 — Non-Functional Requirements

| # | Quality Attribute | Requirement | Measurement Method | Metric | Threshold |
|---|---|---|---|---|---|
| NFR1 | Performance | The system shall return a complete requirement checklist within an acceptable time for a planning task. | Measure end-to-end latency on the evaluation scenario set. | p90 latency | < 10,000 ms |
| NFR2 | Correctness | The checklist shall include the requirements that apply to the described trip. | Compare system output with manually verified expected requirements for each scenario. | Requirement recall (%) | ≥ 90% |
| NFR3 | Correctness | Requirement deadlines shall be calculated correctly from the travel dates. | Compare calculated deadlines with manually calculated dates for each time-bound requirement. | Deadline accuracy (%) | 100% |
| NFR4 | Reliability | The same trip description shall produce the same set of requirements on repeated runs. | Run each scenario three times with the same model settings and compare outputs. | Identical requirement sets (%) | ≥ 95% |
| NFR5 | Cost | The system shall operate within free LLM usage tiers. | Record API usage and charges over the evaluation workload. | Cost per 100 queries; full evaluation run completed within one day's free quota | US$0; yes |
| NFR6 | Maintainability | Updates to an official rule shall be applied without changing application code. | Run a controlled rule-update test (e.g. a fee change). | Update time | < 10 min |

## Section 3 — Responsible AI Requirements

| # | Responsible AI Risk | Requirement | Measurement Method | Metric | Threshold |
|---|---|---|---|---|---|
| RAI1 | Fairness | Requirement detection shall not differ beyond the agreed tolerance between Malaysian and international traveller scenarios. | Evaluate a stratified scenario set and compare requirement recall between the two traveller groups. | Absolute recall gap between groups | ≤ 10 percentage points |
| RAI2 | Safety / Scope | The system shall decline out-of-scope requests (bookings, full itineraries, destinations outside scope, visa eligibility decisions) and redirect users appropriately. | Run a scope test set of at least 20 out-of-scope queries. | Refusal-and-redirect success rate | ≥ 95% |
| RAI3 | Health safety | For health-related queries, the system shall give only general preparation reminders and official health restrictions, and refer users to a doctor or pharmacist. | Run a test set of at least 10 health-related queries and review each response. | Responses that diagnose, name specific medicines or dosages, or judge fitness to travel | 0 |
| RAI4 | Transparency / Grounding | Each listed requirement shall include a verifiable official source reference, and the system shall state when no supporting source can be found. | Check every listed requirement in the evaluation set for a citation and whether the cited source supports it. | Citation presence rate; citation–source match rate | 100%; ≥ 95% |
| RAI5 | Outdated information | Each requirement shall show its last-checked date, and requirements whose last-checked date exceeds the review period shall be flagged for re-verification. | Run test cases that include rules with expired check dates. | Expired rules flagged | 100% (review period: 90 days) |
| RAI6 | Privacy | The system shall collect only the traveller category (Malaysian or international) and shall not send identity numbers such as MyKad or passport numbers to external model APIs. | Run privacy test cases containing identity numbers and inspect outgoing API payloads. | Detected identity-number transmissions | 0 |
