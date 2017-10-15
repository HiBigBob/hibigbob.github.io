---
layout: post
title:  "Comment créer une commande dans une API NodeJs ?"
date:   2017-09-05 09:40:36 +0200
tags: ["NodeJs", "API", "PM2", "PMX", "Commande", "Docker"]
---

# Introduction

Dans de nombreux language web, la création de commande est facilement réalisable. Elle peut être utilisé pour initialiser une base de données par exemple.
Dans cet article, nous allons voir comment créer une commande lorsque l'on est dans une API NodeJs.

# Le fonctionnement

On va partir d'une API NodeJs réalisé avec ExpressJs et qui est éxecuté grâce au package PM2.
A côté des routes, l'api va avoir des actions qui pourront être éxecuté uniquement par PM2 grâce au package PMX.

# Le code

Prérequis : uniquement Docker

Pour tester l'exemple, vous pouvez cloner le projet
```
git clone
```

Puis, lancez l'installation

```
cd api_command
docker-compose up -d
```

# L'API

On initialise l'API reste basique :

```
// index.js
'use strict';

const express = require('express');
const app = express();
const port = process.env.PORT || 4001;

// Import command file
const command = require('./command.js');

app.get('/', (req, res) => {
  res.json({
    status: 'success',
  });
});

app.listen(port, () => {
  console.log('Server started: ' + port);
});
```

Les commandes sont géré via PMX :

```
// command.js
'use strict'

const pmx = require('pmx');

pmx.action('db:dump', function(opt, reply) {

  // Execute dump of db

  reply({success: true, message: {
    opt
  }});
});
```

# L'exécution

Maintenant que le code est mis en place, on peut tester de la manière suivante :

Commande sans paramétre

```
docker-compose exec api pm2 trigger API db:dump
```

Commande avec paramétre

```
docker-compose exec api pm2 trigger API db:dump test=1
```

# Conclusion

L'objectif de cet article était de montrer rapidement comment on peut gérer La gestion des commandes dans une API NodeJs.

