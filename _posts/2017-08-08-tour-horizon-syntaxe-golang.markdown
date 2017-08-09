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

Le point d'entrée du programme est le paquet "main" :

{% highlight golang %}
package "main"

// Importation des paquets

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

Dans les paquets, les éléments exportés sont ceux commençant par une majuscules (CamelCase): noms de fonctions, de types, de méthodes, de constantes, de variables... :

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

Le type des variables se trouve après la variable :

{% highlight golang %}
var a int, b int
{% endhighlight %}

On peut mettre le type uniquement sur le dernier paramètre lorsqu'ils partagent le mème type :

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

Dans les fonctions, on peut aussi faire une déclaration de variable de type implicite :

{% highlight golang %}
a := 1
b := 2
{% endhighlight %}

Au lieu de la déclaration explicite :

{% highlight golang %}
var a int, b int = 1, 2
{% endhighlight %}

On peut convertir la valeur a en type t avec l'expression t(a) :

{% highlight golang %}
var a int = 1
var b string = string(a)
{% endhighlight %}

Lors de l'initialisation d'une variable a partir d'une autre variable sans être explicite sur son type, la première variable (celle de gauche) va prendre le type de la deuxième (celle de droite)

{% highlight golang %}
var a int = 1
b := a
{% endhighlight %}

La variable b est de type int est a pour valeur 1.

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

Comme pour la déclaration de variable, on peut mettre le type uniquement sur le dernier paramètre lorsqu'ils partagent le mème type :

{% highlight golang %}
func Somme(a, b int) int {
    return a + b
}
{% endhighlight %}

Une fonction peut prendre un nombre variable de paramètre avec la syntaxe suivante :

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

On peut retourner plusieurs variables d'une fonction :

{% highlight golang %}
func Calc(a , b int) (int, int) {
    somme = a + b
    multiplication = a * b

    return somme, multiplication
}
{% endhighlight %}

## If, Switch, For

### If

{% highlight golang %}
func Compare(a , b int) (bool) {
    if a < b {
        return true
    } else {
        return false
    }
}
{% endhighlight %}

Le "if" peut prendre une autre forme avec l'initialisation en même temps que la condition :

{% highlight golang %}
func True(a bool) (bool) {
    if a := true ; a {
        return true
    } else {
        return false
    }
}
{% endhighlight %}

### Switch

{% highlight golang %}
a := "foo"
switch a {
    case "foo": return "foo"
    case "bar": return "bar"
    default: return "foobar"
}
{% endhighlight %}

On peut aussi avoir l'initialisation en même temps que la condition :

{% highlight golang %}
switch a := "foo"; a {
    case "foo": return "foo"
    case "bar": return "bar"
    default: return "foobar"
}
{% endhighlight %}

Pour gérer plusieurs cas, la syntaxe est la suivante :

{% highlight golang %}
a := "foo"
switch a {
    case "foo", "bar": return "foo bar"
    case "test": return "test"
    default: return "foobar"
}
{% endhighlight %}

### For

{% highlight golang %}
count := 0
for i := 0; i < 10; i++ {
    count += i
}
{% endhighlight %}

L'écriture peut être simplifié pour prendre la syntaxe du while de la manière suivante :

{% highlight golang %}
count := 0
for i < 10 {
    count += i
}
{% endhighlight %}

## Pointeurs

Un pointeur comporte l'adresse mémoire d'une variable, l'utilisation d'un pointeur permet de récupérer sa valeur et la modifier. Sa syntaxe est la suivante :

{% highlight golang %}
var a int = 2
var b *int = &a // pointeur vers a
var c = *b      // c vaut 2

*b = 3          // a vaut 3
{% endhighlight %}

Pour simplifié, on peut utiliser la syntaxe suivante :

{% highlight golang %}
a := 1
b := &a         // pointeur vers a
c = *b          // c vaut 2
*b = 3          // a vaut 3
{% endhighlight %}

## Les tableaux

La déclaration d'un tableau doit être typé :

{% highlight golang %}
var arrayString = []string
var arrayArrayString = [][]string

var arrayInt = []int
{% endhighlight %}

L'initialisation se fait avec la syntaxe suivante :

{% highlight golang %}
var array1 = []string("a", "b")

var array2 = [2]string
array2[0] = "a"
array2[1] = "b"
{% endhighlight %}


## Conclusion

J'ai volontairement omis de parler des types structurés, des interfaces, et de l'héritage. L'objectif de cet article était plus de donner un aperçu de la base de Go et ces derniers points méritent un article a eux seuls. Mais j'espère que la lecture de cet article vous aura convaincu de la facilité de compréhension de la syntaxe du langage Go.
