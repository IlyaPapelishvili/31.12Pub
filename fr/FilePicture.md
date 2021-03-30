bdhvbgjvhgjbvx

Premier en-tête | Deuxième en-tête
--- | ---
Cellule de contenu | Cellule de contenu
Cellule de contenu | Cellule de contenu

sdgfsfadgsafgfgdsav

Premier en-tête | Deuxième en-tête
--- | ---
Cellule de contenu | Cellule de contenu
Cellule de contenu | Cellule de contenu

![Citadelle](https://vignette.wikia.nocookie.net/masseffect/images/d/d7/MassEffect2Citadel.jpg/revision/latest?cb=20100721191415)

Aligné à gauche | Aligné au centre | Aligné à droite
:-- | :-: | --:
col 3 est | un texte verbeux | **1 600 $**
col 2 est | centré | 12 $
rayures zébrées | sont soignés | ~~ 1 $ ~~

Dillinger est un éditeur HTML5 Markdown optimisé pour le cloud, prêt pour les mobiles, pour le stockage hors ligne et AngularJS.

- Tapez du Markdown sur la gauche
- Voir HTML à droite
- la magie

# vrai

- Importez un fichier HTML et regardez-le se convertir comme par magie en Markdown
- Faites glisser et déposez des images (nécessite que votre compte Dropbox soit lié)

Vous pouvez également:

- Importez et enregistrez des fichiers depuis GitHub, Dropbox, Google Drive et One Drive
- Faites glisser et déposez les fichiers markdown et HTML dans Dillinger
- Exportez des documents au format Markdown, HTML et PDF

Markdown est un langage de balisage léger basé sur les conventions de mise en forme que les gens utilisent naturellement dans les e-mails. Comme l'écrit [John Gruber] sur le [site Markdown] [df1]

> L'objectif de conception primordial de la syntaxe de mise en forme de Markdown est de la rendre aussi lisible que possible. L'idée est qu'un document au format Markdown devrait être publiable tel quel, sous forme de texte brut, sans avoir l'air d'avoir été balisé avec des balises ou des instructions de mise en forme.

Ce texte que vous voyez ici est en *fait* écrit en Markdown! Pour avoir une idée de la syntaxe de Markdown, tapez du texte dans la fenêtre de gauche et regardez les résultats dans la droite.

### faux

Dillinger utilise un certain nombre de projets open source pour fonctionner correctement:

- [AngularJS] - HTML amélioré pour les applications Web!
- [Ace Editor] - génial éditeur de texte basé sur le Web
- [markdown-it] - L'analyseur de Markdown est bien fait. Extension simple et rapide.
- [Twitter Bootstrap] - excellente interface utilisateur standard pour les applications Web modernes
- [node.js] - E / S avec événements pour le backend
- [Express] - Framework d'application réseau rapide node.js [@tjholowaychuk]
- [Gulp] - le système de construction de streaming
- [Breakdance](https://breakdance.github.io/breakdance/) - Convertisseur HTML en Markdown
- [jQuery] - duh

Et bien sûr, Dillinger lui-même est open source avec un [référentiel public] [aneth] sur GitHub.

### Installation

![Ilos](https://lh3.googleusercontent.com/proxy/DDV8a7sLIWurhJtW8Ego9bq-JlwpfFFoR0tkLJQKKYXEXoWHB6ZUP5jGKD2VcYt3z1QVsgcn6L3GoU1ns8m9fvi3U51GzddA70ZUMHgzHvjl4-i7YOJY9cShBPrfjUhMQhxaJ97WFBp612XmjMXVGypfGkiBarN4PWxhiHkiYYNW7HGbtTpOcyt9GQ4Q23C2noxLTWFXZMcQZhRpQA_qzu2n6_H6CPViBnhSHpEl4JZAPaGCSJqgZg)

Dillinger nécessite [Node.js](https://nodejs.org/) v4 + pour fonctionner.

Installez les dépendances et devDependencies et démarrez le serveur.

```sh
$ cd dillinger
$ npm install -d
$ node app
```

Pour les environnements de production ...

```sh
$ npm install --production
$ NODE_ENV=production node app
```

### Plugins

Dillinger est actuellement étendu avec les plugins suivants. Les instructions sur la façon de les utiliser dans votre propre application sont liées ci-dessous.

Brancher | LISEZ-MOI
--- | ---
Dropbox | [plugins / dropbox / README.md] [PlDb]
GitHub | [plugins / github / README.md] [PlGh]
Google Drive | [plugins / googledrive / README.md] [PlGd]
OneDrive | [plugins / onedrive / README.md] [PlOd]
Moyen | [plugins / medium / README.md] [PlMe]
Google Analytics | [plugins / googleanalytics / README.md] [PlGa]

### Développement

Envie de contribuer? Génial!

Dillinger utilise Gulp + Webpack pour un développement rapide. Modifiez votre fichier et visualisez instantanément vos mises à jour!

Ouvrez votre terminal préféré et exécutez ces commandes.

Premier onglet:

```sh
$ node app
```

Deuxième onglet:

```sh
$ gulp watch
```

(facultatif) Troisième:

```sh
$ karma test
```

#### Construire pour la source

Pour la version de production:

```sh
$ gulp build --prod
```

Génération d'archives zip pré-construites pour la distribution:

```sh
$ gulp build dist --prod
```

### Docker

Dillinger est très facile à installer et à déployer dans un conteneur Docker.

Par défaut, le Docker exposera le port 8080, donc modifiez-le dans le Dockerfile si nécessaire. Lorsque vous êtes prêt, utilisez simplement le Dockerfile pour créer l'image.

```sh
cd dillinger
docker build -t joemccann/dillinger:${package.json.version} .
```

Cela créera l'image Dillinger et attirera les dépendances nécessaires. Assurez-vous de remplacer `${package.json.version}` par la version actuelle de Dillinger.

Une fois terminé, exécutez l'image Docker et mappez le port à ce que vous souhaitez sur votre hôte. Dans cet exemple, nous mappons simplement le port 8000 de l'hôte sur le port 8080 du Docker (ou tout autre port exposé dans le Dockerfile):

```sh
docker run -d -p 8000:8080 --restart="always" <youruser>/dillinger:${package.json.version}
```

Vérifiez le déploiement en accédant à l'adresse de votre serveur dans votre navigateur préféré.

```sh
127.0.0.1:8000
```

#### Kubernetes + Google Cloud

Voir [KUBERNETES.md](https://github.com/joemccann/dillinger/blob/master/KUBERNETES.md)

### Todos
