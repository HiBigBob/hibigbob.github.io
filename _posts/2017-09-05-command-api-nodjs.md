---
layout: post
title:  "Comment créer une commande dans une API NodeJs ?"
date:   2017-09-05 09:40:36 +0200
tags: ["NodeJs", "API", "PM2", "PMX", "Commande", "Docker"]
---

# Introduction

Dans de nombreux language web, la création de commande est courante, elle peut être utilisé pour initialiser une base de données par exemple.
Dans cet article, nous allons voir comment créer une commande lorsque l'on est dans une API NodeJs.

# Le fonctionnement

On va partir d'une API NodeJs réalisé avec ExpressJs et qui est éxecuté grâce a PM2. A côté des routes, l'api va avoir des actions qui pourront être éxecuté uniquement par PM2.

# L'API

On initialise l'API avec le code suivant :

```

```

Les commandes sont géré via le package PMX de la manière suivante :

```

```

# L'exécution

Maintenant que le code est mis en place, la commande se lance 

```
docker-compose exec api pm2 trigger API db:dump
```

# Conclusion


