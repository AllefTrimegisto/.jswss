# .jswss

No Node.js, faça o código de um controller que deve implementar os quatro pontos do CRUD, como no exemplo apresentado no hipertexto. 

No entanto, ao invés de salvar na memória, utilize um banco de sua escolha para a persistência dos dados.


const express = require('express');
const router = express.Router();
const mongodb = require('mongodb');
const MongoClient = mongodb.MongoClient;
const uri = "mongodb+srv://<username>:<password>@cluster0.mongodb.net/test?retryWrites=true&w=majority";

let client;

// Conecta ao banco de dados MongoDB
MongoClient.connect(uri, { useNewUrlParser: true }, (err, db) => {
  if (err) {
    console.log(err);
  } else {
    client = db;
  }
});

// Rota para criar um novo recurso
router.post('/', (req, res) => {
  const database = client.db("test");
  const collection = database.collection("resources");

  collection.insertOne(req.body, (err, result) => {
    if (err) {
      res.status(500).send({ error: "Erro ao salvar recurso" });
    } else {
      res.status(201).send({ message: "Recurso criado com sucesso" });
    }
  });
});

// Rota para obter um recurso
router.get('/:id', (req, res) => {
  const database = client.db("test");
  const collection = database.collection("resources");

  collection.findOne({ _id: new mongodb.ObjectID(req.params.id) }, (err, result) => {
    if (err) {
      res.status(500).send({ error: "Erro ao obter recurso" });
    } else if (!result) {
      res.status(404).send({ error: "Recurso não encontrado" });
    } else {
      res.send(result);
    }
  });
});

// Rota para atualizar um recurso
router.put('/:id', (req, res) => {
  const database = client.db("test");
  const collection = database.collection("resources");

  collection.updateOne({ _id: new mongodb.ObjectID(req.params.id) }, { $set: req.body }, (err, result) => {
    if (err) {
      res.status(500).send({ error: "Erro ao atualizar recurso" });
    } else if (result.modifiedCount === 0) {
      res.status(404).send({ error: "Recurso não encontrado" });
    } else {
      res.send({ message: "Recurso atualizado com sucesso" });
    }
  });
});

// Rota para excluir um recurso
router.delete('/:id', (req, res) => {
  const database = client.db("test");
  const collection = database.collection("
