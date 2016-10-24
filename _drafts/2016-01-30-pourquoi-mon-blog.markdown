---
title:      "Pourquoi j'ai fait mon blog ? Et Comment ?"
date:       2016-01-30 13:52:00
img: 		"jekyll.png"
tags:		['jekyll', 'blog static']
---

# Pourquoi j'ai fait mon blog ?

Pendant longtemps, je faisais de la veille mais sans aller très loin dans la compréhension du sujet. Elle se résumait à lire des articles techniques, et à tester les exemples lorsqu'il y en avait.

Le problème : je restais trop à la surface. Je voyais que très peu les problèmatiques.

Ce constat fait couplé à de la motivation, je me suis mis à tester plus en profondeur en réalisant des minis projets aux fonctionnalités très limités.

Le problème : Même si j'avais une meilleur appréhension de la techno, je n'assimilais pas la techno.

L'objectif de ce blog est d'aller plus loin dans ma veille, et ainsi d'apprendre en jouant avec des technos qui me branche. Pour cela, je vais créer des petits projets mais qui tourne et écrire des articles qui parle de ces notions.

# Comment j'ai fait mon blog ?

J'avais régulièrement entendu parler de Jekyll, sans connaître son fonctionnement. Désormais, je trouve que ça vaut vraiment le coup de se poser la question du choix entre Wordpress (pour ne citer que lui) et Jekyll, si on est développeur.

## Qu'est ce que Jekyll ?

Le slogan de leur site parle de lui même : "Transform your plain text into static websites and blogs".
Pour rester dans les grandes lignes, Jekyll est un générateur de site fait en Ruby. Les thêmes sont réalisé entièrement en HTML, et le article sont rédigé en markdown. Jekyll s'occupe du reste.

### Ces avantages

* L’architecture est simple et rapide à prendre en main.
* On a la main sur tout, on est au coeur du code.
* On n'a pas besoin d'avoir une base de données : nous sommes sur du contenu statique.
* Le édploiement en production est simplifié, Il suffit d'avoir un serveur web (Nginx, Apache, etc...)
* Les plugins Ruby sont utilisable si on veut entendre le fonctionnement du site
* Hébergement gratuit possible sur Github via Github pages : le site est un repository. Dans ce cas, le site est disponible sous le nom : username.github.io

### Son architecture

L'architecture de Jekyll reste simple tout en permettant d'éviter de dupliquer du code.

	.
	|-- _includes/      # Includes
	|   |-- footer.html
	|   |-- head.html
	|   |-- nav.html
	|-- _layouts/       # Vos templates
	|   |-- default.html
	|   |-- page.html
	|   |-- post.html
	|-- _site           # Votre site final est compilé par Jekyll dans ce dossier
	|-- assets
	|  	|-- css
	|   |-- fonts
	|   |-- img
	|   |-- js
	|-- _posts/         # Vos posts
	|   |-- 2014-11-15-jekyll.markdown
	|-- index.html      # Vos pages
	|-- about.md
	|-- (...)
	|-- _config.yml     # Fichier de configuration


### La structure de mes pages

Chaque page commence par une entête YAML qui est interprété par Jekyll. On y définit le type de page (layout) et des variables utiles dans mon cas. La suite du fichier, c’est du Markdown.

	---
	layout:     post
	title:      "Jekyll"
	subtitle:   "Le générateur de blogs et de sites statiques pour les développeurs."
	date:       2000-10-01 21:27:00
	author:     "Adrien Bouttier"
	header-img: "img/post-bg-01.jpg"
	---

	## Qu'est ce que Jekyll ?

### Installation en local

Avant de procéder à l'installation, assurez vous que [Ruby](https://www.ruby-lang.org/en/downloads/) et [RubyGem](http://rubygems.org/pages/download) soit bien installé sur votre ordinateur

	~ $ gem install jekyll
	~ $ jekyll new monblog
	~ $ cd monblog
	~/monblog $ jekyll serve # Le site se mettra à jour automatiquement après chaque modification.
	# => Votre site est accessible via l'url : http://localhost:4000


### Hébergement

Le plus simple est d'héberger son blog sur Github via Github Pages, c'est facile et gratuit. Il suffit de créer un dépot **username.github.io** et pousser le code de votre blog sur la branche **master**. Votre blog sera accessible via l'url : http://username.github.io.

Il est possible d’acheter un nom de domaine (sur OVH par exemple) et de faire une redirection DNS :

* Créez un fichier CNAME à la racine du projet, contenant votre nom de domaine
* Faîtes pointer votre nom de domaine vers Github (dans votre espace client d'OVH par exemple).
* Attendez quelques heures le temps que ça se mette à jour

Pour en savoir plus sur cette procédure, vous pouvez vous reporter à la [documentation de Github Pages](https://help.github.com/articles/adding-a-cname-file-to-your-repository/)

### Thêmes

Des éditeurs dédiés tels que Prose.io permettent de générer les templates sans connaissance spécifique.
Vous pouvez aussi télécharger l'un des nombreux thêmes existants disponible sur les sites ci-dessous, et le mettre à la racine de votre blog, il vous restera plus qu'à le personnaliser et vous pourrez rediger vos articles

[Jekyll themes](http://jekyllthemes.org/) <br />
[Jekyll bootstrap](http://jekyllbootstrap.com/)

### Gestion des commentaires

J'ai opté pour confier la gestion des commentaires à Disqus, qui reste le plus simple et le plus efficace.
L'installation m'a pris 5 minutes. Il suffit de s'inscrire sur leur [site](http://www.disqus.com), et de copier le script d'intégration dans le layout "post" de mon blog, et le tour est joué. Simple et efficace !

### Twitter

Pour partager les articles sur Twitter, vous devez copier le code suivant dans le layout "post" de votre blog.

	<a class="twitter-share-button"
	  href="https://twitter.com/share">
	Tweet
	</a>
	<script type="text/javascript">
	window.twttr=(function(d,s,id){var t,js,fjs=d.getElementsByTagName(s)[0];if(d.getElementById(id)){return}js=d.createElement(s);js.id=id;js.src="https://platform.twitter.com/widgets.js";fjs.parentNode.insertBefore(js,fjs);return window.twttr||(t={_e:[],ready:function(f){t._e.push(f)}})}(document,"script","twitter-wjs"));
	</script>

Si vous avez besoin de plus d'information, voivi la [documentation officielle](https://dev.twitter.com/web/tweet-button).

### Le mot de la fin

Si cet article vous a donné envie d'en apprendre plus sur Jekyll, les liens suivants vous donneront les informations nécessaires.

[Jekyll](http://jekyllrb.com) <br />
[Documentation de Jekyll](http://jekyllrb.com/docs/templates/) <br />
[Déployer en production](https://jekyllrb.com/docs/deployment-methods/) <br />
[Github Pages](https://pages.github.com/)