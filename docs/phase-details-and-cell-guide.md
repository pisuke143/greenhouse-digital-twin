# PROJECT EDEN phase guide and notebook cell notes

This guide separates observed implementation from the roadmap. Cell notes below were derived from the notebook source and, for EDEN-02, its saved execution outputs. Later phases have no notebooks in the supplied workspace; their descriptions explain the project direction without claiming implemented cells or fixed notebook assignments.

## Six-phase project map

The project handoff identifies six phases and 16 notebooks (EDEN-00–15), but the full SRS that defines the exact phase boundaries and notebook mapping was not present in the supplied files. The following is a **working outline inferred from the described project sequence**. Confirm or revise it against the SRS before treating phases 2–6 as the approved architecture.

| Phase | Working scope | Current evidence and gate | Short jargon guide |
| --- | --- | --- | --- |
| 1 — Data engineering and inventory | EDEN-00–03: inspect and verify source data, build climate tables, acquire RGB-D pilot, then spatial processing | 00 and 01 have saved successful outputs; 02 reports PASS_RGBD_PILOT; EDEN-03 reports PARTIAL_PASS_QC_FLAGS_PRESENT. Gate: verified inputs, stable keys/splits, valid pairs, and reviewed calibration/QC before geometry claims. | **ETL** = extract, transform, load data. **RGB-D** = color image plus depth image. **QC** = quality control. **Manifest** = file inventory with hashes and provenance. |
| 2 — Climate forecasting | Prepare forecast targets/features and compare time-aware forecasting baselines using only information available at prediction time | Provisional EDEN-04 v1.0 is prepared but unrun; exact phase/notebook allocation awaits the full SRS. Gate should include chronological validation, leakage checks, baseline comparison, and uncertainty reporting. | **Forecast horizon** = how far ahead a prediction reaches. **Baseline** = simple reference model. **Leakage** = using information that would not exist at prediction time. |
| 3 — Crop-state estimation | Convert image/spatial evidence into crop-state features or estimates and assess their quality against available references | Planned. No phase-3 notebook or result was supplied. Gate should include labeled/traceable observations, held-out evaluation, and image/depth QC. | **Segmentation** = labeling pixels by object, such as plant/background. **Feature** = measured input used by a model. **Ground truth** = reference label or measurement used for evaluation. |
| 4 — Physics-guided multimodal fusion | Combine climate/control time series and crop-state observations with physical constraints or process knowledge | Planned. No phase-4 notebook or result was supplied. Gate should compare climate-only, vision-only, and fused approaches with matched evaluation splits. | **Multimodal** = using different data types together. **Fusion** = combining information sources. **Ablation** = remove one component to see what it contributes. |
| 5 — Virtual control and MPC | Simulate candidate greenhouse control actions and evaluate constrained virtual model predictive control | Planned. No phase-5 notebook or result was supplied. No physical actuators are in scope per handoff. Gate should test constraints, uncertainty, and safe simulated scenarios before comparing policies. | **MPC** = model predictive control; repeatedly forecasts outcomes and chooses an action over a moving time window. **Setpoint** = desired target. **Constraint** = limit an action or state must obey. |
| 6 — Interactive digital twin and delivery | Present historical replay, forecasts, crop state, and virtual control scenarios in a static interactive Three.js dashboard | Planned. No phase-6 notebook or site was supplied. Gate should check provenance, state consistency, uncertainty visibility, and reproducible static deployment. | **Three.js** = JavaScript library for 3D graphics. **Replay** = show recorded data over time. **Provenance** = where data/results came from. |

## Phase 1 — cell-by-cell guide

### EDEN-00 v1.1 — Dataset inventory and acquisition

Goal: pin the official source, inspect the numeric archive, and create a traceable inventory before transforming data.

| Cell | What it does | Jargon in brief |
| --- | --- | --- |
| 1. Environment and directories | Imports file/data tools, creates the working/raw/report folders, records runtime versions and checks available disk. | **Path** = address of a file or folder. **Runtime** = language and library versions used for a run. |
| 2. Pin the official source | Records DOI, version, authors, license, source file IDs, expected sizes and publisher MD5 values in a source manifest. | **DOI** = persistent identifier for a research object. **License** = rules for reuse. **MD5** = checksum used here to compare with publisher metadata. |
| 3. Download and verify | Reuses a valid attached copy or streams the permitted files to temporary paths; validates size and MD5 before finalizing and records SHA-256. | **Stream** = process a file in chunks. **Checksum/hash** = compact fingerprint used to detect changed bytes. |
| 4. Read original documentation | Displays the publisher README so data interpretation starts from source documentation. | **Provenance** = record of origin and handling. |
| 5. Inventory numeric archive | Checks ZIP CRC integrity and writes a table of member paths, extensions, sizes and CRCs without extracting everything. | **ZIP member** = file stored inside a ZIP. **CRC** = archive-level corruption check. |
| 6. Inspect every CSV schema | Excludes macOS metadata sidecars from parsing; detects delimiters/headers and records columns, types, samples, missingness and parse errors. | **Schema** = table structure and field types. **Sidecar** = extra metadata file, not a data table. **Missingness** = where values are absent. |
| 7. Propose canonical mappings | Uses field-name patterns to propose standard meanings while marking mappings for human review rather than silently asserting units. | **Canonical** = consistent standard form. **Mapping** = connecting a source field to a standard field. |
| 8. Write report and handoff manifest | Saves inspection reports, proposed schema artifacts, and a file manifest with sizes and SHA-256 values. | **Handoff** = outputs passed to the next notebook. |
| 9. Acceptance gate | Checks required files and inventory conditions; reports whether the acquisition/inspection artifact is ready for downstream work. | **Acceptance gate** = explicit checks that must pass before proceeding. |

Observed EDEN-00 v1.1 checkpoint: eight manifest entries verified, eight genuine CSV tables, zero CSV parse errors. Sidecar exclusion corrected false CSV parsing.

### EDEN-01 v1.1 — Climate data ETL

Goal: transform source tables into consistent, time-aware climate/weather/control datasets and freeze evaluation splits.

| Cell | What it does | Jargon in brief |
| --- | --- | --- |
| 1. Initialize environment | Loads libraries, establishes output folders, and sets reproducible run configuration. | **Reproducible** = same inputs and settings can be checked or rerun. |
| 2. Locate and verify EDEN-00 | Finds upstream ZIP or extracted output and verifies the producer manifest and file hashes before reading. | **Upstream** = earlier pipeline stage. **SHA-256** = content fingerprint. |
| 3. Extract tables and metadata | Reads genuine compartment/weather tables plus publisher metadata; associates team tables with string compartment IDs. | **Metadata** = information describing the data. **Identifier** = stable label for a row/entity. |
| 4. Parse timestamps and measure gaps | Converts timestamps to UTC, checks duplicate keys and intervals, and measures missing five-minute ticks. | **UTC** = shared global time standard. **Timestamp** = date and time. **Gap** = expected observation missing from the timeline. |
| 5. Approve metadata-backed schema | Flattens publisher channel metadata and checks source fields/units before selecting canonical signals. | **Flatten** = turn nested fields into a simpler list/table. **Unit** = measurement scale such as °C. |
| 6. Transform climate and controls | Builds normalized climate and control tables keyed by experiment, compartment and UTC timestamp; preserves realized values separately from setpoints. | **Composite key** = multiple fields together identify one row. **Setpoint** = desired target, distinct from measured/realized state. |
| 7. Align measured weather | Joins weather observations to compartment climate on experiment and time with a many-to-one check; records observation flags. | **Join** = combine tables by matching keys. **Many-to-one** = many climate rows may share one weather row. |
| 8. Quality checks and masks | Flags out-of-range/suspicious values and missing observations without fabricating replacements; produces QC summaries. | **Mask/flag** = indicator showing whether a value is present or passes a check. **Outlier** = unusually extreme value needing review. |
| 9. Freeze evaluation protocols | Assigns primary compartment holdouts and a separate chronological protocol with embargoes; checks splits remain disjoint. | **Holdout** = data reserved for evaluation. **Embargo** = buffer interval excluded to reduce leakage. |
| 10. Explore without training | Plots descriptive time series and distributions for inspection; does not fit predictive models. | **Exploratory** = inspect patterns before modeling. |
| 11. Save data contract and artifacts | Writes Parquet tables, schema/split definitions, reports and plots. | **Parquet** = compressed column-oriented table format. **Data contract** = documented expectations for fields, types and keys. |
| 12. Acceptance gate and manifest | Checks key uniqueness, expected outputs and hashes, then records producer status and output manifest. | **Unique key** = no repeated row identifier. **Manifest** = verified list of output files. |

Observed EDEN-01 checkpoint: saved manifest reports PASS_ETL and 29/29 file hashes verified; 129,966 compartment rows; 129,887 valid target triplets; 12 missing weather ticks. One suspicious -10°C Reference air-temperature value was flagged and retained. Primary split counts: 86,740 train / 20,317 validation / 22,909 test. These describe pipeline/data checks, not forecasting performance.

### EDEN-02 v1.1 — RGB-D acquisition and pairing (executed notebook)

Goal: verify EDEN-01, selectively inspect the large camera ZIP, choose a stratified pilot, pair/decode images, and package a manifest-backed result.

| Cell | What it does | Jargon in brief |
| --- | --- | --- |
| 1. Environment and pilot settings | Loads image, archive, table and hashing libraries; sets pilot/network/storage limits and output directories. | **Budget** = pre-set resource limit, such as bytes or pairs. |
| 2. Find and verify EDEN-01 | Discovers attached ZIP/folder, checks producer/status and all 29 upstream hashes, then loads climate/splits. | **Integrity check** = verify data has not changed. |
| 3. Open canopy archive safely | Uses selective HTTP Range reads and CRC checks to inspect archive contents without downloading the full ~31.4 GB archive. | **HTTP Range** = request only a byte range from a server. **CRC** = ZIP member corruption check. |
| 4. Detect RGB/depth pairs | Parses actual camera filenames, timestamps and camera IDs; reports eligible, unresolved and ambiguous pair keys. | **Pairing** = match the RGB and depth files for one observation. **Ambiguous** = more than one plausible match. |
| 5. Select reproducible pilot | Samples 36 pairs stratified across compartments and train/validation/test splits using frozen split rules. | **Stratified** = preserve representation across selected groups. **Pilot** = small trial subset. |
| 6. Acquire calibration metadata | Discovers six actual camera configuration JSONs and maps each camera ID to its own config. | **Calibration** = relationship between image pixels/depth values and physical camera geometry. **Intrinsic** = camera lens/sensor parameters. |
| 7. Download and decode selected images | Fetches only chosen files, decodes RGB and depth, preserves native depth dtype, and records failures and dimensions. | **Decode** = turn compressed image bytes into pixel arrays. **dtype** = numeric storage type; uint16 is a 16-bit unsigned integer. |
| 8. Visual quality check | Builds a montage and computes simple depth validity summaries for a quick visual/data-quality inspection. | **Montage** = contact sheet of sample images. **Validity** = share/range of values considered usable under current checks. |
| 9. Acceptance and named output ZIP | Checks pair counts, unique IDs/keys and required artifacts, then writes named results ZIP with file hashes and PASS status. | **Acceptance gate** = required checks before handoff. **SHA-256** = file fingerprint. |

Observed saved run: upstream manifest and 29 hashes passed; 7,411 genuine archive members indexed; 1,851 candidate pairs, 1,765 eligible, zero ambiguous keys; 36 selected pairs; all 36 decoded at 2160×3840 with uint16 depth; output status PASS_RGBD_PILOT. The notebook explicitly leaves manual montage review and depth-unit/geometry verification pending. Therefore the run does not yet support metric plant-height claims.

### EDEN-03 v1.0 — Spatial processing (executed notebook)

Goal: use the EDEN-02 pilot to create image-space vegetation masks, summarize native depth codes under those masks, and retain image-quality flags.

| Cell | What it does | Jargon in brief |
| --- | --- | --- |
| 1. Folders and image-space rules | Imports tools, sets input/output paths, preview size, and visible color thresholds. | **Image-space** = pixels or proportions, without real-world units. |
| 2. Verify EDEN-02 result | Finds the named ZIP or extracted folder, confirms producer/version/status, checks every manifest size and SHA-256, then unpacks if needed. | **SHA-256** = file fingerprint used to detect changes. |
| 3. Load pair index | Reads the 36-row RGB/depth table, checks unique IDs and paths, and reads calibration status as evidence only. | **Pair index** = table linking each color image to its matching depth image. |
| 4. Image quality checks | Measures RGB brightness, depth dtype, and shares of code values 0 and 65535; explicitly retains the two reviewed dark frames. | **dtype** = how pixel values are stored; a **code value** is a number whose physical meaning may be unknown. |
| 5. Green-pixel mask | Applies a simple HSV and Excess Green color rule, saves reduced masks, and measures mask area and bounding box in pixels. | **HSV** = hue, saturation, value color representation. **Excess Green** = score emphasizing green over red/blue. **Heuristic** = practical hand-set rule, not a trained model. |
| 6. Native depth summaries | Computes code percentiles under each mask and counts zero/65535 codes without assuming those are invalid. Writes CSV and optional Parquet features. | **Percentile** = value below which a given share of observations falls. **Native depth code** = original stored number, not verified distance. |
| 7. Review montage and report | Places RGB, mask, and display-scaled depth previews together; reports mask coverage and pending review items. | **Display-scaled** = contrast adjusted for viewing only; source depth values remain unchanged. |
| 8. Acceptance gate and ZIP | Requires one feature row and mask per pair, retains QC flags, hashes outputs, and writes the named result ZIP. | **Acceptance gate** = required checks before handoff. |

**Saved run:** `PARTIAL_PASS_QC_FLAGS_PRESENT`; 36 input pairs; all 41 output manifest files passed size and SHA-256 checks. Four RGB frames were flagged very dark; nine masks are empty. Mask coverage ranged from 0 to 99.90%, with a 16.00% median. The montage confirms the green-color heuristic behaves inconsistently across the images and needs review before downstream use.

**Limits:** the color rule is an unvalidated first pass. The notebook does not project points into 3D, calculate plant height, convert depth to millimetres, or claim physical canopy size. Human montage review, threshold review, depth-unit/special-code meaning, numeric intrinsics, and reference-plane verification remain pending.

## Phase 2 — provisional EDEN-04 cell guide

### EDEN-04 v1.0 — Climate forecasting baseline (prepared; Kaggle run pending)

**Working scope:** a simple reference forecast using verified EDEN-01 air-temperature measurements. One hour (12 five-minute steps) is a starter horizon within the split file's 72-step maximum. The full SRS is missing, so this notebook ID, phase assignment, target, and horizon remain provisional.

| Cell | What it does | Jargon in brief |
| --- | --- | --- |
| 1. Forecast settings | Sets `air_temp_c`, the 12-step horizon, and Kaggle folders. | **Horizon** = how far ahead the forecast is made. |
| 2. Find and verify EDEN-01 | Finds one v1.1 PASS_ETL ZIP or extracted folder and verifies all manifest files. | **Manifest** = receipt listing output files and fingerprints. |
| 3. Load climate and splits | Checks target units, unique UTC keys, regular five-minute cadence, and frozen split membership. | **UTC** = shared time standard; **split** = fixed train/validation/test assignment. |
| 4. Build forecast examples | Makes persistence and same-time-yesterday predictions while excluding failed QC rows, embargoes, and windows crossing split boundaries. | **Persistence** = repeat the latest valid reading. **Embargo** = time buffer kept out of evaluation. |
| 5. Score baselines | Reports MAE, RMSE, bias, and per-compartment summaries for separate primary and chronological protocols. | **MAE** = average absolute error; **RMSE** penalizes larger errors more; **bias** = average signed error. |
| 6. Plot and record coverage | Saves a validation example and records window counts and limitations. | **Coverage** = count of usable examples after split and QC rules. |
| 7. Acceptance and ZIP | Checks both baselines and holdout windows, writes a provisional status and hash-listed result ZIP. | **Acceptance gate** = required checks before handoff. |

No EDEN-04 forecast outputs or metrics are claimed until its Kaggle run is returned and reviewed.

## Required next documentation when artifacts arrive

For each future notebook, add a cell table from the actual notebook headings/code, an input/output list, a short glossary for technical terms used in that notebook, saved run evidence, an acceptance gate, and explicit unresolved limitations. Do not write fictional cell descriptions for notebooks that have not been supplied.
