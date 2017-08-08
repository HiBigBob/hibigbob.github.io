---
layout: post
title:  "Tour d'horizon de la syntaxe de Golang !"
date:   2017-08-06 14:02:36 +0200
categories: golang syntaxe
---

Cet article a pour vocation de faire un résumé de la syntaxe du langage Go, de mettre en avant les points qui m'ont donné envie d'aller plus loin avec ce langage.

## Qu'est ce que le langage Go ?

Go est un langage de programmation compilé et concurrent inspiré de C et Pascal, développé par Google et créé par Robert Griesemer, Rob Pike et Ken Thompson.

Le langage a pour but d'être facile a prendre en mains et rapide en temps de compilation et d'exécution. Il peut être utiliser aussi bien pour réaliser des scripts que des applications.

## La gestion des paquets

Les programmes Go sont organisés en paquets.

Le point d'entrée du programme est le paquet "main"

{% highlight golang %}
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
    "paquet"
    "paquet2"
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

Pour importer un paquet hébergé sur github, on stocke les sources dans le dossier "src" du gopath :

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

Dans les paquets, les éléments exportés sont ceux commençant par une majuscules (CamelCase): noms de fonctions, de types, de méthodes, de constantes, de variables... 

{% highlight golang %}
package paquet

// Privée
var fooPrivee string

// Public
var FooPublic string

func Public() string {
	return "Public!"
}

func privee() string {
	return "Privée!"
}
{% endhighlight %}

## Le typage

Les types de base sont :
{% highlight golang %}
string

int  int8  int16  int32  int64 uint uint8 uint16 uint32 uint64 uintptr

float32 float64

complex64 complex128

bool
{% endhighlight %}

Le type des variables se trouve après la variable :

{% highlight golang %}
var a int, b int
{% endhighlight %}

On peut mettre le type uniquement sur le dernier paramètre lorsqu'il partage le mème type

{% highlight golang %}
var a, b int
{% endhighlight %}

L'initialisation des variables peut se faire de différentes manières :

{% highlight golang %}
var a int, b int = 1, 2
{% endhighlight %}

{% highlight golang %}
var (
    a int = 1
    b int = 2
)
{% endhighlight %}

Dans les fonctions, on peut aussi faire une déclaration de variable de type implicite

{% highlight golang %}
a := 1
b := 2

// A la place de 
var a int, b int = 1, 2
{% endhighlight %}

## Les fonctions

Les paramètres des fonctions sont typés ainsi que le retour :

{% highlight golang %}
func Somme(a int, b int) int {
	return a + b
}

func Concat(a int, b int) string {
	return a + " " + b
}
{% endhighlight %}

On peut mettre le type uniquement sur le dernier paramètre lorsqu'il partage le mème type

{% highlight golang %}
func Somme(a, b int) int {
	return a + b
}
{% endhighlight %}

Une fonction peut prendre un nombre variable de paramètre avec la syntaxe suivante

{% highlight golang %}
func Somme(a ...int) int {
    if len(a)==0 {
        return 0
    }
    somme := a[0]
    for _, v := range a {
        somme += v
    }
    return somme
}
{% endhighlight %}

On peut nommer la ou les variables de retour et faire un "return" vide :

{% highlight golang %}
func Somme(a , b int) (somme int) {
    somme = a + b
    return
}

func Calc(a , b int) (somme int, multiplication int) {
    somme = a + b
    multiplication = a * b
    return
}
{% endhighlight %}
