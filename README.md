# deteccao-de-fraudes

<div align="center">

[English](README.en.md) · **Português (Brasil)**

</div>

[![Python 3](https://img.shields.io/badge/Python-3.x-3776AB?style=flat-square&logo=python&logoColor=white)](https://www.python.org/) [![scikit-learn](https://img.shields.io/badge/scikit--learn-F7931E?style=flat-square&logo=scikit-learn)](https://scikit-learn.org/) [![pandas](https://img.shields.io/badge/pandas-150458?style=flat-square&logo=pandas)](https://pandas.pydata.org/) [![pytest](https://img.shields.io/badge/pytest-0A9EDC?style=flat-square&logo=pytest)](https://docs.pytest.org/) [![MIT License](https://img.shields.io/badge/License-MIT-f4c430?style=flat-square)](LICENSE)

### Detecção de fraude em cartão com ML: 3 modelos, 284k transações, ROC AUC 0.94

---

## O que é

Detecção de fraude em cartão com ML: 3 modelos, 284k transações, ROC AUC 0.94

---

## Início Rápido

```bash
# Clone e entre
git clone https://github.com/KelvinOliveiraCode/<repo>.git
cd <repo>

# Instale dependências
pip install -r requirements.txt

# Execute o projeto
python main.py

# Execute os testes
pytest
```

## Como funciona

O projeto é organizado como um **pipeline modular** com etapas independentes:

- `inspecionar_modelo`
- `main`

Cada módulo tem responsabilidade única e pode ser testado ou substituído independentemente.

---

## Capabilities

| Capability | Description |
|------------|-------------|
| Data Loading | Auto-download TensorFlow creditcard.csv (284k transactions) with local caching |
| EDA & Visualization | Class distribution, Amount histograms, correlation heatmaps (matplotlib/seaborn) |
| Preprocessing | RobustScaler on Amount/Time, stratified split, duplicate removal |
| Balancing | SMOTE oversampling (fallback: class_weight="balanced") |
| Modeling | LogisticRegression, RandomForest, ExtraTrees with RandomizedSearchCV (StratifiedKFold) |
| Metrics | Accuracy, Precision, Recall, F1, ROC AUC, Specificity, Confusion Matrix |
| Selection | Weighted score: Recall 40% + F1 30% + Precision 20% + Accuracy 10% |
| Persistence | joblib serialization for model, scaler, and full results |


---

## Showcase

### Fraud Detection Pipeline

1. **Load** ,  Auto-fetch 284k transactions (TensorFlow dataset) with local caching
2. **EDA** ,  Class imbalance (0.17% fraud), Amount distributions, correlation heatmap
3. **Preprocess** ,  RobustScaler on Amount/Time, stratified 80/20 split
4. **Balance** ,  SMOTE oversampling (fallback: class_weight=balanced)
5. **Model** ,  3 algorithms, RandomizedSearchCV, StratifiedKFold, F1 scoring
6. **Select** ,  Weighted score: Recall 40% + F1 30% + Precision 20% + Acc 10%
7. **Export** ,  joblib saves model, scaler, full results

[View main.py](main.py) · [Inspect Model](inspecionar_modelo.py)

---

## FAQ

<details>
<summary><strong>Why SMOTE with class_weight fallback?</strong></summary>

SMOTE generates synthetic minority samples but requires imbalanced-learn. The fallback ensures the pipeline runs even without it.

</details>

<details>
<summary><strong>Why weighted score for model selection?</strong></summary>

Fraud detection prioritizes Recall (catching frauds). The 40/30/20/10 weighting balances Recall with Precision and F1.

</details>

<details>
<summary><strong>Can I use this with new data?</strong></summary>

Yes ,  retrain by running main.py with your CSV (same schema). The pipeline handles download, preprocessing, and model export automatically.

</details>



---

## Documentation

- **Source Code** ,  Browse the `src/` directory for implementation details
- **API Reference** ,  Inline docstrings + type hints
- **Architecture Diagram** ,  See [architecture.mermaid](docs/architecture.mermaid) (if present)
- **Changelog** ,  [CHANGELOG.md](CHANGELOG.md) (if present)
- **Contributing** ,  [CONTRIBUTING.md](CONTRIBUTING.md) (if present)

## License

[MIT](LICENSE) ,  Kelvin Oliveira

---

<div align="center">

**Built with** · Python 3 + scikit-learn + pandas

**Author** · [Kelvin Oliveira](https://github.com/KelvinOliveiraCode) · [Portfolio](https://kelvinoliveiracode.github.io/portfolio/)

</div>