---
"$title": "Créer un widget d'interface utilisateur avec JavaScript personnalisé"
"$order": '101'
formats:
- sites Internet
tutorial: vrai
author:
- morsssss
- CrystalOnScript
description: "Pour les expériences Web nécessitant une grande quantité de personnalisation, AMP a créé amp-script, un composant qui permet l'utilisation de JavaScript arbitraire sur votre page AMP sans affecter les performances de la page."
---

Dans ce didacticiel, vous apprendrez à utiliser `<amp-script>` , un composant qui permet aux développeurs d'écrire du JavaScript personnalisé dans AMP. Vous l'utiliserez pour créer un widget qui vérifie le contenu d'un champ de saisie de mot de passe, lui permettant uniquement d'être soumis lorsque certaines conditions sont remplies. AMP fournit déjà cette fonctionnalité avec `<amp-form>` , mais `<amp-script>` vous permettra de créer une expérience personnalisée.

## Ce dont vous aurez besoin

- Un navigateur Web moderne
- Connaissance de base de HTML, CSS et JavaScript
- Soit:
    - un serveur web local et un éditeur de code comme [SublimeText](https://www.sublimetext.com) ou [VSCode](https://code.visualstudio.com/)
    - *ou* [CodePen](https://codepen.io/) , [Glitch](https://glitch.com/) ou un terrain de jeu en ligne similaire

## Contexte

AMP vise à rendre les sites Web plus rapides et plus stables pour les utilisateurs. Un JavaScript excessif peut ralentir une page Web. Mais parfois, vous devez créer des fonctionnalités que les composants AMP ne fournissent pas. Dans de tels cas, vous pouvez utiliser le composant [`<amp-script>`](../../../documentation/components/reference/amp-script.md) pour écrire du JavaScript personnalisé.

Commençons!

# Commencer

Pour obtenir le code de démarrage, téléchargez ou clonez[ce référentiel github](https://github.com/ampproject/samples/tree/master/amp-script-tutorial) . Une fois que vous avez fait cela, `cd` dans le répertoire que vous avez créé. Vous verrez deux répertoires: `starter_code` et `finished_code` . `finished_code` contient ce que vous allez créer pendant ce tutoriel. Alors ne regardons pas cela encore. Au lieu de cela, `cd` dans `starter_code` . Il contient une page Web qui implémente notre formulaire en utilisant [`<amp-form>`](../../../documentation/components/reference/amp-form.md) seul, sans l'aide de `<amp-script>` .

Pour faire cet exercice, vous devrez exécuter un serveur Web sur votre ordinateur. Si vous faites déjà cela, vous serez prêt! Si tel est le cas, en fonction de votre configuration, vous pourrez accéder à la page Web de démarrage en saisissant dans votre navigateur une URL telle que `http://localhost/amp-script-tutorial/starter_code/index.html` .

Vous pouvez également configurer un serveur local rapide à l'aide de quelque chose comme [serve](https://www.npmjs.com/package/serve) , un serveur de contenu statique basé sur [Node.js.](https://nodejs.org/) Si vous n'avez pas installé Node.js, téléchargez-le [ici](https://nodejs.org/) . Une fois Node installé, tapez `npx serve` sur votre ligne de commande. Vous pouvez ensuite accéder à votre site Web ici:

`http://localhost:5000/`

Vous êtes également libre d'utiliser un terrain de jeu en ligne comme [Glitch](https://glitch.com/) ou [CodePen](https://codepen.io/) . <a href="itch%5D(https://glitch.com/~grove-thankful-ragdoll" target="_blank">Celui-ci</a> contient le même code que le référentiel github, et vous pouvez commencer par là si vous le souhaitez!

Une fois que vous avez fait cela, vous verrez notre page Web de démarrage:

{{image ('/ static / img / docs / tutorials / custom-javascript-tutorial / starter-form.jpg', 600, 325, layout = 'intrinsic', alt = 'Formulaire Web avec entrées d'e-mail et de mot de passe', align = 'centre')}}

Ouvrez `starter_code/index.html` dans votre éditeur de code préféré. Jetez un œil au code HTML de ce formulaire. Notez que le mot de passe `<input>` contient cet attribut:

```html
on="tap:rules.show; input-debounced:rules.show"
```

Cela indique à AMP d'afficher les règles `<div>` lorsque l'utilisateur tape ou clique sur le mot de passe `<input>` , et également après avoir entré un caractère. Nous préférerions utiliser l'événement `focus` , qui couvrirait également le cas où l'utilisateur tabule dans l'entrée. Au moins au moment de la rédaction de ce didacticiel, AMP ne transmet pas cet événement, nous n'avons donc pas cette option. Ne t'inquiète pas. Nous sommes sur le point de résoudre ce problème avec `<amp-script>` !

Le mot de passe `<input>` contient un autre attribut intéressant:

```html
pattern="^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[^a-z\d]).{8,}$"
```

Cette expression régulière combine un ensemble d'expressions régulières plus petites, chacune exprimant l'une de nos règles de validation. AMP [ne permettra pas que le formulaire soit soumis](../../../documentation/components/reference/amp-form.md#verification) tant que le contenu de l'entrée ne correspondra pas. Si l'utilisateur essaie, il verra un message d'erreur qui fournit quelques détails:

{{image ('/ static / img / docs / tutorials / custom-javascript-tutorial / starter-form-error.jpg', 600, 442, layout = 'intrinsic', alt = 'Formulaire Web affichant un message d'erreur' ', align = 'centre')}}

[tip type="note"] Étant donné que le code que nous vous avons fourni n'inclut pas de service Web qui gère les soumissions de formulaires, l'envoi du formulaire ne fera rien d'utile. Bien sûr, vous pouvez ajouter cette fonctionnalité à votre propre code! [/tip]

Cette expérience est acceptable - mais malheureusement, AMP ne peut pas expliquer laquelle de nos règles de vérification a échoué. Il ne peut pas savoir, car nous avons dû écraser les règles en une seule expression régulière.

Maintenant, utilisons `<amp-script>` pour créer une expérience plus conviviale!

# Reconstruire avec &lt;amp-script&gt;

Pour utiliser `<amp-script>` , nous devons importer son propre JavaScript. Ouvrez `index.html` et ajoutez ce qui suit à la `<head>` .

```html
&lt;head&gt;
 ...
  &lt;script async custom-element="amp-script" src="https://cdn.ampproject.org/v0/amp-script-0.1.js"&gt;&lt;/script&gt;
  ...
&lt;/head&gt;

```

`<amp-script>` nous permet d'écrire notre propre JavaScript en ligne ou dans un fichier externe. Dans cet exercice, nous allons écrire suffisamment de code pour mériter un fichier séparé. Créez un nouveau répertoire nommé `js` et ajoutez-y un nouveau fichier appelé `validate.js` .

`<amp-script>` permet à votre JavaScript de manipuler ses enfants DOM - les éléments contenus dans le composant. Il copie ces enfants DOM dans un DOM virtuel et donne à votre code l'accès à ce DOM virtuel. Dans cet exercice, nous voulons que notre JavaScript contrôle notre `<form>` et son contenu. Donc, nous allons envelopper le `<form>` dans un composant `<amp-script>` , comme ceci:

```html
&lt;amp-script src="js/validate.js" layout="fixed" sandbox="allow-forms" height="500" width="750"&gt;
  &lt;form method="post" action-xhr="#" target="_top" class="card"&gt;
    ...
  &lt;/form&gt;
&lt;/amp-script&gt;
```

Notre `<amp-script>` inclut l'attribut `sandbox="allow-forms"` . Cela indique à AMP que le script peut modifier le contenu du formulaire.

Étant donné que AMP vise à garantir une expérience utilisateur rapide et visuellement stable, il ne permettra pas à notre JavaScript d'apporter des modifications illimitées au DOM à tout moment. Votre JavaScript peut apporter d'autres modifications si la taille du composant `<amp-script>` ne peut pas changer. Il permet également des changements plus substantiels après une interaction de l'utilisateur. Vous pouvez trouver des détails dans [la documentation de référence](../../../documentation/components/reference/amp-script.md) . Pour ce didacticiel, il suffit de savoir que nous avons spécifié un type de `layout` qui n'est pas un `container` et que nous avons utilisé des attributs HTML pour verrouiller la taille du composant. Cela signifie que toutes les manipulations DOM sont limitées à une certaine zone de la page.

Si vous utilisez l' [extension Chrome AMP validator,](https://chrome.google.com/webstore/detail/amp-validator/nmoffdblmcmgeicmolmhobpoocbbmknc) vous verrez maintenant un message d'erreur:

{{image ('/ static / img / docs / tutorials / custom-javascript-tutorial / relative-url-error.png', 600, 177, layout = 'intrinsic', alt = 'Error about relative URL', align = 'centre' ) }}

[tip type="note"] Si vous n'avez pas cette extension, ajoutez `#development=1` à votre URL, et AMP affichera les erreurs de validation dans votre console. [/tip]

Qu'est-ce que ça veut dire? Si votre `<amp-script>` charge son JavaScript à partir d'un fichier externe, AMP vous demande de spécifier une URL absolue. Nous pourrions résoudre ce problème en utilisant `http://localhost/js/validate.js` . Mais AMP nécessite également l'utilisation de [HTTPS](https://developers.google.com/web/fundamentals/security/encrypt-in-transit/why-https) . Nous aurions donc toujours une erreur de validation et la configuration de SSL sur notre serveur Web local sort du cadre de ce tutoriel. Si vous souhaitez le faire, vous pouvez suivre les instructions de [cet article](https://timonweb.com/posts/running-expressjs-server-over-https/) .

Ensuite, nous pouvons supprimer l'attribut `pattern` et son expression régulière de notre formulaire: nous n'en aurons plus besoin!

Nous allons aussi supprimer le `on` attribut qui est actuellement utilisé pour dire AMP pour montrer nos règles de mot de passe. Comme annoncé ci-dessus, nous allons utiliser à la place `<amp-script>` pour capturer l'événement de `focus` du navigateur.

```html
pattern="^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[^a-z\d]).{8,}$"
on="tap:rules.show; input-debounced:rules.show"
```

Maintenant, assurons-nous que notre `<amp-script>` fonctionne. Ouvrez le fichier `validate.js` que vous avez créé et ajoutez un message de débogage:

```js
console.log("Hello, amp-script!");
```

Accédez à votre navigateur, ouvrez la console et rechargez la page. Assurez-vous de voir votre message!

{{image ('/ static / img / docs / tutorials / custom-javascript-tutorial / hello-amp-script.png', 600, 22, layout = 'intrinsic', alt = 'Bonjour message amp-script dans la console' , align = 'center')}}

## Où est mon JavaScript?

`<amp-script>` exécute votre JavaScript dans un Web Worker. Les Web Workers ne peuvent pas accéder directement au DOM, donc `<amp-script>` donne au worker l'accès à une copie virtuelle du DOM, qu'il maintient en synchronisation avec le vrai DOM. `<amp-script>` fournit des émulations de nombreuses API DOM courantes, que vous pouvez presque toutes utiliser dans votre JavaScript de la manière habituelle.

Si à un moment donné vous avez besoin de déboguer votre script, vous pouvez définir des points d'arrêt dans JavaScript dans un Web Worker de la même manière que vous le faites avec n'importe quel JavaScript. Vous avez juste besoin de savoir où le trouver.

Dans Chrome DevTools, ouvrez l'onglet "Sources". En bas, vous verrez une longue chaîne hexadécimale comme celle illustrée ci-dessous. Développez cela, puis développez la zone "pas de domaine" et vous verrez votre script:

{{image ('/ static / img / docs / tutorials / custom-javascript-tutorial / script-in-sources.png', 303, 277, layout = 'intrinsic', alt = 'amp-script JavaScript dans le panneau Sources de DevTools ', align =' center ')}}

# Ajout de notre JavaScript

Maintenant que nous savons que notre `<amp-script>` fonctionne, écrivons du JavaScript!

La première chose que nous voulons faire est de récupérer les éléments DOM avec lesquels nous allons travailler et de les cacher dans des globals. Notre code utilisera l'entrée du mot de passe, le bouton Soumettre et la zone qui affiche les règles de mot de passe. Ajoutez ces trois déclarations à `validate.js` :

```js
const passwordBox = document.getElementById("passwordBox");
const submitButton = document.getElementById("submitButton");
const rulesArea = document.getElementById("rules");
```

Notez que nous sommes en mesure d'utiliser des méthodes API DOM classiques comme `getElementById()` . Bien que notre code s'exécute dans un worker, et que les workers ne disposent pas d'un accès direct au DOM, `<amp-script>` fournit une copie virtuelle du DOM et émule certaines API courantes, répertoriées [ici](https://github.com/ampproject/worker-dom/blob/main/web_compat_table.md) . Ces API nous fournissent suffisamment d'outils pour couvrir la plupart des cas d'utilisation. Mais il est important de noter que seul un sous-ensemble de l'API DOM est pris en charge. Sinon, le JavaScript inclus avec `<amp-script>` serait énorme, annulant les avantages de performance d'AMP!

Nous devons ajouter ces identifiants à deux des éléments. Ouvrez `index.html` , recherchez le mot de passe `<input>` et le `<button>` soumission, et ajoutez les identifiants. Ajoutez également un attribut `disabled` au `<button>` soumission, pour empêcher l'utilisateur de cliquer dessus jusqu'à ce que nous le souhaitions.

```html
&lt;input type=password
       id="passwordBox"

...

&lt;button type="submit" id="submitButton" tabindex="3" disabled&gt;Submit&lt;/button&gt;
```

Recharge la page. Vous pouvez vérifier que ces globaux ont été définis correctement en vérifiant dans la console, comme vous le pouviez avec JavaScript non-worker:

{{image ('/ static / img / docs / tutorials / custom-javascript-tutorial / global-set.png', 563, 38, layout = 'intrinsic', alt = 'Message de la console indiquant que submitButton est défini', align = 'centre' ) }}

Nous ajouterons également des identifiants à chaque `<li>` dans `<div id="rules">` . Chacun de ceux-ci contient une règle individuelle dont nous voulons contrôler la couleur. Et nous supprimerons chaque instance de `class="invalid"` . Notre nouveau JavaScript l'ajoutera quand c'est nécessaire!

```html
&lt;ul&gt;
  &lt;li id="lower"&gt;Lowercase letter&lt;/li&gt;
  &lt;li id="upper"&gt;Capital letter&lt;/li&gt;
  &lt;li id="digit"&gt;Digit&lt;/li&gt;
  &lt;li id="special"&gt;Special character (@$!%*?&amp;)&lt;/li&gt;
  &lt;li id="eight"&gt;At least 8 characters long&lt;/li&gt;
&lt;/ul&gt;
```

## Implémentation de nos vérifications de mot de passe en JavaScript

Ensuite, nous décompresserons les expressions régulières de notre attribut `pattern` . Chaque regex représentait l'une de nos règles. Ajoutons une carte d'objets au bas de `validate.js` qui associe chaque règle au critère qu'elle vérifie.

```js
const checkRegexes = {
  lower: /[a-z]/,
  upper: /[A-Z]/,
  digit: /\d/,
  special: /[^a-zA-Z\d]/i,
  eight: /.{8}/
};
```

Une fois ces globaux définis, nous sommes prêts à écrire la logique qui vérifie le mot de passe et ajuste l'interface utilisateur en conséquence. Nous allons mettre notre logique dans une fonction appelée `initCheckPassword` qui prend un seul argument - l'élément DOM du mot de passe `<input>` . Cette approche cache commodément l'élément DOM dans une fermeture.

```js
function initCheckPassword(element) {

}
```

Ensuite, `initCheckPassword` avec les fonctions et les affectations d'écouteurs d'événements dont nous aurons besoin. Tout d'abord, ajoutez une petite fonction qui rend une règle individuelle `<li>` verte si la règle passe - et une autre qui la rend rouge lorsqu'elle échoue.

```js
function initCheckPassword(el) {
  const checkPass = (el) =&gt; {
    el.classList.remove("invalid");
    el.classList.add("valid");
  };

  const checkFail = (el) =&gt; {
    el.classList.remove("valid");
    el.classList.add("invalid");
  };
};
```

Faisons en sorte que ces classes `valid` et `invalid` deviennent réellement vertes ou rouges. Revenez à `index.html` et ajoutez ces deux règles à la `<style amp-custom>` :

```css
li.valid {
  color: #2d7b1f;
}

li.invalid {
  color:#c11136;
}
```

Nous sommes maintenant prêts à ajouter la logique qui vérifie le contenu du mot de passe `<input>` rapport à nos règles. Ajoutez une nouvelle fonction appelée `checkPassword()` à `initCheckPassword()` , juste avant l'accolade fermante:

```js
const checkPassword = () =&gt; {
  const password = element.value;
  let failed = false;

  for (const check in checkRegexes) {
    let li = document.getElementById(check);

    if (password.match(checkRegexes[check])) {
      checkPass(li);
    } else {
      checkFail(li);
      failed = true;
    }
  }

  if (!failed) {
    submitButton.removeAttribute("disabled");
  }
};
```

Cette fonction effectue les opérations suivantes:

1. Récupère le contenu du mot de passe `<input>` .
2. Crée un indicateur appelé `failed` , initialisé à `false` .
3. Itère chacune de nos expressions régulières et teste chacune par rapport au mot de passe:
    - Si le mot de passe échoue à un test, appelez `checkFail()` pour que la règle correspondante devienne rouge. `failed` définition sur `true` .
    - Si le mot de passe réussit un test, appelez `checkPass()` pour faire passer la règle correspondante en vert.
4. Enfin, si aucune règle n'a échoué, le mot de passe est valide et nous activons le bouton Soumettre.

Tout ce dont nous avons besoin maintenant, ce sont deux auditeurs d'événements. Rappelez-vous comment nous n'avons pas pu utiliser l'événement de `focus` dans AMP? Dans `<amp-script>` , nous pouvons. Chaque fois que le mot de passe `<input>` reçoit l'événement de `focus` , nous afficherons les règles. Et chaque fois que l'utilisateur appuie sur une touche dans cette entrée, nous appellerons `checkPassword()` .

Ajoutez ces deux écouteurs d'événements au bas de `initCheckPassword()` , juste avant l'accolade fermante:

```js
element.addEventListener("focus", () =&gt; rulesArea.removeAttribute("hidden"));
element.addEventListener("keyup", checkPassword);
```

Enfin, à la toute fin de `validate.js` , ajoutez une ligne qui initialise `initCheckPassword` avec l'élément DOM password `<input>` :

```js
initCheckPassword(passwordBox);
```

Notre logique est maintenant terminée! Lorsque le mot de passe correspond à tous nos critères, toutes les règles seront vertes et notre bouton d'envoi sera activé. Vous devriez maintenant pouvoir avoir une interaction comme celle-ci:

<figure class="alignment-wrapper margin-">
  <amp-video width="762" height="564" layout="responsive" autoplay loop noaudio>
    <source src="/static/img/docs/tutorials/custom-javascript-tutorial/finished-project.mp4" type="video/mp4">
    <source src="/static/img/docs/tutorials/custom-javascript-tutorial/finished-project.webm" type="video/webm">
  </amp-video>
</figure>

Si vous êtes bloqué, vous pouvez toujours vous référer au code de travail dans le répertoire `finished_code` .

# Toutes nos félicitations!

Vous avez appris à utiliser `<amp-script>` pour écrire votre propre JavaScript dans AMP. Vous avez réussi à améliorer le composant `<amp-form>` avec votre propre logique personnalisée et vos propres fonctionnalités d'interface utilisateur! N'hésitez pas à ajouter plus de fonctionnalités à votre nouvelle page! Et, pour en savoir plus sur `<amp-script>` , consultez [la documentation de référence](../../../documentation/components/reference/amp-script.md) .
