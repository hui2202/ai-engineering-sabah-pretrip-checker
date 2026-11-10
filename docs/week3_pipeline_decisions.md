# Pipeline Decisions — Sabah Pre-Trip Checker

Version: 1.0

Colab notebook: [week3_pipeline.ipynb](https://colab.research.google.com/drive/15oP6IHdPKyiKQ7UdBxEpb6Tz5lNCa2zN?usp=sharing)

## Pipeline Summary

| Item | Decision |
|---|---|
| Topic 2 requirement IDs | **FR3** (original supporting sentence, source reference and last-checked date for each requirement); **RAI5** (last-checked date and detection of outdated rules) |
| Selected source | S01 `sabahparks_mount_kinabalu_climbing.pdf`, the official Sabah Parks Mount Kinabalu climbing guide saved as PDF (retrieved 2026-11-08; page states "Last Updated: 8 November 2026"). Chosen as the representative source because it is the main source of official rules and contains the most formatting problems. |
| Pathway | Pathway A — PDF / text (parsing, cleaning and chunking) |
| Chosen preparation configuration | Normalise spaces and tabs; remove the repeated cookie banner; restore paragraphs by joining lines that do not end a sentence; split into paragraph-aware chunks of at most 500 characters (26 records) |
| Link to Week 2 architecture | Expands the **Official Document Store** component of the V1 architecture (`week2_architecture_v1.png`); its prepared records feed **Passage Retrieval** |
| Output data shape | JSON Lines, one record per chunk. Part B fields: `record_id`, `text`, `source`, `source_sha256`, `processing`, `page`. Part C fields: `id`, `source`, `text`, `page` (source fingerprint and method recorded in `pipeline_manifest.json`) |
| Output location | Part B: `data/processed/prepared_text.jsonl` with lineage in `data/week3_manifest.json`. Part C: `data/pipeline_v1/documents/prepared_text.jsonl` with `data/pipeline_v1/documents/pipeline_manifest.json` |

## Part B Comparison and Evidence

| Approach / Configuration A | Approach / Configuration B | Observed Evidence | Selected Approach and Trade-off |
|---|---|---|---|
| Lab's original cleaning (spaces and tabs only) with fixed-size and paragraph-aware chunking, 500 characters | Same, plus removal of the repeated cookie banner and paragraph restoration before chunking | Configuration A: the extracted text contained 0 blank-line paragraph breaks, so both methods produced the same 24 chunks; the paragraph-aware method was ineffective. Configuration B: 22 fixed chunks versus 26 paragraph-aware chunks; the key rule "Online self-check-in becomes available five days before the climb." was split in the fixed chunks but complete in the paragraph-aware chunks (record `sabahparks_mount_kinabalu_climbing-0008`, which also keeps the related Pre-Climb Preparation text). | **Configuration B, paragraph-aware.** It keeps complete official sentences with their context, which FR3 requires. Trade-off: two extra cleaning steps and slightly more chunks (26 instead of 22). |

## Stage Contracts

| Stage | Contract / evidence | Failure response |
|---|---|---|
| Ingest | The exact approved source file exists in `data/raw/` and its SHA-256 fingerprint is computed | Stop for missing input |
| Inspect | pypdf extracts readable text (10 pages, 11,741 characters, no empty pages) | Stop and flag the source if no text is extracted; OCR or manual transcription is needed |
| Prepare | Cookie banner removed, paragraphs restored and 26 paragraph-aware chunks produced, matching Part B | Stop and report the transformation failure |
| Validate | `prepared_text.jsonl` exists, is non-empty, every chunk contains text, and a repeated run produces identical fingerprints (Cell 9: PASS) | Stop for missing or empty output |
| Record lineage | The pipeline manifest links the source and its SHA-256 to the exact output and processing method | Stop and report missing mapping or metadata |

## Engineering Decisions

| Decision | Choice and rationale |
|---|---|
| ETL or ELT | **ETL.** Text is extracted and transformed (cleaned and chunked) before the prepared records are loaded into the output store, because retrieval for FR3 needs clean, complete sentences. The raw PDF is kept unchanged in `data/raw/`, so the transformation can be rerun if the cleaning rules change. |
| Batch or streaming | **Batch.** Official rules change occasionally rather than continuously, so the pipeline is rerun manually when a source is updated or re-downloaded. Streaming would add complexity without benefit. |
| Cleaning scope | Only noise justified by inspection evidence was removed (the cookie banner repeated 10 times). Additional rules were not added, to avoid altering official wording. |

## Accepted Limitations

- **Single source in V1:** only S01 is processed. S02 (booking terms) and S03 (haze poster) are listed in the data inventory and will be added in a later iteration; Sipadan Island and Tunku Abdul Rahman Marine Park are not yet covered.
- **Remaining noise:** navigation menu text and letter-spaced headings (e.g. "L E M A I N G") remain in the first chunk; they do not affect rule sentences.
- **No page references:** the basic splitter processes the whole document, so `page` is `None` in every record.
- **Simple paragraph rule:** paragraph restoration relies on line endings and line length, so a long heading without punctuation may be joined to the following line.

## Provenance and Access Caveats

- **Version dates:** S01 states its own update date, but S02 and S03 do not, so retrieval dates are recorded as metadata. A changed SHA-256 on re-download indicates that the source has changed and its rules must be re-verified (RAI5).
- **Rights:** the source is © Sabah Parks, all rights reserved. The raw copy is included with attribution and a removal-on-request disclaimer (`data/README.md`), as recorded in the Week 2 architecture decisions.
