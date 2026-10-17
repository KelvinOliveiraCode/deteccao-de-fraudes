<<<<<<< HEAD
# Detecao de Fraudes em Cartoes de Credito

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.9%2B-blue?logo=python" alt="Python">
  <img src="https://img.shields.io/badge/scikit--learn-1.3%2B-orange?logo=scikit-learn" alt="scikit-learn">
  <img src="https://img.shields.io/badge/pandas-2.0%2B-purple?logo=pandas" alt="pandas">
  <img src="https://img.shields.io/badge/License-MIT-yellow" alt="License">
</p>

Pipeline de machine learning que baixa o dataset publico de fraudes de cartao de credito, treina e compara tres modelos de classificacao e salva o melhor modelo com seu scaler para inspecao posterior.

## Problema / Por que existe

Transacoes fraudulentas sao excecoes: no dataset de referencia, menos de 0,2 por cento das transacoes sao fraudes. Com dados tao desbalanceados, a acuracia sozinha engana — um modelo que previr sempre "legitima" teria cerca de 99,8 por cento de acuracia, mas detectaria zero fraudes. O projeto existe para treinar modelos que priorizem a deteccao de fraudes (recall) sem confiar apenas em acuracia, e para deixar os artefatos treinados salvos e inspecionaveis.

## O que faz

O pipeline em `main.py` executa, em ordem:

1. Baixa o dataset `creditcard.csv` (ou usa a copia local se ja existir).
2. Faz analise exploratoria: contagem de classes, valores ausentes, duplicatas, correlacao das features com o alvo e graficos salvos em `plots/`.
3. Pre-processa: remove duplicatas, faz divisao estratificada 80/20 entre treino e teste e aplica `RobustScaler` apenas em `Amount` e `Time` (as demais features ja estao padronizadas via PCA).
4. Tenta balancear as classes com SMOTE; se o pacote `imbalanced-learn` nao estiver instalado, usa `class_weight='balanced'` nos modelos como fallback.
5. Treina `LogisticRegression`, `RandomForestClassifier` e `ExtraTreesClassifier` com `RandomizedSearchCV` (validador `StratifiedKFold` de 3 folds, otimizando f1).
6. Seleciona o melhor modelo por score ponderado: recall 40 por cento, f1 30 por cento, precisao 20 por cento, acuracia 10 por cento.
7. Avalia o vencedor com relatorio de classificacao, matriz de confusao, ROC AUC e especificidade.
8. Gera graficos finais (curva ROC e matriz de confusao) em `plots/`.
9. Salva artefatos: `modelo_<nome>.joblib`, `scaler.joblib` e `resultados_modelos.joblib`.

O script `inspecionar_modelo.py` le os arquivos `.joblib` gerados e exprime detalhes do modelo, do scaler, da tabela comparativa, da importancia das features e dos melhores hiperparametros.

## Stack

| Componente | Versao | Proposito |
|---|---|---|
| Python | 3.9+ | Linguagem |
| pandas | 2.0.0+ | Leitura e manipulacao do dataset |
| NumPy | 1.24.0+ | Operacoes numericas |
| scikit-learn | 1.3.0+ | Modelos, divisao, escalonamento e metricas |
| Matplotlib | 3.7.0+ | Graficos |
| Seaborn | 0.12.0+ | Graficos estatisticos |
| joblib | 1.3.0+ | Serializacao de modelo e scaler |
| imbalanced-learn | opcional | SMOTE para balanceamento de classes |

Instale as dependencias obrigatorias com `pip install -r requirements.txt`. O SMOTE so e usado se `imbalanced-learn` estiver instalado; caso contrario o codigo segue com `class_weight`.

## Como funciona

Os dados vem do dataset Credit Card Fraud Detection (transacoes europeias de cartao de credito), hospedado em:

https://storage.googleapis.com/download.tensorflow.org/data/creditcard.csv

O dataset tem 30 features (`Time`, `Amount` e `V1` a `V28`, ja transformadas por PCA para proteger a privacidade) e a coluna alvo `Class` (0 = legitima, 1 = fraude).

Decisoes de implementacao no codigo:

- `RobustScaler` e aplicado apenas em `Amount` e `Time`; o ajuste (`fit`) e feito somente no conjunto de treino para evitar vazamento de dados.
- A divisao e estratificada, preservando a proporcao de fraudes entre treino e teste.
- Quando `imbalanced-learn` esta disponivel, o SMOTE gera amostras sinteticas da classe minoritaria apenas no treino; o teste permanece na distribuicao original.
- A escolha do modelo usa um score ponderado que prioriza recall, pois em deteccao de fraudes o custo de deixar passar uma fraude e maior que o custo de um falso alarme.
- `RandomizedSearchCV` e configurado com poucas combinacoes (`n_iter=5`) para manter a execucao rapida.

## Como rodar

```bash
git clone https://github.com/KelvinOliveiraCode/fraud-detection-project.git
cd fraud-detection-project
python -m venv venv
venv\Scripts\activate          # Windows
# source venv/bin/activate   # Linux/Mac
pip install -r requirements.txt
python main.py
```

Na primeira execucao o dataset e baixado automaticamente (cerca de 67 MB) e salvo como `creditcard.csv`. Graficos sao gravados em `plots/` e os artefatos do modelo na raiz do projeto.

Para inspecionar os artefatos salvos:

```bash
python inspecionar_modelo.py
```

O tempo total de treinamento depende da maquina e da disponibilidade do SMOTE.

## Estrutura do projeto

```
fraud-detection-project/
|
|-- main.py                  # Pipeline completo: dados, EDA, pre-processamento, modelos, avaliacao e salvamento
|-- inspecionar_modelo.py    # Leitura e visualizacao dos artefatos .joblib gerados
|-- requirements.txt         # Dependencias obrigatorias
|-- gitignore                # Arquivos ignorados pelo Git
|-- LICENSE                  # Licenca MIT
|-- README.md                # Este arquivo
|
|-- creditcard.csv           # Gerado: dataset baixado (ignorado pelo Git)
|-- plots/                   # Gerado: graficos de EDA e resultados finais
|-- modelo_*.joblib          # Gerado: melhor modelo treinado
|-- scaler.joblib            # Gerado: scaler ajustado no treino
|-- resultados_modelos.joblib# Gerado: metricas e hiperparametros de todos os modelos
```

## Trade-offs e decisoes

- **Recall acima de acuracia**: o score de selecao pondera recall em 40 por cento. Isso pode aumentar falsos positivos, o que e aceitavel em deteccao de fraudes onde perder um caso e mais caro que revisar um alarme falso.
- **SMOTE opcional**: nao esta em `requirements.txt` para evitar uma dependencia extra. Se ausente, o codigo usa `class_weight='balanced'` sem interromper a execucao.
- **Execucao rapida**: modelos leves (30–50 arvores, profundidade maxima 10) e poucas combinacoes de hiperparametros. Isso tradea qualidade maximizada por tempo de treinamento reduzido.
- **Apenas `Amount` e `Time` sao escalados**: `V1–V28` ja sao componentes principais padronizados, então escalas-las novamente nao agrega valor.
- **Artefatos em joblib**: simples de salvar e carregar, mas nao incluem versionamento de modelo nem metadata de dados de treino.

## Limitações conhecidas

- Os resultados numericos dependem da execucao e do ambiente; nao sao garantidos nem reproduziveis bit a bit entre maquinas diferentes.
- O modelo e avaliado apenas sobre o mesmo dataset estatico, dentro do split 80/20. Nao ha validacao em dados totalmente novos fora do repositorio.
- As features `V1–V28` sao anonimizadas por PCA, portanto nao sao diretamente interpretaveis como variaveis de negocio.
- Na producao seria necessario: um limiar de decisao ajustavel sobre `predict_proba`, monitoramento de drift de dados e retreino periodico. O projeto nao implementa nada disso.
- Os arquivos `.joblib` contêm modelos treinados em pickle-like format; carregar arquivos de origens nao confiaveis e um risco de seguranca.

## Licenca

MIT License. Veja o arquivo [LICENSE](LICENSE) para detalhes.

## Autor

Kelvin Oliveira

GitHub: [@KelvinOliveiraCode](https://github.com/KelvinOliveiraCode)
=======
# Detecção de Fraudes — ML para Cartão de Crédito

![linguagem: Python](https://img.shields.io/badge/linguagem-Python-blue)  
![status: concluido](https://img.shields.io/badge/status-concluido-green)  
![licenca: MIT](https://img.shields.io/badge/licenca-MIT-blue)

### Descrição
Detecção de fraude em cartão com ML: 3 modelos, 284k transações, ROC AUC 0.94. Pipeline completo, do download do dataset ao modelo serializado em produção.

### O que faz
Detecção de fraude em cartão com ML: 3 modelos, 284k transações, ROC AUC 0.94. Pipeline completo, do download do dataset ao modelo serializado em produção.

### Tecnologias
- Python 3.9+
- scikit-learn

### Como usar
1. Instale as dependências: `pip install -r requirements.txt` (se o arquivo existir no repositório).
2. Execute os testes: `python -m pytest`.
Verifique os arquivos reais do repositório para os comandos exatos.

### Estrutura
Consulte a árvore do repositório para a organização exata de pastas e arquivos.

### Testes
Não há contagem de testes registrada nesta metadata. Adicione e execute testes apropriados à linguagem (pytest para Python, ./mvnw test para Java, npm test para JavaScript/TypeScript).

### Licença
MIT

### Autor
Kelvin Oliveira — github.com/KelvinOliveiraCode

---

# Fraud Detection — ML for Credit Card

![language: Python](https://img.shields.io/badge/language-Python-blue)  
![status: concluido](https://img.shields.io/badge/status-concluido-green)  
![license: MIT](https://img.shields.io/badge/license-MIT-blue)

### Description
Credit card fraud detection with ML: 3 models, 284k transactions, ROC AUC 0.94. Full pipeline, from dataset download to serialized production model.

### What it does
Credit card fraud detection with ML: 3 models, 284k transactions, ROC AUC 0.94. Full pipeline, from dataset download to serialized production model.

### Technologies
- Python 3.9+
- scikit-learn

### How to use
1. Install dependencies: `pip install -r requirements.txt` (if the file exists in the repository).
2. Run tests: `python -m pytest`.
Check the actual repository files for exact commands.

### Structure
Check the repository tree for the exact folder and file organization.

### Tests
No test count recorded in this metadata. Add and run tests appropriate for the language (pytest for Python, ./mvnw test for Java, npm test for JavaScript/TypeScript).

### License
MIT

### Author
Kelvin Oliveira — github.com/KelvinOliveiraCode
>>>>>>> 41bd8c274495d78382e88c7d933e7595598139cb
