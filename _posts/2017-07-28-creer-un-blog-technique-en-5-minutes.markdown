---
layout: post
title:  "Comment créer un blog technique en 5 minutes ?"
date:   2017-07-28 08:23:36 +0200
categories: jekyll github
tags: ["jekyll", "blog technique", "site statique"]
---

## Introduction

A mon sens, la création d'un blog technique doit être simple et entièrement configurable. Les générateurs de site/blog statique répondent a ce besoin, il en existe plusieurs, ils sont souvent écrit dans des langages différents : NodeJs, Golang, Ruby or Python. Voici une liste d'exemple : Jekyll (Ruby), Hexo (NodeJs), Hugo (Golang), Pelican (Python) et j'en passe. 

L'objectif de cet article est de montrer qu'en 5 minutes, on peut créer un blog technique grâce au générateur de site statique et dans cet exemple avec Jekyll (version 3.5).

A noter que le fonctionnement reste aussi simple avec un autre générateur de site statique !

## Comment fonctionne les sites statiques ?

Le principe est le suivant :
 * On crée le site
 * On complête le fichier de configuration avec le nom du site, l'auteur, une description et notre username pour twitter, github, etc.
 * On écrit nos articles dans un fichier markdown.
 * On lance la génération du site statique, qui est accessible directement a l'adresse : http://localhost:4000
 * On peut aussi changer de thême.

## Les étapes pour créer le blog

### Installation de jekyll

{% highlight bash %}
gem install jekyll bundler
{% endhighlight %}

### Création du blog

{% highlight bash %}
# Creation de nouveau blog jekyll
jekyll new myblog

# Bascule dans le dossier du blog
cd myblog

# Compilation du blog et exécution du serveur en local
bundle exec jekyll serve

# Le blog est accessible a l'adresse : http://localhost:4000
{% endhighlight %}

### Personnalisation du blog

J'ouvre un nouvelle onglet dans mon terminal pour garder le serveur Jekyll est en cours d'exécution.

{% highlight bash %}
# Bascule dans le dossier du blog
cd myblog
# Edition du fichier de configuration
vim _config.yml
{% endhighlight %}

On personnalise le titre, l'email, la description, le twitter_username, et le github_username.

On retourne sur le précedent onglet du terminal, on redémarre le serveur Jekyll et le tour est joué !

Si le thême par défaut (minima) ne vous plait pas, vous pouvez en installer un autre et renseigne dans le fichier de configuration.

## Écriture du premier article

Les articles se trouvent dans le dossier "_posts". Il faut en créer un nouveau en s'inspirant de la structure (nommage et structure du fichier) de l'article par défaut.

## Conclusion

Il y a encore peu de temps, je voyais du monde avec un blog technique réalisé avec Wordpress. On vient de voir qu'il existe des solutions pour ceux qui cherche la simplicité.




