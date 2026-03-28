# CopCo_TYP_Frozen_Package_v1.0

Frozen dataset package for the EyeBench `CopCo_TYP` dyslexia-detection benchmark task.

## Scope

- Dataset source: EyeBench CopCo benchmark factory
- Exported task: `CopCo_TYP`
- Task type: binary classification
- Trial key: `unique_trial_id`
- Item key: `item_id` (export alias of EyeBench `unique_paragraph_id`)
- Split grouping key: `split_item_id` (export alias of EyeBench `speech_id`)

## Protocol

- Folds: 4
- For exported `fold_index = k`, validation uses fold `k`
- For exported `fold_index = k`, test uses fold `(k + 1) mod 4`
- Training uses the remaining two folds
- Split regimes: `train`, `unseen_text`, `unseen_reader`, `unseen_reader_text`

## Exported Contents

- `01_manifests/`: protocol and entity manifests
- `02_feature_matrices/`: frozen 1D, 9D, and 567D trial-level baseline matrices
- `03_contextual_data/`: item text inventory and participant metadata
- `04_convenience_exports/`: fold-specific views derived only from the global split manifest

## Build Summary

- Trials: 4782
- Participants: 57
- Items: 452
- Split rows: 19128

## Intended Use

Copy this entire frozen package into a new clean research project. The new project should read the manifests and matrices directly and should not need runtime imports from the EyeBench repository.
