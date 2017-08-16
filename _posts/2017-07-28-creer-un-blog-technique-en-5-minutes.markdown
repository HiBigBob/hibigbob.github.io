---
layout: post
title:  "Comment créer un blog technique en 5 minutes ?"
date:   2017-07-28 08:23:36 +0200
categories: jekyll github
tags: ["jekyll", "blog technique", "site statique", "Github Pages"]
---

## Introduction

A mon sens, la création d'un blog technique doit être simple et entièrement configurable. Les générateurs de site/blog statique répondent à ce besoin, il en existe dans différents langages : NodeJs (Hexo), Golang (Hugo), Ruby (jekyll), Python (Pelican) etc...

L'objectif de ces quelques lignes est de montrer qu'en 5 minutes, on peut créer un blog technique grâce au générateur de site statique, je vais le montrer avec Jekyll (version 3.5). Si vos besoins sont particuliers, il existe de nombreux plugins. Au pire si aucun plugin ne vous convient, il reste toujours la solution de mettre les mains dans le code.

## Les étapes importantes dans la vie du site statique

Les étapes sont les suivantes :
 * La création du site
 * La configuration avec le nom du site, l'auteur, une description et notre username pour twitter, etc ...
 * L'écriture des articles dans un fichier markdown.
 * La génération du site statique (il est accessible directement a l'adresse : http://localhost:4000)
 * Au choix, le changement du thême.

## Exemple :

### Installation de jekyll

{% highlight bash %}
gem install jekyll bundler
{% endhighlight %}

### Création

{% highlight bash %}
# Creation de nouveau blog jekyll
jekyll new myblog

# Bascule dans le dossier du blog
cd myblog

# Compilation et exécution du serveur en local
bundle exec jekyll serve

# Le blog est accessible a l'adresse : http://localhost:4000
{% endhighlight %}

### Personnalisation

Pour garder le serveur Jekyll est en cours d'exécution ouvrez un nouvel onglet dans votre terminal

{% highlight bash %}
# Bascule dans le dossier du blog
cd myblog
# Edition du fichier de configuration
vim _config.yml
{% endhighlight %}

Renseignez les informations : titre, email, description, twitter_username, et github_username.

Maintenant, retournez sur le précedent onglet du terminal puis redémarrez le serveur Jekyll.

Le tour est joué, le site est accessible sur :  <a href="http://localhost:4000" target="_blank">http://localhost:4000</a>

Si le thême par défaut (minima) ne vous plait pas, vous pouvez en installer un autre et le renseigner dans le fichier de configuration.

### Écriture du premier article

Les articles se trouvent dans le dossier "_posts". Il faut en créer un nouveau en s'inspirant de la structure (nommage et structure du fichier) de l'article par défaut.

L'entête du fichier markdown doit contenir la structure suivante pour être utilisable par Jekyll :

{% highlight markdown %}
---
layout: post                          # Template de page où l'article va s'afficher
title:  "Comment créer un blog ... ?" # Le titre
date:   2017-07-28 08:23:36 +0200
categories: jekyll github             # Tags concernant l'article présent dans l'url
---
{% endhighlight %}

## Hébergement

Concernant l'hébergement, le plus simple est d'utilisé Github via Github Pages, c'est facile et gratuit. Il suffit de créer un dépot **username.github.io** et pousser le code de votre blog sur la branche **master**. Votre blog sera accessible via l'url : http://username.github.io.

Il est possible d’acheter un nom de domaine (sur OVH par exemple) et de faire une redirection DNS :

* Créez un fichier CNAME à la racine du projet, contenant votre nom de domaine
* Faîtes pointer votre nom de domaine vers Github (dans votre espace client d'OVH par exemple).
* Attendez quelques heures le temps que ça se mette à jour

Pour en savoir plus sur cette procédure, vous pouvez vous reporter à la [documentation de Github Pages](https://help.github.com/articles/adding-a-cname-file-to-your-repository/)

## Conclusion

Il y a encore peu de temps, je voyais des personnes avec un blog technique réalisé avec Wordpress.
Personnellement, je choisis la simplicité. Même si les générateurs ne sont pas une nouveauté, nous sommes à la version 3.5 de Jekyll, ce qui lui donne du recul et de la stablilité.

