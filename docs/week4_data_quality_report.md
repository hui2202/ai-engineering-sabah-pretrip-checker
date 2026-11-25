# Data Quality, Bias, and Responsible Data Report — Sabah Pre-Trip Checker

Corpus reviewed: Topic 3 output for source S01 (Sabah Parks Mount Kinabalu Climbing guide), 26 chunks, reviewed into 27 chunks.
Notebook: [`notebooks/week4_quality.ipynb`](../notebooks/week4_quality.ipynb) · Reviewed corpus: [`data/prepared_chunks_clean.jsonl`](../data/prepared_chunks_clean.jsonl), [`data/chunks_clean.pkl`](../data/chunks_clean.pkl)

## 1. Data Quality Scorecard

| Dimension | Rating (1–5 / N.V.) | Evidence | Action / limitation |
|---|---:|---|---|
| Completeness | 2 | Every Mount Kinabalu area in the V1 domain is present in 2–10 chunks, and the two package-breakdown tables missing from the PDF were restored from the HTML page. However, Sipadan Island, Tunku Abdul Rahman Marine Park, entry documents and the northeast monsoon have 0 chunks, and the inventoried sources S02 and S03 were not processed (Cell 6). | Collect one official source per V1 site and for entry documents; process S02 and S03 in V2. Until then, answer "not covered" for these areas (FR6). |
| Consistency | 3 | All chunks come from an inventoried source with a hash that matches the Week 3 manifest (R2, R3: 0 failures), and the JSONL and PKL outputs both reload as 27 records. However, the corpus mixes two snapshots of S01 (PDF of 2026-11-08 and HTML of 2026-11-25), and the text format is uneven: 10 chunks still contain letter-spaced headings and 14 keep the split-heading flag. | Rebuild all S01 chunks from one HTML snapshot with heading-aware chunking in V2. |
| Accuracy | 3 | All 6 HTML tables passed verification against the Topic 3 text (Check A) or by arithmetic (Check B), and a negative test with a changed price was rejected (Cell 4D). The private-use character was removed from every chunk. Accuracy of the non-table text was checked only for the 13 chunks inspected manually (Cell 3). | Compare the V2 corpus with the live page for all chunks, not only tables. Answer accuracy is evaluated in Topic 8. |
| Timeliness | 3 | Retrieval dates are recorded for both snapshots and are within the 90-day review period (RAI5). Time-sensitive statements (2026 package rates updated 25 November 2025; the 2027 booking status as of 19 September 2026) carry a re-verify flag in their metadata. | Re-retrieve the page by 2027-02-06 (90 days after 2026-11-08) or when bookings for 2027 open, and compare hashes. |
| Uniqueness | 4 | Exact-hash deduplication found 0 duplicates (27 → 27). A sentence-level check found one repeated line, a table caption introducing two different tables, which was kept. | Exact hashing does not detect near-duplicates. Cross-source duplicates and contradictions are N.V. because only one source has been processed; recheck when S02 is added. |

Ratings: 5 = verified with no known issue; 4 = verified with a minor, documented issue; 3 = partly verified or with known issues controlled by flags; 2 = major gap that limits use; 1 = unusable.

## 2. Validation and Data-Defect Findings

Seven rules were run with Pandera on the 26 Topic 3 chunks (Cell 4). Failures are saved in [`week4_validation_failures.csv`](week4_validation_failures.csv).

| Rule | Quality concern | Observable condition | Failures (of 26) | Examples | Decision |
|---|---|---|---:|---|---|
| R1 Text present | Completeness | `text` is not empty | 0 (0%) | — | Hard fail |
| R2 Source in inventory | Provenance / consistency | `source` is one of the three inventoried files | 0 (0%) | — | Hard fail |
| R3 Hash matches manifest | Consistency | `source_sha256` equals the Week 3 manifest | 0 (0%) | — | Hard fail |
| R4 Minimum useful length | Noise | `char_count` ≥ 200 | 1 (3.8%) | Chunk 25: page footer, 123 characters | Manual review |
| R5 No bad characters | Corruption | No null, replacement or private-use characters | 10 (38.5%) | Icon-font glyph U+E931 in chunks 2, 4, 8, 10, 14, 16, 19, 22, 24, 25 | Correct |
| R6 No letter-spaced headings | Noise | No heading with letters separated by spaces | 10 (38.5%) | "L E M A I N G & P A N A L A B A N" (chunk 0) | Warning |
| R7 No split heading at end | Completeness / consistency | Last paragraph is not a short heading (last chunk excluded) | 17 (65.4%) | "4:30 pm–7:00 pm / Dinner" ends chunk 11; the dinner description starts chunk 12 | Warning |

The R4 threshold of 200 characters was chosen from the evidence: every chunk with rule content had at least 269 characters, while the footer had 123. R7 fails often because the Topic 3 pipeline used paragraph-aware chunks with a fixed 500-character limit, which ignores section headings.

**Summary of defects found**

- **Missing:** no empty text or source metadata. Two package-breakdown tables were missing from the corpus because their collapsed sections were not saved when the web page was converted to PDF.
- **Corrupted:** the private-use character U+E931 in 10 chunks. The lab's corruption check (null and U+FFFD) did not detect it; it was found manually and then added to R5.
- **Noisy:** one footer chunk (removed); letter-spaced headings in 10 chunks (kept, flagged).
- **Inconsistent:** the hostel fee tables were flattened in chunks 2–3, so amounts were separated from their row and column labels, and the Panalaban table was split across two chunks. The date-change penalty table was split across chunks 18–19.
- **Stale risk:** 5 chunks contain dated statements (A2), including the 2027 booking status and the 2026 package rates.
- **Uncertain:** whether the non-table text exactly matches the live page has been checked only for the inspected sample.

**Triage applied (Cells 4B–4E):** chunk 25 removed; 9 remaining chunks corrected for U+E931; the flattened tables in 4 chunks replaced with verified Markdown tables from the HTML page (6 replacements); 2 breakdown tables added as new records with the HTML file and its hash as their source. Result: 26 → 27 chunks, with the R7 splits in records -0003, -0004 and -0019 resolved.

## 3. Duplicate and Anomaly Findings

- **Exact duplicates:** 0 (Cell 5); [`week4_duplicate_review.csv`](week4_duplicate_review.csv) is saved with headers and no rows, so the check is still documented.
- **Near-duplicates:** one repeated caption, "Package price per person (2026 rates, incl. 7% service charge)", in chunks 2 and 4. It introduces two different tables (Lemaing and Panalaban Hostel), so both chunks were kept.
- **Suspicious outliers:** the 123-character footer chunk (removed). After the table corrections, chunk lengths range from 269 to 684 characters; the chunks above 500 characters are intentional, so that each table stays complete with its heading.
- **Contradictions:** none found within S01. Contradictions between sources are N.V., because only S01 has been processed.
- **Extraction problems:** the PDF conversion of the web page caused four types of loss: flattened tables, letter-spaced headings, headings separated from their content by chunking, and collapsed content that was not saved.
- **Provenance preserved:** every chunk keeps `source`, `record_id` and `source_sha256`; the two new records point to the archived HTML snapshot (`data/raw/sabahparks_mount_kinabalu_climbing.html`, retrieved 2026-11-25 07:15:17 UTC, SHA-256 `6c9dd449d1b0ced0…`). Every correction is recorded in the chunk's metadata.

## 4. Representation, Coverage, and Bias Assessment

**Coverage against the V1 scope** (Cell 6; domain grouping is a keyword proxy, not source metadata)

| Required area | Expected evidence | Actual evidence | Gap |
|---|---|---|---|
| Mount Kinabalu procedural rules (8 areas) | Official climbing guide and booking terms | S01: each area in 2–10 chunks; S02: 0 chunks | No for the guide; S02 not yet processed |
| Sipadan Island permit and daily limit | Official Sabah Parks permit information | 0 chunks; no source | Genuine gap |
| Tunku Abdul Rahman Marine Park | Official marine park information | 0 chunks; no source | Genuine gap |
| Entry documents for East Malaysia (FR8) | Official immigration information | 0 chunks; no source | Genuine gap |
| Northeast monsoon / haze (FR5) | Official notice; haze advisory | Monsoon 0, no source; haze: S03 inventoried, 0 chunks | Monsoon: genuine; haze: not yet processed |
| Both traveller categories (RAI1) | Fees and rules for both | Malaysian 5 chunks, International 5 chunks (mentions) | Not determinable from counts |

Source distribution: S01 PDF 25 chunks (92.6%), S01 HTML 2 (7.4%), S02 0, S03 0 ([`week4_coverage_by_source.png`](week4_coverage_by_source.png)).

**Bias risks and propagation paths**

| Bias type | Evidence | Propagation path |
|---|---|---|
| Historical bias | "2026 package rates · price update: 25 November 2025"; "As of 19 September 2026, bookings for the 2027 … season have not opened" | Source records the page at one point in time → time-bound chunks in the corpus → retrieved for a question about a later climb → the user receives an outdated rule with an official citation, which looks more trustworthy than an unofficial answer |
| Representation bias | All 27 chunks from one source owner and one site; Sipadan, Tunku Abdul Rahman Marine Park, entry documents and the monsoon have 0 chunks | Source selection, plus S02 and S03 not processed → corpus covers only Mount Kinabalu → a question about Sipadan retrieves the nearest Mount Kinabalu rules → the user may receive rules for the wrong site, or general knowledge presented as grounded. International travellers are potentially more affected, because entry documents are missing entirely |
| Measurement bias | The International fee rows were cut from their labels (original chunk 3); two breakdown tables were missing; 17 headings were split from their content | Pipeline decision (web page saved as PDF, fixed 500-character chunks) → values separated from the labels that say whom or what they apply to → retrieval returns a price or time without its label → the user may receive the wrong fee for their traveller category or the wrong time for an activity |

These are potential risks identified from the data. The corpus does not show that the final system is unfair; requirement recall by site and traveller category will be measured in Topic 8 (Week 10).

## 5. Privacy, Consent, Rights/Access, and Data Governance

| Question | Evidence | Status | Required action / owner |
|---|---|---|---|
| Source ownership, version and provenance | Owner, retrieval date and SHA-256 recorded for every chunk; R2 and R3 passed. The corpus mixes the PDF (2026-11-08) and HTML (2026-11-25) snapshots of S01 | OK for traceability; Gap for version consistency | Rebuild S01 from one HTML snapshot / Tan Qian Hui, before Week 9 |
| Rights / access basis | Publicly accessible; each owner states "all rights reserved"; no licence or written permission. robots.txt allows the HTML retrieval. Raw copies are kept unchanged for non-commercial educational use, with attribution and removal on request ([`data/README.md`](../data/README.md)); the root README now states the same | To verify (decision: proceed with conditions) | Keep these conditions; add the HTML snapshot to `data/README.md` / Tan Qian Hui, this commit |
| Personal / sensitive data | Cell 7 scan: 0 MyKad or passport numbers; 1 email and 3 phone numbers, all organisational contacts of Sabah Parks and its booking service ([`week4_privacy_scan.csv`](week4_privacy_scan.csv)) | No | Re-run the scan for every new source / Tan Qian Hui |
| Consent / collection basis | Public institutional documents only, so consent does not apply to the corpus. For user input, RAI6 limits collection to the traveller category and forbids sending identity numbers to external model APIs | N/A for the corpus | Test RAI6 when the input stage is built / Tan Qian Hui, Topic 8 |
| Update, version and removal responsibilities | 90-day review period (RAI5); time-sensitive chunks flagged; raw snapshots archived with hashes; removal on request committed in `data/README.md`. No procedure yet for replacing superseded chunks | Gap | Define the update procedure in the V2 pipeline / Tan Qian Hui |

**Unresolved governance gaps:** mixed snapshots of S01; rights not formally verified; no procedure for superseded versions.

## 6. Mitigation Plan and Residual Risk

| Issue | Evidence | Action | Owner / timing | Residual risk |
|---|---|---|---|---|
| V1 sites and entry documents not covered | B2: 0 chunks for Sipadan, Tunku Abdul Rahman Marine Park, entry documents, monsoon | Add one official source per area; add a pipeline coverage check that holds the gate when a V1 site has no official source; answer "not covered" until then (FR6) | Tan Qian Hui / before Week 9 | Official information for some areas may be unavailable or not public; those areas would stay out of scope |
| S02 and S03 not processed | Cell 6: inventoried, 0 chunks | Process both in the V2 pipeline | Tan Qian Hui / before Week 9 | Low once processed; S03 is a secondary (NGO) source and must be labelled as such (FR3) |
| PDF conversion losses (tables, headings, collapsed content) | A3, Cell 4D: flattened tables; 2 tables missing | Ingest web pages as HTML; keep table verification (Check A, Check B, negative test) in the pipeline; keep the PDF only as an archived snapshot | Tan Qian Hui / V2, before Week 9 | Another site's HTML may have a different structure, so the table checks may need adapting |
| Headings split from content | R7: 17 chunks; 14 still flagged | Chunk by section heading in V2 | Tan Qian Hui / V2 | Very long sections may still need splitting; check with R7 after re-chunking |
| Letter-spaced headings | R6: 10 chunks | Read headings from HTML elements instead of PDF text | Tan Qian Hui / V2 | Low |
| Time-sensitive rules | A2: 5 chunks; 2027 booking status | Re-retrieve within 90 days and compare hashes; show the last-checked date with every requirement | Tan Qian Hui / by 2027-02-06 and each 90 days | A rule may change between checks; the system must show the last-checked date so users can confirm with the official source |
| Mixed S01 snapshots | B4: PDF 2026-11-08, HTML 2026-11-25 | Rebuild all S01 chunks from one snapshot | Tan Qian Hui / V2 | Low |
| Rights not formally verified | B4 | Keep raw copies under attribution, non-commercial use and removal on request | Tan Qian Hui / ongoing | A rights holder may ask for removal; the pipeline can then use the URL and hash only |

## 7. Impact on Topic 2 Requirements / Evaluation Criteria

| Requirement / criterion | Threshold | How unresolved data issues could cause failure |
|---|---|---|
| FR2 / NFR2 requirement recall | ≥ 90% | Scenarios for Sipadan, Tunku Abdul Rahman Marine Park or entry documents cannot reach the threshold while these areas have no source |
| FR3 / RAI4 citation–source match | ≥ 95% | Split headings can produce a citation without the rule it introduces, or a rule without its heading |
| FR4 / NFR3 deadline accuracy | 100% | A time separated from its activity (e.g. the dinner time) could be used for the wrong item |
| FR5 seasonal warnings | — | No monsoon source; the haze source is not yet processed |
| FR6 / RAI2 refusal and redirect | ≥ 95% | If the system does not detect that a site is uncovered, it may answer with Mount Kinabalu rules instead of declining |
| FR8 entry-document redirect | — | No official immigration source to redirect to |
| RAI1 recall gap between traveller categories | ≤ 10 points | Missing entry documents and the table defects affected international travellers more; the gap cannot be measured until Topic 8 |
| RAI5 expired rules flagged | 100% (90-day period) | Requires the retrieval date and time-sensitive flags to be kept through the V2 pipeline |
| NFR6 rule update time | < 10 min | Needs the update procedure that is still missing (Section 5) |

## 8. Topic 4 Quality Gate Decision

**Decision: GO WITH CONDITIONS — for Mount Kinabalu only.**

The reviewed corpus is traceable, contains no duplicates or personal data, and its fee tables have been verified against the source, so it can be used for Mount Kinabalu requirements in later stages. The decision is not a full GO, because coverage of the V1 scope is incomplete and some defects are controlled by flags rather than fixed. It is not a HOLD, because the remaining issues are documented, they do not affect the traceability of what the corpus contains, and the uncovered areas can be declined safely under FR6.

**Conditions**

1. Questions about Sipadan Island, Tunku Abdul Rahman Marine Park, entry documents and the monsoon are answered as "not covered" until an official source for each has been added and passed the Topic 4 checks.
2. Before Week 9, the V2 pipeline reads web pages as HTML, chunks by section heading, processes S02 and S03, and rebuilds S01 from a single snapshot; the Topic 4 rules (R1–R7, table checks and privacy scan) are re-run on its output.
3. Time-sensitive chunks keep their re-verify flag, and the page is re-retrieved within the 90-day review period.
4. The raw copies remain under attribution, non-commercial use and removal on request.

Topic 7 (Week 9) must use the reviewed Topic 4 corpus (`data/prepared_chunks_clean.jsonl` and `data/chunks_clean.pkl`), not the original Topic 3 output.
