---
layout: post
title:  "ReactJs - Les fondamentaux"
date:   2016-02-15 10:18:00
tags:   ['ReactJs', 'ES6', 'Webpack', 'BabelJs', 'npm']
---

# Introduction

Après l'article sur l'initialisation d'un projet (React, ES6, Webpack), je voulais aborder les fondamentaux de React en version EcmaScript 6.

# Les fondamentaux

## JSX

JSX est une extension qui permet d'écrire en Javascript dans une syntaxe qui ressemble à du XML. Notre application React va pouvoir charger par exemple des balises html "h1" : 

{% highlight javascript lineanchors %}
import React     from 'react';
import ReactDom  from 'react-dom';

// Afficher notre balise html dans l'élément id "app" de notre DOM
ReactDom.render((
  <h1>Hello World</h1>
), document.getElementById(‘app'));
{% endhighlight %}

Voici la version sans jsx :

{% highlight javascript lineanchors %}
import React     from 'react';
import ReactDom  from 'react-dom';

const ui = React.createElement('h1', {}, 'Hello')

// Afficher notre constante ui dans l'élément id "app" de notre DOM
ReactDom.render(ui, document.getElementById(‘app'));

{% endhighlight %}

La syntaxe avec jsx est tout de même plus intuitive.

----
## Composant

La mise en place d'un composent React reste relativement simple à comprendre. Elle se présente sous cette forme :

{% highlight javascript lineanchors %}
import React     from 'react';
import ReactDom  from 'react-dom';

class App extend React.Component {
  render () {
    return <h1>Hello</h1>;
  }
}

ReactDom.render(
  <App />, 
  document.getElementById(‘app')
);
{% endhighlight %}

----
## Props

Le "props" peut être assimilé comme un parametre que l'on passe au composent React

{% highlight javascript lineanchors %}
import React     from 'react';
import ReactDom  from 'react-dom';

class App extend React.Component {
  render () {
    return <h1>Hello {this.props.text}</h1>;
  }
}

ReactDom.render(
  // Je passe World dans le props "text" de mon composant.
  <App text="World"/>, 
  document.getElementById(‘app')
);
{% endhighlight %}

----
## State

Le "state" peut être assimilé à une "session" Il peut être partager dans ces composants enfants.

{% highlight javascript lineanchors %}
import React     from 'react';
import ReactDom  from 'react-dom';

class App extend React.Component {
  constructor(props) {
    super(props);
    this.state = {
        text: 'World'
    };
  }

  render () {
    return <h1>Hello {this.state.text}</h1>;
  }
}

ReactDom.render(
  <App />, 
  document.getElementById(‘app')
);
{% endhighlight %}

Il est possible de cumuler les props avec le state, la valeur par défaut de notre composant va être celui qui se trouve dans le props :

{% highlight javascript lineanchors %}
import React     from 'react';
import ReactDom  from 'react-dom';

class App extend React.Component {
  constructor(props) {
    super(props);
    this.state = {
        // Par défaut, le state prend la valeur du props
        text: props.text
    };
  }

  render () {
    return <h1>Hello {this.state.text}</h1>;
  }
}

ReactDom.render(
  <App text="World"/>, 
  document.getElementById(‘app')
);
{% endhighlight %}

Il peut être utile de vérifier le type de "props" que l'on passe au composent

{% highlight javascript lineanchors %}
...

Add.propTypes = { 
    text: PropTypes.string.isRequired
}

ReactDom.render(
  <App text="World"/>, 
  document.getElementById(‘app')
);
{% endhighlight %}


----
## Events

L'implémentation d'action interne au composant est possible de la manière suivante :

{% highlight javascript lineanchors %}
import React     from 'react';
import ReactDom  from 'react-dom';

class App extend React.Component {
  constructor(props) {
    super(props);
    this.state = {
        id: 0,
        text: props.text
    };
  }

  handleClick() {
    this.setstate({id: this.state.id + 1});
  }

  render () {
    return (
        <h1>
          Hello 
          <div onCLick={this.handleClick.bind(this)} >
            {this.state.text} number {this.state.id}
          </div>
        </h1>
      );
  }
}

ReactDom.render(
  <App text="World"/>, 
  document.getElementById(‘app')
);
{% endhighlight %}

Les actions peuvent aussi être passer dans le "props" des composants enfants.

Dans cette exemple, nous avons donc un composant parent "App" et un composant enfant "ChildApp". Le parent possède l'action qu'il passe en tant que props à l'enfant. Le state est aussi passer dans le "props" pour l'afficher dans l'enfant

{% highlight javascript lineanchors %}
import React     from 'react';
import ReactDom  from 'react-dom';

class App extend React.Component {
  constructor(props) {
    super(props);
    this.state = {
        id: 0,
        text: props.text
    };
  }

  handleClick() {
    this.setState({id: this.state.id + 1});
  }

  render () {
    return (
        <h1>
          Hello 
          <ChildApp onClick={this.handleClick.bind(this)} text={this.state.text} id={this.state.id} />
        </h1>);
  }
}

class ChildApp extend React.Component {
  render () {
    return (
      <div onClick={this.props.onClick()}> 
        {this.props.text} number {this.props.id}
      </div>
    );
  }
}

ReactDom.render(
  <App text="World" />, 
  document.getElementById(‘app')
);
{% endhighlight %}

----
## Conclusion

J’ai volontairement omis de parler du cycle de vie d'un composant ReactJs, ce dernier mérite un article à lui seul. L'objectif de cet article était de montrer la simplicité de compréhension de ReactJs.
