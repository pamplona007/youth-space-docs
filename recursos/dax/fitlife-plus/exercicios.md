# FitLife+ — Exercícios de DAX

> Exercícios diseñados para praticar: FILTER + CALCULATE, iteradores, ranking, segmentação, churn e medidas estatísticas.
> Nível: Intermediário. Requer familiaridade com medidas básicas e contexto de filtro.
> Antes de começar: execute o script em `script.md` para gerar os CSVs, carregue-os no Power BI e construa o modelo de dados.

---

## PARTE 1 — Segmentação de Membros

A gerência quer dividir os membros em grupos conforme a idade e a frequência de uso.

### Exercício 1 — Idade do Membro (Coluna Calculada)

Crie uma coluna calculada na tabela **membros** chamada `idade` que calcule a idade do membro a partir da sua data de nascimento. Use a data de hoje (`TODAY()`).

<details>
<summary>Dica</summary>

Use `DATEDIFF(TODAY(), membros[data_nascimento], YEAR)` ou `YEAR(TODAY()) - YEAR(data_nascimento)`.

</details>

---

### Exercício 2 — Faixa Etária (Coluna Calculada)

Na tabela **membros**, crie uma coluna chamada `faixa_etaria` que classifique cada membro:

| Idade | Faixa |
|-------|-------|
| 16–25 | "Jovem" |
| 26–40 | "Adulto" |
| 41–60 | "Meia-Idade" |
| 61+   | "Sênior" |

<details>
<summary>Dica</summary>

Use `SWITCH(TRUE(), [idade] <= 25, "Jovem", [idade] <= 40, "Adulto", ...)`. A estrutura `SWITCH(TRUE(), ...)` permite condições de intervalo.

</details>

---

### Exercício 3 — Check-ins por Membro (Medida)

Crie uma medida `Check-ins por Membro` que divida o total de check-ins pelo número de membros únicos. Esse indicador mostra o quanto a academia está ativa em média.

<details>
<summary>Dica</summary>

Use `DIVIDE(COUNTROWS(checkins), DISTINCTCOUNT(checkins[id_membro]))`. Se quiser filtrar apenas check-ins do tipo "Livre", use `CALCULATE(COUNTROWS(checkins), checkins[tipo] = "Livre")`.

</details>

---

### Exercício 4 — Bandas de Frequência de Uso (Medida com Iterador)

Thiago quer classificar os membros conforme o número de check-ins que fazem por ano. Crie uma medida chamada `Membros por Banda` que conte membros únicos por banda:

| Check-ins/ano | Banda |
|---------------|-------|
| 0 | "Inativo" |
| 1–24 | "Ocasional" |
| 25–99 | "Regular" |
| 100–199 | "Frequente" |
| 200+ | "Fan" |

<details>
<summary>Dica</summary>

Você precisa contar membros únicos e comparar com o intervalo. Uma abordagem usa `SUMX` sobre uma tabela com as bandas definidas, contando membros que atendem cada critério. Outra usa `CALCULATE` com `FILTER` para cada banda. Dica: primeiro crie uma medida auxiliar `checkins_membro` que conte check-ins por membro, depois use-a dentro de um `SWITCH`.

</details>

---

## PARTE 2 — FILTER dentro de CALCULATE

Esta é a parte mais importante do dataset. Aqui você vai praticar `FILTER` como argumento de `CALCULATE` para criar filtros complexos que as interfaces do Power BI não conseguem fazer diretamente.

### Exercício 5 — Receita de um Plano Específico (FILTER simples)

Crie uma medida `Receita Plano VIP` que calcule a receita total de faturas apenas para o plano "VIP" (id_plano = 4). O valor está em `faturas[valor]` quando o pagamento é `"Pago"`.

<details>
<summary>Dica</summary>

```
CALCULATE(
    SUM(faturas[valor]),
    faturas[id_plano] = 4,
    faturas[status_pagamento] = "Pago"
)
```

ou com FILTER explícito:

```
CALCULATE(
    SUM(faturas[valor]),
    FILTER(ALL(faturas), faturas[id_plano] = 4 && faturas[status_pagamento] = "Pago")
)
```

Qual é a diferença? `ALL(faturas)` remove todos os filtros existentes sobre a tabela antes de aplicar o novo. Sem `ALL`, o filtro apenas se soma ao contexto de filtro atual.

</details>

---

### Exercício 6 — Receita Total por Plano (FILTER para atravessar relações)

Crie uma coluna calculada em **planos** chamada `receita_total` que some todas as faturas pagas desse plano. Use `CALCULATE` + `FILTER(ALL(...))`.

<details>
<summary>Dica</summary>

```
receita_total =
CALCULATE(
    SUM(faturas[valor]),
    FILTER(ALL(faturas), faturas[id_plano] = planos[id_plano]),
    faturas[status_pagamento] = "Pago"
)
```

`ALL(faturas)` é necessário porque o CALCULATE normalmente herdaria o filtro da linha atual da tabela `planos`. Sem `ALL`, a expressão teria problemas de referência circular.

</details>

---

### Exercício 7 — Receita por Cidade e Plano (FILTER com múltiplas relações)

Crie uma medida `Receita por Cidade e Plano` que funcione com um visual que tenha **cidade** (de `unidades`) e **nome do plano** (de `planos`) como eixos. A medida deve calcular a receita de faturas pagas para essa combinação.

<details>
<summary>Dica</summary>

Lembre-se de que faturas se conectam a membros, e membros a unidades. Você precisa "atravessar" a relação:

```
Receita por Cidade e Plano =
CALCULATE(
    SUM(faturas[valor]),
    faturas[status_pagamento] = "Pago",
    checkins[id_membro] = membros[id_membro],
    membros[id_unidade_principal] = unidades[id_unidade]
)
```

A relação entre `faturas` e `checkins` é feita através de `membros`. Isso funciona porque o Power BI segue os relacionamentos no modelo.

</details>

---

### Exercício 8 — Membros Premium de uma Unidade Específica

Crie uma medida `Membros Premium na Unidade` que conte membros únicos com plano "Premium" ou "VIP" na unidade atualmente filtrada.

<details>
<summary>Dica</summary>

```
Membros Premium na Unidade =
CALCULATE(
    DISTINCTCOUNT(membros[id_membro]),
    FILTER(
        ALL(planos),
        planos[nome_plano] IN {"Premium", "VIP"}
    )
)
```

`ALL(planos)` remove qualquer filtro existente sobre a tabela de planos para que possamos aplicar nosso próprio filtro por nome do plano, independentemente do que estiver selecionado no visual.

</details>

---

## PARTE 3 — KEEPFILTERS e Comparações

### Exercício 9 — KEEPFILTERS: Check-ins no Horário de Pico

Crie uma medida `Check-ins Horário Pico` que conte os check-ins realizados entre 18:00 e 20:59. O campo é `checkins[horario]` (formato texto "HH:MM").

Primeiro, crie uma coluna calculada em **checkins** chamada `hora_num` que extraia a hora como número:
```
hora_num = VALUE(LEFT(checkins[horario], 2))
```

Depois crie a medida:

<details>
<summary>Dica</summary>

```
Check-ins Horário Pico =
CALCULATE(
    COUNTROWS(checkins),
    KEEPFILTERS(checkins[hora_num] >= 18 && checkins[hora_num] <= 20)
)
```

`KEEPFILTERS` faz com que o filtro se intercepte com o contexto de filtro existente em vez de substituí-lo. Isso é importante quando você quer que o visual continue mostrando o total geral, mas a medida seja filtrada.

</details>

---

### Exercício 10 — Membros que Nunca Fizeram Check-in

Usando `KEEPFILTERS`, crie uma medida `Membros Sem Check-in` que conte membros que **nunca** fizeram um check-in.

<details>
<summary>Dica</summary>

Calcule o total de membros e subtraia os que têm pelo menos um check-in:

```
Membros Sem Check-in =
CALCULATE(
    DISTINCTCOUNT(membros[id_membro]),
    FILTER(ALL(membros), NOT EXISTS(checkins, checkins[id_membro] = membros[id_membro]))
)
```

`NOT EXISTS` verifica se não há nenhum registro na tabela `checkins` correspondente ao membro.

</details>

---

### Exercício 11 — Ticket Médio por Aula (KEEPFILTERS em contexto)

Crie uma medida `Ticket Médio por Aula` que calcule o valor médio de fatura por check-in do tipo "Aula", **sem** que os filtros do visual interfiram no cálculo.

<details>
<summary>Dica</summary>

Você precisa dividir a receita total de faturas pagas (filtrada por tipo de check-in "Aula") pelo número de check-ins tipo "Aula". A relação entre `faturas` e `checkins` é indireta (através de `membros`). A solução usa `KEEPFILTERS` para garantir que o filtro se intercepte corretamente:

```
Ticket Médio por Aula =
DIVIDE(
    CALCULATE(
        SUM(faturas[valor]),
        KEEPFILTERS(checkins[tipo] = "Aula"),
        faturas[status_pagamento] = "Pago"
    ),
    CALCULATE(
        COUNTROWS(checkins),
        KEEPFILTERS(checkins[tipo] = "Aula")
    )
)
```

</details>

---

## PARTE 4 — Funções Iteradoras (SUMX, AVERAGEX, MAXX, MINX)

Os iteradores são funções que avaliam linha a linha e depois agregam o resultado. São fundamentais quando você precisa de um cálculo que depende de múltiplas tabelas ou expressões complexas.

### Exercício 12 — Receita Total com SUMX

Crie uma medida `Receita Total SUMX` que use `SUMX` para calcular a receita total de faturas pagas. Compare com a forma simples `SUM(faturas[valor])` filtrada por `"Pago"`. Qual você prefere e por quê?

<details>
<summary>Dica</summary>

```
Receita Total SUMX =
SUMX(
    FILTER(faturas, faturas[status_pagamento] = "Pago"),
    faturas[valor]
)
```

`SUMX(tabela, expressão)` itera sobre cada linha da tabela (após o filtro) e avalia a expressão, depois soma todos os resultados. Para este caso simples, `SUM` é mais eficiente. SUMX brilha quando a expressão é complexa.

</details>

---

### Exercício 13 — Custo Total de Instrutores por Unidade (SUMX + RELATED)

Crie uma medida `Custo Instrutores` que some o salário de todos os instrutores da unidade filtrada atualmente. A tabela `instrutores` tem `salario`; a relação vai de `instrutores[id_unidade]` a `unidades[id_unidade]`.

<details>
<summary>Dica</summary>

Com a relação já existente, é simples:

```
Custo Instrutores =
SUMX(
    instrutores,
    instrutores[salario]
)
```

Mas se não tivesse a relação, teria que usar `FILTER`:

```
SUMX(
    FILTER(instrutores, instrutores[id_unidade] = unidades[id_unidade]),
    instrutores[salario]
)
```

</details>

---

### Exercício 14 — Ticket Médio por Check-in (SUMX avançado)

Crie uma medida `Ticket Médio Check-in` que divida a receita total de faturas pagas pelo número total de check-ins. Use `SUMX` de forma elegante para mostrar como os iteradores permitem dividir agregações de tabelas diferentes.

<details>
<summary>Dica</summary>

```
Ticket Médio Check-in =
DIVIDE(
    SUMX(
        FILTER(faturas, faturas[status_pagamento] = "Pago"),
        faturas[valor]
    ),
    COUNTROWS(checkins)
)
```

Ou itere sobre os check-ins e obtenha o valor da fatura de cada membro:
```
Ticket Médio Check-in =
SUMX(
    checkins,
    CALCULATE(
        MAX(faturas[valor]),
        faturas[status_pagamento] = "Pago",
        faturas[id_membro] = checkins[id_membro]
    )
) / COUNTROWS(checkins)
```

Esta versão atribui o valor do plano do membro a cada check-in. Muito útil para análise de receita por check-in.

</details>

---

### Exercício 15 — Duração Média das Aulas (AVERAGEX)

Crie uma medida `Duração Média Aulas` que calcule a duração média das aulas oferecidas. A duração está em `modalidades[duracao_min]`.

<details>
<summary>Dica</summary>

```
Duração Média Aulas =
AVERAGEX(
    aulas,
    RELATED(modalidades[duracao_min])
)
```

`RELATED` funciona aqui porque você está iterando sobre `aulas` e pode acessar a tabela relacionada `modalidades`.

</details>

---

### Exercício 16 — Instrutor com Maior Comparecimento (MAXX + TOPN)

Crie uma medida `Melhor Instrutor` que retorne o nome do instrutor com maior comparecimento total em todas as suas aulas. Use-a em um cartão.

<details>
<summary>Dica</summary>

Primeiro crie uma medida auxiliar:
```
Total Comparecimentos Instrutor =
SUMX(
    aulas,
    aulas[compareceu]
)
```

Depois a medida para o cartão:
```
Melhor Instrutor =
MAXX(
    TOPN(1, instrutores, [Total Comparecimentos Instrutor], DESC),
    instrutores[nome_instrutor]
)
```

`TOPN(1, ...)` devolve a primeira linha da tabela de instrutores ordenada pela medida de comparecimentos em ordem decrescente. `MAXX` extrai o nome.

</details>

---

### Exercício 17 — Unidade com Maior e Menor Receita (MINX + MAXX)

Crie duas medidas: `Unidade com Maior Receita` e `Unidade com Menor Receita`. Cada uma deve retornar o nome da unidade com maior/menor receita de faturas pagas.

<details>
<summary>Dica</summary>

```
Unidade com Maior Receita =
MAXX(
    TOPN(1, unidades, [Receita Total], DESC),
    unidades[nome_unidade]
)

Unidade com Menor Receita =
MINX(
    TOPN(1, unidades, [Receita Total], ASC),
    unidades[nome_unidade]
)
```

Isso assume que você já tem uma medida `[Receita Total]` criada que calcula a receita por unidade.

</details>

---

## PARTE 5 — RANKX (Ranking de Instrutores e Modalidades)

### Exercício 18 — Ranking de Instrutores por Comparecimento (Coluna Calculada)

Crie uma coluna calculada em **instrutores** chamada `ranking_comparecimento` que atribua um ranking a cada instrutor conforme seu total de comparecimentos (do maior para o menor). Instrutores com o mesmo número devem ter o mesmo ranking (densidade).

<details>
<summary>Dica</summary>

```
ranking_comparecimento =
RANKX(
    ALL(instrutores),
    SUMX(
        FILTER(aulas, aulas[id_instrutor] = instrutores[id_instrutor]),
        aulas[compareceu]
    ),
    ,
    DESC,
    DENSE
)
```

`ALL(instrutores)` garante que o ranking seja sobre todos os instrutores, não apenas os filtrados. `DENSE` evita lacunas no ranking quando há empates.

</details>

---

### Exercício 19 — Top 3 Modalidades por Inscrições (CONCATENATEX)

Crie uma medida `Top 3 Modalidades` que retorne um texto com os nomes das 3 modalidades mais populares por quantidade de inscritos no ano filtrado.

<details>
<summary>Dica</summary>

```
Top 3 Modalidades =
CONCATENATEX(
    TOPN(
        3,
        ADDCOLUMNS(
            modalidades,
            "total_inscritos", SUMX(
                FILTER(aulas, aulas[id_modalidade] = modalidades[id_modalidade]),
                aulas[inscritos]
            )
        ),
        [total_inscritos],
        DESC
    ),
    [nome_modalidade],
    " → ",
    [total_inscritos],
    DESC
)
```

`ADDCOLUMNS` adiciona uma coluna calculada temporária à tabela `modalidades`. `TOPN` pega as 3 primeiras. `CONCATENATEX` concatena os nomes com " → " como separador.

</details>

---

### Exercício 20 — Ranking de Receita por Unidade (RANKX como medida)

Crie uma medida `Ranking Receita Unidade` que funcione em um visual com nomes de unidades: cada unidade mostra sua posição (1, 2, 3...) conforme sua receita total.

<details>
<summary>Dica</summary>

```
Ranking Receita Unidade =
RANKX(
    ALL(unidades),
    [Receita Total],
    ,
    DESC,
    DENSE
)
```

Por ser uma medida (não coluna), o ranking é recalculado para cada contexto de filtro. Se você filtrar por cidade, o ranking será apenas dentro daquela cidade.

</details>

---

## PARTE 6 — Métricas de Churn e Retenção

Thiago está preocupado com a retenção de membros. Ele quer métricas de churn.

### Exercício 21 — Total de Membros Ativos

Crie uma medida `Membros Ativos` que conte apenas membros com status "Ativo".

<details>
<summary>Dica</summary>

```
Membros Ativos =
CALCULATE(
    DISTINCTCOUNT(membros[id_membro]),
    membros[status] = "Ativo"
)
```

Ou mais direto: `COUNTROWS(FILTER(membros, membros[status] = "Ativo"))`.

</details>

---

### Exercício 22 — Taxa de Churn

Crie uma medida `Taxa de Churn` que calcule o percentual de membros que cancelaram (status = "Cancelado") sobre o total de membros.

<details>
<summary>Dica</summary>

```
Taxa de Churn =
DIVIDE(
    CALCULATE(
        DISTINCTCOUNT(membros[id_membro]),
        membros[status] = "Cancelado"
    ),
    DISTINCTCOUNT(membros[id_membro])
)
```

Exprima o resultado como percentual. Você deve encontrar algo em torno de 5%, que é a meta da FitLife+.

</details>

---

### Exercício 23 — Membros Ativos Há Mais de 12 Meses

Crie uma medida `Membros Veteranos` que conte membros ativos cuja `data_matricula` seja há mais de 12 meses. Isso identifica os membros mais fieis.

<details>
<summary>Dica</summary>

```
Membros Veteranos =
CALCULATE(
    DISTINCTCOUNT(membros[id_membro]),
    membros[status] = "Ativo",
    FILTER(
        ALL(membros),
        DATEDIFF(membros[data_matricula], TODAY(), MONTH) > 12
    )
)
```

</details>

---

### Exercício 24 — Taxa de Churn por Plano

Crie uma medida que calcule a taxa de churn **específica** para o plano filtrado no visual. Deve funcionar como coluna de suporte quando você colocar `planos[nome_plano]` como eixo.

<details>
<summary>Dica</summary>

```
Taxa de Churn por Plano =
DIVIDE(
    CALCULATE(
        DISTINCTCOUNT(membros[id_membro]),
        membros[status] = "Cancelado"
    ),
    DISTINCTCOUNT(membros[id_membro])
)
```

Sem filtros adicionais sobre membros. O contexto de filtro do visual (plano) já filtra a tabela de membros automaticamente. Funciona porque há uma relação entre `membros[id_plano]` e `planos[id_plano]`.

</details>

---

## PARTE 7 — Medidas Estatísticas

### Exercício 25 — Idade Média dos Membros (MEDIAN)

Crie uma medida `Idade Média Membros` que calcule a idade média de todos os membros.

<details>
<summary>Dica</summary>

```
Idade Média Membros =
MEDIAN(membros[idade])
```

Se não tiver a coluna calculada `idade`, calcule inline:
```
MEDIAN(
    DATEDIFF(membros[data_nascimento], TODAY(), YEAR)
)
```

</details>

---

### Exercício 26 — Percentil 25 e 75 da Idade

Crie duas medidas: `P25 Idade` (percentil 25) e `P75 Idade` (percentil 75).

<details>
<summary>Dica</summary>

```
P25 Idade = PERCENTILE.INC(membros[idade], 0.25)
P75 Idade = PERCENTILE.INC(membros[idade], 0.75)
```

`PERCENTILE.INC` inclui os extremos. `PERCENTILE.EXC` os exclui. Em contextos de negócio, `INC` é mais comum.

</details>

---

### Exercício 27 — Desvio Padrão da Receita por Unidade

Crie uma medida `STDEV Receita Unidades` que calcule o desvio padrão da receita entre todas as unidades. Isso mostra se a receita está distribuída de forma uniforme ou se há unidades que se destacam.

<details>
<summary>Dica</summary>

```
STDEV Receita Unidades =
STDEVX.P(
    unidades,
    [Receita Total]
)
```

`STDEVX.P` itera sobre a tabela `unidades`, avalia a medida `[Receita Total]` para cada unidade, e calcula o desvio padrão populacional. `STDEVX.S` calcula o desvio padrão amostral.

</details>

---

### Exercício 28 — Receita Máxima e Mínima por Unidade (MAXX/MINX como medida)

Crie medidas `Máxima Receita Unidade` e `Mínima Receita Unidade` usando `MAXX` e `MINX` diretamente sobre a tabela `unidades`.

<details>
<summary>Dica</summary>

```
Máxima Receita Unidade =
MAXX(
    unidades,
    [Receita Total]
)

Mínima Receita Unidade =
MINX(
    unidades,
    [Receita Total]
)
```

MAXX brilha quando a expressão não é simplesmente uma coluna, mas uma medida ou cálculo complexo.

</details>

---

## PARTE 8 — Variáveis (VAR / RETURN)

As variáveis tornam o DAX mais legível, mais rápido e reutilizável. Usá-las é uma boa prática.

### Exercício 29 — Ticket Médio com VAR

Reescreva a medida `Ticket Médio Check-in` (Exercício 14) usando `VAR` para armazenar o numerador e o denominador em variáveis. Retorne o resultado com `RETURN`.

<details>
<summary>Dica</summary>

```
Ticket Médio Check-in v2 =
VAR Receita = SUMX(FILTER(faturas, faturas[status_pagamento] = "Pago"), faturas[valor])
VAR TotalCheckins = COUNTROWS(checkins)
RETURN
DIVIDE(Receita, TotalCheckins)
```

As variáveis são avaliadas uma única vez, o que torna o código mais eficiente quando a expressão é usada várias vezes.

</details>

---

### Exercício 30 — Classificação Dinâmica de Crescimento (VAR + SWITCH)

Crie uma medida `Classificação Crescimento` que classifique o crescimento da receita YoY (ano contra ano) em:
- `+15%+` → "Excelente"
- `+5% a +15%` → "Bom"
- `0% a +5%` → "Estável"
- `-10% a 0%` → "Atenção"
- `< -10%` → "Crítico"

Use `VAR` para armazenar o valor de crescimento antes do `SWITCH`.

<details>
<summary>Dica</summary>

Assuma que você tem as medidas `[Receita Total]` e `[Receita Ano Anterior]`. O crescimento se calcula como:
```
Crescimento % = DIVIDE([Receita Total] - [Receita Ano Anterior], [Receita Ano Anterior])
```

Depois:
```
Classificação Crescimento =
VAR Crec = [Crescimento %]
RETURN
SWITCH(
    TRUE(),
    Crec > 0.15, "Excelente",
    Crec > 0.05, "Bom",
    Crec > 0,    "Estável",
    Crec > -0.10, "Atenção",
    TRUE,        "Crítico"
)
```

O uso de `VAR Crec` antes do `SWITCH` evita calcular o crescimento múltiplas vezes.

</details>

---

## PARTE 9 — Desafio Final: Dashboard de Retenção FitLife+

### Exercício 31 — Construa o Dashboard

Com tudo o que aprendeu, construa um dashboard de 5 páginas para Thiago:

**Página 1 — Resumo Executivo:**
- Cartões: Membros Ativos, Receita Total, Taxa de Churn, Melhor Instrutor, Receita Média por Unidade
- Cada cartão com formatação condicional: verde se está bem, amarelo se precisa de atenção, vermelho se é crítico

**Página 2 — Receita por Unidade:**
- Gráfico de barras: receita por unidade
- Gráfico de linhas: evolução mensal da receita (últimos 12 meses)
- Tabela: ranking de unidades por receita com a coluna de ranking calculada
- Cartão: unidade com maior/menor receita

**Página 3 — Análise de Membros:**
- Gráfico de donut: distribuição por faixa_etaria
- Gráfico de barras: Membros por Banda (0, 1–24, 25–99, 100–199, 200+ check-ins/ano)
- Tabela: taxa de churn por plano (cada plano como linha)
- Filtro de cidade no painel de filtros

**Página 4 — Performance de Instrutores:**
- Tabela: nome, especialidade, unidade, total comparecimentos, nota média, ranking
- Gráfico: comparecimentos por horário do dia (para encontrar o melhor horário)
- Top 3 modalidades como KPI de texto

**Página 5 — Alertas de Churn:**
- Tabela de membros "em risco": membros da unidade filtrada, com plano, última data de check-in, dias sem frequentar
- Para "dias sem frequentar" você precisará de uma coluna calculada em `membros` que busque a última data de check-in do membro
- Medida `Nível de Risco` que atribua "Alto", "Médio", "Baixo" conforme os dias sem frequentar

<details>
<summary>Dicas progressivas para a página 5</summary>

**Coluna calculada em membros — Último check-in:**
```
ultimo_checkin =
CALCULATETABLE(
    MAX(checkins[data_checkin]),
    checkins[id_membro] = membros[id_membro]
)
```

**Coluna — Dias sem frequentar:**
```
dias_sem_frequentar =
DATEDIFF(
    CALCULATE(
        MAX(checkins[data_checkin]),
        checkins[id_membro] = membros[id_membro]
    ),
    TODAY(),
    DAY
)
```

**Medida — Nível de Risco:**
```
Nível de Risco =
VAR dias = [dias_sem_frequentar]
RETURN
SWITCH(
    TRUE(),
    dias > 90, "🔴 Alto Risco",
    dias > 45, "🟡 Risco Médio",
    dias > 15, "🟢 Em Observação",
    TRUE,     "✅ Ativo"
)
```

</details>

---

## Dicas para Completar os Exercícios

1. **Salve frequentemente** — o Power BI pode ficar sem memória com muitos CSVs grandes
2. **Oculte as colunas FK** — no modelo, oculte as colunas `id_*` nas tabelas de fatos para que você veja apenas os campos úteis no painel de campos
3. **Comece pelo modelo** — antes de escrever DAX, verifique se todas as relações estão corretas
4. **Use `SELECTEDVALUE` para títulos dinâmicos** — por exemplo, `SELECTEDVALUE(unidades[nome_unidade], "Todas")`
5. **Experimente com `ALL`** — observe como `ALL(tabela)` muda o comportamento das suas medidas

---

*Exercícios criados para FitLife+ — Thiago, Gerente de Operações.*
