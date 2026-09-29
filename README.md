# Dados ANAC - Databricks

Pipeline de dados (Bronze → Silver) para análise de pontualidade da malha aérea brasileira, unificando dados de **voos (VRA)**, **aeródromos** e **empresas aéreas** da ANAC.

### Desenho da solução

<img width="902" height="282" alt="Diagrama sem nome drawio" src="https://github.com/user-attachments/assets/d36ac275-091e-4e69-b286-0356f691e4d2" />

---

## 📊 Visão Geral

- **Objetivo**: Analisar a pontualidade e o comportamento operacional dos voos regulares no Brasil, cruzando dados de voos com informações de aeródromos e empresas aéreas, para identificar padrões de atraso por rota, companhia, período e tipo de linha.
- **Dataset**: ANAC — VRA (Voo Regular Ativo), Aeródromos, Empresas
- **Período**: 2026/09 ->
- **Tecnologias**: Databricks, SQL, PySpark, Delta Lake

## 🎯 Perguntas de Pesquisa

- [ ] Quais empresas aéreas apresentam maior índice de pontualidade?
- [ ] Quais rotas/aeródromos concentram os maiores atrasos médios?
- [ ] Existe sazonalidade (mês, dia da semana, período do dia) que explica variações de atraso?
- [ ] Quais justificativas (`cd_justificativa`) mais impactam atrasos e cancelamentos?
- [ ] Aeródromos de maior porte/movimento têm melhor ou pior pontualidade que os regionais?

## 📁 Dataset

### Fonte dos Dados
- **Link**: [Dados Abertos VRA - ANAC](https://www.gov.br/anac/pt-br/assuntos/dados-e-estatisticas/dados-e-estatisticas)
- **Tamanho**: A definir após carga completa (registros na bronze `projetos.anac_bronze.vra`)
- **Período**: A partir de 2003/01
- **Descrição**: Três fontes complementares:
  - **VRA**: registros de voos regulares (previsto x realizado, situação, justificativa)
  - **Aeródromos**: dimensão com código ICAO, localização (lat/long), UF e região
  - **Empresas**: dimensão com código ICAO da empresa, razão social e nacionalidade

### Variáveis Principais

| Tabela | Variáveis-chave |
|---|---|
| **VRA (Silver)** | `pk_vra`, `icao_empresa`, `numero_voo`, `icao_origem`, `icao_destino`, `hr_partida_prevista/real`, `hr_chegada_prevista/real`, `diff_partida_minutos`, `diff_chegada_minutos`, `categoria_atraso_partida`, `flag_atraso_15min`, `situacao_voo`, `cd_justificativa` |
| **Aeródromos** | `icao`, `nome_aerodromo`, `uf`, `regiao`, `latitude`, `longitude` |
| **Empresas** | `icao_empresa`, `razao_social`, `nacionalidade` |

## 🏗️ Estrutura do Projeto

```
├── bronze/
│   └── anac_bronze.vra           
│   └── anac_bronze.aerodomos
│   └── anac_bronze.empresas               
├── silver/
│   ├── anac_silver.vra            
│   ├── anac_silver.aerodromos     
│   └── anac_silver.empresas       
├── sql/
│   └── WIP
├── docs/
└── README.md
```

## 📊 Principais Resultados
_A preencher após primeira carga e análise exploratória._

## 📈 Visualizações

Dashboard planejado em 6 páginas — detalhes em [`especificacao_dashboard_vra.md`](./docs/especificacao_dashboard_vra.md):
1. Visão Geral (KPIs de pontualidade)
2. Ranking por Empresa Aérea
3. Rotas e Aeródromos (mapas, uma vez a dimensão de aeródromos incorporada)
4. Padrões Temporais
5. Justificativas e Situação Operacional
6. Detalhe / Consulta de Voo

## 🛠️ Tecnologias Utilizadas

- **Databricks** (Spark SQL / PySpark) — processamento e orquestração
- **Delta Lake** — armazenamento transacional, versionamento e CDC
- **SQL** — transformações, enriquecimento e MERGE incremental

## ⚙️ Configurações Importantes

- Chave primária da tabela de voos: `pk_vra` (empresa + número do voo + timestamp de chegada + origem + destino)
- Carga incremental via `MERGE INTO` (upsert por `pk_vra`), idempotente
- Padrão de pontualidade ANAC: tolerância de 15 minutos
- Tabela Silver particionada por `ano`/`mes`
- `tblproperties` com `enableChangeDataFeed` habilitado para consumo incremental downstream

## 🤝 Contribuindo

Sugestões de enriquecimento e novas análises são bem-vindas — abrir issue ou PR descrevendo a pergunta de negócio a ser respondida.

---
**Última atualização:** Setembro 2026
