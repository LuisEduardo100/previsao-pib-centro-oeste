# Previsão do PIB dos municípios do Centro-Oeste (2024)

Trabalho 2 de **T326 Ciência de Dados** (Universidade de Fortaleza, Prof. Rilder de Sousa Pires). Modelos de aprendizado de máquina para prever o PIB de 2024 dos municípios do Centro-Oeste (GO, MT, MS e DF) e classificar o crescimento econômico de cada um em cinco categorias.

**Equipe:** Walter Gomes e Luis Albuquerque

[![Abrir no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/LuisEduardo100/previsao-pib-centro-oeste/blob/main/notebooks/trabalho2_previsao_pib.ipynb)

## Resultados

Avaliação em 2023: modelos treinados com dados até 2022 e comparados ao PIB oficial de 2023 dos 467 municípios com histórico.

| Abordagem | Modelo escolhido | Desempenho em 2023 |
|---|---|---|
| Regressão (valor do PIB) | Random Forest | R² 0,9997 · MAPE 7,7% (repetir o PIB de 2022 dá 12,1%) |
| Classificação (categoria de crescimento) | Categoria derivada da regressão | F1 macro 0,377 · acurácia 60% |

Previsão para 2024: PIB do Centro-Oeste de R$ 1.229 bilhões (+6,0% sobre 2023). Por categoria: 226 municípios em forte crescimento, 61 em crescimento moderado, 29 em estabilidade, 61 em retração e 90 em forte retração.

Metodologia, comparação entre modelos e discussão estão no [relatório técnico](relatorio/relatorio_tecnico.pdf) e no notebook.

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
├── relatorio/
│   └── relatorio_tecnico.pdf                relatório técnico
├── requirements.txt                         bibliotecas Python
└── README.md
```

## Como executar

**Google Colab (recomendado).** Abra pelo botão acima e use *Ambiente de execução > Executar tudo*. As bibliotecas já vêm instaladas. O notebook baixa os dados brutos do Google Drive da equipe (`gdown`) e o PIB nominal do Brasil da API do SIDRA, e grava os CSVs gerados em `/content`. A execução leva cerca de 5 minutos.

**Local.** Requer Python 3.10 ou mais recente.

```bash
git clone https://github.com/LuisEduardo100/previsao-pib-centro-oeste.git
cd previsao-pib-centro-oeste
python -m venv .venv
source .venv/bin/activate          # no Windows: .venv\Scripts\activate
pip install -r requirements.txt jupyter
jupyter notebook notebooks/trabalho2_previsao_pib.ipynb
```

Depois, *Run > Run All Cells*. Os CSVs são gravados na pasta `notebooks/`.

A célula 0 (comentada) refaz a coleta direto do SIDRA com `sidrapy`. Não é preciso rodá-la: os CSVs coletados já estão no Drive da equipe e são baixados pela célula 1.

## Dados

Fontes (IBGE, SIDRA):
- Tabela 5938: PIB dos Municípios.
- Tabela 6579: Estimativas de População.
- Tabela 5457: Produção Agrícola Municipal.
- Tabela 6784: PIB nominal do Brasil.

A lista de municípios vem da API de Localidades do IBGE. O dicionário completo das variáveis e as instruções de download estão em [`dados/README.md`](dados/README.md).
