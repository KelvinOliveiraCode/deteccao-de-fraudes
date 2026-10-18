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

## Capacidades

| Capacidade | Descrição |
|------------|-------------|
| Carregamento de Dados | Auto-download do TensorFlow creditcard.csv (284k transações) com cache local |
| EDA e Visualização | Distribuição de classes, histogramas de Amount, heatmaps de correlação (matplotlib/seaborn) |
| Pré-processamento | RobustScaler em Amount/Time, split estratificado, remoção de duplicatas |
| Balanceamento | SMOTE oversampling (fallback: class_weight="balanced") |
| Modelagem | LogisticRegression, RandomForest, ExtraTrees com RandomizedSearchCV (StratifiedKFold) |
| Métricas | Accuracy, Precision, Recall, F1, ROC AUC, Specificity, Confusion Matrix |
| Seleção | Score ponderado: Recall 40% + F1 30% + Precision 20% + Accuracy 10% |
| Persistência | Serialização joblib para modelo, scaler e resultados completos |


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

## Perguntas Frequentes

<details>
<summary><strong>Por que SMOTE com fallback class_weight?</strong></summary>

SMOTE gera amostras sintéticas da classe minoritária mas exige imbalanced-learn. O fallback garante que o pipeline rode mesmo sem ele.

</details>

<details>
<summary><strong>Por que score ponderado para seleção de modelo?</strong></summary>

Detecção de fraude prioriza Recall (pegar fraudes). O peso 40/30/20/10 equilibra Recall com Precision e F1.

</details>

<details>
<summary><strong>Posso usar com dados novos?</strong></summary>

Sim ,  retreine rodando main.py com seu CSV (mesmo schema). O pipeline cuida de download, pré-processamento e exportação do modelo automaticamente.

</details>



---

## Documentação

- **Código-Fonte** ,  Navegue pelo diretório `src/` para detalhes de implementação
- **Referência da API** ,  Docstrings inline + type hints
- **Diagrama de Arquitetura** ,  Veja [architecture.mermaid](docs/architecture.mermaid) (se presente)
- **Changelog** ,  [CHANGELOG.md](CHANGELOG.md) (se presente)
- **Contribuindo** ,  [CONTRIBUTING.md](CONTRIBUTING.md) (se presente)

## Licença

[MIT](LICENSE) ,  Kelvin Oliveira

---

<div align="center">

**Construído com** · Python 3 + scikit-learn + pandas

**Autor** · [Kelvin Oliveira](https://github.com/KelvinOliveiraCode) · [Portfolio](https://kelvinoliveiracode.github.io/portfolio/)

</div>