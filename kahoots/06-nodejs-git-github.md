# Kahoot — Node.js & Git/GitHub (Módulo 5)

> 30 perguntas alinhadas ao conteúdo de 8 aulas (32h total)

---

## Kahoot 1: Git Básico (Aula 1)

**P1.** Qual comando inicializa um repositório Git?
- A) `git start`
- B) `git init` ✅
- C) `git new`
- D) `git create`

**P2.** Qual comando adiciona arquivos à área de staging?
- A) `git add` ✅
- B) `git commit`
- C) `git push`
- D) `git stage`

**P3.** Qual comando salva alterações no repositório?
- A) `git save`
- B) `git commit` ✅
- C) `git push`
- D) `git add`

**P4.** O que `.gitignore` faz?
- A) Ignora erros no Git
- B) Especifica arquivos a não versionar ✅
- C) Remove arquivos do repositório
- D) Protege o repositório

**P5.** Qual comando mostra o status do repositório?
- A) `git log`
- B) `git status` ✅
- C) `git diff`
- D) `git show`

**P6.** Como desfazer alterações não commitadas?
- A) `git undo`
- B) `git restore` ✅
- C) `git reset`
- D) `git revert`

**P7.** O que `git log --oneline` mostra?
- A) Histórico detalhado
- B) Cada commit em uma linha ✅
- C) Apenas arquivos modificados
- D) Diferenças entre commits

---

## Kahoot 2: GitHub e Colaboração (Aula 2)

**P1.** Qual comando vincula seu repositório local ao GitHub?
- A) `git remote add origin [url]` ✅
- B) `git connect origin`
- C) `git link origin`
- D) `git github add`

**P2.** Qual comando envia commits para o repositório remoto?
- A) `git commit`
- B) `git push` ✅
- C) `git pull`
- D) `git send`

**P3.** O que um branch representa no Git?
- A) Uma tag
- B) Uma linha independente de desenvolvimento ✅
- C) Um arquivo
- D) Um commit específico

**P4.** Qual comando cria e muda para um novo branch?
- A) `git branch [nome]`
- B) `git checkout -b [nome]` ✅
- C) `git new [nome]`
- D) `git switch [nome]`

**P5.** O que é um Pull Request?
- A) Pedido para baixar código
- B) Proposta de mesclar código de um branch ✅
- C) Comando do Git
- D) Tipo de merge

**P6.** O que fazer quando há conflito de merge?
- A) Ignorar e continuar
- B) Resolver manualmente editando os arquivos ✅
- C) Deletar o branch
- D) Criar novo repositório

**P7.** O que `git merge` faz?
- A) Envia código para remote
- B) Baixa código do remote
- C) Combina branches ✅
- D) Cria novo branch

---

## Kahoot 3: Introdução ao Node.js (Aula 3)

**P1.** O que é Node.js?
- A) Navegador
- B) Runtime de JavaScript no servidor ✅
- C) Biblioteca JavaScript
- D) Framework CSS

**P2.** Qual módulo Node.js lê arquivos?
- A) `http`
- B) `path`
- C) `fs` ✅
- D) `os`

**P3.** Qual é o gerenciador de pacotes do Node.js?
- A) `npm` ✅
- B) `pip`
- C) `yarn`
- D) `composer`

**P4.** O que `require('fs')` faz?
- A) Cria um arquivo
- B) Importa o módulo fs ✅
- C) Deleta um arquivo
- D) Lista arquivos

**P5.** Qual arquivo define dependências de um projeto Node?
- A) `package.json` ✅
- B) `node.json`
- C) `npm.json`
- D) `dependencies.json`

**P6.** O que `process.argv` contém?
- A) Variáveis de ambiente
- B) Argumentos passados ao script ✅
- C) Caminho do Node
- D) Versão do Node

---

## Kahoot 4: Express.js e Rotas (Aula 4)

**P1.** Qual método Express responde a requisições GET?
- A) `app.post()`
- B) `app.get()` ✅
- C) `app.put()`
- D) `app.delete()`

**P2.** O que `req.params` contém em Express?
- A) Query strings
- B) Parâmetros da URL ✅
- C) Body da requisição
- D) Headers

**P3.** O que middleware faz no Express?
- A) Responde requisições
- B) Processa requisições antes das rotas ✅
- C) Cria rotas
- D) Envia arquivos

**P4.** Qual função é usada para enviar JSON no Express?
- A) `res.send()`
- B) `res.json()` ✅
- C) `res.write()`
- D) `res.text()`

**P5.** O que `express.json()` faz?
- A) Envia arquivos JSON
- B) Parser de body JSON ✅
- C) Cria rotas JSON
- D) Valida JSON

---

## Kahoot 5: REST API e CRUD (Aula 5)

**P1.** Qual verbo HTTP é usado para CRIAR um recurso?
- A) GET
- B) POST ✅
- C) PUT
- D) DELETE

**P2.** Qual verbo HTTP é usado para ATUALIZAR um recurso?
- A) POST
- B) PUT ou PATCH ✅
- C) GET
- D) DELETE

**P3.** Qual status code indica "Created" (recurso criado)?
- A) 200
- B) 201 ✅
- C) 204
- D) 301

**P4.** Qual status code indica "Not Found"?
- A) 400
- B) 403
- C) 404 ✅
- D) 500

**P5.** O que CRUD significa?
- A) Create, Read, Update, Delete ✅
- B) Copy, Read, Upload, Download
- C) Create, Replace, Use, Deploy
- D) Connect, Review, Upload, Delete

---

## Kahoot 6: Autenticação JWT (Aula 6)

**P1.** O que JWT significa?
- A) Java Web Token
- B) JSON Web Token ✅
- C) JavaScript Web Tool
- D) JSON Website Token

**P2.** JWT é composto por quantas partes?
- A) 2
- B) 3 ✅
- C) 4
- D) 5

**P3.** Qual parte do JWT contém as claims/informações?
- A) Header
- B) Payload ✅
- C) Signature
- D) Base64

**P4.** Para que serve `@jwt_required()`?
- A) Cria um JWT
- B) Proteger rotas que precisam de autenticação ✅
- C) Decodificar JWT
- D) Validar email

**P5.** Qual biblioteca é usada para fazer hash de senhas?
- A) `jsonwebtoken`
- B) `bcryptjs` ✅
- C) `jwt`
- D) `crypto`

---

## Kahoot 7: Boas Práticas e Estrutura (Aula 7)

**P1.** Qual extensão valida dados em Express?
- A) `zod` ou `joi` ✅
- B) `express`
- C) `json`
- D) `body-parser`

**P2.** O que CORS protege contra?
- A) Ataques de força bruta
- B) Requisições de origens não autorizadas ✅
- C) Injeção de SQL
- D) XSS

**P3.** Onde deve-se armazenar segredos (como chaves JWT)?
- A) No código
- B) Em variáveis de ambiente ✅
- C) Em comentários
- D) Em arquivos públicos

**P4.** O que `morgan` faz?
- A) Criptografa dados
- B) Log de requisições HTTP ✅
- C) Valida tokens
- D) Cria rotas

---

**Total: 35 perguntas em 7 Kahoot(s)**
