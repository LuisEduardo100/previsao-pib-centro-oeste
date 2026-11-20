# Dados: Centro-Oeste

Todos os arquivos são gerados pelo notebook [`notebooks/trabalho2_previsao_pib.ipynb`](../notebooks/trabalho2_previsao_pib.ipynb), rodando as células em ordem, e estão copiados nesta pasta. Todos estão em CSV.

| Arquivo | Conteúdo | Linhas |
|---|---|---|
| `tratados/painel_centro_oeste_tratado.csv` | Painel município × ano (2002 a 2023) após o tratamento de ausentes e inconsistências | 10.258 |
| `tratados/base_modelagem.csv` | Base usada para treinar os modelos: município × ano-alvo (2007 a 2024) | 8.399 |
| `previsoes/previsoes_2024.csv` | **Previsões do PIB e da categoria de crescimento de 2024** | 468 |

## `painel_centro_oeste_tratado.csv`

Uma linha por município e ano (2002 a 2023).

| Coluna | Descrição |
|---|---|
| `cod_mun`, `municipio`, `uf`, `ano` | Código IBGE, nome, UF e ano |
| `pib` | PIB a preços correntes (mil R$), SIDRA 5938 |
| `vab_total`, `vab_agro`, `vab_industria`, `vab_servicos`, `vab_adm_publica` | Valor adicionado bruto total e por setor (mil R$), SIDRA 5938. Só até 2021 |
| `impostos` | Impostos líquidos de subsídios (mil R$), SIDRA 5938. Só até 2021 |
| `populacao` | População estimada, SIDRA 6579. Anos sem estimativa (2007, 2010, 2022 e 2023) interpolados |
| `pam_valor_producao` | Valor da produção das lavouras (mil R$), SIDRA 5457. Sem registro = 0 |
| `pib_per_capita` | PIB × 1000 / população (R$ por habitante) |
| `%vab_agro`, `%vab_industria`, `%vab_servicos`, `%vab_adm_publica` | VAB do setor / PIB |
| `%vab_*_2021` | As mesmas participações, fixadas no valor de 2021 |
| `crescimento_pib_pct` | Crescimento do PIB em relação ao ano anterior (%) |
| `categoria` | Faixa de crescimento (as mesmas cinco categorias do alvo) |
| `log_pib`, `log_pop` | log10 do PIB e da população |

## `base_modelagem.csv`

Cada linha prevê o PIB do ano `ano` (t) com informação publicada antes do PIB municipal de t.

| Coluna | Descrição |
|---|---|
| `cod_mun`, `municipio`, `uf` | Código IBGE, nome e UF |
| `ano` | Ano-alvo t |
| `pib_lag1` | PIB de t-1 (mil R$) |
| `log_pib_lag1` | log10 do PIB de t-1 |
| `g_lag1`, `g_lag2`, `g_lag3` | Crescimento do PIB em log, ln(PIB/PIB anterior), em t-1, t-2 e t-3 |
| `vol5` | Desvio padrão do crescimento em log nos 5 anos anteriores |
| `log_pibpc_lag1` | log10 do PIB per capita de t-1 (R$ por habitante) |
| `log_pop` | log10 da população estimada de t |
| `g_pop` | Crescimento da população entre t-1 e t, em log |
| `pam_share` | Valor da produção das lavouras de t / PIB de t-1 |
| `impacto_pam` | (Valor da produção das lavouras de t − de t-1) / PIB de t-1 |
| `g_pib_brasil` | Crescimento do PIB nominal do Brasil em t, em log (SIDRA 6784) |
| `sh_agro`, `sh_industria`, `sh_servicos`, `sh_adm_publica` | Participação do VAB de cada setor no PIB, no ano t-1 (até 2021, último com VAB) |
| **`g_pib`** | **Alvo da regressão:** crescimento do PIB em log, ln(PIB_t / PIB_t-1). Vazio em 2024 |
| `pib` | PIB de t (mil R$). Vazio em 2024 |
| `crescimento_pct` | Crescimento percentual: (PIB_t − PIB_t-1) / PIB_t-1 × 100 |
| **`categoria`** | **Alvo da classificação:** Forte retração (< -5%), Retração (-5% a -1%), Estabilidade (-1% a 1%), Crescimento moderado (1% a 5%), Forte crescimento (> 5%) |

## `previsoes_2024.csv`

| Coluna | Descrição |
|---|---|
| `cod_mun`, `municipio`, `uf` | Código IBGE, nome e UF |
| `pib_2023` | PIB oficial de 2023 (mil R$) |
| `pib_2024_previsto` | PIB previsto para 2024 (mil R$), Random Forest |
| `crescimento_2024_previsto_pct` | Crescimento previsto de 2023 para 2024 (%) |
| `categoria_2024_prevista` | Categoria de crescimento prevista para 2024 |
| `observacao` | Preenchida só para Boa Esperança do Norte/MT, instalado em 2025 e sem PIB em 2023 e 2024 |

## Como baixar

- Um arquivo: abra o CSV no GitHub e use o botão *Download raw file*.
- Tudo: `git clone https://github.com/LuisEduardo100/previsao-pib-centro-oeste.git` ou *Code > Download ZIP* na página do repositório.
- Para gerar de novo: rode o notebook (instruções no [README principal](../README.md)).
