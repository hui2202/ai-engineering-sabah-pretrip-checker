# Sabah Pre-Trip Checker

> Helps travellers planning a trip to Sabah find the pre-trip requirements for their destinations, with official sources and deadlines calculated from their travel dates.

## Problem Statement

Travellers to Sabah must meet procedural requirements such as permits, daily quotas, booking windows and pre-arrival registration, but this information is scattered across official documents and mixed with outdated or conflicting information online. Missing a single requirement can cause a planned activity to fail. A document-grounded AI assistant can read a traveller's trip description, find the applicable requirements in official sources and calculate when each one must be completed.

## Evidence of the Problem

- ChatGPT and Google AI both missed the online self check-in that becomes available five days before a Mount Kinabalu climb and is needed to prepare the indemnity form required at registration; Google AI also urged users to pay a deposit to secure 2027 slots although the official Sabah Parks page stated that 2027 bookings had not opened ([own tests, October 2026](docs/evidence/ai_tool_tests.md); [Sabah Parks Self Check-In](https://reservation.sabahparks.org.my/v2/mountain-climbing/checkin); [Sabah Parks Mount Kinabalu Climbing](https://sabahparks.org.my/services/mount-kinabalu-climbing)).
- Online sources contradict each other: reported daily climbing permit limits range from 135 to 185 for Mount Kinabalu and between 120 and 250 for Sipadan Island, while the [official Sabah Parks climbing guide](https://sabahparks.org.my/services/mount-kinabalu-climbing) does not state any of the Mount Kinabalu figures.

## Initial Related Work / Existing Approaches

- **TravelPlanner** — Xie et al. (2024), ICML ([arXiv:2402.01622](https://arxiv.org/abs/2402.01622)). Shows LLM agents struggle with multi-constraint travel planning, but does not address official procedural requirements or deadlines.
- **General-purpose AI assistants** (ChatGPT, Google AI) — own exploratory tests. Handle well-known risks but miss procedural requirements in official documents, and source quality varies between tools.
- **Official Sabah Parks website and its AI assistant TAVI, alongside travel information websites** — [Sabah Parks](https://www.sabahparks.org.my) and travel blogs; own test of TAVI (October 2026). Information is fragmented and unofficial sources are often contradictory; TAVI answers specific step-by-step questions well with cited excerpts, but could not answer a trip-level preparation question for a March 2027 climb, does not calculate dated deadlines and covers only Sabah Parks content.

## Engineering Gap / Limitation

General-purpose AI assistants do not systematically check procedural requirements against official documents and vary in source quality, while the official site-specific assistant only answers step-by-step questions for a single agency. Neither calculates requirement deadlines from the traveller's dates.

## Project Objectives

- O1: To design a document-grounded pre-trip requirement assistant for travel in Sabah based on official sources.
- O2: To develop the assistant with retrieval, source citation, rule-based deadline calculation and scope guardrails.
- O3: To evaluate the assistant against a general-purpose LLM baseline in terms of requirement detection rate, citation correctness, deadline accuracy and out-of-scope refusal.

## Target Users

Domestic and international travellers planning independent trips to Sabah, particularly to regulated sites such as Mount Kinabalu, Sipadan Island and the marine parks.

## Data Sources

- Sabah Parks Terms and Conditions (PDF) — https://reservation.sabahparks.org.my/files/TermsandCondition.pdf
- Sabah Parks Mount Kinabalu Climbing guide — https://sabahparks.org.my/services/mount-kinabalu-climbing
- OpenDOSM Monthly Air Pollution dataset (CC BY 4.0) — https://open.dosm.gov.my/data-catalogue/air_pollution
- GEC Haze Safety Advisory for Outdoor Activities (PDF) — https://gec.org.my/wp-content/uploads/2025/07/20250723_haze_a4_poster_01.pdf

Documents whose licence does not allow redistribution are referenced by URL only and are not committed to this repository.

## Weekly Progress

| Week | Focus | Deliverable | Status |
|---|---|---|---|
| W1 | System map + project scope | README + docs | ✅ |
