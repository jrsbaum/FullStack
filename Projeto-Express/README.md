# Projeto com express

### Este é um passo a passo para criar projetos full stack com express. Usarei como anotacao para futuros projetos.

## adicione package-json ao projeto com:

`npm init -y` (flag -y para aceitar tudo)

## adicione express ao projeto com:

`npm install express`

## adicione nodemon ao projeto com:

`npm install nodemon ----save-dev`

## adicione scripts na pasta package.json

```
"scripts": {
"dev": "nodemon index",
"start": "node index"
```

## exemplo de arquivo index.js e codigo

```
const express = require("express");

const servidor = express();

servidor.listen(4000, () => {
  console.log("O servidor ta rodando!");
});
```

## adicionando banco de dados `nedb` ao projeto

`npm install nedb`

## adicione essas duas const no comeco de index.js

```
const NeDB = require("nedb");
const db = new NeDB({
  filename: "products.db",
  autoload: true,
});
```

## o index.js deve ficar assim quando pronto pra listar produtos:

```
const { json } = require("express");
const express = require("express");
const servidor = express();
const NeDB = require("nedb");
const db = new NeDB({
  filename: "products.db",
  autoload: true,
});

servidor.get("/products", (request, response) => {
  db.find({}).exec((erro, dados) => {
    if (erro) {
      console.error(erro);
    } else {
      response.json(dados);
    }
  });
});

servidor.listen(4000, () => {
  console.log("O servidor ta rodando!");
});
```

## podemos mudar o request e response para apenas req e res

## adicione uma nova rota post:

```
servidor.post("/products", (req, res) => {
  res.json(req.body);
});
```

## adicione um middleware abaixo das const para o express "aprender a ler" nosso request:

```
servidor.use(express.json());
servidor.use(express.urlencoded({ extended: true }));
```

## agora precisamos pegar as informacoes e conversar com o banco de dados:

```
servidor.post("/products", (req, res) => {
  db.insert(req.body, (erro, newProduct) => {
    if (erro) {
      console.error(erro);
    } else {
      res.setHeader("Content-Type", "application/json");
      res.status(200).json(newProduct);
    }
  });
});
```

## o codigo do programa ja estara apto a salvar/inserir dados e ficara assim:

```
const { json } = require("express");
const express = require("express");
const servidor = express();
const NeDB = require("nedb");
const db = new NeDB({
  filename: "products.db",
  autoload: true,
});

servidor.use(express.json());
servidor.use(express.urlencoded({ extended: true }));

servidor.get("/products", (req, res) => {
  db.find({}).exec((erro, dados) => {
    if (erro) {
      console.error(erro);
    } else {
      res.json(dados);
    }
  });
});

servidor.post("/products", (req, res) => {
  db.insert(req.body, (erro, newProduct) => {
    if (erro) {
      console.error(erro);
    } else {
      res.setHeader("Content-Type", "application/json");
      res.status(200).json(newProduct);
    }
  });
});

servidor.listen(4000, () => {
  console.log("O servidor ta rodando!");
});

```

## seguindo a mesma logica, vamos alterar um produto porem o id permanece o mesmo. para isso usamos o metodo put:

```
servidor.put("/products/:id", (req, res) => {
  db.update({ _id: req.params.id }, req.body, (erro) => {
    if (erro) {
      console.error(erro);
    } else {
      res.setHeader("Content-Type", "application/json");
      res.status(200).json({
        mensagem: `Produto atualizado com sucesso: ${req.params.id}`,
      });
    }
  });
});

```

## e se quisermos listar apenas um item, podemos copiar o metodo get feito anteriormente e fazer algumas mudancas:

#### `servidor.get("/products"` -> `servidor.get("/products/:id"`

#### `db.find({}).exec((erro, dados)` -> `db.findOne({ _id: req.params.id }).exec((erro, dados)`

## o codigo entao ficara assim:

```
servidor.get("/produtos/:id", (req, res) => {
  db.findOne({ _id: req.params.id }).exec((erro, dados) => {
    if (erro) {
      console.error(erro);
    } else {
      res.setHeader("Content-Type", "application/json");
      res.status(200).json(dados);
    }
  });
});
```
