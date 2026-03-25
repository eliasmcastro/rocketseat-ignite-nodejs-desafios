<h1 align="center">
  <img alt="Ignite" src=".github/logo_ignite.png" width="200px" />
</h1>

<h3 align="center">
  Desafio: Conceitos do Node.js
</h3>

<p align="center">Criação de uma aplicação para gerenciar tarefas (todos) utilizando o Node.js</p>

<p align="center">
  <a href="#como-executar-o-projeto">Como executar o projeto</a>&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;
  <a href="#sobre-o-desafio">Sobre o Desafio</a>
</p>

<p align="center">Back-end</p>

<p align="center">
  <img alt="Back-end" src=".github/backend.png" width="90%">
</p>

## Como executar o projeto

### Requisitos

- [Node.js](https://nodejs.org) na versão 22.21.1
- [Yarn](https://yarnpkg.com) na versão 1.22.5

#### Opcional

- [Insomnia](https://insomnia.rest)

### Passos para a execução

**1. Executar aplicação**

Instalar as dependências do projeto

```bash
yarn
```

Iniciar o servidor de desenvolvimento

```bash
yarn dev
```

A aplicação começará a ser executada em http://localhost:3333

_Dica: utilizar o Insomnia para testar as rotas_

- Abrir o Insomnia -> Application -> Preferences -> Data -> Import Data -> From File -> Selecionar o arquivo insomnia.json

### Testes automatizados

Para começar a utilizar os testes, execute o comando `yarn test`, e ele irá te retornar o resultado dos testes

_Dica: se utilizar o comando `yarn test --watchAll`, o mesmo fica realizando automaticamente os testes toda vez que o arquivo app.js é alterado_

## Sobre o desafio

### Funcionalidades

- Criação de um usuário com `name` e `username`
- Criar um novo *todo*
- Listar todos os *todos*
- Alterar o `title` e `deadline` de um *todo* existente
- Marcar um *todo* como feito
- Excluir um *todo*

Tudo isso para cada usuário em específico (o `username` deve ser passado pelo header)

### Rotas da aplicação

#### POST `/users`

A rota deve receber `name`, e `username` dentro do corpo da requisição. Ao cadastrar um novo usuário, ele deve ser armazenado dentro de um objeto no seguinte formato:

```jsx
{ 
	id: 'uuid', // precisa ser um uuid
	name: 'Danilo Vieira', 
	username: 'danilo', 
	todos: []
}
```

Certifique-se que o ID seja um UUID, e de sempre iniciar a lista `todos` como um array vazio. O objeto do usuário deve ser retornado na resposta da requisição.

#### GET `/todos`

A rota deve receber, pelo header da requisição, uma propriedade `username` contendo o username do usuário e retornar uma lista com todas as tarefas desse usuário.

#### POST `/todos`

A rota deve receber `title` e `deadline` dentro do corpo da requisição e, uma propriedade `username` contendo o username do usuário dentro do header da requisição. Ao criar um novo *todo*, ele deve ser armazenada dentro da lista `todos` do usuário que está criando essa tarefa. Cada tarefa deverá estar no seguinte formato:

```jsx
{ 
	id: 'uuid', // precisa ser um uuid
	title: 'Nome da tarefa',
	done: false, 
	deadline: new Date(deadline), 
	created_at: new Date()
}
```

**Observação**: Lembre-se de iniciar a propriedade `done` sempre como `false` ao criar um *todo*.

**Dica 1**: Ao fazer a requisição com o Insomnia ou Postman, preencha a data de `deadline` com o formato `ANO-MÊS-DIA`

**Dica 2**: Usar new Date(deadline) irá realizar a transformação da string "ANO-MÊS-DIA" (por exemplo "2021-02-25") para uma data válida do JavaScript.

O objeto do todo deve ser retornado na resposta da requisição.

#### PUT `/todos/:id`

A rota deve receber, pelo header da requisição, uma propriedade `username` contendo o username do usuário e receber as propriedades `title` e `deadline` dentro do corpo. É preciso alterar **apenas** o `title` e o `deadline` da tarefa que possua o `id` igual ao `id` presente nos parâmetros da rota.

#### PATCH `/todos/:id/done`

A rota deve receber, pelo header da requisição, uma propriedade `username` contendo o username do usuário e alterar a propriedade `done` para `true` no *todo* que possuir um `id` igual ao `id` presente nos parâmetros da rota.

#### DELETE `/todos/:id`

A rota deve receber, pelo header da requisição, uma propriedade `username` contendo o username do usuário e excluir o *todo* que possuir um `id` igual ao `id` presente nos parâmetros da rota.

### Específicação dos testes

#### Testes de usuários

- **Should be able to create a new user**

Para que esse teste passe, você deve permitir que um usuário seja criado e retorne um JSON com o usuário criado.

Também é necessário que você retorne a resposta com o código `201`.

- **Should not be able to create a new user when username already exists**

Para que esse teste passe, antes de criar um usuário você deve validar se outro usuário com o mesmo `username` já existe. Caso exista, retorne uma resposta com status `400` e um json no seguinte formato:

```jsx
{
	error: 'Mensagem do erro'
}
```

#### Testes de *todos*

**Middleware**

Para completar todos os testes referentes à *todos* é necessário antes ter completado o código que falta no middleware `checkExistsUserAccount`. Para isso, você deve pegar o `username` do usuário no header da requisição, verificar se esse usuário existe e então colocar esse usuário dentro da `request` antes de chamar a função `next`. Caso o usuário não seja encontrado, você deve retornar uma resposta contendo status `404` e um json no seguinte formato:

```jsx
{
	error: 'Mensagem do erro'
}
```

- **Should be able to list all user's todos**

Para que esse teste passe, na rota GET `/todos` é necessário pegar o usuário que foi repassado para o `request` no middleware `checkExistsUserAccount` e então retornar a lista `todos` que está no objeto do usuário conforme foi criado.

- **Should be able to create a new todo**

Para que esse teste passe, na rota POST `/todos` é necessário pegar o usuário que foi repassado para o `request` no middleware `checkExistsUserAccount`, pegar também o `title` e o `deadline` do corpo da requisição e adicionar um novo *todo* na lista `todos` que está no objeto do usuário.

Após adicionar o novo *todo* na lista, é necessário retornar um status `201` e o *todo* no corpo da resposta.

- **Should be able to update a todo**

Para que esse teste passe, na rota PUT `/todos/:id` é necessário atualizar um *todo* existente, recebendo o `title` e o `deadline` pelo corpo da requisição e o `id` presente nos parâmetros da rota.

- **Should not be able to update a non existing todo**

Para que esse teste passe, você não deve permitir a atualização de um *todo* que não existe e retornar uma resposta contendo um status `404` e um json no seguinte formato:

```jsx
{
	error: 'Mensagem do erro'
}
```

- **Should be able to mark a todo as done**

Para que esse teste passe, na rota PATCH `/todos/:id/done` você deve mudar a propriedade `done`de um *todo* de `false` para `true`, recebendo o `id` presente nos parâmetros da rota.

- **Should not be able to mark a non existing todo as done**

Para que esse teste passe, você não deve permitir a mudança da propriedade `done` de um *todo* que não existe e retornar uma resposta contendo um status `404` e um json no seguinte formato: 

```jsx
{
	error: 'Mensagem do erro'
}
```

- **Should be able to delete a todo**

Para que esse teste passe, DELETE `/todos/:id` você deve permitir que um *todo* seja excluído usando o `id` passado na rota. O retorno deve ser apenas um status `204` que representa uma resposta sem conteúdo.

- **Should not be able to delete a non existing todo**

Para que esse teste passe, você não deve permitir excluir um *todo* que não exista e retornar uma resposta contendo um status `404` e um json no seguinte formato:

```jsx
{
	error: 'Mensagem do erro'
}
```