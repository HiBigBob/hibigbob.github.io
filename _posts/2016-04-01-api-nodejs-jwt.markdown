---
layout: post
title:  "Création d'une API NodeJS"
date:       2016-04-01 23:07:00
tags:       ['NodeJs', 'JWT', 'Restful', 'ExpressJs', 'MongoDb', débutant, découverte]
---

# Introduction

De nos jours, on peut faire un API avec des nombreux language, framework. Personnellement, j'apprecie beaucoup NodeJS, il correspond bien au besoin d'une API, efficacité et rapidité de réponses aux requêtes.
Pour aller encore plus vite dans la création de notre application, j'utilise Express.js, un micro framework pour NodeJs. 

## Qu'elles sont les fonctionnalité de cette API.

L'idée est de faire une API qui regroupe une série de fonctionnalité d'une TodoList.

Utilisateurs :
- Création, modification et suppression de compte utilisateur
- Authentification basé sur un token (JWT)

Liste de tâches :
- Création, modification et suppression de liste de tâches

Tâches :
- Ajout et suppression de tâche dans une liste
- Gestion de l'état de la tâches (A faire, Fait)
- Modification de la tâche

Tag :
- Ajout et suppression de tag à une tache

# Initialisation du projet

Pour continuer, il faudrait que NodeJS (https://nodejs.org/en/download/) soit installer sur votre poste

Création du répertoire qui contiendra le projet
{% highlight javascript lineanchors %}
$ mkdir api-todo
$ cd api-todo
{% endhighlight %}

Utilisation de la commande npm init pour créer le fichier packages.json. Ce fichier regroupe toutes les informations concernant le projet et ses dépendances.

{% highlight javascript lineanchors %}
$ npm init
{% endhighlight %}

Installation des dépendances de ce projet. Le option --save permet de les sauvegarder dans le fichier packages.json

{% highlight javascript lineanchors %}
$ npm install express mongoose morgan body-parser jwt-simple moment --save
{% endhighlight %}

express : le micro framwork pour NodeJS
mongoose : Interaction avec la base de données MongoDB
morgan : Affichage des requetes dans la console. C'est très pratique.
body-parser : Récuperation les parametres post dans la requête
jwt-simple : Création et vérification du:w token JWT
moment : Gestion des dates

## Structure du projet

Maintenant que les packages sont installés, voici la structure finale du projet

{% highlight javascript lineanchors %}
- app/
----- config/
---------- config.js
---------- routing.js
----- models/
---------- list.js
---------- task.js
---------- tag.js
----- controllers/
---------- auth.js
---------- list.js
---------- task.js
---------- tag.js
----- middlewares/
---------- oauth.js
---------- required.js
- node_modules/
- .gitignore
- package.json
- app.js
{% endhighlight %}

Les besoins du projets peuvent etre créer avec les commandes suivantes :

{% highlight javascript lineanchors %}
$ mkdir -p app/config
$ mkdir app/models
$ mkdir app/controllers
$ mkdir app/middlewares

$ touch app/config/config.js
$ touch app/config/routing.js

$ touch app/models/auth.js
$ touch app/models/list.js
$ touch app/models/task.js
$ touch app/models/tag.js

$ touch app/controllers/auth.js
$ touch app/controllers/list.js
$ touch app/controllers/task.js
$ touch app/controllers/tag.js

$ touch app/middlewares/auth.js
$ touch app/middlewares/required.js
{% endhighlight %}


# L'application (app.js)

Ce fichier correspond à notre API, il est le point central de l'application. A tel point que c'est lui seul que nous allons lancé (avec la commande 'node app.js') pour démarrer notre API.

Ce fichier regroupe l'initialisation et la configuration :
 - des différents packages utilisés
 - du serveur de base de données (MongoDb)
 - du serveur NodeJs
 - des routes de l'application.

Regardons un peu le contenu de ce fichier.

{% highlight javascript lineanchors %}

// ====================================
// Liste des packages
// ====================================
var express     = require('express');
var path        = require('path');
var logger      = require('morgan');
var bodyParser  = require('body-parser');
var mongoose    = require('mongoose');
var jwt         = require('jwt-simple');

// Prise en charge du fichier de configuration
var config      = require('./app/config/config');

// ====================================
// Configuration
// ====================================
var app = express();
var port = process.env.PORT || 4000;
app.set('jwtTokenSecret', config.secret);

// ====================================
// Base de données
// ====================================
mongoose.connect(config.database);
var db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error:'));
db.once('open', function (callback) {
  console.log("Connected !");
});

// ====================================
// Initialisation
// ====================================

// Prise en charge des packages
app.use(logger('dev'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));

// Paramétrage du header
app.use(function(req, res, next) {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Methods", "GET,POST,PUT,DELETE");
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept, x-access-token");
  next();
});

// Pris en charge du router
var routing = require('./config/routing')
routing.set(app);

// ====================================
// Démarrage du serveur
// ====================================
app.listen(port);

module.exports = app;

{% endhighlight %}

Maintenant que nous avons un exemple, nous allons pouvoir aborder le fonctionnement de Express.Js

### Précisions sur le fonctionnement d'Express.Js

Express.js est basé sur le concept très intéressants de middlewares. Ce sont des fonctionnalités de l'application qui rendent chacun un service spécifique. Vous pouvez charger uniquement les middlewares dont vous avez besoin, ou chainer plusieurs middlewares entre eux.

Pour utiliser un middleware, il suffit d'appeler la méthode app.use(). 
Dans le app.js, on constate qu'au niveau de la prise en charge des packages que l'on a des "use" de différents packages. Cela signifie que pour chaque requêtes, je vais appliqué chaque fonctionnalité en chaine (par exemple : "logger" puis "bodeparser")

## Les fonctionnalitées

Avant de démarrer notre serveur Node, il faut mettre en place certaines fonctionnalités.

### Le fichier de configuration

Mise en place du fichier de configuration, il contient la chaine de connexion à votre serveur mongodb et une valeur secrete. 
Elle sera utilisé plus tard pour générer un token de connexion.

{% highlight javascript lineanchors %}
// app/config/config.js
module.exports = {
    'secret': 'YOUR_SECRET_STRING',
    'database': 'mongodb://localhost:27017/api-todo'
};
{% endhighlight %}

### Le router

On peut assimiler les controlleurs a des middlewares que l'on va appliquer uniquement sur une route

{% highlight javascript lineanchors %}
// app/config/routing.js

// ========================================
// Import des controlleurs
// ========================================
var user          = require('../controllers/user');
var fixture       = require('../controllers/fixture');
var authenticate  = require('../controllers/auth');
var post          = require('../controllers/post');
var tag           = require('../controllers/tag');

// ========================================
// Import des middlewares spécifiques
// ========================================
var auth          = require('../middlewares/auth');
var required      = require('../middlewares/required');

// ========================================
// Liste des routes avec leurs controlleurs
// ========================================
module.exports.set  = function(app) {
  app.use('/authenticate', authenticate);
  app.use('/fixture', fixture);

  // Routes protégés par un token
  app.use('/users', [auth, require], user);
  app.use('/lists', [auth, required], list);
  app.use('/tasks', [auth, required], task);
  app.use('/tags',  [auth, required], tag);
}

{% endhighlight %}

Vous remarquerez que l'on applique une syntaxe particulière sur les routes protégé par un token.

Mais à quoi sert auth et required ?

On a vu que j'assimilé les controlleurs comme des middlewares que l'on applique à une route, mais on peut aussi appeler d'autres fonctionnalités avant de rentrer dans le controlleur.
Dans notre cas, je veux vérifier, grâce au middleware "auth", pour chaque requête à la route "/users", l'existance d'un token d'authentification dans la requête. Donc si un token est présent, je vais chercher l'utilisateur correspondant, puis je le rajoute dans la requête. Ensuite, je passe au deuxième middleware "required", il a pour rôle de vérifier l'exitance d'un utilisateur dans la requête, si ce n'est pas le cas, il renvoie une réponse 401 (Not authorized), sinon il rentre dans le controlleur.

Avec ce fonctionnement, vous pourrez vous authentifier à l'API, cela vous permettra de recupérer vos listes, vos taches et vos informations utilisateur.

### Les middlewares

Nous venons de voir que l'on avait deux middlewares maisons sur notre API.

Dans un premier temps, on cherche à s'authentifier de la maniere suivante :

**Auth**

{% highlight javascript lineanchors %}
// app/middlewares/oauth.js
var url       = require('url')
var user      = require('../models/user')
var jwt       = require('jwt-simple');
var config    = require('../config/config');

module.exports = function(req, res, next){
  var parsed_url = url.parse(req.url, true)


   <!-- Take the token from:

    - the POST value access_token
    - the GET parameter access_token
    - the x-access-token header
      ...in that order.
   -->
  var token = (req.body && req.body.access_token) || parsed_url.query.access_token || req.headers["x-access-token"];

  if (token) {
    try {
      var decoded = jwt.decode(token, config.secret)

      // Est ce que le token a expiré
      if (decoded.exp <= Date.now()) {
        res.end('Access token has expired', 400)
      }

      user.findOne({ '_id': decoded.iss }, function(err, user){
        // Pas d'erreur, j'ajoute l'utilisateur à la requête et je passe à la suite
        if (!err) {
          req.user = user
          return next()
        }
      })
    } catch (err) {
      return next()
    }
  } else {
    next()
  }
}

{% endhighlight %}

Une fois que l'on est passé par le middleware auth, on vérifie si on est authentifié grâce au middleware required.

**Required**

{% highlight javascript lineanchors %}
//app/middlewares/required.js
module.exports = function(req, res, next){
  if (!req.user) {
    res.end('Not authorized', 401)
  } else {
    next()
  }
}
{% endhighlight %}

### Les controlleurs

On va commencer par voir comment s'authentifier à l'API.

**Authentification**

{% highlight javascript lineanchors %}
var express     = require('express');
var router      = express.Router();
var UserModel   = require('../models/user');
var jwt         = require('jwt-simple');
var moment      = require('moment');
var config      = require('../config/config');

router.post('/', function(req, res){

  //Vérification de la présence des parametres dans la requête
  if (req.body.username && req.body.password) {

    // Récupération de l'utilisateur
    UserModel.findOne({ username: req.body.username }, function(err, user) {
      if (err) {
        res.status(401).json({ error: 'Authentication error' })
      }

      if (user.password != req.body.password) {
        res.status(401).json({ error: 'Authentication failed. Wrong password.' });
      } else {

          // L'utilisateur est authentifié avec success, donc on peut générer et renvoyer le token.
          var expires = moment().add(1, 'days')
          var token = jwt.encode(
            {
              iss: user.id,
              exp: expires
            },
            config.secret
          );
          user.password = '';

          // On retourne le token et l'utilisateur
          res.json({
            user : user,
            access_token : {
              token : token,
              expires : expires
            }
          });
      }
    });
  } else {
    res.status(401).json({ error: 'Authentication errors' })
  }
});

module.exports = router;
{% endhighlight %}

**Informations utilisateur**

{% highlight javascript lineanchors %}
var express       = require('express');
var router        = express.Router();
var auth          = require('../middlewares/auth');
var required      = require('../middlewares/required');

router.get('/', auth, required, function(req, res){
  // On retourne l'utilisateur ajouter dans la requête par les middlewares "auth".
  res.json(req.user);
});

module.exports = router;
{% endhighlight %}

**Liste de tâches**

{% highlight javascript lineanchors %}
var express     = require('express');
var router      = express.Router();
var Post        = require('../models/post');
var auth        = require('../middlewares/auth');
var required    = require('../middlewares/require');
var getSlug     = require('../utils/slug');

router.param("id", function (req, res, next, value) {
  var query = {_id: value};
  if (value.match('^([a-z]*-[a-z]*)')) {
    query = {slug: value};
  };

  Post.findOne(query, function(err, post) {
    if (err) { res.status(403).json({error: 'Not found'}); }
    req.post = post;
    next();
  })
});

router.get('/', function(req, res, next){
  Post
    .find({}, function (err, posts) {
      if (err) return next(new Error(err));
      res.json(posts);
    }
  );
});

router.get('/:id', function(req, res){
    res.json(req.post);
});

router.post('/', auth, required, function(req, res, next){
  if (!req.body.title && !req.body.content) return next(new Error('No data provided.'));

  var post = new Post({
    userId: req.user._id,
    title: req.body.title,
    slug: getSlug(req.body.title),
    content: req.body.content,
  });

  post.save(function(err) {
    if (err) console.log(err);
    console.log('Post saved successfully');
  });

  res.status(200).json(post);
});

router.put('/:id', auth, required, function(req, res, next) {
  if (!req.body.title && !req.body.content && !req.body.tags) return next(new Error('Param is missing.'));
  Post.update({_id: req.post._id}, {$set: {title: req.body.title, content: req.body.content, tags: req.body.tags}}, function(error, count) {
    if (error) return next(error);
    console.info('Updated post %s.', req.post._id);

    Post.findOne({ _id: req.post._id}, function(err, post) {
      res.status(200).json(post);
    });
  });
});

router.delete('/:id', auth, required, function(req, res, next) {
  Post.remove({_id: req.post._id}, function(error, count) {
    if (error) return next(error);
    console.info('Deleted post %s.', req.post._id);
    res.status(204).send();
  });
});

module.exports = router;

{% endhighlight %}
