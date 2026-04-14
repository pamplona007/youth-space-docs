# PlayMax — Plataforma de Streaming de Games

> Contexto da empresa fictícia usada nos exercícios de limpeza de dados.

## Sobre a Empresa

A **PlayMax** é uma plataforma brasileira de streaming de jogos eletrônicos, similar a uma Twitch ou Kick nacional. Criadores de conteúdo transmitem jogos ao vivo e os espectadores podem assinar canais, doar bits (moeda virtual) e comprar subscrições mensais.

## Gestor

**Fernanda** — Analista de Dados
- Responsável por manter a base de dados limpa e confiável
- Utiliza o Power BI para criar dashboards de monitoramento
- Recebe dados crus de múltiplas fontes e precisa tratá-los antes da análise

## Banco de Dados

A base é composta pelas seguintes tabelas:

| Tabela | Descrição |
|--------|-----------|
| `transmissoes` | Informações das lives transmitidas (800 registros) |
| `categorias` | Categorias de jogos (20 registros) |
| `streamers` | Cadastro dos criadores de conteúdo (50 registros) |
| `doacoes` | Doações e bits recebidos durante as transmissões (15.000 registros) |
| `usuarios` | Usuários/espectadores da plataforma (3.000 registros) |
| `calendario` | Tabela calendário (2 anos) |

## Dados Gerados

O script [script.md](script.md) gera CSVs com dados **intencionais com problemas** para exercício de limpeza:

### Problemas de Qualidade Incluídos

| Problema | Tabela Afetada | Descrição |
|----------|----------------|-----------|
| Duplicatas | `transmissoes`, `doacoes` | Registros duplicados por falha no sistema |
| Valores nulos | Todas | Células vazias, "NA", "-", "null" |
| Inconsistência | `categorias`, `streamers` | Mesmo valor escrito diferente (e.g., "RPG", "rpg", "Rpg") |
| Datas inválidas | `doacoes` | Datas fora do período, formato inconsistente |
| Outliers | `doacoes` | Valores de doação impossíveis (negativos, extremamente altos) |
| Espaços | `streamers` | Espaços extras no início/fim dos nomes |
| IDs órfãos | `doacoes` | Transações referencing streamers que não existem |
| Valores de texto | `categorias` | Categorias com emoji, texto misturado |
