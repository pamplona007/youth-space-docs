# FitLife+ — Resoluções dos Exercícios de DAX

> As soluções apresentadas aqui são uma forma possível de resolver cada exercício.
> Pode haver múltiplas abordagens corretas — o importante é entender o raciocínio por trás de cada fórmula.

---

## PARTE 1 — Segmentação de Membros

### Exercício 1 — Idade do Membro

```dax
Idade =
DATEDIFF(membros[data_nascimento], TODAY(), YEAR)
```

Ou, de forma equivalente:

```dax
Idade =
YEAR(TODAY()) - YEAR(membros[data_nascimento])
```

**Explicação:** `DATEDIFF` calcula a diferença em anos entre a data de nascimento e hoje. `TODAY()` retorna a data atual e é recalculada automaticamente cada vez que o relatório é atualizado.

---

### Exercício 2 — Faixa Etária

```dax
Faixa Etária =
SWITCH(
    TRUE(),
    membros[Idade] <= 25, "Jovem",
    membros[Idade] <= 40, "Adulto",
    membros[Idade] <= 60, "Meia-Idade",
    "Sênior"
)
```

**Explicação:** `SWITCH(TRUE(), ...)` avalia cada condição em ordem. Assim que uma condição for verdadeira, retorna o valor correspondente. A última condição (`"Sênior"`) não precisa de comparação porque qualquer idade acima de 60 já terá passado pelas condições anteriores.

---

### Exercício 3 — Check-ins por Membro

```dax
Check-ins por Membro =
DIVIDE(
    COUNTROWS(checkins),
    DISTINCTCOUNT(checkins[id_membro])
)
```

**Explicação:** `COUNTROWS(checkins)` conta todas as linhas de check-in. `DISTINCTCOUNT` conta quantos membros únicos aparecem na tabela de check-ins (não na de membros — membros que nunca fizeram check-in não são contados aqui). `DIVIDE` evita erro de divisão por zero.

Para contar apenas check-ins do tipo "Livre":

```dax
Check-ins por Membro (Livre) =
DIVIDE(
    CALCULATE(COUNTROWS(checkins), checkins[tipo] = "Livre"),
    DISTINCTCOUNT(checkins[id_membro])
)
```

---

### Exercício 4 — Bandas de Frequência de Uso

Primeiro, crie uma medida auxiliar que conta check-ins por membro:

```dax
Check-ins por Membro (medida) =
COUNTROWS(checkins)
```

Depois use-a dentro de um SWITCH com bandas:

```dax
Membros por Banda =
VAR Banda0  = COUNTROWS(FILTER(ALL(membros), [Check-ins por Membro (medida)] = 0))
VAR Banda1  = COUNTROWS(FILTER(ALL(membros), [Check-ins por Membro (medida)] >= 1 && [Check-ins por Membro (medida)] <= 24))
VAR Banda25 = COUNTROWS(FILTER(ALL(membros), [Check-ins por Membro (medida)] >= 25 && [Check-ins por Membro (medida)] <= 99))
VAR Banda100 = COUNTROWS(FILTER(ALL(membros), [Check-ins por Membro (medida)] >= 100 && [Check-ins por Membro (medida)] <= 199))
VAR Banda200 = COUNTROWS(FILTER(ALL(membros), [Check-ins por Membro (medida)] >= 200))
RETURN
SWITCH(
    TRUE(),
    SELECTEDVALUE(bandas[banda], "Todas") = "Inativo",    Banda0,
    SELECTEDVALUE(bandas[banda], "Todas") = "Ocasional",  Banda1,
    SELECTEDVALUE(bandas[banda], "Todas") = "Regular",    Banda25,
    SELECTEDVALUE(bandas[banda], "Todas") = "Frequente",  Banda100,
    SELECTEDVALUE(bandas[banda], "Todas") = "Fan",        Banda200,
    Banda0 + Banda1 + Banda25 + Banda100 + Banda200
)
```

**Nota:** Esta solução exige uma tabela auxiliar `bandas` com uma coluna `banda` contendo os valores: "Inativo", "Ocasional", "Regular", "Frequente", "Fan". Sem a tabela auxiliar, a solução fica mais simples — use a medida `Check-ins por Membro (medida)` diretamente em um visual de tabela com a coluna `faixa_etaria` de membros.

---

## PARTE 2 — FILTER dentro de CALCULATE

### Exercício 5 — Receita do Plano VIP

Solução simples (filtros como argumentos de CALCULATE):

```dax
Receita Plano VIP =
CALCULATE(
    SUM(faturas[valor]),
    faturas[id_plano] = 4,
    faturas[status_pagamento] = "Pago"
)
```

Solução com FILTER explícito:

```dax
Receita Plano VIP (com FILTER) =
CALCULATE(
    SUM(faturas[valor]),
    FILTER(ALL(faturas),
        faturas[id_plano] = 4 &&
        faturas[status_pagamento] = "Pago"
    )
)
```

**Diferença-chave:** `ALL(faturas)` remove todos os filtros existentes sobre a tabela `faturas` antes de aplicar o filtro. Na prática, para filtrar por uma coluna, ambos os formatos funcionam — mas `ALL` é necessário quando você quer garantir que nenhum filtro externo interfira.

---

### Exercício 6 — Receita Total por Plano (Coluna Calculada)

```dax
Receita Total do Plano =
CALCULATE(
    SUM(faturas[valor]),
    FILTER(ALL(faturas), faturas[id_plano] = planos[id_plano]),
    faturas[status_pagamento] = "Pago"
)
```

**Por que `ALL(faturas)` é necessário?** Sem ele, o CALCULATE tenta filtrar `faturas` pela coluna `id_plano`, mas ao mesmo tempo está sendo avaliado dentro da tabela `planos`. Isso gera uma ambiguidade — `faturas[id_plano]` referencia a si mesma. `ALL(faturas)` quebra esse contexto circular e permite comparar com a linha atual de `planos`.

---

### Exercício 7 — Receita por Cidade e Plano

```dax
Receita por Cidade e Plano =
CALCULATE(
    SUM(faturas[valor]),
    faturas[status_pagamento] = "Pago",
    checkins[id_membro] = membros[id_membro],
    membros[id_unidade_principal] = unidades[id_unidade]
)
```

**Explicação:** Como `faturas` não tem relação direta com `unidades` ou `cidades`, precisamos atravessar as relações através de `membros`. O Power BI segue automaticamente os caminhos de relacionamento quando você referencia colunas de outras tabelas dentro de `CALCULATE`. Isso é conhecido como **propagação de filtro**.

---

### Exercício 8 — Membros Premium na Unidade

```dax
Membros Premium na Unidade =
CALCULATE(
    DISTINCTCOUNT(membros[id_membro]),
    FILTER(
        ALL(planos),
        planos[nome_plano] IN {"Premium", "VIP"}
    )
)
```

**Explicação:** `ALL(planos)` remove qualquer filtro que venha do visual (por exemplo, um filtro de cidade que também filtre planos). Depois, `FILTER` aplica apenas os planos "Premium" e "VIP". O resultado é a contagem de membros que têm um desses dois planos, respeitando qualquer outro filtro do visual (como cidade ou status).

---

## PARTE 3 — KEEPFILTERS e Comparações

### Exercício 9 — Check-ins no Horário de Pico

Coluna calculada em **checkins**:

```dax
Hora Num =
VALUE(LEFT(checkins[horario], 2))
```

Medida:

```dax
Check-ins Horário Pico =
CALCULATE(
    COUNTROWS(checkins),
    KEEPFILTERS(checkins[Hora Num] >= 18 && checkins[Hora Num] <= 20)
)
```

**Por que `KEEPFILTERS`?** Sem ele, o filtro `>= 18 && <= 20` **substituiria** qualquer outro filtro existente sobre `hora_num` no visual. Com `KEEPFILTERS`, o filtro se **intercepeta** (interseção) com o contexto existente. Isso é útil quando você quer que a medida seja filtrada mas o visual continue mostrando todos os valores.

---

### Exercício 10 — Membros Sem Check-in

```dax
Membros Sem Check-in =
CALCULATE(
    DISTINCTCOUNT(membros[id_membro]),
    FILTER(
        ALL(membros),
        NOT EXISTS(checkins, checkins[id_membro] = membros[id_membro])
    )
)
```

**Explicação:** `ALL(membros)` garante que a contagem seja sobre todos os membros, não apenas os filtrados. `NOT EXISTS` verifica se não há nenhuma linha em `checkins` com o mesmo `id_membro`. Membros que nunca fizeram check-in não aparecem em `checkins`, então o `EXISTS` retorna falso para eles.

---

### Exercício 11 — Ticket Médio por Aula

```dax
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

**Nota sobre o modelo:** Esta medida funciona porque existe uma relação entre `checkins[id_membro]` e `membros[id_membro]`, e entre `membros[id_plano]` e `faturas[id_plano]`. O Power BI consegue "percorrer" esse caminho automaticamente.

---

## PARTE 4 — Funções Iteradoras

### Exercício 12 — Receita Total com SUMX

```dax
Receita Total SUMX =
SUMX(
    FILTER(faturas, faturas[status_pagamento] = "Pago"),
    faturas[valor]
)
```

**Comparação:** Para este caso simples, `SUM(faturas[valor])` com filtro direto em CALCULATE é mais eficiente e legível. `SUMX` brilha quando a expressão dentro do segundo argumento é complexa — por exemplo, quando você precisa calcular algo linha a linha antes de somar.

```dax
Receita Total (recomendada) =
CALCULATE(
    SUM(faturas[valor]),
    faturas[status_pagamento] = "Pago"
)
```

---

### Exercício 13 — Custo de Instrutores por Unidade

Com relação existente (forma simples):

```dax
Custo Instrutores =
SUMX(
    instrutores,
    instrutores[salario]
)
```

Sem relação (usando FILTER):

```dax
Custo Instrutores (sem relação) =
SUMX(
    FILTER(instrutores, instrutores[id_unidade] = unidades[id_unidade]),
    instrutores[salario]
)
```

**Explicação:** A versão com `FILTER` é mais explícita e funciona mesmo se a relação for desativada ou removida do modelo. A versão simples é mais limpa quando a relação está ativa.

---

### Exercício 14 — Ticket Médio por Check-in

```dax
Ticket Médio Check-in =
DIVIDE(
    SUMX(
        FILTER(faturas, faturas[status_pagamento] = "Pago"),
        faturas[valor]
    ),
    COUNTROWS(checkins)
)
```

Versão alternativa (atribuindo valor por membro):

```dax
Ticket Médio Check-in (por membro) =
SUMX(
    checkins,
    CALCULATE(
        MAX(faturas[valor]),
        faturas[status_pagamento] = "Pago",
        faturas[id_membro] = checkins[id_membro]
    )
) / COUNTROWS(checkins)
```

**Diferença:** A primeira versão divide a receita total pelo total de check-ins. A segunda versão atribui a mensalidade do plano a cada check-in do membro — útil para análise de "quanto cada visita gera de receita".

---

### Exercício 15 — Duração Média das Aulas

```dax
Duração Média Aulas =
AVERAGEX(
    aulas,
    RELATED(modalidades[duracao_min])
)
```

**Explicação:** `AVERAGEX` itera sobre cada linha de `aulas`. Dentro de cada linha, `RELATED(modalidades[duracao_min])` busca a duração correspondente na tabela `modalidades` através do relacionamento. Isso funciona porque a relação entre `aulas[id_modalidade]` e `modalidades[id_modalidade]` está ativa no modelo.

---

### Exercício 16 — Melhor Instrutor

Primeiro, a medida auxiliar:

```dax
Total Comparecimentos Instrutor =
SUMX(
    aulas,
    aulas[compareceu]
)
```

Depois, a medida para o cartão:

```dax
Melhor Instrutor =
MAXX(
    TOPN(1, instrutores, [Total Comparecimentos Instrutor], DESC),
    instrutores[nome_instrutor]
)
```

**Explicação passo a passo:**
1. `TOPN(1, ...)` ordena `instrutores` por `[Total Comparecimentos Instrutor]` em ordem decrescente e pega a primeira linha
2. `MAXX` avalia a expressão `instrutores[nome_instrutor]` sobre essa tabela (que tem apenas 1 linha) e retorna o nome
3. Se houver empate em primeiro lugar, `TOPN` retorna apenas uma linha arbitrária entre as empatadas

Para desempatar por nota, use uma coluna de desempate:

```dax
Melhor Instrutor (com desempate) =
MAXX(
    TOPN(
        1,
        instrutores,
        [Total Comparecimentos Instrutor], DESC,
        instrutores[avaliacao_media], DESC
    ),
    instrutores[nome_instrutor]
)
```

---

### Exercício 17 — Unidade com Maior e Menor Receita

```dax
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

**Explicação:** `ASC` no `TOPN` coloca o menor valor primeiro. `MINX` então extrai o nome. Para evitar empates, considere adicionar uma segunda coluna de ordenação (por exemplo, área ou número de membros).

---

## PARTE 5 — RANKX

### Exercício 18 — Ranking de Instrutores

```dax
Ranking Comparecimento =
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

**Passo a passo:**
1. `ALL(instrutores)` remove qualquer filtro sobre a tabela de instrutores — o ranking é sempre sobre todos os 30 instrutores, não apenas os visíveis
2. `SUMX` + `FILTER` soma os comparecimentos apenas das aulas desse instrutor
3. `DESC` = ordem decrescente (1º lugar = mais comparecimentos)
4. `DENSE` = empates recebem o mesmo rank sem pular números (se dois empatam em 1º, o próximo é 2º, não 3º)
5. A vírgula extra antes de `DESC` é necessária porque o 4º argumento de `RANKX` é o tipo de ordenação (omitido = DESC por padrão)

---

### Exercício 19 — Top 3 Modalidades

```dax
Top 3 Modalidades =
CONCATENATEX(
    TOPN(
        3,
        ADDCOLUMNS(
            modalidades,
            "total_inscritos",
                SUMX(
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

**Explicação:**
1. `ADDCOLUMNS` adiciona uma coluna temporária `total_inscritos` à tabela `modalidades`
2. `TOPN(3, ...)` pega as 3 modalidades com mais inscritos
3. `CONCATENATEX` concatena os resultados: o primeiro argumento é a tabela, o segundo é a coluna a exibir, o terceiro é o separador, o quarto é a coluna de ordenação

---

### Exercício 20 — Ranking de Receita por Unidade (medida)

```dax
Ranking Receita Unidade =
RANKX(
    ALL(unidades),
    [Receita Total],
    ,
    DESC,
    DENSE
)
```

**Diferença em relação à coluna calculada (Exercício 18):** Por ser uma medida, o ranking é recalculado para cada contexto de filtro. Se você filtrar por cidade, o ranking será apenas entre as unidades daquela cidade. Se quiser o ranking global mesmo com filtros, use `ALL(unidades)` como no exemplo.

---

## PARTE 6 — Churn e Retenção

### Exercício 21 — Membros Ativos

```dax
Membros Ativos =
CALCULATE(
    DISTINCTCOUNT(membros[id_membro]),
    membros[status] = "Ativo"
)
```

**Forma alternativa:**

```dax
Membros Ativos (alternativa) =
COUNTROWS(FILTER(membros, membros[status] = "Ativo"))
```

Ambas funcionam. A primeira é mais concisa para filtros simples.

---

### Exercício 22 — Taxa de Churn

```dax
Taxa de Churn =
DIVIDE(
    CALCULATE(
        DISTINCTCOUNT(membros[id_membro]),
        membros[status] = "Cancelado"
    ),
    DISTINCTCOUNT(membros[id_membro])
)
```

Formate a medida como **porcentagem**. O resultado esperado é aproximadamente 5% (~150 cancelados / 3.000 membros).

---

### Exercício 23 — Membros Veteranos

```dax
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

**Nota:** O filtro de `status = "Ativo"` é necessário porque membros cancelados ou inativos não devem ser contados como veteranos.

---

### Exercício 24 — Taxa de Churn por Plano

```dax
Taxa de Churn por Plano =
DIVIDE(
    CALCULATE(
        DISTINCTCOUNT(membros[id_membro]),
        membros[status] = "Cancelado"
    ),
    DISTINCTCOUNT(membros[id_membro])
)
```

**Como funciona com o visual:** Quando você coloca `planos[nome_plano]` como eixo de um visual, o Power BI automaticamente filtra a tabela `membros` para mostrar apenas os membros daquele plano. A medida então calcula o churn dentro desse subconjunto, automaticamente.

---

## PARTE 7 — Medidas Estatísticas

### Exercício 25 — Idade Média

```dax
Idade Média Membros =
MEDIAN(membros[Idade])
```

Se não tiver a coluna `Idade`:

```dax
Idade Média Membros (inline) =
MEDIAN(
    DATEDIFF(membros[data_nascimento], TODAY(), YEAR)
)
```

**Nota:** `MEDIAN` no DAX calcula a mediana real (o valor do meio quando ordenamos os dados). Não confunda com média aritmética (`AVERAGE`).

---

### Exercício 26 — Percentis 25 e 75

```dax
P25 Idade = PERCENTILE.INC(membros[Idade], 0.25)
P75 Idade = PERCENTILE.INC(membros[Idade], 0.75)
```

**Diferença entre INC e EXC:**
- `PERCENTILE.INC`: inclui os valores das extremidades. Resultado é sempre um dos valores da coluna ou o valor interpolado entre dois valores existentes.
- `PERCENTILE.EXC`: exclui os valores das extremidades. Usa apenas os valores internos para interpolar. Pode retornar um valor que não existe na coluna original.

Para dados de negócio, `INC` é quase sempre a escolha certa.

---

### Exercício 27 — Desvio Padrão da Receita

```dax
STDEV Receita Unidades =
STDEVX.P(
    unidades,
    [Receita Total]
)
```

**X vs. não-X:** `STDEVX.P` é a versão iteradora. Itera sobre cada linha da tabela `unidades` e avalia a expressão `[Receita Total]`. `STDEV` (sem X) calcula o desvio padrão sobre uma coluna diretamente — útil quando a expressão é simplesmente uma coluna.

| Função | Uso |
|--------|-----|
| `STDEVX.P` | Itera e avalia uma expressão/medida |
| `STDEVX.S` | Versão amostral de STDEVX.P |
| `STDEV` | Desvio padrão sobre uma coluna |
| `STDEV.P` | Versão populacional de STDEV (não iteradora) |

---

### Exercício 28 — Máxima e Mínima Receita

```dax
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

**Alternativa mais simples:** Se `[Receita Total]` é uma medida que já respeita o filtro de unidade:

```dax
Máxima = MAX(unidades[Receita Total])   -- não funciona diretamente
```

Na prática, `MAXX` / `MINX` com uma medida como expressão é a forma mais confiável.

---

## PARTE 8 — Variáveis

### Exercício 29 — Ticket Médio com VAR

```dax
Ticket Médio Check-in v2 =
VAR Receita = SUMX(
    FILTER(faturas, faturas[status_pagamento] = "Pago"),
    faturas[valor]
)
VAR TotalCheckins = COUNTROWS(checkins)
RETURN
DIVIDE(Receita, TotalCheckins)
```

**Benefícios das variáveis:**
1. **Legibilidade** — o código diz claramente o que é cada parte
2. **Performance** — `Receita` é calculada uma única vez, não três (numerador, denominador e dentro do DIVIDE)
3. **Depuração** — você pode retornar `Receita` ou `TotalCheckins` individualmente para verificar valores intermediários

---

### Exercício 30 — Classificação de Crescimento com VAR + SWITCH

Assumindo que você já tem as medidas `[Receita Total]` e `[Receita Ano Anterior]` (calculada com `SAMEPERIODLASTYEAR`):

```dax
Crescimento % =
DIVIDE(
    [Receita Total] - [Receita Ano Anterior],
    [Receita Ano Anterior]
)

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

**Por que `VAR Crec`?** Sem a variável, `[Crescimento %]` seria recalculado em cada condição do `SWITCH`, degradando a performance. Com `VAR`, é calculado uma única vez e reutilizado.

---

## PARTE 9 — Dashboard de Retenção

### Colunas calculadas para a Página 5

**Último check-in do membro:**

```dax
Último Check-in =
CALCULATETABLE(
    MAX(checkins[data_checkin]),
    checkins[id_membro] = membros[id_membro]
)
```

**Dias sem frequentar:**

```dax
Dias Sem Frequentear =
DATEDIFF(
    CALCULATETABLE(
        MAX(checkins[data_checkin]),
        checkins[id_membro] = membros[id_membro]
    ),
    TODAY(),
    DAY
)
```

### Medida de Nível de Risco

```dax
Nível de Risco =
VAR dias = [Dias Sem Frequentear]
RETURN
SWITCH(
    TRUE(),
    dias > 90, "🔴 Alto Risco",
    dias > 45, "🟡 Risco Médio",
    dias > 15, "🟢 Em Observação",
    TRUE,     "✅ Ativo"
)
```

### Cores condicionais para a medida de risco

No Power BI, aplique formatação condicional ao campo da medida:
- "🔴 Alto Risco" → vermelho
- "🟡 Risco Médio" → amarelo
- "🟢 Em Observação" → verde claro
- "✅ Ativo" → verde escuro

---

## Resumo das Funções Aprendidas

| Função | Onde foi usada |
|--------|--------------|
| `CALCULATE` | Exercícios 5, 6, 7, 8, 21, 22, 23, 24 |
| `FILTER` | Exercícios 4, 5, 6, 7, 8, 10, 23 |
| `ALL` | Exercícios 5, 6, 8, 10, 18, 20, 23 |
| `KEEPFILTERS` | Exercícios 9, 11 |
| `SUMX` | Exercícios 4, 12, 13, 14, 15, 16, 18, 19, 29 |
| `AVERAGEX` | Exercício 15 |
| `MAXX` | Exercícios 16, 17 |
| `MINX` | Exercícios 17, 28 |
| `CONCATENATEX` | Exercício 19 |
| `TOPN` | Exercícios 16, 17, 19 |
| `RANKX` | Exercícios 18, 20 |
| `DIVIDE` | Exercícios 3, 4, 14, 22, 24, 25, 29, 30 |
| `DISTINCTCOUNT` | Exercícios 8, 21, 22, 23, 24 |
| `COUNTROWS` | Exercícios 4, 9, 11, 14, 29 |
| `SWITCH(TRUE())` | Exercícios 2, 4, 30, 31 |
| `VAR / RETURN` | Exercícios 29, 30, 31 |
| `MEDIAN` | Exercício 25 |
| `PERCENTILE.INC` | Exercício 26 |
| `STDEVX.P` | Exercício 27 |
| `RELATED` | Exercício 15 |
| `EXISTS` | Exercício 10 |
| `ADDCOLUMNS` | Exercício 19 |
| `CALCULATETABLE` | Exercícios 10, 31 |
| `DATEDIFF` | Exercícios 1, 23, 31 |
| `SELECTEDVALUE` | Dica geral |

---

*Resoluções criadas para FitLife+ — Thiago, Gerente de Operações.*
