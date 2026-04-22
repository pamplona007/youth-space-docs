# FitLife+ — Analytics de Academia

## Contexto da Empresa

**FitLife+** é uma rede de academias que atua em cinco cidades do Nordeste brasileiro. Fundada em 2018, a rede cresceu até 8 unidades e atende mais de 3.000 membros ativos. A empresa oferece acesso à academia, aulas em grupo (spinning, funcional, yoga, CrossFit, HIIT), sessões com personal trainer e serviço de orientação nutricional.

A equipe de gestão é liderada por **Thiago**, o gerente de operações, que usa o Power BI para monitorar o desempenho das unidades, o engajamento dos membros, a popularidade das aulas e a produtividade dos instrutores. Thiago é um usuário de Power BI de nível intermediário — já sabe criar medidas básicas em DAX, mas quer aprofundar seus conhecimentos em filtragem, segmentação e ranking.

---

## Esquema do Banco de Dados

O banco de dados SQL Server da FitLife+ segue o seguinte esquema estrela:

### Tabelas Dimensão

#### `unidades` — Unidades da Academia
| Coluna | Descrição | Exemplo |
|--------|-----------|---------|
| `id_unidade` | Chave primária | 1, 2, 3... |
| `nome_unidade` | Nome da unidade | "FitLife+ Norte", "FitLife+ Centro" |
| `cidade` | Cidade | "Natal", "Recife", "Fortaleza", "Salvador", "João Pessoa" |
| `data_inauguracao` | Data de abertura | 2018-01-15 |
| `area_m2` | Área em metros quadrados | 450, 800, 1200 |
| `equipamentos` | Número de equipamentos | 40, 60, 85, 120 |

#### `planos` — Planos de Assinatura
| Coluna | Descrição | Exemplo |
|--------|-----------|---------|
| `id_plano` | Chave primária | 1, 2, 3, 4, 5 |
| `nome_plano` | Nome do plano | "Básico", "Plus", "Premium", "VIP", "Corporativo" |
| `valor_mensal` | Mensalidade (R$) | 79.90, 129.90, 189.90, 299.90, 249.90 |
| `descricao` | Descrição | "Academia + aulas coletivas", "Tudo incluso + personal" |
| `tipo` | Categoria | "Acesso", "Corporativo" |

#### `modalidades` — Tipos de Aula
| Coluna | Descrição | Exemplo |
|--------|-----------|---------|
| `id_modalidade` | Chave primária | 1, 2, 3, 4, 5, 6 |
| `nome_modalidade` | Nome da aula | "Musculação", "Spinning", "Funcional", "Yoga", "CrossFit", "HIIT" |
| `descricao` | Descrição | Descrição livre |
| `capacidade` | Vagas da sala | 15, 20, 25, 30 |
| `duracao_min` | Duração em minutos | 45, 50, 60 |
| `nivel` | Nível de dificuldade | "Iniciante", "Intermediário", "Avançado", "Todos" |

#### `instrutores` — Instrutores
| Coluna | Descrição | Exemplo |
|--------|-----------|---------|
| `id_instrutor` | Chave primária | 1, 2, 3... (30 instrutores) |
| `nome_instrutor` | Nome completo | Nome completo |
| `id_unidade` | Unidade vinculada | FK → unidades |
| `especialidade` | Especialidade | "Musculação", "Spinning", "Funcional", "Yoga", "CrossFit", "HIIT", "Geral" |
| `data_contratacao` | Data de admissão | 2018-03-01, 2020-06-15... |
| `salario` | Salário mensal (R$) | 2500–6000 |
| `avaliacao_media` | Nota média (1–5) | 3.5, 4.0, 4.5, 5.0 |

#### `membros` — Membros
| Coluna | Descrição | Exemplo |
|--------|-----------|---------|
| `id_membro` | Chave primária | 1, 2, 3... |
| `nome_membro` | Nome completo | Nome completo |
| `data_nascimento` | Data de nascimento | 1990-05-12, 2002-08-23... |
| `sexo` | Gênero | "M", "F", "Outro" |
| `cidade` | Cidade de residência | Uma das 5 cidades |
| `id_plano` | Plano atual | FK → planos |
| `id_unidade_principal` | Unidade principal | FK → unidades |
| `data_matricula` | Data de matrícula | 2019-01-15, 2023-06-01... |
| `status` | Status da assinatura | "Ativo", "Inativo", "Suspenso", "Cancelado" |
| `data_cancelamento` | Data de cancelamento | NULL se ativo |

### Tabelas Fato

#### `checkins` — Registros de Entrada
| Coluna | Descrição | Exemplo |
|--------|-----------|---------|
| `id_checkin` | Chave primária | 1, 2, 3... (~45.000 registros) |
| `id_membro` | Membro | FK → membros |
| `id_unidade` | Unidade visitada | FK → unidades |
| `data_checkin` | Data da entrada | 2024-01-01 a 2025-12-31 |
| `horario` | Horário | 06:00, 14:30, 20:45... |
| `tipo` | Tipo de visita | "Livre" (academia), "Aula" (aula em grupo) |

#### `aulas` — Aulas em Grupo
| Coluna | Descrição | Exemplo |
|--------|-----------|---------|
| `id_aula` | Chave primária | 1, 2, 3... (~6.500 registros) |
| `id_modalidade` | Tipo de aula | FK → modalidades |
| `id_instrutor` | Instrutor responsável | FK → instrutores |
| `id_unidade` | Unidade | FK → unidades |
| `data_aula` | Data da aula | 2024-01-01 a 2025-12-31 |
| `horario` | Horário de início | 06:00, 07:00, 08:00... |
| `vagas` | Total de vagas | 15, 20, 25, 30 |
| `inscritos` | Matriculados | 5 a capacidade |
| `compareceu` | Presenças confirmadas | 0 a inscritos |

#### `faturas` — Faturas Mensais
| Coluna | Descrição | Exemplo |
|--------|-----------|---------|
| `id_fatura` | Chave primária | 1, 2, 3... (~8.000 registros) |
| `id_membro` | Membro cobrado | FK → membros |
| `id_plano` | Plano na época da cobrança | FK → planos |
| `data_vencimento` | Data de vencimento | Datas mensais |
| `data_pagamento` | Data do pagamento | NULL se não pago |
| `valor` | Valor da fatura (R$) | 79.90, 129.90... |
| `status_pagamento` | Status do pagamento | "Pago", "Pendente", "Atrasado", "Cancelado" |

#### `calendario` — Dimensão Data
| Coluna | Descrição |
|--------|-----------|
| `data` | Chave de data (2024-01-01 a 2025-12-31) |
| Calculadas no Power BI | Ano, Mês, Nome do Mês, Trimestre, Dia, Dia da Semana, Semana do Ano, Fim de Semana, Feriado |

---

## Características dos Dados

- **Membros:** 3.000 no total; ~2.200 ativos, ~400 inativos, ~250 suspensos, ~150 cancelados
- **Unidades:** 5 cidades × 8 unidades; algumas cidades têm 2 unidades
- **Planos:** 5 níveis; "Básico" é o mais comum (~40%), "VIP" o menos comum (~5%)
- **Modalidades:** 6 tipos; Musculação é sempre oferecida; HIIT é a menos frequente
- **Check-ins:** ~45.000 registros em 2 anos; horário de pico: 7–9h e 18–20h
- **Faturas:** ~8.000 registros; ~90% pagas em dia, ~7% atrasadas, ~3% em aberto
- **Instrutores:** 30 profissionais nas 8 unidades; a maioria tem especialidade em uma modalidade
- **Idade dos membros:** 16 a 72 anos
- **Período de matrículas:** 2018 a 2025

---

## Qualidade dos Dados

Os dados são **limpos** — este dataset foca em habilidades de **DAX**, não em limpeza de dados. Não há IDs inválidos, duplicatas, valores nulos em campos-chave nem datas fora do intervalo esperado. Thiago quer focar inteiramente na escrita de fórmulas DAX.

---

## O Que Este Dataset Ensina

Após completar VendaMais (vendas) e PlayMax (limpeza de dados), o FitLife+ introduz:

1. **FILTER dentro de CALCULATE** — filtragem com múltiplas condições
2. **KEEPFILTERS** — controle de contexto de filtro em comparações
3. **Funções iteradoras (SUMX, AVERAGEX, MAXX, MINX, CONCATENATEX)** — avaliação linha a linha
4. **Ranking (RANKX)** — classificação de instrutores e modalidades
5. **Segmentação (IF/SWITCH)** — faixas etárias, bandas de frequência
6. **Métricas de churn e retenção** — transições de status
7. **MEDIAN, PERCENTILE, STDEV** — medidas estatísticas
8. **Variáveis (VAR/RETURN)** — DAX legível e reutilizável

---

## Como Usar Este Dataset

1. Leia `info.md` (este arquivo) para entender o contexto do negócio
2. Leia `script.md` para gerar os arquivos CSV com os dados
3. Abra todos os CSVs no Power BI e construa o modelo de dados (ligue os fatos às dimensões)
4. Crie a tabela `calendario` como dimensão de data
5. Siga os exercícios do arquivo `exercicios.md`
6. Compare suas soluções com `resolucoes.md`

**Sequência recomendada:** Info → Script → Exercícios → Resoluções
