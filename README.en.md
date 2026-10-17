# Credit Card Fraud Detection

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.9%2B-blue?logo=python" alt="Python">
  <img src="https://img.shields.io/badge/scikit--learn-1.3%2B-orange?logo=scikit-learn" alt="scikit-learn">
  <img src="https://img.shields.io/badge/pandas-2.0%2B-purple?logo=pandas" alt="pandas">
  <img src="https://img.shields.io/badge/License-MIT-yellow" alt="License">
</p>

Machine learning pipeline that downloads the public credit card fraud dataset, trains and compares three classification models, and saves the best model with its scaler for later inspection.

## Problem / Why it exists

Fraudulent transactions are exceptions: in the reference dataset, less than 0.2 percent of transactions are fraud. With such imbalanced data, accuracy alone is misleading — a model that always predicts "legitimate" would have about 99.8 percent accuracy but would detect zero fraud. This project exists to train models that prioritize fraud detection (recall) without relying only on accuracy, and to keep trained artifacts saved and inspectable.

## What it does

The pipeline in `main.py` runs, in order:

1. Downloads `creditcard.csv` (or uses the local copy if it already exists).
2. Performs exploratory analysis: class counts, missing values, duplicates, feature correlation with the target, and charts saved in `plots/`.
3. Pre-processes: removes duplicates, performs stratified 80/20 train/test split, and applies `RobustScaler` only to `Amount` and `Time` (the remaining features are already standardized via PCA).
4. Attempts to balance classes with SMOTE; if the `imbalanced-learn` package is not installed, uses `class_weight='balanced'` in models as fallback.
5. Trains `LogisticRegression`, `RandomForestClassifier` and `ExtraTreesClassifier` with `RandomizedSearchCV` (`StratifiedKFold` validator of 3 folds, optimizing f1).
6. Selects the best model by weighted score: recall 40 percent, f1 30 percent, precision 20 percent, accuracy 10 percent.
7. Evaluates the winner with classification report, confusion matrix, ROC AUC, and specificity.
8. Generates final charts (ROC curve and confusion matrix) in `plots/`.
9. Saves artifacts: `modelo_<name>.joblib`, `scaler.joblib` and `resultados_modelos.joblib`.

The `inspecionar_modelo.py` script reads the generated `.joblib` files and displays model, scaler, comparison table, feature importance, and best hyperparameters details.

## Stack

| Component | Version | Purpose |
|---|---|---|
| Python | 3.9+ | Language |
| pandas | 2.0.0+ | Dataset reading and manipulation |
| NumPy | 1.24.0+ | Numerical operations |
| scikit-learn | 1.3.0+ | Models, splitting, scaling, and metrics |
| Matplotlib | 3.7.0+ | Charts |
| Seaborn | 0.12.0+ | Statistical charts |
| joblib | 1.3.0+ | Model and scaler serialization |
| imbalanced-learn | optional | SMOTE for class balancing |

Install required dependencies with `pip install -r requirements.txt`. SMOTE is only used if `imbalanced-learn` is installed; otherwise the code proceeds with `class_weight`.

## How it works

The data comes from the Credit Card Fraud Detection dataset (European credit card transactions), hosted at:

https://storage.googleapis.com/download.tensorflow.org/data/creditcard.csv

The dataset has 30 features (`Time`, `Amount`, and `V1` to `V28`, already transformed by PCA to protect privacy) and the target column `Class` (0 = legitimate, 1 = fraud).

Implementation decisions in the code:

- `RobustScaler` is applied only to `Amount` and `Time`; fitting is done only on the training set to avoid data leakage.
- The split is stratified, preserving the fraud proportion between train and test.
- When `imbalanced-learn` is available, SMOTE generates synthetic samples of the minority class only in training; the test set remains in the original distribution.
- Model selection uses a weighted score that prioritizes recall, because in fraud detection the cost of letting a fraud pass is higher than the cost of a false alarm.
- `RandomizedSearchCV` is configured with few combinations (`n_iter=5`) to keep execution fast.

## How to run

```bash
git clone https://github.com/KelvinOliveiraCode/fraud-detection-project.git
cd fraud-detection-project
python -m venv venv
venv\Scripts\activate          # Windows
# source venv/bin/activate   # Linux/Mac
pip install -r requirements.txt
python main.py
```

On the first run, the dataset is downloaded automatically (about 67 MB) and saved as `creditcard.csv`. Charts are written to `plots/` and model artifacts to the project root.

To inspect saved artifacts:

```bash
python inspecionar_modelo.py
```

Total training time depends on the machine and SMOTE availability.

## Project structure

```
fraud-detection-project/
|
|-- main.py                  # Full pipeline: data, EDA, preprocessing, models, evaluation, and saving
|-- inspecionar_modelo.py    # Reading and visualization of generated .joblib artifacts
|-- requirements.txt         # Required dependencies
|-- gitignore                # Files ignored by Git
|-- LICENSE                  # MIT License
|-- README.en.md             # This file
|
|-- creditcard.csv           # Generated: downloaded dataset (ignored by Git)
|-- plots/                   # Generated: EDA and final result charts
|-- modelo_*.joblib          # Generated: best trained model
|-- scaler.joblib            # Generated: scaler fitted on training
|-- resultados_modelos.joblib# Generated: metrics and hyperparameters of all models
```

## Trade-offs and decisions

- **Recall above accuracy**: the selection score weights recall at 40 percent. This may increase false positives, which is acceptable in fraud detection where missing a case costs more than reviewing a false alarm.
- **Optional SMOTE**: not in `requirements.txt` to avoid an extra dependency. If absent, the code uses `class_weight='balanced'` without interrupting execution.
- **Fast execution**: lightweight models (30–50 trees, max depth 10) and few hyperparameter combinations. This trades maximized quality for reduced training time.
- **Only `Amount` and `Time` are scaled**: `V1–V28` are already standardized principal components, so scaling them again adds no value.
- **joblib artifacts**: simple to save and load, but do not include model versioning or training data metadata.

## Known limitations

- Numeric results depend on execution and environment; they are not guaranteed nor bit-by-bit reproducible across different machines.
- The model is evaluated only on the same static dataset, within the 80/20 split. There is no validation on entirely new data outside the repository.
- Features `V1–V28` are anonymized by PCA, so they are not directly interpretable as business variables.
- In production it would be necessary: an adjustable decision threshold over `predict_proba`, data drift monitoring, and periodic retraining. This project implements none of that.
- `.joblib` files contain models trained in a pickle-like format; loading files from untrusted sources is a security risk.

## License

MIT License. See the [LICENSE](LICENSE) file for details.

## Author

Kelvin Oliveira

GitHub: [@KelvinOliveiraCode](https://github.com/KelvinOliveiraCode)
