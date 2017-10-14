---
layout: post
title:  "ReactJs - Initialisation d'un projet"
date:   2016-02-01 10:18:00
img:    "reactjs.png"
tags:   ['ReactJs', 'ES6', 'Webpack', 'BabelJs', 'npm']
source-code: "https://github.com/HiBigBob/ember-admin-blog"
---

# Introduction

React, utilisant ES6 et le tous couplé à webpack nous donne un super trio pour une application web. Je voulais vous expliquer le résultat de ma veille sur comment mettre en place ce type de projet, pour obtenir une bonne expérience de développement (serveur + hot reload) et un projet optimisé pour la production. 

Cet article va présenter une architecture d'un projet qui affichera "Hello world". L'objectif est de fournir un squelette qui pourra être réutiliser à chaque création de projet. Il faudra juste remplacer le composant qui affiche "hello world" par le composant principale du nouveau projet. 

# Initialisation

Commençons dans un nouveau dossier pour y créer le fichier npm packages.json. Ce fichier contient les diverses informations sur le projet, on y précise les dépendances et les tâches. Pour créer ce fichier, exécutez la commande suivante dans le terminal :

{% highlight javascript lineanchors %}
npm init
{% endhighlight %}

Cette commande vous posera une série de questions concernant votre projet (comme le nom, la description, l'auteur, etc). Les réponses ne sont pas important si vous ne souhaitez pas publier votre projet sur npm. Vous pourrez toujours les ajouter par la suite dans le fichier.

## React

Une fois que le fichier packages est en place, on ajoute React dans les dépendances avec la commande :

{% highlight javascript lineanchors %}
npm install react react-dom --save
{% endhighlight %}

## Babel

Dans mon projet, j'aimerais pouvoir utiliser du code ES6 ou même ES7. Malheureusement, les navigateurs ne supportent pas toujours les dernières versions du language Ecmascript (le standard qui définit l'implémentation de Javascript). Pour résoudre cette problèmatique, nous allons utiliser Babel. C'est un outil qui permet de traduire le code Javascript en version compatible tous navigateur. 

Vu que Babel est très modulaire, nous allons implémenter plusieurs modules npm. Certains sont pour implémenter Babel et d'autres pour faire du hot reload (ou hot module replacement), qui permet de reloader le navigateur après chaque sauvegarde de code (c'est très pratique !!):

 - babel-core : le coeur de l'outil
 - babel-loader : l'intégration avec webpack
 - react-transform-hmr : intégration de hot module replacement
 - babel-preset-es2015 : la compilation en ES6
 - babel-preset-react : la compilation avec React
 - babel-preset-react-hmre : la compilation avec hot module replacement
 - babel-plugin-react-transform : le plugin pour le hot module replacement

On ajoute ces dépendances avec la commande :

{% highlight javascript lineanchors %}
npm install --save-dev babel-core babel-loader babel-preset-es2015 babel-preset-react babel-plugin-react-transform babel-preset-react-hmre react-transform-hmr
{% endhighlight %}

Babel a besoin du fichier de configuration (.babelrc)

{% highlight javascript lineanchors %}
// .babelrc
{
  "presets": ["react", "es2015"],
  "env": {
    "development": {
      "presets": ["react-hmre"],
      "plugins": [
        ["react-transform", {
          "transforms": [{
            "transform": "react-transform-hmr",
            "imports": ["react"],
            "locals": ["module"]
          }]
        }]
      ]
    }
  }
}

{% endhighlight %}

## Projet

Maintenant que tous est installé, nous allons voir la structure du projet qui va se découper en deux dossiers :

{% highlight javascript lineanchors %}
  components // les composants
    hello.js
  builds // les fichiers qui sont compilé (généré par Webpack) 
    bundle.js
  index.html
  index.js
  packages.json
{% endhighlight %}

Notre serveur web va donc pointer à la racine du projet et plus particulièrement sur le index.html :

{% highlight javascript lineanchors %}
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Todo</title>
  </head>
  <body>
    <div id="app"></div>
    <script src="builds/bundle.js"></script>
  </body>
</html>
{% endhighlight %}

Ensuite, nous allons créer le fichier principal (index.js) de notre application javascript :

{% highlight javascript lineanchors %}
// index.js
import React, {Component}   from 'react'; 
import ReactDOM             from 'react-dom';
import Hello                from './components/Hello';

ReactDOM.render(
  <Hello />,
  document.getElementById('app')
);

{% endhighlight %}

{% highlight javascript lineanchors %}
// ./components/hello.js
import React, {Component} from 'react'; 

class Hello extends Component {
  render() {
    return (
        <h1>
            Hello world
        </h1>
    );
  }
}

export default Hello

{% endhighlight %}

Pour voir le résultat dans le navigateur, nous allons devoir transformer le fichier index.js en bundle.js en utilisant Webpack. 
Ce qui vient à poser la question, comment fonctionne Webpack ?

# Webpack

## Installation

{% highlight javascript lineanchors %}
npm install webpack --save-dev
{% endhighlight %}

## Comment fonctionne Webpack ?

Webpack permet d'organiser votre projet comme une graphique de module. Nativement, il s'occupe que des ressources Javascript, mais il propose un système de "loader" pour transformer tout type de fichiers (CSS, SCSS, JS, SVG, HTML, etc...) en "string" pour qu'il soit consommable en tant que module sous forme d'import sur n'importe quelle page.
Au final, chaque ressource sera incluse dans le package finale, et oui, un fichier CSS peut être inclus dans mon fichier Javascript. 

Exemple :

{% highlight javascript lineanchors %}
import style  from'./style/default.scss';
import logo   from'./images/logo.svg';

console.log(style);
// "body{color: red;}"
console.log(logo);
// "data:image/svg+xml;base64,FTP49dzdx2094b[...]"
{% endhighlight %}

Vous comprendrez bien l'intêret de Webpack sur le nombre de request HTTP. Si vous ajoutez à ça les différentes optimisations que l'on peut faire sur la compilation de votre pack (fichier Javascript compilé), vous aurez un fichier prêt pour les performances que nécessite un environnement de production. De plus, Webpack a été pensé afin de permettre de séparer votre projet en plusieurs pack selon vos besoins, pour vous donner une plus grande souplesse.
Je précise qu'il fonctionne aussi très bien pour n'importe quel type de projet Javascript.

## Première compilation

Maintenant que vous en savez plus sur Webpack, nous allons rentrer dans le vif du sujet, la compilation de pack (bundle.js), grâce à la commande suivante :

{% highlight javascript lineanchors %}
node_modules/.bin/webpack index.js builds/bundle.js
{% endhighlight %}

Si vous ouvrez maintenant le fichier index.html dans votre navigateur, vous verez s'afficher "Hello World".

## Configuration de Webpack

Webpack offre une configuration qui reste simple et flexible, elle se construit dans le fichier webpack.config.js, il se positionne à la racine du projet :

{% highlight javascript lineanchors %}
// webpack.config.js
module.exports = {
  entry:  __dirname + '/index.js',
  output: {
    path: __dirname + '/builds',
    filename: 'bundle.js',
  },
};
{% endhighlight %}

Désormais, la compilation se fera avec la commande :
{% highlight javascript lineanchors %}
node_modules/.bin/webpack --progress
{% endhighlight %}

L'exécution de commande tel que node_modules/.bin/webpack est long et source d'erreur. Par chance, npm peut être éxécuté comme task runner, et ainsi cacher les scripts trop verbeux derrière une simple commande comme "npm start". Pour cela, il faut ajouter la commande dans le fichier packages.json :

{% highlight javascript lineanchors %}
// packages.json
{
  ...
    "scripts": {
      "start": "webpack --progress"
    },
  ...
}
{% endhighlight %}

Il faut noter que tous les scripts configurés dans le packages.json ont déjà “node_modules/.bin” dans le path, plus besoin de la rajoute dans les scripts

### Les paramètres utiles de la commande Webpack

Webpack offre différents paramètres qui peuvent s'avèré utile, en voici deux exemples :

Afficher la progression de la compilation :
{% highlight javascript lineanchors %}
webpack --progress
{% endhighlight %}

Exporter au format Json le résultat de la compilation dans le but de le faire analyser avec l'outil http://webpack.github.io/analyse/
{% highlight javascript lineanchors %}
webpack --json
{% endhighlight %}

### Les sources maps

La gestion des source maps est l'une des options les plus importants a utilisé. On a vu les performances données par Webpack sur le chargement d'un fichier dans le navigateur qui inclut tous les modules JavaScript (ou autre : CSS, PNG, etc) du projet. Mais cette optimisation apporte un inconvénient évident sur le débogage dans le navigateur, qui sera difficile avec le manque de visibilité sur le fichier d'origine. Cependant Webpack peut générer des sources maps qui fournit le mapping entre le fichier compilé et les modules du projet, ce qui rend le code plus lisible et plus facile à déboguer dans le navigateur

Les sources maps se configure avec le paramètre "devtool" de notre fichier

{% highlight javascript lineanchors %}
// webpack.config.js
module.exports = {
  devtool: process.env.NODE_ENV == "production" ? 'cheap-module-source-map' : 'eval-source-map',
  entry:  __dirname + '/index.js',
  output: {
    path: __dirname + '/builds',
    filename: 'bundle.js',
  },
};
{% endhighlight %}

Il existe de plusieurs options possible de devtool, personnellement j'utilise "eval-source-map" en développement et 'cheap-module-source-map' en production. Le premier permet d'avoir de bonne performance de compilation, c'est important lorsque l'on est en développement. Et le deuxième permet de minimiser le fichier de sortie.

## Webpack Development Server

Webpack propose un serveur de développement local comme LiveReload ou Browsersync. C'est un serveur node.js qui va lancé la compilation webpack automatiquement en actualisant le navigateur dès que l'on change un fichier du projet. Ce serveur est un module npm que l'on peut ajouter à ces dépendances de développement :

{% highlight javascript lineanchors %}
npm install --save-dev webpack-dev-server
{% endhighlight %}

Nous allons pouvoir rajouter une nouvelle commande dans notre script npm (packages.json). Nous aurons donc "npm run dev" pour l'environnement de développement et "npm run prod" lorsque nous serons en production. 

{% highlight javascript lineanchors %}
// packages.json
{
  ...
    "scripts": {
      "dev": "webpack-dev-server --progress --inline --hot"
      "prod": "NODE_ENV=production webpack --progress -p"
    },
  ...
}
{% endhighlight %}

## Babel avec webpack

Babel doit être configuré comme étant un module dans le fichier webpack.config.js :

{% highlight javascript lineanchors %}
// webpack.config.js
module.exports = {
  ...
  module: {
    loaders: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel',
        include: __dirname
      },
    ]
  },
};
{% endhighlight %}

## CSS avec Webpack

Je vous expliquais précédemment que webpack proposait un système de loader de fichier qui permet de considèrer les fichiers même de types (fonts, css, etc) comme étant un module.

La transformation de fichier css en module se fait à travers deux dépendances : 

 - css-loader : importe le fichier css
 - style-loader : charge le style

    On ajoute aux dépendances de développement avec la commande :

{% highlight javascript lineanchors %}
npm install --save-dev style-loader css-loader
{% endhighlight %}

{% highlight javascript lineanchors %}
// webpack.config.js
module.exports = {
  ...
  module: {
    loaders: [
      ...
      {
        test: /\.css$/,
        loader: 'style!css'
      }
    ]
  },
};
{% endhighlight %}

## Les plugins

Il existe de nombreux plugins dans webpack, dans mon cas, j'utilise que UglifyJsPlugin (compresse et minifie)

    Les plugin se trouvent dans une nouvelle partie "plugin" de notre webpack.config.js :

{% highlight javascript lineanchors %}
// webpack.config.js
module.exports = {
  ...
  module: { ... },

  plugins: [
    new webpack.optimize.UglifyJsPlugin({
          output: {
            comments: false
          }
    })
  ]
};
{% endhighlight %}

# Configuration finale

Maintenant que nous avons vu les différents éléments de l'initialisation de notre projet, voici la configuration dans son ensemble.

## webpack.config.js

La configuration de webpack final va ressembler à :

{% highlight javascript lineanchors %}
// webpack.config.js
var webpack = require("webpack");

module.exports = {
  devtool: process.env.NODE_ENV == "production" ? 'cheap-module-source-map' : 'eval-source-map',
  entry: "./index.js",
  output: {
    path: __dirname + '/builds',
    filename: "bundle.js",
    publicPath: "/builds/"
  },
  module: {
    loaders: [
        {
            test: /\.js?$/,
            exclude: /node_modules/,
            loaders: ["babel"],
            include: __dirname
        },
        {
            test: /\.css$/,
            loader: 'style!css'
        }
    ]
  },
  plugins: [
      new webpack.optimize.UglifyJsPlugin({
          output: {
            comments: false
          }
      })
  ]
}

{% endhighlight %}

## packages.json

La configuration npm finale va ressembler à :

Le code source de packages.json

{% highlight javascript lineanchors %}
// packages.json
{
  "name": "test",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "dev": "webpack-dev-server --progress --inline --hot",
    "prod": "NODE_ENV=production webpack --progress -p"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "babel-core": "^6.5.2",
    "babel-loader": "^6.2.3",
    "babel-plugin-react-transform": "^2.0.0",
    "babel-preset-es2015": "^6.5.0",
    "babel-preset-react": "^6.5.0",
    "babel-preset-react-hmre": "^1.1.0",
    "css-loader": "^0.23.1",
    "file-loader": "^0.8.5",
    "react-transform-hmr": "^1.0.2",
    "style-loader": "^0.13.0",
    "url-loader": "^0.5.7",
    "webpack": "^1.12.13",
    "webpack-dev-server": "^1.14.1"
  },
  "dependencies": {
    "react": "^0.14.7",
    "react-dom": "^0.14.7"
  }
}

{% endhighlight %}

# Conclusion

J'aimerais mettre en évidence l'intérêt de webpack et de cette configuration et plus particulièrement sur la taille de l'unique fichier de sortie (bundle.js) qui sera charger dans le navigateur.

Si je compile en environnement de développement avec la commande (npm run dev) et que je regarde la taille du fichier bundle.js, je peux constater qu'il fait 2,79MB, rien que ça. mais attention ce n'est pas optimisé pour la production.

Maintenant si je compile en environnement de production avec la commande (npm run prod). Le fichier fait 190 KB et 140 bytes pour les sources maps. Maintenant, nous sommes optimisé pour la production.

Pour conclure, ces exemples nous démontrent que webpack est un bon choix pour gérer ses projets React. Il offre de bonne performances pour la production, tout en restant avec une configuration accessible. 
