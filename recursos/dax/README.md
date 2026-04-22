# DAX — Exercícios

Exercícios práticos de DAX para Power BI.

---

## Datasets Disponíveis

### 1. VendaMais Distribuidora

**Tema:** Distribuição de produtos (vestuário, calçados, eletrônicos)
**Nível:** Básico a Intermediário
**Foco:** Colunas calculadas, medidas DAX, inteligência temporal

**Arquivos em `vendas-mais/`:**

| Arquivo | Descrição |
|---------|-----------|
| [vendas-mais/info.md](vendas-mais/info.md) | Contexto da empresa e estrutura do banco de dados |
| [vendas-mais/script.md](vendas-mais/script.md) | Script Python para gerar os CSVs do dataset |
| [vendas-mais/exercicios.md](vendas-mais/exercicios.md) | Exercícios básicos — colunas calculadas e medidas DAX |
| [vendas-mais/resolucoes.md](vendas-mais/resolucoes.md) | Resoluções dos exercícios básicos |
| [vendas-mais/exercicios-time-intelligence.md](vendas-mais/exercicios-time-intelligence.md) | Exercícios avançados — Inteligência de Tempo (21 exercícios) |
| [vendas-mais/resolucoes-time-intelligence.md](vendas-mais/resolucoes-time-intelligence.md) | Resoluções dos exercícios de Inteligência de Tempo |

**Sequência recomendada para VendaMais:**
1. Leia o contexto em `vendas-mais/info.md`
2. Gere os dados com `vendas-mais/script.md`
3. Faça os exercícios em `vendas-mais/exercicios.md` (básico)
4. Confira as resoluções em `vendas-mais/resolucoes.md`
5. Avance para `vendas-mais/exercicios-time-intelligence.md` (avançado)
6. Confira as resoluções em `vendas-mais/resolucoes-time-intelligence.md`

---

### 2. PlayMax — Limpeza de Dados

**Tema:** Streaming de games (plataforma tipo Twitch brasileira)
**Nível:** Intermediário
**Foco:** Limpeza de dados, tratamento de valores inválidos, deduplicação

**Arquivos em `playmax/`:**

| Arquivo | Descrição |
|---------|-----------|
| [playmax/info.md](playmax/info.md) | Contexto da empresa e tipos de problemas de dados |
| [playmax/script.md](playmax/script.md) | Script Python para gerar CSVs com problemas propositais |
| [playmax/exercicios.md](playmax/exercicios.md) | Exercícios de limpeza (28 exercícios) |
| [playmax/resolucoes.md](playmax/resolucoes.md) | Resoluções com código M (Power Query) e DAX |

**Problemas incluídos no dataset:**
- Duplicatas em transações
- Valores nulos ("NA", "null", "-", vazios)
- Inconsistência de case ("RPG" vs "rpg" vs "RPG ")
- IDs órfãos (referências inválidas)
- Datas fora do período
- Outliers (valores impossíveis)
- Espaços extras em textos
- Emojis em categorias

**Sequência recomendada para PlayMax:**
1. Leia o contexto em `playmax/info.md`
2. Gere os dados com `playmax/script.md`
3. Abra os CSVs no Power BI (Power Query)
4. Siga os exercícios em `playmax/exercicios.md`
5. Confira as resoluções em `playmax/resolucoes.md`

---

### 3. FitLife+ — Academia (Fitness)

**Tema:** Rede de academias no Nordeste brasileiro
**Nível:** Intermediário
**Foco:** FILTER + CALCULATE, iteradores (SUMX, AVERAGEX, MAXX, MINX), ranking (RANKX), segmentação (IF/SWITCH), churn e métricas estatísticas (MEDIAN, PERCENTILE, STDEV), variáveis (VAR/RETURN)

**Arquivos em `fitlife-plus/`:**

| Arquivo | Descrição |
|---------|-----------|
| [fitlife-plus/info.md](fitlife-plus/info.md) | Contexto da empresa e estrutura do banco de dados |
| [fitlife-plus/script.md](fitlife-plus/script.md) | Script Python para gerar os CSVs do dataset |
| [fitlife-plus/exercicios.md](fitlife-plus/exercicios.md) | Exercícios práticos (31 exercícios em 9 partes) |
| [fitlife-plus/resolucoes.md](fitlife-plus/resolucoes.md) | Resoluções detalhadas com explicações |

**Tópicos abordados:**
- Faixas etárias e bandas de frequência de uso
- FILTER dentro de CALCULATE com múltiplas condições
- KEEPFILTERS para controle de contexto de filtro
- Iteradores: SUMX, AVERAGEX, MAXX, MINX, CONCATENATEX
- Ranking de instrutores e modalidades com RANKX e TOPN
- Métricas de churn: taxa de cancelamento, membros ativos, veteranos
- Medidas estatísticas: MEDIAN, PERCENTILE.INC, STDEVX.P
- Variáveis VAR/RETURN para DAX legível e eficiente
- Dashboard completo de retenção com alertas de churn

**Sequência recomendada para FitLife+:**
1. Leia o contexto em `fitlife-plus/info.md`
2. Gere os dados com `fitlife-plus/script.md`
3. Carregue os CSVs no Power BI e construa o modelo de dados
4. Crie a tabela `calendario` como dimensão de data
5. Siga os exercícios em `fitlife-plus/exercicios.md`
6. Confira as resoluções em `fitlife-plus/resolucoes.md`
