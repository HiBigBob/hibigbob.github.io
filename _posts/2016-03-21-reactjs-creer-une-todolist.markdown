---
layout: post
title:  "ReactJs - Créer une Todolist"
date:   2016-03-21 10:18:00
img:    "reactjs.png"
tags:   ['ReactJs', 'ES6', 'Webpack', 'BabelJs', 'npm']
---

# Introduction

Après l'article sur l'initialisation d'un projet (React, ES6, Webpack), et celui sur les fondamentaux de ReactJs, nous allons appliquer ces fondamentaux dans la création d'une application web "TodoList". Elle permettra de :

 - lister des taches
 - ajouter une tâche
 - changer le statut d'une tâche (effectué ou pas)
 - filtrer sur les tâches
 - trier sur les tâches

----
# Le TodoList

## La structure de projet

Cette structure est celle que j'ai expliqué dans l'article sur "initialisation d'un projet (React, ES6, Webpack)"

{% highlight javascript lineanchors %}
  components // les composents de notre application
    Todo.js
    Item.js
  builds // les fichiers qui sont compilé (généré par Webpack) 
    bundle.js
  assets
    style.css
    search.woff
  .babelrc // Configuration de babel
  .gitignore // Configuration des exclusions dans git
  index.html
  index.js
  webpack.config.js // Configuration de webpack
  packages.json // Configuration du projet et de ces dépendances
{% endhighlight %}


----
## Le code HTML (index.html)

{% highlight javascript lineanchors %}
<!DOCTYPE html>
<html>
  <head>
    <title>React Todo Example</title>
  </head>
  <body>
    <div id="app" />
    <script src="builds/bundle.js"></script>
  </body>
</html>

{% endhighlight %}

----
## Le fichier principale (index.js)

{% highlight javascript lineanchors %}
import React    from 'react';
import ReactDOM from 'react-dom';
import Todo     from './components/Todo';

// Importer du style css que l'on va utiliser dans nos composants
import './assets/style.css';

// Collection d'objet "todo" initiale
const todos = [
  {"id": 1, "text": "This is one comment", completed: false},
  {"id": 2, "text": "This is comment", completed: false}
];

// Afficher notre composant Todo dans l'élément id "app" de notre DOM
ReactDOM.render(
  <Todo todos={todos} />,
  document.getElementById('app')
);
{% endhighlight %}

----
## Les différentes fonctionnalités du TodoList

### Affichage de la collection Todos passée en paramètre

{% highlight javascript lineanchors %}
import React, {PropTypes, defaultProps, Component} from 'react'; 

class Todo extends Component {
  constructor(props) {
    super(props);
    this.state = {
        todos: props.todos,
    };
  }

  render() {
    var todos = [];
    if (this.state.todos) {
      todos = this.state.todos;
    }

    // Collection d'éléments qui seront affichés. Ce sera utile lorsque l'on voudra rajouter le formulaire d'ajout de tâche
    var items = [];

    // Ajout de chaque tâche dans "Items"
    todos.map(function (key) {
      var item = this.state.todos[key];
      items.push(
        <li className="done">
            {item.text}
        </li>
      );
    }, this);

    return (
        <ul>
            {items}
        </ul>
    );
  }
}

// Typage du props
Todo.propTypes = { 
    todos: PropTypes.arrayOf(PropTypes.shape({
        id: PropTypes.number.isRequired,
        completed: PropTypes.bool.isRequired,
        text: PropTypes.string.isRequired
    }).isRequired).isRequired
}

export default Todo
{% endhighlight %}

----
### Ajouter une tâche

Formulaire d'ajout : 

{% highlight javascript lineanchors %}

    // Ajout de chaque tâche dans "Items"
    ...

    // Ajout du formulaire d'ajout de tâche dans notre liste "Items"
    items.push(
        <li className="footer">
            <input ref="text" onKeyDown={this.handleKeyDown.bind(this)} placeholder="New task" />
            <i className="icon-plus-squared icon"></i>
        </li>
    );

    ...
    // return

{% endhighlight %}

Action : 

{% highlight javascript lineanchors %}
  // constructor
  ... 

  handleKeyDown(event) {
    if (event.which === 13) {
        let text = this.refs.text.value;
        let state = this.state.todos;
        this.setState({
            todos: [
                ...state,
                {
                    id: state.length + 1,
                    text: text,
                    completed: false
                }
            ]
        });
        this.refs.text.value = '';
    }
  }

  ...
  // render
{% endhighlight %}

A ce stade, on peut lister et ajouter des tâches à la liste existante. Maintenant, nous allons voir pour rechercher dans la liste

----
### Rechercher une tâche

Initialisation de la recherche à vide :

{% highlight javascript lineanchors %}
  ...
  constructor(props) {
    super(props);
    this.state = {
        todos: props.todos,
        filter: ''
    };
  }
  ...
{% endhighlight %}

Formulaire de recherche :

{% highlight javascript lineanchors %}
    // var items = [];

    items.push(
        <li className="head">
            <input ref="filter" onKeyUp={this.handleKeyChange.bind(this)} placeholder="Search" />
            <i className="icon-search search"></i>
        </li>
    );

    // Ajout de chaque tâche dans notre liste "Items"
{% endhighlight %}

Action :

{% highlight javascript lineanchors %}
  // constructor
  ...

  handleKeyChange() {
      this.setState({filter: this.refs.filter.value});
  }

  ...
  // render
{% endhighlight %}

Filtre sur la recherche dans la liste :

{% highlight javascript lineanchors %}
  ...

  // On remplace cette ligne
  // todos = this.state.todos;

  // Par cette boucle qui va filtrer sur le terme recherché
  todos = Object.keys(this.state.todos).filter(function (key) {
      let todo = this.state.todos[key];

      // Filtre sur la recherche
      if (this.state.filter && (todo.text.toLowerCase().indexOf(this.state.filter) === -1)) {
          return;
      }

      return todo;
  }, this);

  ...
{% endhighlight %}

----
### Trier les tâches

On ajoute le tri par défaut dans le "state" de notre composant Todo :

{% highlight javascript lineanchors %}
  constructor(props) {
    super(props);
    this.state = {
        todos: props.todos,
        filter: '',
        sort: 'asc'
    };
  }
{% endhighlight %}

Application de l'ordre de tri sur la liste de tâches :

{% highlight javascript lineanchors %}

    // Par cette boucle qui va filtrer sur le terme recherché
    ...

    if (this.state.sort == 'asc') {
        todos.sort();
    } else {
        todos.reverse();
    }

    // Ajout de chaque tâche dans "Items"
    ...
{% endhighlight %}

Action :

{% highlight javascript lineanchors %}

  // constructor
  ...

  sort() {
      this.setState({sort: this.state.sort === 'asc' ? 'desc' : 'asc'});
  }

  // handleKeyChange
  ...
{% endhighlight %}

Bouton pour changer l'ordre de tri :

{% highlight javascript lineanchors %}
    // var items = [];

    items.push(
        <li className="head">
            <i className="icon-sort sort" onClick={this.sort.bind(this)} /> 
            ...
        </li>
    );

    // Ajout de chaque tâche dans notre liste "Items"
{% endhighlight %}

Jusqu'à présent, on listait la liste de tâches sans afficher son statut (si elle est effectué ou pas), et sans donner la possiblité de changer son statut. Ce que l'on va faire dès maintenant mais dans un autre composant que l'on appelera "Item".

----

### Le composant "Item"

Au lieu de boucler sur un élément "li", on va le faire sur le composant "Item". On lui passe un objet dans le "props" ainsi qu'une action qui va passer la tâche a effectué.

On va donc remplacer le code suivant :

{% highlight javascript lineanchors %}

    // Ajout de chaque tâche dans "Items"
    todos.map(function (key) {
      var item = this.state.todos[key];
      items.push(
        <li className="done">
            {item.text}
        </li>
      );
    }, this);

{% endhighlight %}

Par cette version :

{% highlight javascript lineanchors %}

    // Ajout de chaque tâche dans "Items"
    todos.map(function (key) {
      var item = this.state.todos[key];
      items.push(
        <Item
            key={item.id}
            {...item}
            onClick={() => this.toggleTodo.bind(this, item.id)}
        />
      );
    }, this);

{% endhighlight %}

L'action qui va changer le statut d'une tâche :

{% highlight javascript lineanchors %}
  // constructor

  toggleTodo(id) {
    let todos = this.state.todos.map(todo => {
        if (todo.id !== id) {
            return todo
        }

        return Object.assign({}, todo, {
            completed: !todo.completed
        })
    })
    this.setState({todos: todos});
  }

  // handleKeyChange
{% endhighlight %}

Le composant Item :

{% highlight javascript lineanchors %}

import {Component} from 'react';

class Item extends Component {
  render() {
    const {text, onClick} = this.props;
    const textDeco = this.props.completed ? 'line-through' : 'none';
    const classname = this.props.completed ? 'icon-check icon' : 'icon-check-empty icon';

    return (
        <li onClick={onClick()} className="done"
            style={{ textDecoration: this.props.completed ? 'line-through' : 'none'}} >
                {text}
                <i className={ classname }></i>
        </li>
    );
  }
}

export default Item

{% endhighlight %}

----

### Filtre sur le statut des tâches

On ajoute le filtre par défault sur le statut des tâches dans le composant Todo :

{% highlight javascript lineanchors %}
  constructor(props) {
    super(props);
    this.state = {
        todos: props.todos,
        filter: '',
        sort: 'asc',
        filterStatus: '',
    };
  }
{% endhighlight %}

Action :

{% highlight javascript lineanchors %}

  // constructor
  ...

  filterStatus(param) {
      this.setState({filterStatus: param});
  }

  // sort
  ...
{% endhighlight %}

Bouton pour changer le filtre :

{% highlight javascript lineanchors %}
    // var items = [];

    items.push(
        <li className="head">
            ...
            <a href="#" className={ this.state.filterStatus === 'all' ? 'selected' : ''} onClick={this.filterStatus.bind(this, 'all')} >All</a>
            <a href="#" className={ this.state.filterStatus === 'active' ? 'selected' : ''} onClick={this.filterStatus.bind(this, 'active')} >Active</a>
            <a href="#" className={ this.state.filterStatus === 'completed' ? 'selected' : ''} onClick={this.filterStatus.bind(this, 'completed')} >Completed</a>
            ...
        </li>
    );

    // Ajout de chaque tâche dans notre liste "Items"
{% endhighlight %}

On applique le filtre sur la liste des tâches :

{% highlight javascript lineanchors %}

    // Par cette boucle qui va filtrer sur le terme recherché
    if (this.state.todos) {
        todos = Object.keys(this.state.todos).filter(function (key) {
            let todo = this.state.todos[key];

            // Filtre sur le statut de la tâche
            if (this.state.filterStatus && (this.state.filterStatus === 'active') && todo.completed) {
                return;
            }

            // Filtre sur le statut de la tâche
            if (this.state.filterStatus && (this.state.filterStatus === 'completed') && !todo.completed) {
                return;
            }

            // Filtre sur le terme recherché
            if (this.state.filter && (todo.text.toLowerCase().indexOf(this.state.filter) === -1)) {
                return;
            }

            return todo;
        }, this);
    }

    // var items = [];

    ...
{% endhighlight %}
