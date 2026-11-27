# PROJECT EDEN phase guide and notebook cell notes

This guide records actual source and saved evidence. The original handbook and Notion phase map were recovered and agree on the six phases below. The climate notebooks named 04/05 were created out of that sequence; they remain useful prototypes for original 08. EDEN-06 consolidates the missing original 04–06 vision work; its saved outputs are reviewed below. EDEN-07 alignment results are verified; EDEN-08 climate baselines are prepared for Kaggle.

## Six-phase project map — original handbook

| Phase | Original scope | Gate / brief terminology |
| --- | --- | --- |
| 1 — Data engineering & inventory | 00 inventory, 01 climate ETL, 02 RGB-D acquisition, 03 spatial processing | Verified tables/images, frozen splits, reviewed quality and calibration. ETL = extract, transform, load. RGB-D = color plus depth. |
| 2 — Visual intelligence & climate baselines | 04 vision baselines, 05 RGB-D network, 06 frozen features, 07 alignment, 08 climate baselines | Valid labeled comparisons; group separation; no future imagery; training-only preprocessing. Embedding = compact learned image description. |
| 3 — Physics, temporal modeling & fusion | 09 physics calibration, 10 action-conditioned temporal model, 11 fusion | Compare physics, climate-only and fused models under the same protocol. Fusion = combine different evidence sources. |
| 4 — Twin replay & virtual control | 12 replay/scenarios, 13 constrained control | Check state initialization, simulated rollouts and action feasibility. MPC = choose actions by predicting future outcomes repeatedly. |
| 5 — Research evaluation & browser export | 14 frozen evaluation, 15 export | Freeze selection, disclose earlier test exposure, verify browser predictions match the exported model. Export parity = the two implementations agree. |
| 6 — Website, dashboard & release | Shared browser state, worker inference, 2D/3D dashboard, deployment | Verify observed/predicted/simulated provenance, consistent state and reproducible release. Provenance = where a value came from. |

The recovered source is EDEN SRS & Implementation Handbook rev 1.0. The separate v2 file named by the handoff is still unavailable; no v2-only requirements are inferred. Original 07 has a verified saved result, 08 is prepared, and 09–15 plus the website remain planned.

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

## Historical climate prototypes — useful for original Notebook 08

### EDEN-04 v1.0 — Climate forecasting baseline (saved run; protocol correction required)

**Historical scope:** a one-hour temperature baseline using EDEN-01. This file was named 04 before reconciling the original roadmap. It does not implement original 04 vision baselines; retain its outputs as diagnostic climate evidence for original 08.

| Cell | What it does | Jargon in brief |
| --- | --- | --- |
| 1. Forecast settings | Sets `air_temp_c`, the 12-step horizon, and Kaggle folders. | **Horizon** = how far ahead the forecast is made. |
| 2. Find and verify EDEN-01 | Finds one v1.1 PASS_ETL ZIP or extracted folder and verifies all manifest files. | **Manifest** = receipt listing output files and fingerprints. |
| 3. Load climate and splits | Checks target units, unique UTC keys, regular five-minute cadence, and frozen split membership. | **UTC** = shared time standard; **split** = fixed train/validation/test assignment. |
| 4. Build forecast examples | Makes persistence and same-time-yesterday predictions while excluding failed QC rows, embargoes, and windows crossing split boundaries. | **Persistence** = repeat the latest valid reading. **Embargo** = time buffer kept out of evaluation. |
| 5. Score baselines | Reports MAE, RMSE, bias, and per-compartment summaries for separate primary and chronological protocols. | **MAE** = average absolute error; **RMSE** penalizes larger errors more; **bias** = average signed error. |
| 6. Plot and record coverage | Saves a validation example and records window counts and limitations. | **Coverage** = count of usable examples after split and QC rules. |
| 7. Acceptance and ZIP | Checks both baselines and holdout windows, writes a provisional status and hash-listed result ZIP. | **Acceptance gate** = required checks before handoff. |

**Saved result:** PASS_BASELINE_EVAL_PROVISIONAL; 6/6 output hashes verified. Primary validation MAE: persistence 0.8282 °C, yesterday 0.9453 °C. Chronological validation MAE: persistence 0.8236 °C, yesterday 1.1568 °C.

**Review correction:** Cell 4 checked origin and target split labels but did not check the seasonal predictor's history label. Its reading is 23 hours before the origin, beyond the six-hour history contract. Exactly 1,656 validation and 1,656 test rows in the chronological output use history from a different split or embargo. Preserve the saved scores as diagnostic only. EDEN-05 rebuilds valid matched windows and recomputes persistence.

### Legacy EDEN-05 v1.0 — Climate model (saved result reviewed)

Input: EDEN-01 v1.1 and EDEN-04 v1.0 result bundles. Output: EDEN-05-Climate-Model-Results-v1.0.zip. CPU, Internet Off; leave test scoring disabled during development.

| Cell | Short note | Term explained |
| --- | --- | --- |
| 1. Settings | Choose 72 input readings and a 12-step forecast horizon. | Lag = an earlier reading. |
| 2. Verify inputs | Verify both producers, file hashes, and their shared EDEN-01 lineage; accept ZIPs or extracted folders. | Lineage = which input version produced a result. |
| 3. Check contracts | Validate target units, UTC cadence, and both frozen split definitions. | Embargo = unused time buffer between chronological groups. |
| 4. Build windows | Require all input-to-target ticks to stay in one split; reject windows with missing or suspect temperature inputs. | Window = the history and target for one forecast. |
| 5. Train and select | Fit scaler and Ridge on training only; select alpha on validation, then freeze portable coefficients. | Ridge = linear model with a penalty on large weights; alpha controls that penalty. |
| 6. Evaluate | Compare Ridge/persistence on identical validation rows; optionally score frozen models on test; report exploratory day-block error intervals. | Bootstrap = resample groups of observations to inspect estimate variability. |
| 7. Package | Verify matched counts, export versions, contracts, models, reports, and hashed ZIP. | A pipeline pass does not imply improvement over persistence. |

**Saved evidence:** all 15 manifest hashes verified; `PASS_VALIDATION_MODEL_PILOT_PROVISIONAL`. Primary validation Ridge/persistence MAE: 0.8188/0.8305 °C on 20,138 rows. Chronological: 0.7832/0.8229 °C on 27,444 rows. Test scoring was off. These results are development evidence for original 08, not completion of original 05 vision training.

## EDEN-06 — Vision recovery and feature extraction v1.0

**Status:** saved Kaggle run verified; 28/28 file hashes pass and 24 metrics were independently recomputed. Combines original 04 baselines, 05 neural models and 06 feature exports. Canopy transfer remains unvalidated. Next is original 07 alignment.

**Inputs:** EDEN-01 v1.1, EDEN-02 v1.1 and EDEN-03 v1.0 results. GPU and Internet On. It fetches 383 RGB/depth pairs from the labeled 2023 pre-trial (about 5.61 GB); publisher test and infrared are excluded. No extra manual dataset upload is required.

| Cell | Short note | Term explained |
| --- | --- | --- |
| 1. Settings | Set GPU training, folders, seed, 224-pixel inputs, and versions. | Seed = starting point for repeatable random choices. |
| 2. Upstream verification | Check manifests, hashes, producer lineage, and unchanged 2024 splits. | Hash = file fingerprint. |
| 3. Open pre-trial | Read the ZIP index and individual files through bounded partial requests; never fetch the full archive. | HTTP Range = request only a selected byte interval. |
| 4. Labels and groups | Match real RGB/depth filenames to four measured traits; group repeated plant identities across dates. | Label = measured answer. Grouped validation keeps related plants together. |
| 5. Paired preprocessing | Resize/pad both views together; preserve depth codes; cache relative-depth inputs and simple color/depth features. | Letterbox = preserve shape and pad unused borders. Relative depth is not physical distance. |
| 6. Original 04 baselines | Compare training mean, Ridge and Random Forest on identical eligible validation labels. | MAE = average absolute error. RMSE penalizes large errors more. |
| 7. Original 05 network | Build MobileNetV3 RGB and shallow depth branches, a shared representation and four missing-label-aware trait heads. | CNN = network that learns image patterns. Masked Huber loss ignores absent labels and softens large errors. |
| 8. Train and compare | Fit RGB-only, depth-only and RGB-D; select each best epoch using development validation; freeze the RGB-D encoder. | Ablation = remove an input to measure what it contributes. |
| 9. Original 06 OOF | Train three RGB-D fold models; each predicts plant groups absent from its training. Fold scales use training only. | Out-of-fold = prediction from a model that did not train on that group. |
| 10. Canopy features | Apply the disjoint frozen model to 2024 images; export embeddings, proxy trait means/variance and QC flags; mask unusable rows. | Dropout variance = variability across repeated model predictions, not calibrated confidence. |
| 11. Acceptance and ZIP | Check all module deliverables and unchanged splits; package allow-listed artifacts with sizes/hashes. | Pipeline pass = required files/checks succeeded, not proof of useful predictions. |

**Output contract:** `vision_baseline.json`, validation `metrics.json` and `predictions.parquet`; `models/vision.pt` and config; `vision/vision_features.parquet` for the 2024 canopy pilot; `vision/oof_features.parquet` for genuine 2023 training-fold predictions; split/audit/source reports; manifest. No raw images are required downstream.

**Leakage rules:** 2023 publisher train/validation samples form a new 80/20 plant-group development protocol, with three OOF folds inside training. Publisher test is excluded. The final encoder sees only 2023 training data, which is disjoint from 2024 fusion data. The 2024 table uses this frozen-disjoint alternative; it is not mislabeled as canopy K-fold predictions. Existing 2024 compartment and chronological splits are preserved.

**Limits:** single-plant-to-canopy transfer is unvalidated; canopy values use `proxy_*` fields. Native depth units and physical geometry remain unverified; color masks remain heuristic. Dropout variance is uncalibrated. Preparation inspected source test metadata, but did not evaluate or select using test performance; the notebook never loads publisher test labels. A successful run establishes computational outputs, not full scientific acceptance.

### EDEN-06 observed result

311 training images / 146 plant groups, 72 validation images / 37 disjoint groups. All 311 OOF rows map to held groups absent from their fold model's training; all six label scalers match their fit IDs. The 36 canopy IDs and split labels are unchanged; 32 have usable features and four dark frames retain missing model values.

| Trait | RGB-only validation MAE | RGB-D validation MAE |
| --- | --- | --- |
| Height | 3.342 cm | 3.377 cm |
| Fresh weight | 34.362 g | 37.577 g |
| Leaf area | 157.199 cm² | 149.267 cm² |
| Red-fruit count | 3.593 | 3.986 |

RGB-only has lower error on three traits; RGB-D has lower leaf-area error. There is no general depth benefit established here. Negative fruit predictions occur in 21 RGB validation rows, seven RGB-D validation rows and 92 OOF rows. Validation fruit labels reach 124 versus 37 in training. These are unrestricted regression estimates, not usable physical counts. EDEN-07 excludes all trait proxies from default model inputs and retains them only for auditing.

## EDEN-07 — Climate and vision alignment v1.0

**Status:** saved Kaggle result verified; 16/16 output hashes and all 1,273,094 full window intervals checked independently. **Original run settings:** CPU, Internet Off; EDEN-01 v1.1 and EDEN-06 v1.0 inputs.

| Cell | Short note | Term explained |
| --- | --- | --- |
| 1. Settings | Set 72 history readings, three climate targets and five forecast horizons. | Origin = forecast start; horizon = how far ahead. |
| 2. Verify inputs | Check all files and shared climate lineage; preserve split bytes. | Manifest = file receipt with fingerprints. |
| 3. Check contracts | Verify unique keys, UTC grid, frozen split labels and disjoint encoder provenance. | Key = fields identifying one observation. |
| 4. Climate and controls | Verify already joined weather, add recorded controls, distinguish setpoints, preserve raw values and QC masks. | Setpoint = requested value, not the measured actuator state. |
| 5. Backward image join | Join only prior images within the same group/split; mask old or rejected image values. | Backward join = use the most recent observation that already existed. |
| 6. Forecast windows | Index complete histories and same-split future endpoints at 15/30/60/180/360 minutes. | Window = one history and its forecast target; embargo = excluded buffer time. |
| 7. Scalers | Fit separately on each protocol's training data; count each training image once for image-level statistics. | Scaling = place values on comparable numeric ranges; validity mask = whether an input is usable. |
| 8. Coverage and export | Save aligned tables, windows, scaler parameters, input contracts and coverage. | Coverage = how often usable image/context data exists. |
| 9. Gate and ZIP | Check row pointers, continuity, splits, hashes and serialization; package a named result. | Pointer = row number used to retrieve the exact observation. |

**Timing:** 72 samples spaced five minutes apart span 355 minutes. Use that as the image-age cap. Image features are origin-only; do not stack prior carried-forward image context as history. Images cannot cross chronological split/embargo boundaries. The latest bad frame is flagged rather than silently replaced by an older good frame.

**Feature contract:** climate, recorded weather, measured actuators, separately named logged setpoints, deterministic time features and their masks. Optional origin image embeddings, age and quality remain experimental. Trait proxies, heuristic green masks, stale-image diagnostics, future observed weather/actions and 2023 OOF rows are excluded from default model inputs. Image capture time is treated as availability time; operational processing delay is not measured.

**Outputs:** `data/aligned.parquet` (one copy per evaluation protocol), `data/window_index.parquet`, `data/windows.json`, `data/scaler.json`, feature/split/schema contracts, coverage and rejection reports, manifest. Raw missing values stay missing. Later models may use zero after normalization only alongside the explicit validity mask. Constant/absent training fields stay inactive even if they vary in validation/test.

**Saved evidence:** 259,932 aligned rows represent 129,966 observations under two protocols. All row pointers, full interval continuity, split membership, target QC and past-image checks pass. Recomputed all 158 scaler fields per protocol from training data. At one-hour validation: primary 20,138 windows / 320 with vision; chronological 27,444 / 578. No forecast performance is claimed by alignment. Next: prepared original EDEN-08 climate baselines.

## EDEN-08 — Climate baselines v1.0

**Status:** prepared and statically checked; no local execution or model results. **Kaggle:** CPU, Internet Off, attach only EDEN-07 v1.0 results. Uses all eligible training data and validation windows at five horizons for three climate targets, with test excluded from modeling.

| Cell | Short note | Term explained |
| --- | --- | --- |
| 1. Settings | Fix the model settings, three targets and CPU folders. | Persistence = repeat the current reading. |
| 2. Verify EDEN-07 | Check every input file/hash and the window, split and scaler contracts. | Contract = rules the next notebook must obey. |
| 3. Check development windows | Exclude test; check every timestamp across each history-to-target interval and required target validity. | Development = training plus validation, separate from test. |
| 4. Past-only features | Build current values, 12/72-reading means, target lags, masks and known future clock features; fit training-only time-of-day reference. | Coverage = fraction of readings actually present in a summary. |
| 5. Fit/freeze models | Train Ridge searches and 30 fixed-config boosted regressors; choose Ridge alpha on validation; verify exported prediction parity. | Ridge = penalized weighted sum; boosting = a sequence of corrective trees; parity = reloaded model agrees. |
| 6. Matched evaluation | Score all four models on identical rows; report MAE/RMSE/bias/skill and paired UTC-day error intervals. | Day-block resampling keeps neighboring errors together; intervals remain exploratory. |
| 7. Plots/contracts | Show every horizon and save exact input/normalization definitions. | Horizon = how far ahead the forecast reaches. |
| 8. Gate/package | Verify finite matched predictions, frozen files and validation-only scope; package the named result. | A pipeline pass does not mean complex models beat persistence. |

**Seasonal qualification:** daily seasonal persistence violates the six-hour history because its needed observation is 18–23.75 hours before origin. It is explicitly not run. `seasonal_mean_train_only` is a separate baseline learned from training clock-time averages. No history limit or holdout rule is weakened.

**Forecasting scope:** same past climate/weather/control summaries and clock features for Ridge/boosting. No future measured weather, future realized actions or image features. Inputs retain validity/coverage indicators; engineered-feature normalization fits training rows only. Models predict normalized changes from the latest valid target and restore physical units without clipping.

**Outputs:** `data/baseline_predictions.parquet`, metrics, validation selections, paired day-block MAE-difference intervals, portable Ridge coefficients, boosted model exports, model-input contracts and manifest. Score development validation only. Daily intervals are not calibrated prediction bands or independent test evidence. Original EDEN-09 physics calibration follows after review.

**Notion documentation:** [Phase 2](https://app.notion.com/p/424637e14f0f816894c7d81821ec09e0) contains full source-cell toggles for 06/07/08 and the historical 04/05 climate prototypes, with saved versus prepared status clearly separated.

## Required next documentation when artifacts arrive

For each future notebook, add a cell table from the actual notebook headings/code, an input/output list, a short glossary for technical terms used in that notebook, saved run evidence, an acceptance gate, and explicit unresolved limitations. Do not write fictional cell descriptions for notebooks that have not been supplied.
