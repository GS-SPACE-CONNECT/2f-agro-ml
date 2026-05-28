# 2f-agro-ml

Pipeline ML do 2F-AGRO - Space Risk IA aplicado a agricultura familiar.
Materia: **IA / Machine Learning** (10 pts) | FIAP 3ES | GS 2026.1

[![Hub](https://img.shields.io/badge/hub-2f--agro-success)](https://github.com/GS-SPACE-CONNECT/2f-agro)

## Objetivo
Notebook Jupyter (Colab) com 4 modelos sobre dataset_space_risk_global_solution.csv:
1. Regressao Linear -> indice_impacto (perda de safra)
2. Regressao Logistica (Pipeline+StandardScaler) -> risco_alto
3. Arvore de Decisao (max_depth=4) -> comparar
4. K-Means (k=4) -> segmentar

## Owner
[@lucksza](https://github.com/lucksza) | Team [`ml-os`](https://github.com/orgs/GS-SPACE-CONNECT/teams/ml-os)

## Entregaveis
- Notebook .ipynb executado
- Relatorio PDF ate 6 paginas (resumo, dataset, 4 resultados, ODS)
- CSV anexado

## Regras criticas
- NUNCA regiao_id como feature
- NUNCA indice_impacto ao prever risco_alto (vazamento)
- NUNCA risco_alto no K-Means

## Perguntas obrigatorias
1. R² alto significa o que?
2. Probabilidade ~1 = ?
3. **Falso positivo ou negativo e mais perigoso? Falso NEGATIVO**
4. Arvore e mais explicavel que logistica?
5. Modelo mais util pro prototipo?

## Stack
Python 3.10+ | pandas | numpy | matplotlib | seaborn | scikit-learn (LinearRegression, LogisticRegression, DecisionTreeClassifier, KMeans, StandardScaler, Pipeline)

## Clusters esperados
- 0: Estaveis | 1: Secas | 2: Encharcadas | 3: Encostas

## Links
- [Spec ML](https://github.com/GS-SPACE-CONNECT/2f-agro/blob/main/docs/specs/2026-05-27-2f-agro-design.md)
