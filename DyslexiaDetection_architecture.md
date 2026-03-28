# DyslexiaDetection Project Architecture

## 1. Purpose

This document defines the **frozen project architecture** for the `DyslexiaDetection` research project.

The goal of this architecture is not only to organize files, but to enforce clear boundaries between:

- **frozen input data**
- **core reusable code**
- **execution pipelines**
- **analysis and visualization**
- **result artifacts**
- **project documentation and experiment bookkeeping**

This project is the **clean research project** that starts **after** the EyeBench-based repository has already exported a frozen dataset package.

The benchmark repository is therefore treated only as a **dataset/protocol factory**.
This `DyslexiaDetection` project is the actual long-term experiment project.

---

## 2. Design Principles

### 2.1 Frozen data, clean experiments

The project must treat `CopCo_TYP_Frozen_Package_v1.0` as a **read-only external asset**.

Rules:

- Never modify files inside `data/frozen/`.
- Never regenerate benchmark splits inside this project.
- Never depend at runtime on benchmark-repo code.
- All train/val/test logic must be read from the frozen package.

### 2.2 Strict separation of concerns

Each top-level folder has exactly one responsibility:

- `data/` stores data only
- `src/` stores reusable package code only
- `run/` stores runnable pipeline entrypoints only
- `analysis/` stores notebooks and figure-generation code only
- `result/` stores run outputs only
- `docs/` stores documentation and tracking files only

### 2.3 Results are immutable per run

Every experiment run must write to a unique timestamped output directory.

Rules:

- Never overwrite previous run folders.
- Never mix outputs from different run types.
- Every run folder must be self-describing.

### 2.4 Layered research progression

All work in this project is assigned to one of four layers:

- **Layer 1**: classical baselines
- **Layer 2**: text/fusion baselines
- **Layer 3**: mechanism-based models
- **Layer 4**: advanced bottleneck / concept models

This architectural separation is not cosmetic. It is intended to prevent experiments from becoming mixed, hard to interpret, or impossible to compare cleanly.

### 2.5 Reusable code lives in `src/`, not in notebooks

Notebooks are for:

- exploratory analysis
- diagnostics
- visualization
- manual inspection

Notebooks are **not** the source of truth for:

- training logic
- data split logic
- metrics computation
- result writing

Those belong in `src/` and are called from `run/`.

---

## 3. Final Project Tree

```text
DyslexiaDetection/
│
├── pyproject.toml
│
├── data/
│   ├── frozen/
│   │   └── CopCo_TYP_Frozen_Package_v1.0/
│   └── working/
│
├── docs/
│   ├── experiment_registry.csv
│   ├── project_protocol.md
│   ├── mechanism_dictionary.md
│   ├── variable_inventory.md
│   └── paper_tables_outline.md
│
├── result/
│   ├── layer1/
│   ├── layer2/
│   ├── layer3/
│   └── layer4/
│
├── analysis/
│   ├── layer1/
│   │   ├── notebooks/
│   │   └── figures/
│   ├── layer2/
│   │   ├── notebooks/
│   │   └── figures/
│   ├── layer3/
│   │   ├── notebooks/
│   │   └── figures/
│   └── layer4/
│       ├── notebooks/
│       └── figures/
│
├── run/
│   ├── run_layer1_inventory.py
│   ├── run_layer1_aligned.py
│   ├── run_calibration_diagnostics.py
│   ├── run_layer2_text_only.py
│   ├── run_layer2_fusion.py
│   ├── run_layer3_grouped.py
│   └── run_layer4_advanced.py
│
└── src/
    └── dyslexia_pred/
        ├── __init__.py
        ├── config/
        │   ├── __init__.py
        │   ├── paths.py
        │   ├── constants.py
        │   └── run_naming.py
        ├── dataloaders/
        │   ├── __init__.py
        │   ├── load_frozen.py
        │   ├── split_parser.py
        │   ├── feature_joiner.py
        │   ├── text_loader.py
        │   └── participant_aggregation.py
        ├── preprocessing/
        │   ├── __init__.py
        │   ├── feature_checks.py
        │   ├── scaling.py
        │   ├── missingness.py
        │   └── grouped_features.py
        ├── models/
        │   ├── __init__.py
        │   ├── common/
        │   │   ├── __init__.py
        │   │   ├── base_wrapper.py
        │   │   ├── interfaces.py
        │   │   └── prediction_schema.py
        │   ├── layer_1/
        │   │   ├── __init__.py
        │   │   ├── logistic_regression/
        │   │   │   ├── __init__.py
        │   │   │   ├── config.py
        │   │   │   └── wrapper.py
        │   │   ├── linear_svm/
        │   │   │   ├── __init__.py
        │   │   │   ├── config.py
        │   │   │   └── wrapper.py
        │   │   ├── random_forest/
        │   │   │   ├── __init__.py
        │   │   │   ├── config.py
        │   │   │   └── wrapper.py
        │   │   ├── xgboost/
        │   │   │   ├── __init__.py
        │   │   │   ├── config.py
        │   │   │   └── wrapper.py
        │   │   └── ebm/
        │   │       ├── __init__.py
        │   │       ├── config.py
        │   │       └── wrapper.py
        │   ├── layer_2/
        │   │   ├── __init__.py
        │   │   ├── text_only_basic/
        │   │   │   ├── __init__.py
        │   │   │   ├── config.py
        │   │   │   └── wrapper.py
        │   │   ├── text_frozen_embed/
        │   │   │   ├── __init__.py
        │   │   │   ├── config.py
        │   │   │   ├── embedder.py
        │   │   │   └── wrapper.py
        │   │   └── late_fusion/
        │   │       ├── __init__.py
        │   │       ├── config.py
        │   │       └── wrapper.py
        │   ├── layer_3/
        │   │   ├── __init__.py
        │   │   ├── grouped_lr/
        │   │   │   ├── __init__.py
        │   │   │   ├── config.py
        │   │   │   └── wrapper.py
        │   │   ├── grouped_xgb/
        │   │   │   ├── __init__.py
        │   │   │   ├── config.py
        │   │   │   └── wrapper.py
        │   │   └── grouped_ebm/
        │   │       ├── __init__.py
        │   │       ├── config.py
        │   │       └── wrapper.py
        │   └── layer_4/
        │       ├── __init__.py
        │       ├── concept_bottleneck/
        │       ├── mechanism_bottleneck/
        │       └── advanced_fusion/
        ├── evaluation/
        │   ├── __init__.py
        │   ├── metrics.py
        │   ├── calibration.py
        │   ├── thresholding.py
        │   ├── confusion_matrix.py
        │   ├── roc_pr.py
        │   └── summaries.py
        ├── results/
        │   ├── __init__.py
        │   ├── writer.py
        │   ├── registry.py
        │   ├── manifests.py
        │   └── schema_checks.py
        └── utils/
            ├── __init__.py
            ├── logging_utils.py
            ├── io.py
            ├── seed.py
            └── time_utils.py
```

---

## 4. Top-Level Folder Definitions

## 4.1 `pyproject.toml`

### Purpose

Defines the project as an installable Python package so that `src/` code can be imported cleanly from anywhere in the project.

### Responsibilities

- package name registration
- dependency declaration
- editable install support
- Python version declaration
- tool config if needed later (`pytest`, `ruff`, `black`)

### Rules

- This file is mandatory.
- The project must be usable via `pip install -e .`.
- All runtime code must import from `dyslexia_pred`, not from relative notebook paths.

---

## 4.2 `data/`

This folder stores only data artifacts.
No training logic. No notebook code. No model code.

### `data/frozen/`

Contains **read-only external dataset packages**.

Expected contents initially:

- `CopCo_TYP_Frozen_Package_v1.0/`

This folder is the source of truth for:

- manifests
- splits
- frozen feature matrices
- contextual text data
- participant metadata
- package metadata

### `data/working/`

Contains local intermediate artifacts generated inside this project.

Examples:

- joined tables for fast access
- cached text embeddings
- grouped feature tables
- temporary participant aggregation tables
- cached calibration inputs

### Rules

- `data/frozen/` is immutable.
- `data/working/` may be regenerated.
- Any file in `data/working/` must be derivable from frozen package + project code.
- If something becomes essential and stable, promote it into an explicit pipeline output or document it in `project_protocol.md`.

---

## 4.3 `docs/`

Stores all project-level documentation and bookkeeping.

### Required files

#### `experiment_registry.csv`
Master table of all experiment runs.

#### `project_protocol.md`
Defines the project’s operational rules.

Must include:

- what counts as Layer 1 / 2 / 3 / 4
- naming rules for runs
- allowed split names
- participant aggregation rules
- threshold selection policy
- when a baseline can be considered frozen

#### `mechanism_dictionary.md`
Used in Layer 3 and beyond.

Each mechanism entry should define:

- definition
- theoretical motivation
- behavioral interpretation
- candidate gaze features
- candidate text-conditioning variables
- confounds
- expected direction
- priority

#### `variable_inventory.md`
Inventory of all gaze/text variables considered by the project.

#### `paper_tables_outline.md`
Predefined structure of main paper tables and appendix tables.

### Rules

- `docs/` files are part of the scientific workflow, not optional extras.
- If a new experiment type is added, update `project_protocol.md` first.

---

## 4.4 `result/`

Stores all run outputs.

This folder must be append-only in practice.

### Layer structure

```text
result/
  layer1/
  layer2/
  layer3/
  layer4/
```

### Run-type structure

Each layer contains subfolders by experiment family.

Examples:

```text
result/layer1/native_inventory/
result/layer1/aligned_567d/
result/layer2/text_only_basic/
result/layer2/late_fusion/
result/layer3/grouped_xgb/
```

### Timestamp structure

Every concrete run goes in a timestamped folder.

Example:

```text
result/layer1/aligned_567d/2026-03-28_100000/
```

### Every run folder must contain

At minimum:

- `run_config.json`
- `metrics_overall.csv`
- `metrics_by_fold.csv`
- `metrics_by_regime.csv`
- `metrics_participant_level.csv`
- `predictions_sample_level.parquet`
- `predictions_participant_level.parquet`
- `calibration_curve.csv`
- `threshold_sweep.csv`
- `brier_scores.csv`
- `roc_curve.csv`
- `pr_curve.csv`
- `confusion_matrix.csv`
- `log.txt`

Optional for explainable models:

- `feature_importance.csv`
- `group_importance.csv`
- `shap_values.parquet`
- `ebm_global_effects.csv`

### Rules

- Never overwrite an old run folder.
- Never mix predictions from different models in one folder.
- One run folder = one model/config/split protocol execution.
- Cross-model comparison lives in `analysis/`, not by mixing outputs inside `result/`.

---

## 4.5 `analysis/`

Used for post-hoc analysis and figure generation.

### Structure

Each layer gets:

- `notebooks/`
- `figures/`

### Examples

Layer 1 notebooks:

- baseline comparison
- participant aggregation diagnostics
- threshold analysis
- hardest-regime inspection
- calibration comparison

Layer 2 notebooks:

- text-only shortcut check
- fusion gain by regime
- embedding coverage analysis

Layer 3 notebooks:

- grouped feature stability
- grouped SHAP / grouped EBM effect inspection
- mechanism case studies

### Rules

- `analysis/` can read from `result/` and `data/`, but must not be the source of production training code.
- Figures saved for reports should go in `analysis/layerX/figures/`.

---

## 4.6 `run/`

Contains executable orchestration scripts.

These are **entrypoints**, not libraries.

They should:

- parse arguments
- load frozen package assets
- instantiate model wrappers
- run training/evaluation
- write results
- update experiment registry

They should not contain large reusable logic bodies.
Those belong in `src/`.

### Required scripts

#### `run_layer1_inventory.py`
Runs the **native inventory** baselines:

- LR on 1D reading-speed matrix
- SVM on 9D shallow matrix
- RF/XGB/EBM on 567D matrix

#### `run_layer1_aligned.py`
Runs the **fair aligned baseline matrix** on shared 567D input:

- LR-567
- Linear-SVM-567
- RF-567
- XGB-567
- EBM-567

This is the main Layer 1 script for clean model-vs-model comparison.

#### `run_calibration_diagnostics.py`
Runs calibration / threshold analyses on existing prediction outputs or reruns selected models when needed.

#### `run_layer2_text_only.py`
Runs text-only baselines.

#### `run_layer2_fusion.py`
Runs frozen text embedding and late-fusion models.

#### `run_layer3_grouped.py`
Runs grouped mechanism models.

#### `run_layer4_advanced.py`
Reserved for concept bottleneck / mechanism bottleneck / advanced multimodal variants.

### Rules

- Every run script must accept enough arguments to reproduce a run.
- Every run script must write a full config snapshot.
- Every run script must update `experiment_registry.csv`.

---

## 5. `src/dyslexia_pred/` Package Definition

This is the reusable codebase of the project.

Everything here should be importable and testable.

---

## 5.1 `config/`

Defines project-wide configuration helpers.

### `paths.py`
Centralizes known project paths.

Should expose helpers such as:

- project root
- frozen package root
- working data root
- result root
- docs root

### `constants.py`
Stores stable project constants.

Examples:

- layer names
- split alias names
- canonical regime names
- file naming constants
- default random seed

### `run_naming.py`
Builds standardized run names and timestamped output folder names.

Purpose:

- consistent output directory structure
- consistent `run_id`
- no duplicated naming logic across scripts

---

## 5.2 `dataloaders/`

This module is one of the most critical pieces of the project.

Its job is to turn the frozen package into clean, model-ready tables without redefining benchmark semantics.

### `load_frozen.py`
Reads frozen package assets.

Functions should include:

- load global trial manifest
- load global split manifest
- load participant manifest
- load item manifest
- load text inventory
- load participant metadata
- load any named feature matrix
- load package metadata

### `split_parser.py`
Converts frozen split manifests into concrete train/val/test slices for a chosen fold.

It must not invent new split logic.
It should only interpret exported protocol.

Expected responsibilities:

- return row ids by fold and split role
- optionally subset by regime for test-only reporting
- expose both alias regime names and canonical regime names

### `feature_joiner.py`
Joins manifests with feature matrices.

Responsibilities:

- join on `unique_trial_id`
- enforce row alignment checks
- optionally join participant/item metadata for analysis
- avoid leakage by keeping split assignment external to matrix files

### `text_loader.py`
Loads item-level text from the frozen package and aligns it to trial rows.

Needed for Layer 2 and for Layer 3 text-conditioning work.

### `participant_aggregation.py`
Computes participant-level aggregation from sample-level predictions.

Must support explicit aggregation modes such as:

- mean probability
- median probability
- max probability
- majority vote

Rules:

- participant aggregation is a project-level formal concept, not notebook-only logic
- all aggregation functions must be deterministic and documented

---

## 5.3 `preprocessing/`

Contains project-side preprocessing logic that happens **after** loading frozen assets.

This is not benchmark preprocessing.
This is experiment-side preprocessing.

### `feature_checks.py`
Validation on matrices before training.

Examples:

- column existence
- dtype checks
- duplicate key checks
- constant-column detection
- extreme missingness checks

### `scaling.py`
Scaling utilities for linear models and SVMs.

Should support train-only fit, then transform val/test safely.

### `missingness.py`
Policies for handling missing values.

Purpose:

- centralize imputation logic
- prevent ad hoc notebook handling

### `grouped_features.py`
Builds grouped mechanism-level representations for Layer 3.

Responsibilities:

- read grouped-feature mapping file
- subset selected raw features
- aggregate into mechanism groups if needed
- produce grouped matrix outputs reproducibly

---

## 5.4 `models/`

This is the model layer.

The design principle is:

- **one folder per model family**
- **one wrapper per trainable unit**
- **config isolated from wrapper implementation**

This is the right place to keep model-specific hyperparameters and training/prediction interfaces separate.

### `models/common/`

Shared interfaces and abstractions.

#### `base_wrapper.py`
Optional parent class for common model wrapper behaviors.

Responsibilities may include:

- fit
- predict_proba
- predict_label
- save_config
- export_feature_importance if supported

#### `interfaces.py`
Defines expected interfaces for all wrappers.

Purpose:

- enforce consistent return formats
- avoid special-case handling in run scripts

#### `prediction_schema.py`
Defines the standard prediction table format.

All model wrappers must return prediction tables with identical required columns.

---

### 5.4.1 `models/layer_1/`

Classical baselines.

These are split into subfolders by model family.

#### `logistic_regression/`
Used for:

- native 1D LR baseline
- aligned 567D LR baseline

Files:

- `config.py`: default hyperparameters and named config presets
- `wrapper.py`: sklearn-based fit/predict wrapper

#### `linear_svm/`
Used for aligned Layer 1 fair comparison.

Files:

- `config.py`
- `wrapper.py`

Note:

- native EyeBench-style shallow SVM and aligned linear SVM should be supported distinctly if both are kept
- if needed later, create separate presets inside the same family or split into `rbf_svm/` and `linear_svm/`

#### `random_forest/`
Standard RF wrapper.

#### `xgboost/`
Strong tabular baseline wrapper.

#### `ebm/`
Explainable boosting baseline wrapper.

Special responsibility:

- export model explainability outputs in a standardized format

---

### 5.4.2 `models/layer_2/`

Text and fusion baselines.

#### `text_only_basic/`
Simple text-only baseline.

Possible roles:

- tf-idf + linear classifier
- lightweight embedding + classifier

#### `text_frozen_embed/`
Handles frozen encoder usage.

Files:

- `embedder.py`: extracts text embeddings from item text
- `wrapper.py`: classifier on frozen embeddings

#### `late_fusion/`
Combines text representation with gaze/tabular representation.

Typical design:

- text embedding
- gaze feature vector
- concat
- simple classifier head

---

### 5.4.3 `models/layer_3/`

Mechanism-based models.

#### `grouped_lr/`
Linear mechanism baseline.

#### `grouped_xgb/`
Main grouped-feature strong baseline.

#### `grouped_ebm/`
Main grouped-feature interpretable baseline.

These models must operate on the grouped feature representation produced by `preprocessing/grouped_features.py`.

---

### 5.4.4 `models/layer_4/`

Reserved for advanced structured models.

Candidates:

- concept bottleneck
- mechanism bottleneck
- advanced gaze-text structured fusion

This layer should remain lightweight until Layer 1–3 results justify it.

---

## 5.5 `evaluation/`

All metric logic must live here.

### `metrics.py`
Computes core metrics:

- AUROC
- balanced accuracy
- recall / sensitivity
- specificity
- precision
- F1
- optional accuracy

Must support:

- sample-level evaluation
- participant-level evaluation

### `calibration.py`
Calibration diagnostics:

- Brier score
- calibration bins
- reliability curve data

### `thresholding.py`
Threshold sweeps and threshold selection policy.

Policies may include:

- default 0.5
- validation-optimized BA
- recall-priority threshold

### `confusion_matrix.py`
Produces confusion matrix tables in a consistent schema.

### `roc_pr.py`
Produces ROC and PR curve data.

### `summaries.py`
Builds:

- per-fold summaries
- per-regime summaries
- overall summaries
- participant-level summaries

---

## 5.6 `results/`

Handles standardized writing of outputs.

### `writer.py`
Writes outputs to a run folder using the agreed schema.

### `registry.py`
Appends or updates `docs/experiment_registry.csv`.

### `manifests.py`
Creates run manifests and config snapshots.

### `schema_checks.py`
Validates that outputs satisfy expected structure before a run is marked complete.

---

## 5.7 `utils/`

General support utilities.

### `logging_utils.py`
Structured logger configuration.

### `io.py`
Small safe I/O helpers.

### `seed.py`
Global reproducibility helpers.

### `time_utils.py`
Timestamp creation and formatting helpers.

---

## 6. Layer Definitions and Their Architectural Meaning

## 6.1 Layer 1

### Purpose
Build the baseline foundation.

### Internal split

Layer 1 must be treated as **two subfamilies**:

#### Layer 1A: native inventory
Replicates native benchmark-style baseline families.

Includes:

- LR on 1D reading-speed matrix
- SVM on 9D shallow matrix
- RF on 567D matrix
- XGB on 567D matrix
- EBM on 567D matrix

#### Layer 1B: aligned fair matrix
Uses one shared rich matrix for fair classifier comparison.

Includes:

- LR on 567D
- Linear SVM on 567D
- RF on 567D
- XGB on 567D
- EBM on 567D

### Why this split must exist

Because Layer 1A answers:

> how do native baseline families behave on the task?

while Layer 1B answers:

> on the same feature representation, how much difference comes from the classifier itself?

Both are necessary.

---

## 6.2 Layer 2

### Purpose
Test whether text contributes genuine signal beyond gaze/tabular information.

### Required experiment families

- text-only baseline
- frozen text embedding baseline
- simple late fusion baseline

### Architectural implication

Layer 2 requires:

- text loading from frozen package
- embedding caching in `data/working/`
- modality-aware experiment logging

---

## 6.3 Layer 3

### Purpose
Build theory-informed mechanism representations.

### Required project artifacts

- `docs/mechanism_dictionary.md`
- grouped-feature mapping file
- grouped representation builder
- grouped explainability analysis

### Architectural implication

Layer 3 must reuse Layer 1 evaluation and result-writing infrastructure rather than invent new metrics/output layouts.

---

## 6.4 Layer 4

### Purpose
Test advanced structured models only after Layer 1–3 justify them.

### Architectural implication

Do not expand Layer 4 early.
Keep folder placeholders ready, but implementation minimal until needed.

---

## 7. Data Flow

The project should follow this strict flow:

```text
Frozen package in data/frozen/
    ↓
load_frozen.py
    ↓
split_parser.py + feature_joiner.py
    ↓
preprocessing (scaling / missingness / grouped features / text embeddings)
    ↓
model wrapper
    ↓
evaluation
    ↓
results writer
    ↓
result/<layer>/<experiment_family>/<timestamp>/
    ↓
analysis notebooks read result outputs
```

This flow must not be bypassed casually.

Especially important:

- notebooks should not create unofficial train/test splits
- notebooks should not save official results into ad hoc folders
- model wrappers should not directly write analysis figures

---

## 8. Result Naming and Output Contract

## 8.1 Standard run folder path

```text
result/{layer}/{experiment_family}/{timestamp}/
```

Examples:

```text
result/layer1/native_inventory/2026-03-28_180000/
result/layer1/aligned_567d/2026-03-28_183500/
result/layer2/late_fusion/2026-04-02_090000/
```

## 8.2 Required prediction columns

Every sample-level prediction table should contain at least:

- `unique_trial_id`
- `participant_id`
- `fold_index`
- `split_role`
- `split_regime`
- `true_label`
- `prediction_prob`
- `prediction_label`
- `model_name`
- `feature_set`
- `run_id`

Participant-level prediction tables should contain at least:

- `participant_id`
- `fold_index`
- `aggregation_mode`
- `true_label`
- `prediction_prob`
- `prediction_label`
- `n_trials_aggregated`
- `model_name`
- `run_id`

## 8.3 Required registry fields

`docs/experiment_registry.csv` should contain at least:

- `run_id`
- `date`
- `layer`
- `experiment_family`
- `model_name`
- `feature_set`
- `modality`
- `fold_index`
- `split_protocol`
- `participant_aggregation`
- `calibration_method`
- `threshold_mode`
- `train_n`
- `valid_n`
- `test_n`
- `auroc`
- `balanced_accuracy`
- `recall_dyslexia`
- `specificity`
- `precision`
- `f1`
- `brier_score`
- `result_path`
- `status`
- `notes`

---

## 9. What Must Not Happen

To keep the architecture clean, the following are forbidden:

### 9.1 No hidden split logic in notebooks

All official split usage must come from the frozen split manifest and `split_parser.py`.

### 9.2 No model-specific ad hoc metrics code in run scripts

Metrics belong in `evaluation/`.

### 9.3 No silent overwrite of old runs

If a run is repeated, it gets a new timestamped folder.

### 9.4 No direct editing of frozen package

If the data package truly needs correction later, create a new version such as:

- `CopCo_TYP_Frozen_Package_v1.1`
- `CopCo_TYP_Frozen_Package_v2.0`

Do not patch files in place.

### 9.5 No result tables built manually from memory

All comparison tables should be derived from saved result files or the experiment registry.

---

## 10. Recommended Implementation Order

## Phase 1: project bootstrap

1. create `pyproject.toml`
2. create `src/dyslexia_pred/`
3. copy frozen package into `data/frozen/`
4. implement `load_frozen.py`
5. implement `split_parser.py`
6. implement `feature_joiner.py`
7. implement `results/writer.py`
8. create `docs/project_protocol.md`
9. create `docs/experiment_registry.csv`

## Phase 2: Layer 1 foundation

1. implement Layer 1 wrappers
2. implement evaluation modules
3. implement participant aggregation
4. implement `run_layer1_inventory.py`
5. implement `run_layer1_aligned.py`
6. implement `run_calibration_diagnostics.py`
7. run native inventory
8. run aligned fair matrix

## Phase 3: Layer 2

1. implement text loading and embedding cache
2. implement text-only baseline
3. implement late fusion baseline
4. extend analysis notebooks

## Phase 4: Layer 3

1. create mechanism dictionary
2. define grouped feature mapping
3. implement grouped feature builder
4. run grouped LR/XGB/EBM
5. analyze grouped stability and interpretation

## Phase 5: Layer 4

Only after Layer 1–3 justify it.

---

## 11. Final Project Identity

This project is not a generic benchmark fork.
It is a **clean, layered, experiment-driven dyslexia detection research project** built on top of a frozen benchmark-derived data package.

Its architecture is designed to guarantee:

- reproducibility
- clean split semantics
- no hidden benchmark dependency
- easy comparison across layers
- stable baseline infrastructure
- controlled expansion toward mechanism-based modeling

That is the standard this project should follow going forward.
