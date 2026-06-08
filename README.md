# 🧠 2f-agro-ml

> Pipeline ML do 2F-AGRO — **Space Risk IA** aplicado à agricultura familiar.
> Matéria: **IA / Machine Learning** (10 pts) · FIAP 3ES · GS 2026.1

[![Hub](https://img.shields.io/badge/hub-2f--agro-success)](https://github.com/GS-SPACE-CONNECT/2f-agro)
[![Colab](https://img.shields.io/badge/Google-Colab-orange)](https://colab.research.google.com/drive/1An7ZQ_cfi8rCoA63eyO26cX0nsLTw4uR?usp=sharing)

## 🎯 Objetivo
Notebook Jupyter (Colab) com 4 modelos sobre o dataset oficial `dataset_space_risk_global_solution.csv`:
1. **Regressão Linear Múltipla** → `indice_impacto` (perda de safra)
2. **Regressão Logística** (Pipeline + StandardScaler) → `risco_alto`
3. **Árvore de Decisão** (max_depth=4) → comparação com a logística
4. **K-Means** (k=4) → segmentar perfis agronômicos

## 👥 Owner
[@lucksza](https://github.com/lucksza) · Team [`ml-os`](https://github.com/orgs/GS-SPACE-CONNECT/teams/ml-os)

## 📦 Entregáveis
- [x] Notebook executado → [`space_risk_ia.ipynb`](space_risk_ia.ipynb) (todas as células com saída)
- [x] Relatório de até 6 páginas → [`relatorio_space_risk.pdf`](relatorio_space_risk.pdf) · [`.docx`](relatorio_space_risk.docx)
- [x] Dataset oficial → [`dataset_space_risk_global_solution.csv`](dataset_space_risk_global_solution.csv)

## 📈 Resultados obtidos
| Etapa | Modelo | Métrica principal |
|---|---|---|
| Regressão linear | `LinearRegression` | R² = **0,974** · MAE = **3,23** |
| Classificação | `LogisticRegression` (Pipeline) | Acurácia = **0,938** · AUC = **0,972** |
| Classificação | `DecisionTreeClassifier` (depth 4) | Acurácia = **0,969** |
| Segmentação | `KMeans` (k=4) | 4 perfis ambientais distintos |

> Validação cruzada (5-fold): logística **0,928 ± 0,038**, árvore **0,878 ± 0,044** — a logística é mais estável.

## 🚫 Regras críticas (cuidado!)
- ❌ **NUNCA** usar `regiao_id` como feature
- ❌ **NUNCA** usar `indice_impacto` ao prever `risco_alto` (vazamento)
- ❌ **NUNCA** usar `risco_alto` no K-Means

## 🔑 Perguntas obrigatórias do relatório
1. R² alto significa o quê?
2. Probabilidade próxima de 1 → o que indica?
3. **Falso positivo ou falso negativo é mais perigoso?** → **FALSO NEGATIVO** (não avisar é pior que falso alarme)
4. A árvore é mais explicável que a logística?
5. Modelo mais útil pro protótipo?

## 🧩 Stack
Python 3.10+ · pandas · numpy · matplotlib · seaborn · scikit-learn (LinearRegression, LogisticRegression, DecisionTreeClassifier, KMeans, StandardScaler, Pipeline)

## 🏷️ Clusters encontrados (k=4)
- **0** — Urbanas com chuva intensa (densidade ~1500/km², chuva alta)
- **1** — Secas / áridas (~35 °C, umidade ~21%, NDVI baixo)
- **2** — Estáveis / vegetadas (NDVI ~0,67, poucos eventos → **baixo risco**)
- **3** — Periurbanas com chuva (chuva ~70 mm, densidade média)

## 🔗 Links
- [Spec § 4.1 ML](https://github.com/GS-SPACE-CONNECT/2f-agro/blob/main/docs/specs/2026-05-27-2f-agro-design.md)
