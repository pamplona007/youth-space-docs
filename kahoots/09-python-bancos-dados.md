# Kahoot — Python & Bancos de Dados (Módulo 2 - Fullstack Python)

> 35 perguntas alinhadas ao conteúdo de 8 aulas (32h total)

---

## Kahoot 1: Python Intermediário - Strings, Arquivos, Módulos (Aula 1)

**P1.** Qual método converte string para maiúsculas?
- A) `.upper()` ✅
- B) `.lower()`
- C) `.capitalize()`
- D) `.case()`

**P2.** O que `f-strings` permite em Python?
- A) Criar arquivos
- B) Formatar texto com variáveis diretamente ✅
- C) Criar funções
- D) Arrays de strings

**P3.** Qual modo de `open()` cria novo arquivo ou sobrescreve existente?
- A) `r`
- B) `w` ✅
- C) `a`
- D) `rb`

**P4.** O que `with open()` garante?
- A) Arquivo será criado
- B) Arquivo será fechado automaticamente ✅
- C) Arquivo será deletado
- D) Arquivo será encriptado

**P5.** Qual módulo gera números aleatórios?
- A) `math`
- B) `random` ✅
- C) `os`
- D) `sys`

**P6.** Como importar apenas a função `sqrt` do módulo `math`?
- A) `import math.sqrt`
- B) `from math import sqrt` ✅
- C) `import sqrt from math`
- D) `require math.sqrt`

---

## Kahoot 2: Orientação a Objetos (Aula 2)

**P1.** Qual método é chamado ao criar um objeto?
- A) `__create__()`
- B) `__init__()` ✅
- C) `__new__()`
- D) `__make__()`

**P2.** O que `self` representa em uma classe?
- A) A classe
- B) O módulo
- C) A instância do objeto ✅
- D) Uma função

**P3.** Como criar herança em Python?
- A) `class Filho(Pai)` ✅
- B) `class Filho extends Pai`
- C) `class Filho: Pai`
- D) `class Filho inherits Pai`

**P4.** O que `super()` faz?
- A) Cria um objeto
- B) Chama método da classe pai ✅
- C) Define superclasse
- D) Importa módulo

**P5.** Atributos com `__` (dunder) são:
- A) Públicos
- B) Privados por convenção ✅
- C) Estáticos
- D) Globais

---

## Kahoot 3: Tratamento de Exceções e Dicionários (Aula 3)

**P1.** Qual bloco captura exceções?
- A) `catch`
- B) `try/except` ✅
- C) `handle`
- D) `error`

**P2.** O que `raise` faz?
- A) Captura erro
- B) Levanta/exibe uma exceção ✅
- C) Trata erro
- D) Ignora erro

**P3.** Como acessar valor de chave inexistente em dicionário sem erro?
- A) `dict[key]`
- B) `dict.get(key)` ✅
- C) `dict[key] if key in dict`
- D) `dict.exists(key)`

**P4.** Qual método retorna todas as chaves de um dicionário?
- A) `.values()`
- B) `.keys()` ✅
- C) `.items()`
- D) `.get()`

**P5.** O que `{k: v for k, v in dict.items()}` cria?
- A) Lista
- B) Tupla
- C) Dict comprehension ✅
- D) Set

---

## Kahoot 4: SQL Básico (Aula 4)

**P1.** Qual comando cria uma tabela?
- A) `CREATE TABLE` ✅
- B) `ADD TABLE`
- C) `NEW TABLE`
- D) `INSERT TABLE`

**P2.** Qual comando insere dados?
- A) `ADD INTO`
- B) `INSERT INTO` ✅
- C) `CREATE INTO`
- D) `SET INTO`

**P3.** Qual comando filtra resultados?
- A) `FILTER`
- B) `WHERE` ✅
- C) `SELECT`
- D) `FROM`

**P4.** Qual comando ordena resultados?
- A) `ORDER` (com `BY`) ✅
- B) `SORT`
- C) `ARRANGE`
- D) `GROUP`

**P5.** Qual tipo de dado SQL armazena texto?
- A) `TEXT` ou `VARCHAR` ✅
- B) `INT`
- C) `BOOLEAN`
- D) `FLOAT`

---

## Kahoot 5: SQL Avançado - Joins e Agregações (Aula 5)

**P1.** Qual join retorna todos os registros da tabela esquerda + correspondentes da direita?
- A) `INNER JOIN`
- B) `LEFT JOIN` ✅
- C) `RIGHT JOIN`
- D) `FULL JOIN`

**P2.** Qual função conta registros?
- A) `SUM()`
- B) `COUNT()` ✅
- C) `TOTAL()`
- D) `NUMBER()`

**P3.** Qual cláusula agrupa resultados?
- A) `GROUP` ✅
- B) `AGGREGATE`
- C) `CLUSTER`
- D) `COMBINE`

**P4.** O que `HAVING` faz?
- A) Filtra antes do GROUP BY
- B) Filtra depois do GROUP BY ✅
- C) Ordena resultados
- D) Limita resultados

**P5.** Para que serve PRIMARY KEY?
- A) Define senha
- B) Identifica exclusivamente cada registro ✅
- C) Criptografa dados
- D) Ordena tabela

---

## Kahoot 6: SQLite com Python (Aula 6)

**P1.** Qual módulo SQLite é nativo do Python?
- A) `sqlite`
- B) `sqlite3` ✅
- C) `pysqlite`
- D) `db`

**P2.** Como confirmar transações no SQLite?
- A) `conn.save()`
- B) `conn.commit()` ✅
- C) `conn.execute()`
- D) `conn.close()`

**P3.** O que `conn.cursor()` retorna?
- A) Dados
- B) Objeto para executar queries SQL ✅
- C) Tabela
- D) Status da conexão

**P4.** Como evitar SQL Injection?
- A) Concatenar strings
- B) Usar parameterized queries ✅
- C) Não usar SQL
- D) Usar inputs apenas

---

## Kahoot 7: SQLAlchemy ORM (Aula 7)

**P1.** ORM significa:
- A) Object Relational Mapping ✅
- B) Object Review Model
- C) Online Repository Manager
- D) Object Reference Method

**P2.** Qual classe é base para modelos SQLAlchemy?
- A) `Model`
- B) `Base` ✅
- C) `Table`
- D) `Schema`

**P3.** Qual método adiciona registro ao banco?
- A) `session.insert()`
- B) `session.add()` ✅
- C) `session.commit()`
- D) `session.push()`

**P4.** Como buscar todos os registros de um modelo?
- A) `Model.all()`
- B) `session.query(Model).all()` ✅
- C) `Model.find()`
- D) `Model.get()`

---

**Total: 33 perguntas em 7 Kahoot(s)**
