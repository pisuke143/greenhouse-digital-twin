# PROJECT EDEN — Greenhouse Digital Twin

PROJECT EDEN (Environmental Digital Twin for Ecosystem Intelligence and Navigation) is a reproducible greenhouse digital twin project. It combines climate and control data, RGB-D crop observations, physics-guided multimodal modeling, virtual model predictive control, and an interactive dashboard. This README distinguishes completed artifacts from planned work.

## Current state

- **Phase 1 — Data engineering & inventory (EDEN-00–03): in progress.** EDEN-00 v1.1 and EDEN-01 v1.1 have saved successful outputs. EDEN-02 v1.1 has a saved `PASS_RGBD_PILOT` result with 36 RGB/depth pairs. EDEN-03 v1.0 ran and produced `PARTIAL_PASS_QC_FLAGS_PRESENT`; mask and image QC review remains open.
- **EDEN-04 v1.0:** saved run received; all 6 manifest file hashes verified. Saved status is `PASS_BASELINE_EVAL_PROVISIONAL`, but review found seasonal history exceeding the six-hour limit and crossing chronological split boundaries. Treat these scores as diagnostic.
- **Legacy EDEN-05 climate prototype:** saved run reviewed; all 15 manifest hashes verified. Validation-only Ridge results are recorded below. This is early work for original Notebook 08, not original vision Notebook 05.
- **EDEN-06 vision recovery:** implemented and statically checked, awaiting Kaggle execution. Consolidates the missing original 04 vision baselines, 05 RGB-D model comparisons, and 06 frozen feature extraction.
- **EDEN-07–15 and dashboard:** planned. The recovered original handbook and existing Notion phase map define their allocation; see the roadmap below.
- **Plan correction:** the climate notebooks named 04/05 were produced out of the original sequence. Their files and results are retained as climate prototypes. They do not establish completion of the original vision stages.

## Repository structure

- `phases/phase-1-data-engineering/notebooks/` contains latest notebook versions available here.
- `phases/phase-2-visual-intelligence/notebooks/` contains the consolidated EDEN-06 vision notebook.
- `phases/phase-2-climate-forecasting/notebooks/` preserves the legacy climate prototypes named EDEN-04/05; their results may inform original Notebook 08.
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

The original **EDEN SRS & Implementation Handbook rev 1.0** and the existing Notion roadmap agree on this mapping. The handbook was recovered from the user's Downloads; the separately named v2 SRS remains unavailable. Recovery changes where the missing vision work is implemented, not the remaining milestone sequence.

| Phase | Original notebooks and purpose | Current state / gate |
| --- | --- | --- |
| 1 — Data engineering & inventory | 00 inventory; 01 climate ETL; 02 RGB-D acquisition; 03 spatial processing | Saved 00–03 results; 03 has unresolved mask/calibration QC. |
| 2 — Visual intelligence & climate baselines | 04 vision baselines; 05 RGB-D model; 06 frozen features; 07 climate/vision alignment; 08 climate baselines | Missing original 04–06 work consolidated into new 06. Kaggle run and review pending. 07/08 not implemented. |
| 3 — Physics, temporal modeling & fusion | 09 physics calibration; 10 action-conditioned temporal model; 11 physics/vision/climate fusion | Planned. Fit on training only; compare matched models and missing-image behavior. |
| 4 — Twin replay & virtual control | 12 replay and scenarios; 13 constrained virtual control | Planned. Distinguish historical replay from simulated intervention; no physical actuators. |
| 5 — Research evaluation & browser export | 14 frozen evaluation; 15 browser model/artifact export | Planned. Record earlier test exposure; verify evaluation protocol and export parity. |
| 6 — Website, dashboard & release | Static dashboard, browser inference, deployment | Planned. One coherent state for 2D/3D views, provenance, uncertainty, replay and sandbox. |

### EDEN-04 saved run and review

All 6 manifest-listed output files passed size/SHA-256 verification. Saved validation MAE (degrees Celsius): primary persistence 0.8282 versus yesterday 0.9453; chronological persistence 0.8236 versus yesterday 1.1568. These are the saved values, not acceptance of the evaluation design.

The yesterday predictor uses a reading 23 hours before the forecast origin, beyond EDEN-01's six-hour maximum history. In the chronological results, 1,656 validation rows and 1,656 test rows use seasonal history from another split or embargo. The original result remains preserved as evidence; the seasonal scores do not satisfy the frozen contract. EDEN-05 recomputes persistence on its own matched, valid windows rather than comparing against these rows.

### Legacy EDEN-05 saved climate result

All 15 manifest-listed files match their sizes and SHA-256 values. Saved status: `PASS_VALIDATION_MODEL_PILOT_PROVISIONAL`. It fixes the earlier split-window problem and scores validation only. Primary validation: Ridge MAE **0.8188 °C**, matched persistence **0.8305 °C** (20,138 rows). Chronological validation: Ridge **0.7832 °C**, persistence **0.8229 °C** (27,444 rows). These are exploratory development results, not untouched-test performance or proof that the original vision module was completed.

### Next to run: EDEN-06 vision recovery and features

[EDEN-06 Vision Recovery and Features v1.0](phases/phase-2-visual-intelligence/notebooks/EDEN-06-Vision-Recovery-and-Features-v1.0.ipynb) implements all three missing original vision stages in one runnable notebook:

1. **Original 04:** color masks/depth measurements and mean/Ridge/Random Forest trait baselines with grouped validation.
2. **Original 05:** MobileNetV3 color branch plus a small depth network; paired augmentation, missing-label-aware loss, RGB-only/depth-only/RGB-D comparisons.
3. **Original 06:** freeze the encoder; export embeddings, trait estimate means/variance, quality flags, and genuine grouped out-of-fold training predictions.

**Kaggle settings:** GPU, Internet On. Attach **EDEN-01 v1.1, EDEN-02 v1.1 and EDEN-03 v1.0** results, as ZIPs or extracted folders. No additional manually uploaded dataset is required: the notebook selectively reads **383 labeled pre-trial pairs, about 5.61 GB**, from the public source. It excludes publisher test and infrared images. The full archive is not downloaded; checked resized pairs are cached in the session. Three model comparisons and three fold fits are included, with resumable epoch checkpoints.

**Data separation:** 2023 development plants are grouped by plant identity across dates; the notebook creates a reproducible 80/20 development split and three training folds. Publisher train/validation sets are pooled for this new grouped protocol; publisher test is excluded. These splits do not modify the frozen 2024 climate/image splits. The final encoder is trained on disjoint 2023 data. Fold predictions in `oof_features.parquet` are explicitly 2023 traits; 2024 `vision_features.parquet` uses the disjoint frozen encoder alternative allowed by the handbook.

**Limits:** single-plant training does not validate physical traits in canopy photographs. Canopy predictions use `proxy_*` fields and `transfer_validated=False`; dark/unusable images retain rows with missing model features. Depth remains relative, green masks are unvalidated, and dropout variance is not calibrated uncertainty. The result reports implementation checks separately from model usefulness. Source preparation inspected test metadata; no test performance or test-driven selection was used. The notebook itself does not read test labels.

**Return:** `EDEN-06-Vision-Recovery-and-Features-Results-v1.0.zip`. It contains the frozen model/config, comparison metrics, predictions, both feature tables, split records, source receipts and a hash manifest. After reviewing the real result, continue with **original EDEN-07 alignment**, then **EDEN-08 climate baselines**. Notebook cells have not been executed locally.

## Reproducibility rules

- Use experiment `AGC4-2024`, compartment IDs as strings, timezone-aware UTC, and five-minute keys.
- Keep frozen `splits.json`: primary train 3.01/3.02/3.03/3.06, validation 3.07, test 3.08. Preserve chronological embargoes; do not randomly split or tune on holdout.
- Keep realized actuator states distinct from setpoints. Preserve missingness/QC; never invent replacement values.
- Verify producer/status, file sizes, and SHA-256 at each handoff. Freeze models before test; report baselines, ablations, and uncertainty.
- Do not commit large source/result bundles or secrets. Preserve attribution and CC BY 4.0 licensing.

## Run instructions

Run the phase-1 notebooks in their documented order. EDEN-03 requires the EDEN-02 result and produces image-space/native-depth features, with QC review still pending. For the next run, use the EDEN-06 inputs and GPU settings above; the legacy climate 04/05 results are not inputs to vision recovery. Require each handoff's manifest verification and review its limitations before moving on.

## Source

[Labeled dwarf-tomato pre-trial, v1](https://doi.org/10.4121/e1ee9de9-6ce9-4502-a37c-34b5b1372bed.v1), Wageningen University & Research / 4TU.ResearchData, CC BY 4.0. Used for supervised single-plant training in EDEN-06.

Maree et al. (2025), [4th Autonomous Greenhouse Challenge: Dwarf Tomato Timeseries and Images, v1](https://doi.org/10.4121/fa102772-32db-4b30-bace-12f2016722ce.v1), 4TU.ResearchData, CC BY 4.0.

### Output-captured EDEN-02 notebook

The companion [EDEN-02-RGBD-Acquisition-v1.1-with-outputs.ipynb](phases/phase-1-data-engineering/notebooks/EDEN-02-RGBD-Acquisition-v1.1-with-outputs.ipynb) preserves the user's executed notebook and cell outputs. Its saved run verifies the EDEN-01 manifest and 29 hashes, indexes 7,411 genuine camera archive members, finds 1,851 RGB/depth pairs (1,765 eligible; zero ambiguous), selects 36 pairs across the frozen splits, decodes all 36 at 2160×3840 as uint16 depth, and reports PASS for the RGB-D pilot. The notebook explicitly leaves manual montage review and depth-unit/geometry verification pending. Its saved named output ZIP is 559,027,047 bytes; the ZIP itself remains excluded from Git.

## In-depth phase and cell guide

See [the original phase roadmap and short notebook cell guide](docs/phase-details-and-cell-guide.md). It distinguishes the original module plan, historical climate prototypes, and the consolidated EDEN-06 implementation.
