# Enterprise Network Cyber Attack & Intrusion Detection using NSL-KDD

A machine learning-based Intrusion Detection System (IDS) for classifying enterprise network connections into Normal traffic and four major cyber-attack categories using the NSL-KDD dataset.

## Project Overview

This project formulates intrusion detection as a 5-class classification problem:

| Class | Description |
|---|---|
| Normal | Legitimate network connection |
| DoS | Denial of Service attacks |
| Probe | Network scanning and reconnaissance |
| R2L | Remote-to-Local attacks |
| U2R | User-to-Root attacks |

The project evaluates five machine learning approaches and compares predictive performance, minority-class detection, generalization, and prediction latency.

## Dataset

The project uses the NSL-KDD KDDTrain+ and KDDTest+ datasets.

- KDDTrain+: 125,973 training connections
- KDDTest+: 22,544 test connections
- Final processed representation: 122 features
- Target classes: Normal, DoS, Probe, R2L, U2R

The official KDDTest+ set is used as the primary unseen test benchmark.

Raw datasets and processed serialized data are intentionally excluded from this repository.

## Preprocessing

- One-Hot Encoding for categorical features
- RobustScaler for continuous features
- Binary features preserved separately
- difficulty feature removed
- handle_unknown='ignore' used for categorical encoding
- Final feature representation contains 122 features

## Machine Learning Models

1. Decision Tree - Gini
2. Decision Tree - Entropy
3. Gaussian Naive Bayes
4. LinearSVC
5. Balanced LinearSVC

Balanced LinearSVC uses class_weight='balanced' to give greater importance to underrepresented classes.

Experiments use random_state=42 where applicable.

## Evaluation

The models were evaluated using:

- Accuracy
- Macro Precision
- Macro Recall
- Macro F1
- Per-class Precision, Recall and F1
- Confusion matrices
- 5-fold Stratified Cross-Validation
- Generalization gap
- Prediction latency
- Minority attack recall

Because NSL-KDD is highly imbalanced, Macro F1 is emphasized instead of relying only on accuracy.

## Final Test Results

Results on the official KDDTest+ test set:

| Model | Accuracy | Macro F1 | Prediction Time / 10k |
|---|---:|---:|---:|
| Decision Tree - Entropy | 77.56% | 56.14% | 2.428 ms |
| Decision Tree - Gini | 75.86% | 55.46% | 2.983 ms |
| Balanced LinearSVC | 73.19% | 51.76% | 8.919 ms |
| LinearSVC | 71.67% | 47.03% | 9.430 ms |
| Gaussian Naive Bayes | 33.23% | 24.89% | 48.162 ms |

### Overall Ranking

1. Decision Tree - Entropy
2. Decision Tree - Gini
3. Balanced LinearSVC
4. LinearSVC
5. Gaussian Naive Bayes

The Entropy Decision Tree achieved the highest test Macro F1 and the lowest prediction latency among the evaluated models.

## Minority Attack Detection

R2L and U2R are substantially more difficult to detect than Normal and DoS traffic.

### R2L Recall

- Entropy Decision Tree: 8.35%
- Balanced LinearSVC: 12.86%
- LinearSVC: 0.66%

### U2R Recall

- Gaussian Naive Bayes: 64.18%
- Balanced LinearSVC: 32.84%
- Entropy Decision Tree: 13.43%

High recall alone does not necessarily indicate good detection quality. Gaussian Naive Bayes achieved 64.18% U2R recall but only 1.06% U2R precision, resulting in a U2R F1 score of 2.08%.

## Class Balancing Experiment

| Metric | LinearSVC | Balanced LinearSVC |
|---|---:|---:|
| Accuracy | 71.67% | 73.19% |
| Macro Precision | 78.77% | 60.66% |
| Macro Recall | 46.80% | 55.64% |
| Macro F1 | 47.03% | 51.76% |
| R2L Recall | 0.66% | 12.86% |
| U2R Recall | 2.99% | 32.84% |

Balanced LinearSVC improves minority-class recall and Macro F1, while reducing macro precision.

## Five-Fold Cross-Validation

| Model | CV Accuracy | CV Macro F1 |
|---|---:|---:|
| Decision Tree - Entropy | 99.80% | 90.79% |
| Decision Tree - Gini | 99.78% | 90.57% |
| Balanced LinearSVC | 94.69% | 72.05% |
| LinearSVC | 96.57% | 59.18% |
| Gaussian Naive Bayes | 41.11% | 30.78% |

Stratified 5-fold cross-validation was performed on the training representation.

### Cross-Validation Limitation

The preprocessing representation was created before cross-validation and then used by the folds. Therefore, these CV results evaluate the models on a fixed training-derived feature representation rather than a completely fold-isolated preprocessing pipeline.

## Generalization Analysis

| Model | CV Macro F1 | Test Macro F1 | Gap |
|---|---:|---:|---:|
| Decision Tree - Entropy | 90.79% | 56.14% | 34.65 pp |
| Decision Tree - Gini | 90.57% | 55.46% | 35.11 pp |
| Balanced LinearSVC | 72.05% | 51.76% | 20.29 pp |
| LinearSVC | 59.18% | 47.03% | 12.15 pp |
| Gaussian Naive Bayes | 30.78% | 24.89% | 5.89 pp |

The large reduction in Macro F1, particularly for the Decision Trees, indicates substantially weaker performance on the official unseen test distribution compared with training-distribution cross-validation.

## Prediction Latency

Latency was measured using 10,000 test connection records, excluding model training time.

| Model | Average Time / 10k | Connections / ms |
|---|---:|---:|
| Decision Tree - Entropy | 2.428 ms | 4118.94 |
| Decision Tree - Gini | 2.983 ms | 3351.85 |
| Balanced LinearSVC | 8.919 ms | 1121.19 |
| LinearSVC | 9.430 ms | 1060.46 |
| Gaussian Naive Bayes | 48.162 ms | 207.63 |

## Repository Structure

```text
NSL-KDD-IDS/
├── notebooks/
│   ├── Alma_DT_IDS.ipynb
│   └── Nami_NB_SVM_IDS.ipynb
├── figures/
│   ├── confusion_matrices/
│   ├── final_model_comparison.csv
│   ├── final_model_ranking.csv
│   ├── final_results_summary.csv
│   ├── generalization_gap.png
│   ├── latency_comparison.png
│   └── minority_attack_recall.png
├── README.md
├── .gitignore
└── requirements.txt
```

## Notebook Organization

### Alma_DT_IDS.ipynb

Contains preprocessing, attack-class mapping, Decision Tree experiments, and evaluation.

### Nami_NB_SVM_IDS.ipynb

Contains Gaussian Naive Bayes, LinearSVC, Balanced LinearSVC, cross-validation, generalization analysis, latency benchmarking, confusion matrices, and final model comparison.

## Key Findings

- Entropy Decision Tree achieved the best overall test Macro F1 at 56.14%.
- Decision Trees achieved the strongest test performance among the evaluated models.
- Balanced LinearSVC improved minority-class recall and Macro F1 compared with standard LinearSVC.
- R2L and U2R remain difficult to classify reliably.
- Gaussian Naive Bayes showed poor overall performance despite relatively high U2R recall because its U2R precision was extremely low.
- The official KDDTest+ benchmark revealed a substantial generalization gap compared with cross-validation.
- Decision Trees provided the lowest prediction latency.
- Accuracy alone is insufficient for this highly imbalanced IDS problem.

## Limitations

1. NSL-KDD is a benchmark dataset and may not represent modern enterprise network traffic.
2. The official test distribution differs from the training distribution.
3. R2L and U2R remain difficult to classify.
4. Cross-validation used a preprocessing representation generated before fold splitting.
5. LinearSVC produced convergence warnings under the tested configuration.
6. The current system is an offline machine learning evaluation rather than a production real-time IDS.

## Future Work

- SMOTE and other imbalance-handling techniques
- Cost-sensitive learning
- Ensemble models such as Random Forest and Gradient Boosting
- Feature engineering and feature selection
- Threshold optimization
- Concept drift and distribution-shift analysis
- Additional modern intrusion-detection datasets
- Explainable AI techniques
- Real-time network monitoring integration
- Streaming IDS deployment

## Reproducibility

Experiments use fixed random seeds where applicable:

```text
random_state = 42
```

Raw datasets are not stored in this repository and must be obtained separately for reproduction.

## Project Status

Completed:

- Dataset preprocessing
- Attack-class classification
- Decision Tree experiments
- Gaussian Naive Bayes
- LinearSVC
- Balanced LinearSVC
- Test-set evaluation
- Cross-validation
- Generalization analysis
- Minority attack analysis
- Prediction latency benchmarking
- Confusion matrices
- Final model comparison
