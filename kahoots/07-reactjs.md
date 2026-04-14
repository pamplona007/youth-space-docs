# Kahoot — React.js (Módulo 6)

> 35 perguntas alinhadas ao conteúdo de 8 aulas (32h total)

---

## Kahoot 1: React Fundamentos e JSX (Aula 1)

**P1.** O que é JSX?
- A) Uma linguagem de programação
- B) HTML dentro do JavaScript ✅
- C) Um banco de dados
- D) Um tipo de CSS

**P2.** Qual ferramenta é usada para criar um projeto React com Vite?
- A) `npm create vue`
- B) `npm create vite@latest` ✅
- C) `npm start react`
- D) `npx create-react`

**P3.** Como se cria um componente funcional em React?
- A) `function = MeuComponente()`
- B) `function MeuComponente() {}` ✅
- C) `component MeuComponente()`
- D) `class MeuComponente {}`

**P4.** Props são usadas para:
- A) Armazenar estado
- B) Passar dados entre componentes ✅
- C) Criar estilos
- D) Definir rotas

**P5.** Qual método渲染iza uma lista em React?
- A) `forEach()`
- B) `filter()`
- C) `.map()` ✅
- D) `.reduce()`

**P6.** Por que cada item de uma lista precisa de uma "key"?
- A) Para styling
- B) Para React identificar e optimizar renderização ✅
- C) Para animação
- D) Para validação

**P7.** JSX é convertido em qual função React?
- A) `React.createElement()` ✅
- B) `React.render()`
- C) `React.component()`
- D) `React.make()`

---

## Kahoot 2: useState e Estado (Aula 2)

**P1.** O que é "estado" (state) em React?
- A) Estilos CSS
- B) Dados que mudam e fazem componente re-renderizar ✅
- C) Props passadas
- D) URLs

**P2.** Qual hook gerencia estado em componente funcional?
- A) `useProps`
- B) `useState` ✅
- C) `useData`
- D) `useValue`

**P3.** Como atualizar estado com useState?
- A) `state.name = "João"`
- B) `setState({ name: "João" })` ✅
- C) `this.state = { name: "João" }`
- D) `state = { name: "João" }`

**P4.** O que `onClick` faz em React?
- A) Define estilo
- B) Escuta evento de clique ✅
- C) Cria componente
- D) Navega para página

**P5.** Componentes controlados são aqueles onde:
- A) O DOM controla o componente
- B) O React controla o estado do formulário ✅
- C) O navegador controla
- D) CSS controla

---

## Kahoot 3: Renderização Condicional e Listas (Aula 3)

**P1.** Como fazer renderização condicional com `&&`?
- A) `{condition && <Elemento />}` ✅
- B) `{{condition} && <Elemento />}`
- C) `<if condition><Elemento /></if>`
- D) `{condition} ? <Elemento />`

**P2.** Por que evitar usar índice de array como key?
- A) É muito rápido
- B) Pode causar problemas com reordenação/alterações ✅
- C) Não funciona em React
- D) É deprecated

**P3.** Qual operador ternário é usado em JSX?
- A) `??`
- B) `? :` ✅
- C) `&&`
- D) `||`

**P4.** Como ocultar um elemento condicionalmente?
- A) `display: none`
- B) Não renderizar com `{!condition && <Elemento />}` ✅
- C) `hidden: true`
- D) `visible: false`

---

## Kahoot 4: useEffect e Ciclo de Vida (Aula 4)

**P1.** Quando useEffect executa por padrão?
- A) Apenas uma vez
- B) Após toda renderização ✅
- C) Nunca
- D) Apenas no primeiro render

**P2.** O que array vazio `[]` como segundo argumento de useEffect significa?
- A) Executa sempre
- B) Executa apenas uma vez (como mount) ✅
- C) Nunca executa
- D) Executa no unmount

**P3.** Para que serve cleanup em useEffect?
- A) Limpar memória do navegador
- B) Executar lógica quando componente desmonta ✅
- C) Remover o componente
- D) Limpar erros

**P4.** O que useEffect com `[count]` faz?
- A) Executa apenas no mount
- B) Executa quando `count` muda ✅
- C) Nunca executa
- D) Executa sempre

**P5.** Como cancelar requisições fetch em useEffect?
- A) Não é possível
- B) Com AbortController ✅
- C) Com clearInterval
- D) Com useState

---

## Kahoot 5: Context API (Aula 5)

**P1.** Qual problema Context resolve?
- A) Performance
- B) Prop drilling ✅
- C) Styling
- D) Routing

**P2.** Como criar contexto em React?
- A) `new Context()`
- B) `createContext()` ✅
- C) `useContext()`
- D) `Context.Provider()`

**P3.** Como acessar valor de um Context?
- A) `this.context`
- B) `useContext()` ✅
- C) `context.get()`
- D) `Context.use()`

**P4.** Qual componente fornece valor para consumidores?
- A) `Context.Consumer`
- B) `Context.Provider` ✅
- C) `Context.Value`
- D) `Context.Wrap`

---

## Kahoot 6: Hooks Customizados e React Router (Aula 6)

**P1.** Qual é a convenção de nomenclatura para hooks customizados?
- A) `customHook`
- B) `HookName`
- C) `useNomeDoHook` ✅
- D) `NomeDoHook`

**P2.** Qual biblioteca é usada para rotas em React?
- A) `react-router`
- B) `react-router-dom` ✅
- C) `react-navigation`
- D) `react-routes`

**P3.** Qual componente define uma rota?
- A) `Router`
- B) `Route` ✅
- C) `Switch`
- D) `Link`

**P4.** Qual componente cria links sem reload?
- A) `a href`
- B) `Link` ✅
- C) `NavLink` apenas
- D) `Router`

**P5.** Para que serve `useParams`?
- A) Acessar query strings
- B) Acessar parâmetros da URL ✅
- C) Criar parâmetros
- D) Validar rotas

---

## Kahoot 7: Requisições HTTP e Boas Práticas (Aula 7)

**P1.** Qual biblioteca é comum para fazer requisições HTTP em React?
- A) `fetch`
- B) `axios` ✅
- C) `http`
- D) `request`

**P2.** O que são interceptadores de axios?
- A) Bloquear requisições
- B) Modificar requisições/respostas globalmente ✅
- C) Cancelar requisições
- D) Criar URLs

**P3.** O que useFetch deve retornar (mínimo)?
- A) Apenas dados
- B) `data`, `loading`, `error` ✅
- C) Apenas loading
- D) Dados e headers

---

**Total: 33 perguntas em 7 Kahoot(s)**
