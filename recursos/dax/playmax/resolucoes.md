# PlayMax — Resoluções dos Exercícios de Limpeza

> As soluções abaixo usam apenas a **interface visual** do Power Query. Sem código!

---

## Modelo de Dados (Power BI)

Após importar os CSVs, configure os relacionamentos na view de "Modelo":

1. Arraste `doacoes[id_transmissao]` até `transmissoes[id_transmissao]`
2. Arraste `doacoes[id_streamer]` até `streamers[id_streamer]`
3. Arraste `doacoes[id_usuario]` até `usuarios[id_usuario]`
4. Arraste `transmissoes[id_streamer]` até `streamers[id_streamer]`
5. Arraste `transmissoes[id_categoria]` até `categorias[id_categoria]`
6. Arraste `doacoes[data]` até `calendario[data]`

---

## Fase 1 — Identificação de Problemas

### 1. Contagem de Registros

**Passo a passo:**

1. Clique com botão direito na query → "Cont Linhas"
2. Ou: vá em "Exibir" → "Barra de Status" (mostra a contagem na parte inferior)

**Resultado esperado (antes da limpeza):**

| Tabela | Registros |
|--------|-----------|
| categorias | 20 |
| streamers | 50 |
| calendario | 730 |
| transmissoes | ~820 (com duplicatas) |
| usuarios | 3.000 |
| doacoes | ~16.000 (com duplicatas) |

---

### 2. Visualize os Dados Brutos

No Power Query Editor, observe:

1. **Colunas com erro** — células em vermelho
2. **Valores problemáticos** — procure por "null", "NA", "-", "#N/A", "", "NULL"
3. **Espaços visíveis** — textos que parecem normais mas têm espaço antes/depois
4. **Números suspeitos** — negativos, zeros, valores estranhos

**Dica:** Para encontrar valores problemáticos:
1. Clique no ícone de filtro na coluna
2. Veja a lista de valores únicos — procure por anomalias
3. Ou use "Substituir Valores" (botão direito → Substituir Valores):
   - Substitua `null` por `(Vazio)`
   - Substitua `NA` por `(Vazio)`
   - Substitua `-` por `(Vazio)`
   - Substitua `#N/A` por `(Vazio)`

---

## Fase 2 — Limpeza de Textos

### Categorias

**3. Remover Espaços Extras**

1. Selecione a coluna `nome_categoria`
2. Vá em "Transformar" → "Formatar" → "Aparar"

**4. Padronizar Maiúsculas/Minúsculas**

1. Com a coluna `nome_categoria` selecionada
2. Vá em "Transformar" → "Formatar" → "Primeira letra maiúscula"

**5. Remover Emojis**

Infelizmente, não há botão nativo para remover emojis. Use este truque:

1. Clique com botão direito na coluna → "Substituir Valores"
2. Substitua `🎮` por `(Vazio)`
3. Substitua `🎯` por `(Vazio)`
4. Substitua `✨` por `(Vazio)`
5. (Repita para outros emojis que encontrar)

**Resultado:**

| Antes | Depois |
|-------|--------|
| "rpg 🎮" | "Rpg" |
| "FPS " | "Fps" |
| "  MOBA  " | "Moba" |
| "battle royale ✨" | "Battle Royale" |

---

### Streamers

**6. Limpar Nomes**

1. Selecione a coluna `nome_streamer`
2. Vá em "Transformar" → "Formatar" → "Aparar"

**7. Padronizar Case**

1. Com a coluna `nome_streamer` selecionada
2. Vá em "Transformar" → "Formatar" → "Primeira letra maiúscula"

---

### Usuários

**8. Limpar Nomes de Usuário**

1. Selecione a coluna `nome_usuario`
2. Vá em "Transformar" → "Formatar" → "Aparar"

**9. Verificar Idades**

1. Clique na seta de filtro da coluna `idade`
2. Desmarque valores < 13 e > 100
3. Ou: "Filtros de Número" → "Entre" → 13 a 100

**Resultado:** Linhas com idade 5, 150, -20, etc. são removidas.

---

## Fase 3 — Tratamento de Valores Inválidos

### Doações

**10. Valores Negativos ou Zeros**

1. Clique no filtro da coluna `valor`
2. "Filtros de Número" → "É maior que" → 0

**11. Valores Extremos (Outliers)**

1. Primeiro, identifique os valores > 10.000:
   - Clique no filtro → "É maior que" → 10000
   - Anote quantos registros são
2. Decida: são erros ou doações legítimas?
   - Se erro: remova (same步骤 acima, mas < 10000)
   - Se legítimo: volte e mantenha

**Análise:**
- R$ 9.999.999 → erro, remover
- R$ 5.000 → legítimo, manter
- R$ 50 → legítimo, manter

**12. Streamers Inválidos (IDs Órfãos)**

Isso requer uma técnica de "merge":

1. Faça um **Merge** da query `doacoes` com `streamers`:
   - Selecione `doacoes[id_streamer]` (esquerda)
   - Selecione `streamers[id_streamer]` (direita)
   - Tipo: "Left Outer" (todos da esquerda)
2. Expanda a nova coluna e marque apenas `id_streamer` (da direita)
3. Filtre: onde a nova coluna for `null` → remover
4. Remova a coluna de merge depois

**13. Transmissões Inválidas (IDs Órfãos)**

Mesmo processo do exercício 12:

1. Merge `doacoes` com `transmissoes`:
   - `doacoes[id_transmissao]` ↔ `transmissoes[id_transmissao]`
2. Filtre onde a coluna expandida for `null` → remover
3. Remova a coluna de merge

**14. Mensagens de Doação**

1. Clique com botão direito na coluna `mensagem` → "Substituir Valores"
2. Substitua `(Vazio)` por "Sem mensagem"

---

### Transmissões

**15. Datas Inválidas**

1. Clique no filtro de `data_inicio`
2. "Filtros de Data" → "Entre"
3. Configure: de 01/01/2024 até 31/12/2025

**16. Duração Negativa ou Zero**

1. Clique no filtro de `duracao_min`
2. "Filtros de Número" → "É maior que" → 0

**17. Status Inconsistentes**

1. Primeiro, veja os valores únicos:
   - Clique no filtro → "Remover Vazio"
   - Leia os valores: "Ativa", "Encerrada", "Ao Vivo", "Cancelada"
2. Padronize manualmente:
   - Se encontrar "ativa" → substitua por "Ativa"
   - Se encontrar "encerrado" → substitua por "Encerrada"
   - Se encontrar "ao vivo" → substitua por "Ao Vivo"

---

## Fase 4 — Remoção de Duplicatas

**18. Transmissões Duplicadas**

1. Selecione a query `transmissoes`
2. Vá em "Página Inicial" → "Remover Linhas" → "Remover Duplicatas"
3. Escolha a coluna: `id_transmissao`

**19. Doações Duplicadas**

1. Selecione a query `doacoes`
2. Vá em "Página Inicial" → "Remover Linhas" → "Remover Duplicatas"
3. Escolha a coluna: `id_doacao`

**20. Verificar Após Limpeza**

1. Faça "Cont Linhas" em cada query
2. Compare com os valores da Fase 1

**Resultado esperado (após limpeza):**

| Tabela | Antes | Depois |
|--------|-------|--------|
| categorias | 20 | 20 |
| streamers | 50 | 50 |
| transmissoes | ~820 | 800 |
| usuarios | 3.000 | ~2.990 |
| doacoes | ~16.000 | ~14.500 |
| calendario | 730 | 730 |

---

## Fase 5 — Validação Final

**21. Integridade Referencial**

Faça merge para verificar:

1. Merge `doacoes` + `streamers` (id_streamer)
2. Filtre onde resultado for null → deve retornar 0 linhas
3. Repita com `doacoes` + `transmissoes` (id_transmissao)

Se aparecerem linhas, há IDs órfãos que não foram removidos.

**22. Completude dos Dados**

1. Selecione a query `doacoes`
2. Vá em "Exibir" → "Perfis de Coluna"
3. Verifique que não há nulos em:
   - `data`
   - `valor`
   - `id_streamer`

Repita para `transmissoes` e `streamers`.

**23. Documentar Limpezas**

Crie uma tabela simples no Power BI ou Excel:

| Problema | Ação no Power Query |
|----------|-------------------|
| Espaços extras | Transformar → Formatar → Aparar |
| Case inconsistente | Transformar → Formatar → Primeira letra maiúscula |
| Emojis | Substituir Valores (remover um por um) |
| Idades inválidas | Filtro de número (13 a 100) |
| Valores <= 0 | Filtro de número (> 0) |
| Outliers (> 10k) | Filtro de número (< 10000) |
| IDs órfãos | Merge + filtrar nulls |
| Datas fora do período | Filtro de data (entre) |
| Duração <= 0 | Filtro de número (> 0) |
| Duplicatas | Página Inicial → Remover Duplicatas |
| Mensagens vazias | Substituir Valores ("Sem mensagem") |

---

## Fase 6 — Medidas no Power BI (Interface)

Nessa fase, você pode criar medidas básicas usando a interface:

**24-27. Criar Medidas pelo ícone "+":**

1. Clique com botão direito em qualquer tabela → "Criar Medida"
2. Use o autocomplete para digitar:
   ```
   Receita Total = SUM(doacoes[valor])
   ```
3. Ou: selecione a coluna → "Ferramentas de Coluna" → "Soma"

Para média, máximo, mínimo:
1. Selecione a coluna numérica
2. Vá em "Ferramentas de Coluna" → "Média", "Máximo", etc.

**28. Visual de Evolução Mensal:**

1. Crie uma medida: `Total Doações = SUM(doacoes[valor])`
2. Crie visual de "Gráfico de Colunas Clusterizadas"
3. Arraste `calendario[Ano]` e `calendario[Mês]` para o eixo
4. Arraste `Total Doações` para os valores

---

## Atalhos Úteis no Power Query

| Problema | Solução Visual |
|----------|---------------|
| Remover espaços | Transformar → Formatar → Aparar |
| Maiúsculas/minúsculas | Transformar → Formatar → ... |
| Substituir valores | Botão direito → Substituir Valores |
| Filtrar dados | Clique na seta do filtro |
| Remover duplicatas | Página Inicial → Remover Duplicatas |
| Mesclar tabelas | Página Inicial → Mesclar Consultas |
| Ver contagem | Barra de status (inferior) |

---

## Dicas Finais

1. **Sempre faça uma cópia** da query antes de limpar (botão direito → Duplicar)
2. **Renomeie os passos** no painel lateral — "Aparar Espaços" é melhor que "Etapa 14"
3. **Verifique a contagem** após cada limpeza usando a barra de status
4. **Não tenha medo de desfazer** — clique no X ao lado do passo para remover
5. **Aplique no Power BI** só depois de tudo limpo — "Fechar e Aplicar"
