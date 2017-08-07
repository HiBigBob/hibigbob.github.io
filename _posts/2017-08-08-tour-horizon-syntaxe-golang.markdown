---
layout: post
title:  "Tour d'horizon de la syntaxe de Golang !"
date:   2017-08-06 14:02:36 +0200
categories: golang syntaxe
---

Cet article a pour vocation de faire un résumé de la syntaxe de Go, de mettre en avant les points qui m'ont donné envie d'aller plus loin avec ce language.

## Qu'est ce que le langage Go ?

Go est un langage de programmation compilé et concurrent inspiré de C et Pascal, développé par Google et créé par Robert Griesemer, Rob Pike et Ken Thompson.

Le langage a pour but d'être facile a prendre en mains et rapide en temps de compilation et d'exécution. Il peut être utiliser aussi bien pour réaliser des scripts ou des applications.

## La gestion des paquets

Les programmes Go sont organisés en paquets.

Le points d'entrée du programme est le paquet "main"

{% highlight ruby %}
    package "main"

    // Import des paquets

    func main () {}
{% endhighlight %}

### Importation des paquets

Pour importer un paquet :

{% highlight golang %}
    import "paquet"
{% endhighlight %}

Pour importer une liste de paquets :

{% highlight golang %}
    import (
        "fmt"
        "paquet"
        ...
    )
{% endhighlight %}

Pour renommer un paquet importer :

{% highlight golang %}
    import (
        "paquet"
        renommer "nom/origin"
    )
{% endhighlight %}

Pour importer un paquet hébergé sur github, par convention, on stocke les sources dans le dossier src du gopath :

{% highlight shell %}
    cd ~/Documents # Répertoire de développement
    mkdir go # Créé votre espace pour vos applications Go (=> GOPATH)
    export GOPATH=~/workspace/go # GOPATH
    cd $GOPATH
    mkdir -p src/github.com/hibigbob
    cd src/github.com/hibigbob
    git clone git@github.com:hibigbob/paquet.git
{% endhighlight %}

Puis, on importe les sources :

{% highlight golang %}
    import (
        "github.com/hibigbob/paquet"
        ...
    )
{% endhighlight %}



## Le typage


