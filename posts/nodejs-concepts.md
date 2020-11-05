---
title: 'Conceitos NodeJS'
date: '2020-11-04'
---

O node.js nos permite rodar **javascript** no backend.

O backend é onde contém a regra de negocio da aplicação, nele, não lidamos com eventos do usuário (isso ocorre no frontend), mas sim ao acessar as rotas (url), que ao ser acessada, códigos são executados e retorna algo.

Nodejs não é uma linguagem, ele utiliza a linguagem javascript. É considerada uma plataforma pra desenvolvimento backend.

Ele é construído em cima da engine V8 (motor que interpreta javascript)

Comparável a PHP, Ruby, Python, GO

NPM (Node Package Manager) - Gerenciador de pacotes que nos permite instalar libs de terceiros.

YARN - mais rápido e avança mais rápido que o NPM, também é um package manager

Comparável a composer do PHP, PIP do Python, Gems do Ruby

## Características do Nodejs

Arquitetura Event-Loop

- Baseada em eventos (rotas na maioria das vezes) -
- Call Stack - pilha de eventos, basicamente o nodejs fica rodando em loop vendo se alguma função (evento) foi disparada da Call Stack e ai executa em formato de pilha. LIFO (last-in-first-out)
- Single-thread - utiliza apenas uma thread do processador para seu processamento, porém o nodejs roda C++ por trás com uma lib chamada libuv, e isso permite o nodejs executar outras threads em background
- Non-blocking I/O (Input e output não bloqueante) - ao fazer uma requisição pro node, não precisa retornar toda a listagem de uma vez, mas podemos retornar em partes. Dar uma resposta para o cliente, não significa que o resultado será bloqueado. por exemplo, um cliente solicita um uma listagem de todos os estados do brasil que será retornado em json, essa requisição pode ser quebrada e retornada em partes, e se outra pessoa solicitar outra requisição nesse mesmo momento que está retornando a primeira, o nodejs consegue receber mais requisições e ir entregando em partes, ou seja, ele não bloqueia a segunda requisição para poder terminar a primeira.

### Frameworks

- ExpressJS
  - Sem opinião, ou seja, temos total liberdade, não tem uma estrutura fechada, podemos criar a estrutura que quisermos
  - Micro-serviços - Consiste separar sua aplicação em serviços menores
- Frameworks Opinados - são frameworks que já vem com uma estrutura pronta (fechada) feito para dar uma maior produtividade ao desenvolvedor
  - AdonisJS
  - NestJS

---

# API REST

Fluxo de requisição e resposta

- requisição feita por um cliente
- Resposta retornada através de uma estrutura de dados (JSON)
- cliente recebe resposta e processa o resultado

Nesse caso o cliente retorna apenas os dados, e não a aplicação completa com HTML, CSS, JS

As rotas utilizam métodos HTTP

![https://github.com/marcelogaldino/nextjs-blog/blob/feat/new_post/public/images/metodos-http.png](https://github.com/marcelogaldino/nextjs-blog/blob/feat/new_post/public/images/metodos-http.png)

Benefícios da API REST

- Múltiplos clientes (front-end), mesmo backend
- Protocolo de comunicação padronizado
  - Mesma estrutura para web /mobile / API publica
  - Comunicação com serviços externos

Toda a linguagem que utiliza o conceito de API REST utiliza o formato JSON (JavaScript Object Notation) como padrão

Conteúdo da requisição

![https://raw.githubusercontent.com/marcelogaldino/nextjs-blog/feat/new_post/public/images/request-content.png](https://raw.githubusercontent.com/marcelogaldino/nextjs-blog/feat/new_post/public/images/request-content.png)

Nos métodos POST e PUT, quando queremos criar ou editar alguma informação, usamos o body (corpo da requisição) para trafegar os dados

![https://raw.githubusercontent.com/marcelogaldino/nextjs-blog/feat/new_post/public/images/post-put.png](https://raw.githubusercontent.com/marcelogaldino/nextjs-blog/feat/new_post/public/images/post-put.png)

Dessa forma os dados ficam protegidos, ao invés de visíveis na url

Junto com as requisições, temos o HEADER (cabeçalho da requisição) usado geralmente para enviar informações adicionais, autenticação, formatação... Elas não influenciam nos dados que estamos enviando.

HTTP codes

![https://raw.githubusercontent.com/marcelogaldino/nextjs-blog/feat/new_post/public/images/http-codes.png](https://raw.githubusercontent.com/marcelogaldino/nextjs-blog/feat/new_post/public/images/http-codes.png)

# Métodos HTTP

GET → é utilizado quando queremos buscar informações do backend

POST → quando queremos criar uma informação no backend

PUT → quando queremos atualizar uma informação no backend
PATCH → é utilizado também para atualizar, mas para atualizar apenas um dado específico

DELETE → quando queremos deletar uma informação do backend

Usando métodos HTTP

```jsx
const express = require('express');
const server = express();

// Lista todos os projetos
server.get('/projects', (request, response) => {
  return response.json(['projeto 1', 'projeto 2']);
});

// Cria um novo projeto
server.post('/projects', (request, response) => {
  return response.json(['projeto 1', 'projeto 2', 'projeto 3']);
});

// Atualiza um projeto específico,
// passando um route params para identificar o projeto
server.put('/projects/:id', (request, response) => {
  return response.json(['projeto 4', 'projeto 2', 'projeto 3']);
});

// Deleta um projeto, identifica o projeto
// a ser deletado da mesma forma do PUT
server.delete('/projects/:id', (request, response) => {
  return response.json(['projeto 2', 'projeto 3']);
});

// Inicia o servidor na porta 3333
server.listen(3333, () => {
  console.log('🚀 Server is running');
});
```

# Tipos de parâmetros

Query Params → utilizados principalmente para filtros e paginação

```jsx
// Exemplo de query params
// http://localhost:3333/projects?title=React&owner=Marcelo
// estrutura do query params ?title=React&owner=Marcelo

server.get('/projects', (request, response) => {
  const { title, owner } = request.query;
  // ou
  const query = request.query;

  console.log(query);
  // ou
  console.log(title, owner);
  // saida esperada
  // { title: "React", owner: "Marcelo" }
  return response.json(['projeto 1', 'projeto 2']);
});
```

Route Params → Identificar recursos ao atualizar ou deletar

```jsx
// projects é o recurso
// id é o parametro da rota
// http://localhost:3333/projects/1

server.put('/projects/:id', (request, response) => {
  const { id } = request.params;

  console.log(id);
  // saida esperada
  // 1
  return response.json(['projeto 4', 'projeto 2', 'projeto 3']);
});
```

Request Body → Contem o conteúdo na hora de criar ou editar um recurso

```jsx
server.post('/projects', (request, response) => {
  const { title, owner } = request.body;

  console.log(title, owner);
  // saida esperada
  // React, Marcelo
  return response.json(['projeto 1', 'projeto 2', 'projeto 3']);
});
```

# Middlewares

Middleware é um interceptador de requisições, ele pode:

1. Interromper totalmente uma requisição
2. Alterar dados da requisição, antes dela ser retornada pelo usuário

Um middleware é uma função

```jsx
// Criando um middleware
function logRequests(request, response, next) {
  const { method, url } = request;

  const label = `[${method.toUpperCase()}] ${url}`;

  console.log(label);

  return next(); // Vai para o proximo middleware, no caso as rotas
}
```

O middleware pode ser usado de modo geral com o

```jsx
app.use(middleware);
// isso significa que em qualquer rota ele sera chamado
```

ou podemos passar em rotas especificas um ou mais middlewares

```jsx
app.get(
  '/projects',
  logRequests,
  middleware1,
  middleware2,
  (request, response) => {
    const { title } = request.query;

    // filtro via query params
    const results = title
      ? projects.filter((project) => project.title.includes(title))
      : projects;

    return response.json(results);
  }
);
```

Utilizando uma função como middleware, vamos descobrir o tempo em ms que uma requisição demora pra retornar

```jsx
const express = require('express');
const { uuid } = require('uuidv4');

const app = express();

app.use(express.json());

const projects = [];

function logRequests(request, response, next) {
  const { method, url } = request;

  const label = `[${method.toUpperCase()}] ${url}`;

  console.time(label);

  next(); // Vai para o proximo middleware, no caso as rotas

  console.timeEnd(label);
}

app.use(logRequests);

app.get('/projects', (request, response) => {
  const { title } = request.query;

  // filtro via query params
  const results = title
    ? projects.filter((project) => project.title.includes(title))
    : projects;

  return response.json(results);
});

app.post('/projects', (request, response) => {
  const { title, owner } = request.body;

  const project = { id: uuid(), title, owner };

  projects.push(project);

  return response.json(project);
});

app.put('/projects/:id', (request, response) => {
  const { id } = request.params;
  const { title, owner } = request.body;

  const projectIndex = projects.findIndex((project) => project.id === id);

  if (projectIndex < 0) {
    return response.status(400).json({ message: 'Projeto não encontrado' });
  }

  const project = {
    id,
    title,
    owner,
  };

  projects[projectIndex] = project;

  return response.json(project);
});

app.delete('/projects/:id', (request, response) => {
  const { id } = request.params;

  const projectIndex = projects.findIndex((project) => project.id === id);

  if (projectIndex < 0) {
    return response.status(400).json({ message: 'Projeto não encontrado' });
  }

  projects.splice(projectIndex, 1);

  return response.status(204).send();
});

app.listen(3333, () => {
  console.log('🚀 Server is running');
});

// saida
/*
[GET] /projects?title=React: 4.718ms
[POST] /projects/: 0.906ms
*/
```
