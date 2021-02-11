# Construindo sexy APIs usando arquitetura serverless

#### Serverless?

- Vou rodar uma função
- Vou pagar o quanto eu uso ou não
- Vou aumentar o número de fn com um click
- Vou ter facilidade no bootstraping
- Vou ter mais facilidade de por outros triggers

**Azure Functions**

AWS Lambda, Google Cloud Functions, Open Whisk

**Por que usar?**

Criar APIs

- listar
- criar
- update
- delete

Precisa de uma conta de Microsoft Azure;

https://azure.microsoft.com/

Extensão do VSCode, Azure Functions

Azure Functions Azure Tools

#### Pré-requisitos

Para executar os comandos você vai precisar do CLI do Azure instalado localmente.

https://docs.microsoft.com/pt-br/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash

**Iniciando o Projeto**

`func init`

-- node

-- javascript

**Criar uma função**

`func new`

8 Http trigger

Function Name: GetProducts

**No VSCode**

dar sim na notificação

No index.js

*Parte 3*

Pelo terminal

`func host start`



**Teste pelo Postman**

Listagem de produtos

localhost:7071/api/products

```js
const createMongoClient = require('../shared/mongoClient');

module.exports = async context => {
  const { client: MongoClient, closeConnectionFn } = await createMongoClient();
  const Products = MongoClient.collection('products');
  const res = await Products.find({});
  const body = await res.toArray();
  
  closeConnectionFn();
  context.res = { status: 200, body };
};
```

**Instalação da dependencia**

`npm i mongodb`

`func host start`

**Para criar produtos**

`func new`

8

CreateProduct

o mesmo para UpdateProducts

```js
const createMongoClient = require('../shared/mongoClient');

module.exports = async function (context, req) {
  const product = req.body || {};

  if (product) {
    context.res = {
      status: 400,
      body: 'Product is required',
    };
  }

  const { client: MongoClient, closeConnectionFn } = await createMongoClient();
  const Products = MongoClient.collection('products');

  try {
    const products = await Products.insert(product);
    closeConnectionFn();
    context.res = { status: 201, body: products.ops[0] };
  } catch (error) {
    context.res = {
      status: 500,
      body: 'Error on insert product',
    }; 
  }
};
```

**Para deletar**

localhost:7071/api/products

**Deploy na Azure**

https://azure.microsoft.com/

**Estrutura**

```
├── CreateProduct
│   ├── function.json
│   └── index.js
├── DeleteProduct
│   ├── function.json
│   └── index.js
├── GetProductById
│   ├── function.json
│   └── index.js
└── GetProduct
    ├── function.json
    └── index.js
```

Arquivo mongoClient.js

```js
const { MongoClient } = require('mongodb');
const config = {
  url: 'mongodb+srv://god:dog@cluster0-dfsvs.mongodb.net/dgo?retryWrites=true&w=majority',
};

module.exports = () => new Promise((resolve, reject) => {
  MongoClient
    .connect(config.url, { useNewUrlParser: true }, (err, mongoConnection) =>
      err
      ? reject(err)
      : resolve({
          client: mongoConnection.db(config.dbName),
          closeConnectionFn: () => setTimeout(() => {
            mongoConnection.close();
          }, 1000),
          mongoConnection,
        })
    );
});
```

