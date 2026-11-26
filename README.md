# PROJECT EDEN — Greenhouse Digital Twin

PROJECT EDEN (Environmental Digital Twin for Ecosystem Intelligence and Navigation) is a reproducible greenhouse digital twin project. It combines climate and control data, RGB-D crop observations, physics-guided multimodal modeling, virtual model predictive control, and an interactive dashboard. This README distinguishes completed artifacts from planned work.

## Current state

- **Phase 1 — Data engineering & inventory (EDEN-00–03): in progress.** EDEN-00 v1.1 and EDEN-01 v1.1 have saved successful outputs. EDEN-02 v1.1 has a saved `PASS_RGBD_PILOT` result with 36 RGB/depth pairs. EDEN-03 v1.0 ran and produced `PARTIAL_PASS_QC_FLAGS_PRESENT`; mask and image QC review remains open.
- **EDEN-04 v1.0:** a provisional, unrun climate baseline notebook is prepared in the Phase 2 folder. Its one-hour air-temperature horizon and scope need confirmation against the full SRS.
- **EDEN-05–15 and dashboard:** planned; no implementation or performance results are claimed.
- The handoff names 16 notebooks and six phases, but the complete SRS defining phases 2–6 is not present in this workspace. Those phase boundaries remain pending specification rather than guessed.

## Repository structure

- `phases/phase-1-data-engineering/notebooks/` contains latest notebook versions available here.
- `phases/phase-2-climate-forecasting/notebooks/` contains the provisional EDEN-04 baseline notebook.
- `archive/phase-1/prior-notebook-versions/` retains originals and earlier versions.
- Results archives and raw data are excluded because they are large; EDEN-02 result ZIP is ~559 MB and the source camera archive is ~31.4 GB.

## Phase 1 — Data engineering & inventory

| Notebook | Purpose | Latest saved result | Outputs |
| --- | --- | --- | --- |
| EDEN-00 v1.1 — Inventory | Pin official source; inspect numeric archive and schemas; record hashes | 8/8 manifest entries verified, eight genuine CSVs, zero parse errors | inventory, source manifest, schema inventory/map, reports |
| EDEN-01 v1.1 — Climate ETL | Normalize time, preserve QC/missingness, align climate/weather/controls, freeze splits | `PASS_ETL`; 29/29 tracked hashes verified; 129,966 compartment rows | climate, weather, controls, raw tables, splits, quality reports |
| EDEN-02 v1.1 — RGB-D acquisition | Select bounded image pilot; selectively read archive ranges; pair/decode RGB and depth | `PASS_RGBD_PILOT`; 36 pairs; empty decode-failures report | image index, RGB/depth PNGs, calibration candidates, pairing/acquisition reports |
| EDEN-03 v1.0 — Spatial processing | Create first-pass green-pixel masks, summarize native depth codes, and flag image QC | `PARTIAL_PASS_QC_FLAGS_PRESENT`; 36 pairs processed; 41/41 output hashes verified; 4 dark-image flags; 9 empty masks | image-space features, masks, montage, QC report |

### EDEN-00/01 findings

EDEN-01 saved output reports 129,966 compartment rows, five indoor and six outdoor climate signals, 13 control signals, 129,887 valid target triplets, and 12 missing weather ticks. One suspicious -10°C Reference reading was flagged and retained. Saved primary split counts: 86,740 train / 20,317 validation / 22,909 test. These are pipeline/data checks, not model-performance results.

### EDEN-02 saved run

The supplied `EDEN-02-RGBD-Acquisition-Results-v1.1.zip` is 559,027,047 bytes. Its manifest identifies producer EDEN-02, v1.1, `PASS_RGBD_PILOT`, source DOI `10.4121/fa102772-32db-4b30-bace-12f2016722ce.v1`, upstream EDEN-01 manifest SHA-256 `204286cacea8d02ba33f391292e2953c210988322de2b8d2dbbb1aef1a7670ac`, and source verification mode `http_range_partial_crc`. It contains 36 RGB and 36 depth PNGs; paired CSV/Parquet indices; six calibration candidates and an index; pairing diagnostics, archive inventory, selected-pair table, montage, split file, source metadata, licenses, decode-failure report, and per-file SHA-256 entries.

**Calibration limit:** candidate files do not yet prove metric calibration. Verify numeric intrinsics, depth units/invalid codes, and reference-plane semantics before reporting metric geometry. Until then, label features as image-space or relative and preserve original depth values. The pilot is a subset, not the complete image dataset.

### EDEN-03 saved run

The EDEN-03 v1.0 result ZIP contains 41 manifest-listed output files; every size and SHA-256 matched. It processed 36 pairs and reports `PARTIAL_PASS_QC_FLAGS_PRESENT`, with no metric geometry. Four images were flagged as very dark and nine produced empty green-pixel masks. Mask coverage ranged from 0 to 99.90% (median 16.00%), showing the color heuristic needs human review before these masks are used downstream. The four flagged images include the two previously reviewed dark frames and dark frames in 3.07 and 3.08. Depth remains `unverified_native`; numeric calibration remains false. The result ZIP is excluded from Git.

## Roadmap and gates

The handoff describes notebooks EDEN-00 through EDEN-15 across six phases. Only Phase 1's notebook allocation (00–03) is specified in the files currently supplied. Detailed definitions for phases 2–6 await the full SRS.

| Phase | Scope currently known | State |
| --- | --- | --- |
| 1 — Data engineering & inventory | EDEN-00–03: numeric inventory, climate ETL, RGB-D pilot, spatial processing | In progress; 00–02 pass saved outputs, EDEN-03 partial with QC review open |
| 2 | Full SRS phase detail unavailable | Pending specification |
| 3 | Full SRS phase detail unavailable | Pending specification |
| 4 | Full SRS phase detail unavailable | Pending specification |
| 5 | Full SRS phase detail unavailable | Pending specification |
| 6 | Full SRS phase detail unavailable | Pending specification |

### EDEN-04 provisional notebook

[EDEN-04 Climate Forecasting Baseline v1.0](phases/phase-2-climate-forecasting/notebooks/EDEN-04-Climate-Forecasting-Baseline-v1.0.ipynb) is prepared but has not been run. It uses EDEN-01 climate data to compare persistence and same-time-yesterday references for one-hour-ahead air temperature, scoring the primary and chronological protocols separately. This is a working outline because the full SRS is unavailable; no forecast metrics are claimed yet.

Later project areas listed in the handoff include climate forecasting, crop-state estimation, physics-guided multimodal fusion, virtual MPC, and a Three.js dashboard. These remain planned until their module mapping and runnable notebooks are available.

## Reproducibility rules

- Use experiment `AGC4-2024`, compartment IDs as strings, timezone-aware UTC, and five-minute keys.
- Keep frozen `splits.json`: primary train 3.01/3.02/3.03/3.06, validation 3.07, test 3.08. Preserve chronological embargoes; do not randomly split or tune on holdout.
- Keep realized actuator states distinct from setpoints. Preserve missingness/QC; never invent replacement values.
- Verify producer/status, file sizes, and SHA-256 at each handoff. Freeze models before test; report baselines, ablations, and uncertainty.
- Do not commit large source/result bundles or secrets. Preserve attribution and CC BY 4.0 licensing.

## Run instructions

Run latest notebooks in order in Kaggle. Use CPU and internet for EDEN-00/01/02. Attach EDEN-01 v1.1 output to EDEN-02 (ZIP or supported extracted folder). Keep selective HTTP Range access; do not download the full camera archive. For EDEN-03 v1.0, use CPU, Internet Off, and attach the named EDEN-02 result ZIP. Review its partial status, empty masks, and dark-image flags before relying on spatial outputs. For provisional EDEN-04 v1.0, use CPU, Internet Off, and attach the EDEN-01 v1.1 `PASS_ETL` result. Download its named result ZIP and review baseline metrics before planning further models. Require each notebook's acceptance status and verify its output manifest. EDEN-03 v1.0 reports image-space features and native depth codes only; it must not claim metric geometry until calibration and depth units are validated.

## Source

Maree et al. (2025), [4th Autonomous Greenhouse Challenge: Dwarf Tomato Timeseries and Images, v1](https://doi.org/10.4121/fa102772-32db-4b30-bace-12f2016722ce.v1), 4TU.ResearchData, CC BY 4.0.

### Output-captured EDEN-02 notebook

The companion [EDEN-02-RGBD-Acquisition-v1.1-with-outputs.ipynb](phases/phase-1-data-engineering/notebooks/EDEN-02-RGBD-Acquisition-v1.1-with-outputs.ipynb) preserves the user's executed notebook and cell outputs. Its saved run verifies the EDEN-01 manifest and 29 hashes, indexes 7,411 genuine camera archive members, finds 1,851 RGB/depth pairs (1,765 eligible; zero ambiguous), selects 36 pairs across the frozen splits, decodes all 36 at 2160×3840 as uint16 depth, and reports PASS for the RGB-D pilot. The notebook explicitly leaves manual montage review and depth-unit/geometry verification pending. Its saved named output ZIP is 559,027,047 bytes; the ZIP itself remains excluded from Git.

## In-depth phase and cell guide

See [the phase roadmap, short jargon glossary, and cell-by-cell guide for EDEN-00–02](docs/phase-details-and-cell-guide.md). Later phase scopes are labeled as a working outline because the full SRS is not present; the guide does not invent future notebook cells.
