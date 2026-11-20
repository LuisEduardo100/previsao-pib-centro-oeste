# Previsão do PIB dos municípios do Centro-Oeste (2024)

Trabalho 2 de **T326 Ciência de Dados** (Universidade de Fortaleza, Prof. Rilder de Sousa Pires). Modelos de aprendizado de máquina para prever o PIB de 2024 dos municípios do Centro-Oeste (GO, MT, MS e DF) e classificar o crescimento econômico de cada um em cinco categorias.

**Equipe:** José Walter de Oliveira Gomes, João Lucas Lobo Pinto Barboza e Luis Eduardo de Paula Albuquerque

[![Abrir no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/LuisEduardo100/previsao-pib-centro-oeste/blob/main/notebooks/trabalho2_previsao_pib.ipynb)

## Metodologia

1. **Dados:** painel município × ano de 2002 a 2023 com PIB, população e valor da produção agrícola (SIDRA), mais o PIB nominal do Brasil. São 468 municípios.
2. **Variáveis:** histórico do PIB (porte, crescimento e volatilidade), população, produção agrícola do ano, crescimento do PIB do Brasil, composição setorial do VAB e UF.
3. **Avaliação em duas etapas:** primeiro, os modelos são treinados com anos até 2022, e as previsões de 2023 são comparadas ao PIB oficial de 2023. Depois, os modelos escolhidos são retreinados com dados até 2023 para prever 2024.
4. **Regressão** (valor do PIB): Ridge, Random Forest e HistGradientBoosting, que preveem o crescimento do PIB. Métricas: R² e MAPE.
5. **Classificação** (categoria de crescimento): Regressão Logística, Random Forest e HistGradientBoosting, comparados com a categoria derivada do crescimento previsto pela regressão. Métricas: F1-score (média macro) e matriz de confusão.

Categorias de crescimento do PIB em relação ao ano anterior:

| Categoria | Crescimento |
|---|---|
| Forte retração | menor que -5% |
| Retração | de -5% a -1% |
| Estabilidade | de -1% a 1% |
| Crescimento moderado | de 1% a 5% |
| Forte crescimento | acima de 5% |

## Resultados

Avaliação em 2023, nos 467 municípios com histórico de PIB:

| Abordagem | Modelo escolhido | Desempenho em 2023 |
|---|---|---|
| Regressão (valor do PIB) | Random Forest | R² 0,9997 · MAPE 7,7% (repetir o PIB de 2022 dá 12,1%) |
| Classificação (categoria de crescimento) | Categoria derivada da regressão | F1 macro 0,377 · acurácia 60% |

Previsão para 2024 ([`previsoes_2024.csv`](dados/previsoes/previsoes_2024.csv)): PIB do Centro-Oeste de R$ 1.229 bilhões, 6,0% acima de 2023. Por categoria, são 226 municípios em forte crescimento, 61 em crescimento moderado, 29 em estabilidade, 61 em retração e 90 em forte retração. Boa Esperança do Norte (MT) aparece sem previsão: foi instalado em 2025 e não tem PIB em 2023 nem em 2024.

A comparação completa entre os modelos, os gráficos e as conclusões estão no notebook.

## Estrutura

```
├── notebooks/
│   └── trabalho2_previsao_pib.ipynb         coleta, EDA, pré-processamento, modelos e previsões
├── dados/
│   ├── README.md                            descrição das variáveis e das variáveis-alvo, como baixar
│   ├── tratados/
│   │   ├── painel_centro_oeste_tratado.csv  painel município × ano após o tratamento
│   │   └── base_modelagem.csv               base usada no treino dos modelos
│   └── previsoes/
│       └── previsoes_2024.csv               PIB e categoria de crescimento previstos para 2024
├── requirements.txt                         bibliotecas Python
└── README.md
```

## Como executar

**Google Colab (recomendado).** Abra pelo botão acima e use *Ambiente de execução > Executar tudo*. As bibliotecas já vêm instaladas. O notebook baixa os dados brutos do Google Drive da equipe (`gdown`) e o PIB nominal do Brasil da API do SIDRA, e grava os CSVs gerados em `/content`. Estimativa de 10 a 15 minutos no Colab gratuito, que tem 2 CPUs.

**Local.** Testado com Python 3.12 e 3.13.

```bash
git clone https://github.com/LuisEduardo100/previsao-pib-centro-oeste.git
cd previsao-pib-centro-oeste
python -m venv .venv
source .venv/bin/activate          # no Windows: .venv\Scripts\activate
pip install -r requirements.txt jupyter
jupyter notebook notebooks/trabalho2_previsao_pib.ipynb
```

Depois, *Run > Run All Cells*. Os CSVs são gravados na pasta `notebooks/`.

A seção 0 do notebook (código comentado) refaz a coleta direto do SIDRA com `sidrapy`. Não é preciso rodá-la: os CSVs coletados já estão no Drive da equipe e são baixados na seção 1.

## Dados

Fontes (IBGE, SIDRA):
- Tabela 5938: PIB dos Municípios.
- Tabela 6579: Estimativas de População.
- Tabela 5457: Produção Agrícola Municipal.
- Tabela 6784: PIB nominal do Brasil.

A lista de municípios vem da API de Localidades do IBGE. O dicionário completo das variáveis e as instruções de download estão em [`dados/README.md`](dados/README.md).
