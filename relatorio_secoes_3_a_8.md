# Texto para o relatório: seções 3 a 8

Texto pronto para colar no Google Docs, no lugar dos trechos [PREENCHER]. Todos os números saem do notebook `Trabalho_2_Aprendizado_de_Máquina_Previsão_do_PIB_dos_Municípios.ipynb` (seções 5.5 a 8).

**Figuras (limite de 5).** Sugestão:

| Figura | Conteúdo | Onde está no notebook |
|---|---|---|
| 1 | Evolução do PIB por UF | 5.1 (já existia) |
| 2 | Correlação de Spearman | 5.4 (já existia) |
| 3 | Crescimento anual dos municípios x PIB do Brasil | 5.6 (nova) |
| 4 | PIB real x previsto em 2023 e histograma do erro | 7.1 (nova) |
| 5 | Matriz de confusão de 2023 | 7.2 (nova) |

O gráfico log PIB x log população e o de barras das categorias saem como figura e entram só como números no texto (abaixo). Assim o relatório fica dentro do limite.

---

## 3.1 Principais análises exploratórias

- **Estatísticas descritivas (2023).** O PIB municipal mediano é de R$ 544 milhões e a média é de R$ 2,48 bilhões, puxada por poucos municípios: Brasília, Goiânia, Campo Grande, Cuiabá e Rio Verde somam 47% do PIB da região, e só Brasília responde por 31,5%. A população mediana é de 9,6 mil habitantes. O PIB per capita mediano é de R$ 47,7 mil, com máximos em municípios pouco populosos e de produção muito alta, como Santa Rita do Trivelato/MT (R$ 382 mil) e Selvíria/MS (R$ 340 mil). A assimetria do PIB é de 19,2 na escala original e cai para 0,57 em log10, o que justifica trabalhar em log.
- **Evolução do PIB por UF (Figura 1).** Entre 2002 e 2023, o PIB nominal do DF cresceu 6,8 vezes, o de GO 8,7, o de MS 11,2 e o de MT 14,2. Com isso, a participação do DF no PIB da região caiu de 42,1% para 31,5%, e a de MT subiu de 15,0% para 23,5%, reflexo da expansão do agronegócio.
- **Log do PIB x log da população.** A relação é quase linear (correlação de Pearson de 0,89 em 2023, inclinação de 1,03): em escala log, o PIB cresce praticamente na mesma proporção da população. Os municípios que mais se afastam da reta são pequenos e com PIB per capita alto, ligados ao agro e à indústria.
- **Distribuição das categorias de crescimento.** É muito desbalanceada. De 2003 a 2023, forte crescimento reúne 71,3% das observações, crescimento moderado 10,6%, forte retração 8,4%, retração 5,7% e estabilidade 4,1%. Como o PIB é nominal, a inflação empurra quase todos os municípios para cima. A proporção varia muito de ano para ano: forte crescimento chegou a 93,1% em 2021 e caiu para 61,5% em 2023, quando 15,8% dos municípios tiveram forte retração.
- **Correlação de Spearman (Figura 2).** O PIB tem correlação muito alta com o VAB de serviços (0,96), administração pública (0,93) e indústria (0,92), e alta com a população (0,79). O PIB per capita quase não se relaciona com a população (0,14), mas se relaciona com o VAB agro (0,73) e com o valor da produção agrícola (0,65): no Centro-Oeste, o que torna um município rico por habitante é o agro, não o tamanho. O valor da PAM e o VAB agro têm correlação de 0,85, o que confirma a PAM como boa aproximação do agro em 2022 e 2023, anos sem VAB.
- **Crescimento anual x PIB do Brasil (Figura 3).** A mediana do crescimento nominal dos municípios vai de 1,9% (2005) a 24,8% (2003) e acompanha o PIB nominal do Brasil (correlação de 0,63). Boa parte do crescimento de um município num ano é um efeito comum a todos (inflação, ciclo das commodities). Em 2020 e 2021 a região cresceu bem acima do país (mediana de 15,6% e 21,7%, contra 3,0% e 18,4% do Brasil), puxada pelo agro.
- **Variáveis explicativas x crescimento.** Na base de modelagem, as maiores correlações de Spearman com o crescimento do PIB são a variação do valor da produção agrícola em relação ao PIB anterior (0,51), o crescimento do PIB do Brasil (0,28) e o peso da lavoura na economia (0,20). Os crescimentos passados têm correlação levemente negativa (de -0,05 a -0,12): depois de um ano muito bom, a tendência é de algum recuo.

## 3.2 Tratamento de valores ausentes, duplicados e inconsistências (complementos)

- **PIB ausente: causa e tratamento.** São municípios ainda não instalados no ano: Figueirão/MS, Ipiranga do Norte/MT e Itanhangá/MT (PIB a partir de 2005) e Paraíso das Águas/MS (a partir de 2013). Esses anos não foram imputados. Eles não entram como alvo, e os crescimentos passados que faltam no primeiro ano de cada município são imputados pela mediana dentro do pipeline de modelagem.
- **População: anos interpolados.** 2007, 2010, 2022 e 2023, anos sem estimativa na tabela 6579 (2007 e 2010 são anos de Contagem e Censo; 2022 e 2023 vêm logo após o Censo 2022). A interpolação vale para todos os municípios.
- **Duplicados:** nenhum na chave (município, ano).
- **Inconsistências.** Nenhuma divergência acima de 1% entre PIB e VAB mais impostos, nem entre VAB total e soma dos setores. Houve um único valor não positivo: Cachoeira Dourada/GO em 2021, com VAB total de -R$ 227,7 milhões e VAB da indústria de -R$ 477,3 milhões. É o valor oficial do IBGE e foi mantido. O efeito desses casos extremos na modelagem é controlado pela winsorização do alvo (seção 3.3).
- **Boa Esperança do Norte/MT** está entre os 468 municípios, mas não tem nenhum dado até 2023: foi instalado em 2025.

## 3.3 Transformações aplicadas aos dados (complementos)

- **Base de modelagem.** Uma linha por município e ano-alvo *t* (2007 a 2024; 8.399 linhas), montada só com informação disponível antes da divulgação do PIB municipal de *t* (seção 3.4). Fica salva em `base_modelagem.csv`.
- **Normalização da escala.** PIB, população e PIB per capita entram em log10. O alvo da regressão é o crescimento em log, ln(PIB_t / PIB_t-1), e o PIB previsto é PIB_t-1 × exp(crescimento previsto).
- **Padronização.** `StandardScaler` (média 0 e desvio 1) nas variáveis numéricas dos modelos lineares (Ridge e Regressão Logística). Os modelos de árvore não dependem de escala e não recebem padronização.
- **Codificação de variáveis categóricas.** A UF vira 4 colunas binárias (one-hot). A categoria de crescimento, alvo da classificação, é tratada como rótulo de classe.
- **Ausentes na modelagem.** Imputação pela mediana (`SimpleImputer`), ajustada só nos dados de treino e aplicada dentro do pipeline, para não vazar informação do teste.
- **Outliers do alvo.** No treino da regressão, o crescimento é limitado aos percentis 0,5% e 99,5% do próprio treino (winsorização). Cachoeira Dourada, por exemplo, tem crescimento de -83% em 2021 e +1.321% em 2022, valores que distorceriam os modelos.
- **Composição setorial sem vazamento.** A composição fixa de 2021 (descrita acima) usaria informação futura para prever anos anteriores a 2021. Na modelagem, cada ano-alvo usa a composição do ano anterior, e a de 2021 vale para 2022 em diante.

## 3.4 Seleção e definição das variáveis utilizadas nos modelos

A regra para escolher as variáveis é usar apenas o que já está publicado quando se quer prever o PIB de um ano. O PIB municipal sai com cerca de dois anos de defasagem, e várias fontes do mesmo ano saem antes dele.

| Grupo | Variáveis | Justificativa |
|---|---|---|
| Histórico do PIB | `log_pib_lag1` (log do PIB de t-1), `g_lag1`, `g_lag2`, `g_lag3` (crescimento em t-1, t-2, t-3), `vol5` (desvio padrão do crescimento nos 5 anos anteriores), `log_pibpc_lag1` (log do PIB per capita de t-1) | PIB até t-1 já publicado; captura porte, tendência e instabilidade do município |
| População de t | `log_pop`, `g_pop` (crescimento da população) | Estimativa publicada em agosto de t |
| Lavouras de t (PAM) | `pam_share` (valor da produção / PIB de t-1), `impacto_pam` ((PAM_t − PAM_t-1) / PIB_t-1) | PAM publicada em setembro de t+1; substitui o VAB agro, que não existe em 2022 e 2023 |
| Macro | `g_pib_brasil` (crescimento do PIB nominal do Brasil em t, SIDRA 6784) | Contas Nacionais publicadas em março de t+1; captura o efeito comum do ano |
| Estrutura setorial | `sh_agro`, `sh_industria`, `sh_servicos`, `sh_adm_publica` (participação no PIB) | Última composição conhecida (t-1, até 2021) |
| Localização | `uf` (one-hot) | Diferenças estruturais entre os estados |

**Variáveis-alvo.** Regressão: `g_pib`, crescimento do PIB em log, convertido para PIB em mil R$. Classificação: `categoria`, uma das cinco faixas do enunciado aplicadas ao crescimento percentual de *t*.

## 4.1 Modelos de regressão e classificação utilizados

**Regressão (prevê o crescimento do PIB):**
- **Ridge:** regressão linear com regularização L2. É simples e interpretável, e a regularização lida com variáveis correlacionadas (os lags e as participações setoriais).
- **Random Forest:** média de árvores de decisão. Capta relações não lineares e interações (por exemplo, a queda da lavoura pesa mais onde o agro é grande) e é robusta a outliers.
- **HistGradientBoosting:** árvores construídas em sequência, cada uma corrigindo o erro da anterior. Costuma ser o mais preciso em dados tabulares e aceita valores ausentes nativamente.
- **Baselines:** repetir o PIB de 2022, e aplicar ao PIB de 2022 o crescimento do PIB do Brasil. Servem para mostrar se os modelos acrescentam algo.

**Classificação (prevê a categoria de crescimento):**
- **Regressão Logística multinomial**, **Random Forest** e **HistGradientBoosting**, todos com `class_weight="balanced"` por causa do desbalanceamento das classes.
- **Categoria derivada da regressão:** aplicar as faixas do enunciado ao crescimento previsto pelo melhor regressor.
- **Baseline:** repetir a categoria do ano anterior.

## 4.2 Estratégias de treinamento e validação

- **Divisão treino/teste temporal, como pede o enunciado.** Treino com anos-alvo de 2007 a 2022 (7.465 linhas) e teste com 2023 (467 municípios). Os dados oficiais de 2023 não são usados no treino nem no ajuste.
- **Ajuste de hiperparâmetros.** `GridSearchCV` com validação cruzada temporal de janela expansível, em 4 folds: cada fold treina com os anos anteriores a *v* e valida em *v*, para *v* = 2019, 2020, 2021 e 2022. Nunca se usa o futuro para prever o passado. Métrica de ajuste: erro absoluto médio do crescimento em log na regressão (equivale aproximadamente ao MAPE) e F1 macro na classificação.
- **Grades testadas.**
  - Ridge: alpha em {0,1; 1; 10; 100}.
  - Random Forest (regressão): min_samples_leaf em {5, 20} e max_features em {1,0; 0,5}.
  - HistGradientBoosting: learning_rate em {0,03; 0,1} e min_samples_leaf em {20, 50}.
  - Regressão Logística: C em {0,1; 1; 10}.
  - Random Forest (classificação): min_samples_leaf em {3, 10} e max_features em {sqrt; 0,5}.
  - Todos os ensembles usam 300 árvores ou iterações e `random_state=42`.
- **Previsão de 2024.** Os modelos escolhidos são retreinados com os anos-alvo de 2007 a 2023, com os mesmos hiperparâmetros, e aplicados às variáveis de 2024: PIB de 2023, população e PAM de 2024 e PIB do Brasil de 2024.

## 4.3 Procedimentos adotados para treinamento e avaliação dos modelos

1. Montagem da base de modelagem a partir do painel tratado e do PIB do Brasil, com checagens automáticas: chave única, coerência entre o crescimento defasado e o alvo do ano anterior, e variáveis de 2024 completas.
2. Pré-processamento dentro de pipelines do scikit-learn (imputação, padronização quando aplicável e one-hot da UF), ajustados só no treino de cada etapa.
3. Ajuste de hiperparâmetros por validação temporal, treino com os melhores parâmetros e previsão de 2023.
4. Avaliação no teste de 2023 contra os baselines.
5. Seleção do regressor pela menor soma de rankings nas duas métricas pedidas (MAPE e R²), e da abordagem de classificação pelo maior F1 macro.
6. Retreino até 2023 e previsão de 2024, salva em `previsoes_2024.csv`. As previsões de 2023 por município ficam em `previsoes_2023_validacao.csv`.
7. Reprodutibilidade: sementes fixas e notebook executável de ponta a ponta (cerca de 5 minutos em CPU).

## 5.1 Métricas utilizadas para avaliação

- **R²:** proporção da variância do PIB explicada pelo modelo. Em valor de PIB, ele é dominado pelos grandes municípios (Brasília responde por 31% da região) e fica perto de 1 até para um baseline ingênuo. Por isso também reportamos, como complemento, o R² do log do PIB e o R² do crescimento.
- **MAPE:** erro percentual absoluto médio. Cada município pesa o mesmo, então é a métrica que mostra a qualidade da previsão para a maioria dos municípios, que são pequenos.
- **F1-score, média macro:** média simples do F1 das cinco classes. Com 61,5% dos municípios de 2023 em forte crescimento, a acurácia e o F1 ponderado ficariam altos mesmo acertando só a classe dominante. A média macro dá o mesmo peso às classes raras. F1 ponderado e acurácia aparecem como complemento.
- **Matriz de confusão:** mostra entre quais categorias os erros acontecem (Figura 5).

## 5.2 Comparação entre modelos

**Regressão, teste de 2023 (467 municípios):**

| Modelo | R² | R² log(PIB) | R² crescimento | MAPE (%) | Melhores parâmetros |
|---|---|---|---|---|---|
| Baseline: PIB 2022 | 0,9895 | 0,9855 | -0,319 | 12,09 | – |
| Baseline: PIB 2022 × crescimento Brasil | 0,9991 | 0,9890 | 0,000 | 10,78 | – |
| Ridge | 0,9951 | 0,9938 | 0,432 | 7,67 | alpha = 100 |
| **Random Forest** | **0,9997** | 0,9935 | 0,405 | 7,73 | min_samples_leaf = 5, max_features = 1,0 |
| HistGradientBoosting | 0,9986 | 0,9930 | 0,361 | 8,09 | learning_rate = 0,03, min_samples_leaf = 20 |

**Classificação, teste de 2023:**

| Abordagem | F1 macro | F1 ponderado | Acurácia |
|---|---|---|---|
| Baseline: categoria de 2022 | 0,154 | 0,411 | 0,460 |
| **Categoria derivada da regressão (Random Forest)** | **0,377** | **0,629** | **0,602** |
| Regressão Logística (C = 1) | 0,225 | 0,345 | 0,332 |
| Random Forest (min_samples_leaf = 3, max_features = 0,5) | 0,274 | 0,528 | 0,518 |
| HistGradientBoosting (learning_rate = 0,03, min_samples_leaf = 20) | 0,267 | 0,511 | 0,493 |

Modelos escolhidos: **Random Forest** para a regressão e **categoria derivada da regressão** para a classificação.

## 5.3 Discussão dos resultados

- **Os três regressores superam os baselines.** O MAPE cai de 12,1% (repetir 2022) e 10,8% (aplicar o crescimento do Brasil) para 7,7% a 8,1%. O ganho vem principalmente da produção agrícola do próprio ano: `impacto_pam` é, de longe, a variável mais importante no teste de 2023 (importância por permutação, seção 7.3 do notebook).
- **Por que o Random Forest, e não o Ridge.** O Ridge teve o menor MAPE, mas só 0,06 ponto abaixo do Random Forest, e o pior R² entre os modelos. A diferença está nos grandes municípios: o Ridge previu crescimento de 3,4% para Brasília, que cresceu 11,2%, enquanto o Random Forest errou o PIB de Brasília em apenas 0,8%. Como Brasília é 31% do PIB da região, isso pesa muito no total. Na validação temporal de 2019 a 2022, Random Forest e HistGradientBoosting também tiveram erro menor que o Ridge.
- **Onde o modelo erra.** 43% dos municípios ficam com erro de até 5% e 73% com erro de até 10% (Figura 4). A mediana do erro absoluto é de 0,8% no DF, 4,5% em GO, 6,9% em MS e 8,6% em MT: quanto mais a economia depende do agro, mais volátil e difícil de prever. Há um viés: 71% dos municípios foram subestimados em 2023 (erro mediano de -4,1%).
- **Classificação.** Derivar a categoria do crescimento previsto supera os classificadores diretos (F1 macro de 0,377 contra no máximo 0,274). As categorias são cortes de uma variável contínua. O classificador direto ignora a ordem entre elas (errar "retração" por "forte retração" custa o mesmo que errar por "forte crescimento") e sofre com as classes raras. A matriz de confusão (Figura 5) mostra bons acertos nas pontas, com F1 de 0,82 em forte crescimento e 0,56 em forte retração. Nas três faixas centrais o F1 fica perto de 0,17: elas têm só 2 a 4 pontos de largura, e um erro pequeno no crescimento já troca a faixa. A maioria dos erros cai em faixas vizinhas.

## 5.4 Apresentação e análise das previsões do PIB

O arquivo `previsoes_2024.csv` traz o PIB previsto de 2024 para os 467 municípios com histórico. Agregado por UF:

| UF | PIB 2023 (R$ bi) | PIB 2024 previsto (R$ bi) | Crescimento |
|---|---|---|---|
| DF | 365,67 | 398,77 | +9,1% |
| GO | 336,75 | 361,83 | +7,5% |
| MS | 184,40 | 190,04 | +3,1% |
| MT | 273,01 | 278,44 | +2,0% |
| **Centro-Oeste** | **1.159,83** | **1.229,08** | **+6,0%** |

- **Grandes municípios:** os maiores PIBs previstos são Brasília (R$ 398,8 bi, +9,1%), Goiânia (R$ 84,5 bi, +11,6%), Campo Grande (R$ 47,0 bi, +11,3%) e Cuiabá (R$ 43,8 bi, +12,3%). Entre os grandes, Rio Verde (+3,3%) e Dourados (+4,6%), mais ligados ao agro, crescem menos.
- **Distribuição:** o crescimento mediano previsto é de 4,7%, indo de -23,8% (Turvânia/GO) a +46,8% (Aporé/GO). As maiores quedas previstas são em municípios pequenos de GO e MT com grande peso da lavoura (Turvânia, Planalto da Serra, Panamá, Nortelândia e Nova Lacerda): nelas o valor das lavouras equivale a 53% a 96% do PIB, contra 27% na mediana da região.
- **Explicação:** MS e MT crescem bem menos que DF e GO porque o valor da produção das lavouras caiu 30,8% em MS e 21,3% em MT em 2024 (PAM), contra 11,7% em GO e 4,4% no DF.
- **Boa Esperança do Norte/MT:** está no arquivo sem previsão. O município foi instalado em 2025, então não existe PIB dele em 2023 nem em 2024 (o território era contado em Sorriso e Nova Ubiratã).

## 5.5 Apresentação e análise das categorias de crescimento econômico para 2024

| UF | Forte retração | Retração | Estabilidade | Cresc. moderado | Forte crescimento | Total |
|---|---|---|---|---|---|---|
| DF | 0 | 0 | 0 | 0 | 1 | 1 |
| GO | 22 | 20 | 19 | 27 | 158 | 246 |
| MS | 27 | 17 | 4 | 13 | 18 | 79 |
| MT | 41 | 24 | 6 | 21 | 49 | 141 |
| **Total** | **90** | **61** | **29** | **61** | **226** | **467** |

- Quase metade dos municípios (226) fica em forte crescimento, em linha com o crescimento nominal do país em 2024 (7,6%).
- As retrações se concentram onde o agro pesa mais. Em MS, 44 dos 79 municípios aparecem em retração ou forte retração, e em MT são 65 de 141, acompanhando a queda do valor da produção agrícola em 2024. Em GO, 158 dos 246 municípios ficam em forte crescimento.
- Em comparação com 2023 (74 municípios em forte retração), 2024 deve ter mais municípios em queda (90), já que a perda de valor das lavouras foi maior (-20,6% na região, contra -9,4% em 2023).
- **Confiabilidade:** pelas métricas de 2023, as categorias das pontas (forte crescimento e forte retração) são as mais confiáveis. Retração, estabilidade e crescimento moderado devem ser lidas como indicação de crescimento perto de zero, não como classificação precisa.

## 6.1 Principais resultados e aprendizados

- Os modelos reduziram o erro da previsão do PIB de 12,1% (baseline ingênuo) para 7,7% de MAPE em 2023, com R² de 0,9997. A Random Forest foi escolhida por equilibrar MAPE e R² e por acertar os grandes municípios.
- Prever o **crescimento**, em vez do valor do PIB, foi decisivo: o valor do PIB é quase todo explicado pelo do ano anterior, e o que importa é a variação.
- A informação que mais ajuda é a do próprio ano que já foi publicada antes do PIB: a produção agrícola (PAM) e o PIB do Brasil. Ela compensa, em parte, a ausência do VAB setorial em 2022 e 2023.
- Na classificação, derivar a categoria da regressão foi melhor que treinar classificadores diretos, porque as faixas são cortes de uma variável contínua.
- Para 2024, a previsão é de crescimento de 6,0% no PIB da região, com retrações concentradas em MS e MT por causa da queda do valor das lavouras.

## 6.2 Limitações da abordagem

- A ausência do VAB por atividade em 2022 e 2023 impede o uso de variáveis setoriais atualizadas; a composição de 2021 é uma aproximação.
- O PIB é nominal. As categorias misturam crescimento real e inflação, e a faixa de estabilidade (-1% a 1%) é rara, o que deixa poucos exemplos para aprender.
- As faixas centrais são estreitas (2 a 4 pontos), e um erro pequeno no crescimento muda a categoria. O F1 macro de 0,38 reflete essa dificuldade.
- O modelo subestimou 71% dos municípios em 2023 (erro mediano de -4,1%). Choques que o histórico não mostra, como preços de commodities e grandes obras ou usinas, não são captados.
- A pecuária não entra nas variáveis, embora seja relevante em MS e MT. As pesquisas de indústria (PIA) e serviços (PAS) só existem por estado e não puderam ser usadas por município.
- Há só um ano de teste (2023). O desempenho pode variar em anos com choques diferentes.

## 6.3 Possíveis melhorias e trabalhos futuros

- Incluir a Pesquisa da Pecuária Municipal (efetivo de rebanhos e produção de origem animal) e a produção por cultura (soja, milho, algodão), separando quantidade e preço.
- Usar o PIB das UFs de 2024 (Contas Regionais), quando publicado, como variável macro mais próxima da região.
- Prever o crescimento real e a inflação separadamente, para categorias menos dependentes do nível de preços.
- Testar combinações de modelos (média de Random Forest, HistGradientBoosting e Ridge) e corrigir o viés de subestimação observado em 2023.
- Retreinar com a nova série do IBGE quando o VAB setorial de 2022 em diante for publicado (prevista para 2027).

## 7. Contribuição dos autores (linha a acrescentar)

| Integrante | Atividades |
|---|---|
| [PREENCHER: nome] | Análise exploratória complementar (distribuições, efeito do ano, desbalanceamento das categorias, relação das variáveis com o alvo); pré-processamento para a modelagem (base de modelagem sem vazamento, normalização, padronização, codificação e winsorização); treinamento, validação e comparação dos modelos de regressão e classificação; previsões do PIB e das categorias de 2024 |

## 8. Referências (acrescentar)

- IBGE. SIDRA, Tabela 6784: Produto Interno Bruto, Produto Interno Bruto per capita, população residente e deflator. https://sidra.ibge.gov.br/tabela/6784
- PEDREGOSA, F. et al. Scikit-learn: Machine Learning in Python. Journal of Machine Learning Research, v. 12, p. 2825-2830, 2011.
- Biblioteca sidrapy: https://pypi.org/project/sidrapy/
- Repositório da equipe (notebook, dados tratados e previsões): https://github.com/LuisEduardo100/previsao-pib-centro-oeste
