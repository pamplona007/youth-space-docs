# Resoluções dos Exercícios DAX

---

## Colunas Calculadas

### Calendário

**1. Ano**
```dax
Ano = YEAR(calendario[data])
```

**2. Mês (número)**
```dax
Mes = MONTH(calendario[data])
```

**3. Nome do Mês**
```dax
Nome do Mes =
SWITCH(
    MONTH(calendario[data]),
    1,  "Janeiro",
    2,  "Fevereiro",
    3,  "Março",
    4,  "Abril",
    5,  "Maio",
    6,  "Junho",
    7,  "Julho",
    8,  "Agosto",
    9,  "Setembro",
    10, "Outubro",
    11, "Novembro",
    12, "Dezembro"
)
```

**4. Trimestre**
```dax
Trimestre = "Q" & QUARTER(calendario[data])
```

**5. Dia**
```dax
Dia = DAY(calendario[data])
```

**6. Dia da Semana (nome)**
```dax
Dia da Semana =
SWITCH(
    WEEKDAY(calendario[data], 2),
    1, "Segunda",
    2, "Terça",
    3, "Quarta",
    4, "Quinta",
    5, "Sexta",
    6, "Sábado",
    7, "Domingo"
)
```
> `WEEKDAY(..., 2)` usa segunda-feira como primeiro dia da semana (retorna 1 a 7).

**7. Semana do Ano**
```dax
Semana do Ano = WEEKNUM(calendario[data], 2)
```

**8. É Fim de Semana?**
```dax
Fim de Semana = WEEKDAY(calendario[data], 2) >= 6
```

---

### Vendas

**9. Valor Original**
```dax
Valor Original = vendas[qtd] * RELATED(produtos[preco_unitario])
```

**10. Valor Final**
```dax
Valor Final = vendas[Valor Original] * (1 - vendas[desconto])
```

**11. Custo Total**
```dax
Custo Total = vendas[qtd] * RELATED(produtos[custo_unitario])
```

**12. Lucro**
```dax
Lucro = vendas[Valor Final] - vendas[Custo Total]
```

---

## Medidas

### Básicas

**1. Total de Vendas**
```dax
Total de Vendas =
CALCULATE(
    SUMX(vendas, vendas[Valor Final]),
    vendas[status_venda] = "Concluída"
)
```

**2. Total de Custo**
```dax
Total de Custo =
CALCULATE(
    SUMX(vendas, vendas[Custo Total]),
    vendas[status_venda] = "Concluída"
)
```

**3. Lucro Bruto**
```dax
Lucro Bruto = [Total de Vendas] - [Total de Custo]
```

**4. Margem %**
```dax
Margem % = DIVIDE([Lucro Bruto], [Total de Vendas])
```

**5. Quantidade Vendida**
```dax
Quantidade Vendida =
CALCULATE(
    SUM(vendas[qtd]),
    vendas[status_venda] = "Concluída"
)
```

**6. Número de Vendas**
```dax
Numero de Vendas =
CALCULATE(
    COUNTROWS(vendas),
    vendas[status_venda] = "Concluída"
)
```

**7. Ticket Médio**
```dax
Ticket Medio = DIVIDE([Total de Vendas], [Numero de Vendas])
```

---

### Clientes e Produtos

**8. Clientes Únicos**
```dax
Clientes Unicos =
CALCULATE(
    DISTINCTCOUNT(vendas[id_cliente]),
    vendas[status_venda] = "Concluída"
)
```

**9. Produtos Vendidos**
```dax
Produtos Vendidos =
CALCULATE(
    DISTINCTCOUNT(vendas[id_produto]),
    vendas[status_venda] = "Concluída"
)
```

**10. Vendas Canceladas %**
```dax
Vendas Canceladas % =
DIVIDE(
    CALCULATE(COUNTROWS(vendas), vendas[status_venda] = "Cancelada"),
    COUNTROWS(vendas)
)
```

**11. Ranking de Produtos**
```dax
Ranking de Produtos =
RANKX(
    ALL(produtos[nome_produto]),
    [Total de Vendas],
    ,
    DESC,
    DENSE
)
```

---

### Inteligência de Tempo

**12. Vendas Acumuladas no Ano (YTD)**
```dax
Vendas YTD =
TOTALYTD(
    [Total de Vendas],
    calendario[data]
)
```

**13. Vendas no Mesmo Período do Ano Anterior (SPLY)**
```dax
Vendas SPLY =
CALCULATE(
    [Total de Vendas],
    SAMEPERIODLASTYEAR(calendario[data])
)
```

**14. Crescimento Ano a Ano (%)**
```dax
Crescimento YoY % =
DIVIDE(
    [Total de Vendas] - [Vendas SPLY],
    [Vendas SPLY]
)
```

**15. Vendas Acumuladas no Mês (MTD)**
```dax
Vendas MTD =
TOTALMTD(
    [Total de Vendas],
    calendario[data]
)
```

**16. Vendas nos Últimos 30 Dias**
```dax
Vendas Ultimos 30 Dias =
CALCULATE(
    [Total de Vendas],
    DATESINPERIOD(
        calendario[data],
        MAX(calendario[data]),
        -30,
        DAY
    )
)
```
