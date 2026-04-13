# Release materials for the Latin Christian measurement paper

## Overview

This repository contains the released prediction outputs and supporting documentation for the Latin text-classification and measurement analyses described in the paper. The main release file is a document-level table containing model-based scores, probabilities, and thresholded labels for all texts in the corpus. Supporting files document corpus auditing, held-out performance, deployment thresholds, final model hyperparameters, and the BERT run configuration.

All the code and raw data required to reproduce the outputs here can be found at this repo: [christian_measurement](https://github.com/christian-classification-johd/christian_measurement).

## Files in this release

### Main released data

- `corpus_600ad_combined_predictions.csv`  
  Main document-level release table. Contains metadata for each text together with model-specific probabilities, continuous scores, and thresholded labels.

### Supporting documentation and model metadata

- `data_audit_summary.csv`  
  One-row summary of corpus auditing and general pipeline settings.

- `model_metrics_oof.csv`  
  Out-of-fold performance metrics for the non-BERT models on the labeled set.

- `bert_metrics_oof.csv`  
  Out-of-fold performance metrics for the BERT model on the labeled set.

- `model_thresholds_deployment.csv`  
  Deployment thresholds used to convert calibrated probabilities into binary Christian and non-Christian labels for the non-BERT models.

- `model_hyperparameters_full.csv`  
  Final hyperparameters used for the non-BERT models fit on the full labeled data.

- `bert_run_config.json`  
  Training and deployment configuration for the BERT model, including batch sizes, learning rate, number of epochs, and deployment thresholds.

## Corpus summary

The release contains labels and scores for: 

- 780 total corpus texts
- 362 manually labeled texts
- 418 model labeled texts

## Main data file: `corpus_600ad_combined_predictions.csv`

Each row corresponds to one text in the corpus.

### Metadata columns

- `row_id`: row number in the release table
- `id`: text identifier
- `title`: text title
- `creator`: author name
- `date_range_start`: earliest year associated with the text
- `date_range_end`: latest year associated with the text
- `type`: broad text type (i.e., prose or poetry)
- `file`: source filename used in the pipeline
- `n_chars_raw`: raw character count
- `n_chars`: processed character count
- `n_words_raw`: raw word count
- `n_words`: processed word count

### Label/provenance columns

- `manual_label`: manual label for labeled texts; missing for unlabeled texts
- `prediction_source`: indicates how the released prediction for that row was generated
  - `cross_fitted_labeled`: the row belongs to the labeled set, and the released prediction is an out-of-fold / cross-fitted prediction
  - `full_fit_unlabeled`: the row belongs to the unlabeled set, and the released prediction is from a model fit on the full labeled data and then applied to unlabeled texts

### Model output columns

The file includes outputs for six models:

- Naive Bayes
- Elastic net logistic regression
- SVM with Platt scaling
- XGBoost
- LightGBM
- Latin BERT

For each model, the file includes some or all of the following as relevant:

- `prob_*`: calibrated probability of the positive class
- `score_*_logit`: logit-scale score derived from the calibrated probability
- `score_*_margin`: raw model margin or decision score, where applicable
- `label_*_youden`: hard label using the Youden threshold
- `label_*_f1`: hard label using the F1-optimizing threshold

### Important interpretation notes

1. `prob_*` columns are the released quantities to use for comparison across models.
2. `score_*_logit` columns are monotonic transforms of probabilities and are useful when a continuous unbounded score is preferred.
3. `score_*_margin` columns are model-specific raw scores and should generally only be interpreted within a given model, not compared numerically across different model families.
4. `label_*_youden` and `label_*_f1` are thresholded versions of the probabilities using different threshold-selection criteria.
5. Missing `manual_label` means the text was not part of the labeled training/evaluation set.

## Threshold files

### `model_thresholds_deployment.csv`

This file reports the deployment thresholds for the non-BERT models:

- `model`
- `threshold_youden`
- `threshold_f1`
- `threshold_scope`

These thresholds are the cutpoints used to generate the binary labels.

### BERT thresholds

BERT threshold values are provided in:

- `bert_metrics_oof.csv`
- `bert_run_config.json`

## Performance files

### `model_metrics_oof.csv`

This file reports out-of-fold performance for the non-BERT models on labeled texts:

- AUC
- log loss
- Brier score
- accuracy
- balanced accuracy
- precision
- recall
- specificity
- F1
- MCC
- confusion-count summaries (`tp`, `tn`, `fp`, `fn`)

These are given separately for thresholds selected by the Youden criterion and by F1.

### `bert_metrics_oof.csv`

This file reports analogous out-of-fold metrics for the BERT model: 

- AUC
- log loss
- Brier score
- threshold values
- accuracy
- precision
- recall
- F1

## Hyperparameter and configuration files

### `model_hyperparameters_full.csv`

Final non-BERT model settings fit on the full labeled data. The table contains model-specific hyperparameters for the linear, SVM, boosted-tree, and related models.

### `bert_run_config.json`

Configuration for the BERT model, including:

- pretrained model name
- maximum sequence length
- training and evaluation batch sizes
- gradient accumulation steps
- learning rate
- weight decay
- number of epochs
- warmup ratio
- early-stopping patience
- number of outer folds
- validation fraction
- threshold values
- final temperature-scaling value

## Evaluation and release policy

The release distinguishes between two kinds of predictions:

1. **Labeled texts**: released predictions are cross-fitted / out-of-fold, so each labeled document’s evaluation prediction comes from a model that was not trained on that document.
2. **Unlabeled texts**: released predictions come from models fit on the full labeled set and then applied to the unlabeled corpus.

This distinction is recorded in the `prediction_source` column and should be kept in mind for downstream analysess.

## Suggested use

For most downstream work, the user might want to:

- use `corpus_600ad_combined_predictions.csv` as the main data file
- use `prob_*` as the primary released continuous measurements
- use `label_*_youden` or `label_*_f1` only when a binary classification is required
- consult `model_metrics_oof.csv` and `bert_metrics_oof.csv` for performance comparisons
- consult `model_thresholds_deployment.csv` and `bert_run_config.json` for the exact thresholding rules used in the release

## Citation

Please cite the accompanying paper if you use these release materials in research or downstream applications.

## Contact

Questions about the release should be directed to the authors of the accompanying paper.
