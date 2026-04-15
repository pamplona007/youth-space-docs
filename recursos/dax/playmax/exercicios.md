# PlayMax — Exercícios de Limpeza de Dados

## Contexto

A **Fernanda**, analista de dados da PlayMax, recebeu os CSVs brutos do time de engenharia. Antes de criar qualquer dashboard, ela precisa garantir que os dados estão limpos e confiáveis.

**Sua missão:** Seguir os passos de limpeza abaixo para preparar a base para análise.

---

## Modelo de Dados

Antes de começar, conecte os CSVs no Power BI e crie os relacionamentos:

- `doacoes[id_transmissao]` → `transmissoes[id_transmissao]`
- `doacoes[id_streamer]` → `streamers[id_streamer]`
- `doacoes[id_usuario]` → `usuarios[id_usuario]`
- `transmissoes[id_streamer]` → `streamers[id_streamer]`
- `transmissoes[id_categoria]` → `categorias[id_categoria]`
- `calendario[data]` → `doacoes[data]`

---

## Fase 1 — Identificação de Problemas

> **Antes de limpar, você precisa entender o que tem de errado.**

**1. Contagem de Registros**
Verifique quantos registros cada tabela possui. Anote para comparar após a limpeza.

**2. Visualize os Dados Brutos**
No Power BI, vá em "Transformar Dados" (Power Query) e observe:
- Valores em vermelho (erro)
- Células com "null", "NA", "-", "#N/A" ou vazias
- Textos com espaços extras
- Categorias escritas de formas diferentes

---

## Fase 2 — Limpeza de Textos

> Resolva cada tabela uma de cada vez.

### Categorias

**3. Remover Espaços Extras**
Use `Text.Trim` para remover espaços no início e fim de `nome_categoria`.

**4. Padronizar Maiúsculas/Minúsculas**
Uniformize todas as categorias para "Título" (primeira letra maiúscula).

**5. Remover Emojis**
Remova emojis das categorias (ex: "RPG 🎮" → "RPG").

---

### Streamers

**6. Limpar Nomes**
Aplique `Text.Trim` em `nome_streamer`.

**7. Padronizar Case**
Uniformize todos os nomes para Title Case.

---

### Usuários

**8. Limpar Nomes de Usuário**
Aplique `Text.Trim` em `nome_usuario`.

**9. Verificar Idades**
Identifique e remova linhas onde `idade` seja menor que 13 ou maior que 100 (valores impossíveis).

---

## Fase 3 — Tratamento de Valores Inválidos

### Doações

**10. Valores Negativos ou Zeros**
Identifique transações onde `valor` é menor ou igual a 0. Remova essas linhas.

**11. Valores Extremos (Outliers)**
Identifique valores de doação acima de R$ 10.000. Analise: são erros ou doações legítimas?
- Se forem erros, remova as linhas.
- Se forem legítimos, mantenha.

**12. Streamers Inválidos (IDs Órfãos)**
Identifique doações que referenciam `id_streamer` que não existe na tabela `streamers`. Remova essas linhas.

**13. Transmissões Inválidas (IDs Órfãos)**
Identifique doações que referenciam `id_transmissao` que não existe na tabela `transmissoes`. Remova essas linhas.

**14. Mensagens de Doação**
Valores nulos ou vazios em `mensagem` são aceitáveis? Se não, preencha com "Sem mensagem".

**14b. Status de Doação Inconsistente**
Verifique valores únicos de `status_doacao`. Padronize para: "Pendente", "Concluída", "Cancelada" ( Title Case).

**14c. Datas de Doação Inválidas**
Identifique doações com `data` fora do período de 2024-2025. Remova essas linhas.

---

### Transmissões

**15. Datas Inválidas**
Identifique transmissões com `data_inicio` fora do período de 2024-2025. Remova essas linhas.

**16. Duração Negativa ou Zero**
Remova linhas onde `duracao_min` seja <= 0.

**17. Status Inconsistentes**
Verifique valores únicos de `status`. Padronize para: "Ao Vivo", "Encerrada", "Cancelada".

---

## Fase 4 — Remoção de Duplicatas

**18. Transmissões Duplicadas**
Identifique e remova linhas duplicadas em `transmissoes` (mesmo `id_transmissao`).

**19. Doações Duplicadas**
Identifique e remova linhas duplicadas em `doacoes` (mesmo `id_doacao`).

**20. Verificar Após Limpeza**
Recompute a contagem de registros e compare com a Fase 1.

---

## Fase 5 — Validação Final

**21. Integridade Referencial**
Confirme que toda `doacao` tem um `streamer` válido.
Confirme que toda `doacao` tem uma `transmissao` válida.

**22. Completude dos Dados**
Verifique se há valores nulos em colunas críticas:
- `doacoes`: `data`, `valor`, `id_streamer`, `status_doacao`
- `transmissoes`: `data_inicio`, `duracao_min`
- `streamers`: `nome_streamer`, `seguidores`

**23. Documentar Limpezas**
Crie uma tabela resumo explicando:
- Quantos registros foram removidos
- Quais tipos de problemas foram corrigidos
- Quantas duplicatas foram eliminadas

---

## Fase 6 — Criação de Colunas Calculadas (Power BI)

> Após a limpeza, crie as seguintes colunas calculadas:

**24. Receita Total por Streamer**
Some o `valor` das doações para cada streamer (apenas `status_doacao` = "Concluída").

**25. Tempo Total por Streamer**
Some a `duracao_min` de todas as transmissões por streamer.

**26. Média de Viewers**
Calcule a média de `viewers_pico` por streamer.

**27. Categoria Mais Transmita**
Para cada streamer, identifique a categoria mais transmitida.

**28. Evolução Mensal de Doações**
Agrupe as doações por mês e calcule o total.

---

## Dicas

- Sempre faça cópias de segurança dos CSVs originais antes de limpar
- Use o Power Query para todas as transformações (não limpe no Excel)
- Teste cada passo e verifique os resultados antes de avançar
- Se algo parecer suspeito demais (ex: donation de R$ 1M), provavelmente é erro
