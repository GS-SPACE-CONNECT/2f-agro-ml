# Space Risk IA — Sistema inteligente de alerta com dados espaciais simulados
### Relatório final · Global Solution 1º/2026 · IA & Machine Learning · Desafio FIAP: Indústria Espacial

**Integrantes:** Lucca Borges (RM 554608), Ruan Melo (RM 557599), Rodrigo Jimenez (RM 558148), João Victor Franco (RM 556790), Bruno Leão (RM 555563)
**Turma:** 3ESPZ

---

## 1. Resumo da solução proposta
Construímos no Google Colab um protótipo de IA que apoia **defesa civil, agricultura e gestão
ambiental** na análise de risco de regiões monitoradas por satélite (dados simulados). A solução
responde a três perguntas operacionais: (1) **qual o índice de impacto** estimado de uma região
(regressão linear), (2) **se a região é de baixo ou alto risco** (regressão logística e árvore de
decisão) e (3) **quais regiões têm perfis ambientais parecidos** (K-Means). O objetivo é permitir
**alertas antecipados e priorização de recursos** antes de eventos críticos.

## 2. Descrição do dataset
`dataset_space_risk_global_solution.csv` — **320 regiões** (linhas) × 14 colunas, **sem valores nulos
e sem duplicatas**. São **11 variáveis ambientais/operacionais** (temperatura, umidade do solo, NDVI
de vegetação, chuva prevista, vento, proximidade urbana, histórico de eventos em 5 anos, altitude,
inclinação, densidade populacional, cobertura de nuvens) e **2 alvos**: `indice_impacto` (numérico,
para regressão) e `risco_alto` (0 = baixo, 1 = alto, para classificação). O identificador `regiao_id`
**não** é usado no treino, por ser apenas um rótulo. O alvo de classificação está levemente
desbalanceado (**200 alto / 120 baixo**, 62,5% / 37,5%), o que tratamos com divisão **estratificada**.

## 3. Análise exploratória e correlação
O mapa de calor mostra que o **NDVI (vegetação) tem a correlação negativa mais forte** com o impacto
(≈ −0,62) e com o risco (≈ −0,51): **regiões mais verdes são mais seguras**. Em sentido oposto,
`historico_eventos_5anos` (≈ +0,78 com o impacto), `vento_kmh`, `temperatura_media_c`,
`inclinacao_terreno_graus` e `chuva_prevista_mm` aumentam o risco. *Correlação não prova causa* — ela
apenas orientou a escolha de variáveis e a leitura dos modelos.

## 4. Resultados — Regressão linear múltipla (`indice_impacto`)
Divisão treino/teste 80/20 (`random_state=42`), `LinearRegression` sobre as 11 variáveis ambientais
(sem `regiao_id` e sem os alvos). Resultados no teste:

| Métrica | Valor |
|---|---|
| **R²** | **0,974** |
| **MAE** | **3,23** (numa escala de 0 a ~97) |

**Leitura:** o R² alto indica que as variáveis ambientais **explicam muito bem** o índice de impacto —
ele é quase reconstruível a partir das condições da região. O erro médio de ~3 pontos é pequeno na
prática. Os coeficientes confirmam: NDVI reduz o índice; histórico de eventos, vento e temperatura o
aumentam.

## 5. Resultados — Regressão logística (`risco_alto`)
`Pipeline(StandardScaler → LogisticRegression)`, divisão **estratificada**, **sem usar `indice_impacto`**
como entrada (evita vazamento de resposta).

| Métrica | Valor |
|---|---|
| **Acurácia** | **0,938** |
| **AUC (ROC)** | 0,972 |
| Matriz de confusão | TN=21, **FP=3**, **FN=1**, TP=39 |

**Respostas obrigatórias:** (1) probabilidade próxima de 1 (`predict_proba`) significa **alta confiança**
de que a região é de alto risco — vai para o topo da fila de alerta; (2) houve **mais falso positivo (3)
do que falso negativo (1)**; (3) num sistema de alerta o **falso negativo é mais perigoso** — dizer
"baixo risco" para uma região de alto risco faz a defesa civil **não agir**, com custo de vidas e safras;
o falso positivo gera apenas custo de verificação. Por isso priorizaríamos o **recall da classe de alto risco**.

## 6. Resultados — Árvore de decisão
`DecisionTreeClassifier(max_depth=4)`, mesmo X e y da logística.

| Modelo | Acurácia |
|---|---|
| Árvore de decisão | **0,969** |
| Regressão logística | 0,938 |

A árvore foi ligeiramente mais acurada e, principalmente, **mais fácil de explicar**: o `export_text`
gera regras como *"se histórico de eventos ≤ X e NDVI > Y → baixo risco"*, auditáveis por pessoas não
técnicas. A logística, por sua vez, entrega **probabilidades** úteis para priorização. Ambas concordam
que **NDVI e histórico de eventos** são as variáveis mais decisivas.

## 7. Resultados — K-Means (segmentação)
`KMeans(k=4)` sobre variáveis ambientais padronizadas (`StandardScaler`), **sem usar `risco_alto`**.
O método do cotovelo apoia k=4. Quatro perfis emergiram:

| Cluster | Nome | Perfil dominante | Risco médio* |
|---|---|---|---|
| 0 | Urbanas com chuva intensa | densidade ~1500/km², chuva alta, perto do urbano | ~0,78 |
| 1 | Secas / áridas | ~35 °C, umidade ~21%, NDVI baixo | ~0,84 |
| 2 | Estáveis / vegetadas | NDVI alto ~0,67, poucos eventos | **~0,02 (baixo)** |
| 3 | Periurbanas com chuva | chuva ~70 mm, densidade média | ~0,89 |

\* `risco_alto` foi usado **só na leitura** dos grupos, não no treino. O cluster verde/estável isolou-se
naturalmente como **baixo risco** — confirma que as variáveis ambientais carregam o sinal de risco mesmo
sem supervisão (conexão direta com o ODS 2: vegetação saudável = agricultura segura).

## 8. Conclusão — conexão com o briefing e os ODS
Esta solução ajuda **defesa civil, agricultura e gestão ambiental** a **antecipar regiões em risco** de
seca, chuva extrema ou perda de vegetação, priorizando recursos antes do desastre. Em produção, ela
seria alimentada por **dados reais de satélite** (NDVI, temperatura de superfície, umidade do solo,
precipitação — fontes como NASA, ESA e a International Charter Space and Major Disasters), no lugar dos
dados simulados.

**ODS atendidos:** **ODS 2 (Fome zero / agricultura sustentável)** — identifica regiões agrícolas
ameaçadas; **ODS 13 (Ação contra a mudança do clima)** — modela variáveis de eventos extremos; e, de
forma transversal, **ODS 9 (Indústria, inovação e infraestrutura)** — IA + dados espaciais como solução
tecnológica de impacto.

**Modelo mais útil:** para um sistema de alerta, **árvore de decisão** (transparente e auditável) +
**regressão logística** (probabilidades para priorização) formam a melhor dupla; o K-Means agrega valor
ao revelar **perfis de território** para políticas diferenciadas. **Com mais tempo**, usaríamos dados
reais de satélite, validação cruzada, ajuste de hiperparâmetros, séries temporais e calibração do limiar
para **maximizar o recall de alto risco**, reduzindo falsos negativos.

**Limitações.** As métricas são muito altas (R² ≈ 0,97 e 90–97% de acurácia) em boa parte porque os
dados são **simulados e "limpos"**, com relações quase determinísticas e sem o ruído de sensores reais
de satélite (nuvens, falhas de leitura, valores faltantes). Em dados reais o desempenho tende a cair —
inclusive a validação cruzada já mostra a árvore menos estável que a logística — e o sistema exigiria
re-treino periódico, tratamento de ruído/dados faltantes e validação contínua antes de uso operacional.

