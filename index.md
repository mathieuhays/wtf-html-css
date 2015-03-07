---
layout: default
---

### Contenu

- [Déclarer un doctype](#doctype)
- [Les mathématiques du Box model](#box-model-math)
- [Unité Rem et Safari Mobile](#rems-mobile-safari)
- [Flottants en premier](#floats-first)
- [Éléments flottant et clear](#floats-clearing)
- [Flottants et hauteur calculée](#floats-computed-height)
- [Les flottants sont des blocs](#floats-block-level)
- [Fusion des marges verticales](#vertical-margins-collapse)
- [Mettre en forme les lignes de tableau](#styling-table-rows)
- [Firefox et les boutons `<input>`](#buttons-firefox)
- [Contour intérieur des boutons dans Firefox](#buttons-firefox-outline)
- [Toujours définir un `type` sur les `<button>`](#buttons-type)
- [Limite du nombre de sélecteur sous Internet Explorer](#ie-selector-limit)
- [Explications des positions](#position-explained)
- [Position et width](#position-width)
- [Position fixed et transform](#position-transforms)


<a name="doctype"></a>
### Déclarer un doctype
Toujours inclure un doctype. Je recommande le tout simple doctype HTML5 :

```html
<!DOCTYPE html>
```

[Omettre le doctype peut causer des erreurs](http://quirks.spec.whatwg.org) comme la malformation des éléments `<table>`, `<input>` et bien d'autres encore vu que le rendu sera fait en "Quirks mode".

<a name="box-model-math"></a>
### Les mathématiques du Box model
Les éléments qui ont une largeur définie (`width`) deviennent *plus large* quand ils ont un `padding` et/ou `border-width`. Pour éviter ces problèmes, utilisez le maintenant répandu [`box-sizing: border-box;` reset](http://www.paulirish.com/2012/box-sizing-border-box-ftw/).

<a name="rems-mobile-safari"></a>
### Unité Rem et Safari Mobile
Pendant que Safari Mobile supporte l'utilisation des `rem` pour toutes les propriétés, il semblerait que ça foire quand les `rem` sont utilisés avec les "media queries" et causent des flashs incessant de texte sur plusieurs tailles de navigateur.

Pour l'instant, utilisez les `em` à la place des `rem`.

```css
html {
  font-size: 16px;
}

/* Cause le bug du flash dans Safari Mobile */
@media (min-width: 40rem) {
  html {
    font-size: 20px;
  }
}

/* Marche bien dans Safari Mobile */
@media (min-width: 40em) {
  html {
    font-size: 20px;
  }
}
```

**Aide!** *Si vous avez un lien pour le signalement de ce bug chez Apple ou Webkit, j'aimerai bien l'inclure. Je ne suis pas sur où signaler ça vu que ça ne s'applique qu'à la version Mobile de Safari.*


<a name="floats-first"></a>
### Flottants en premier
Les éléments flottant (`float`) devraient toujours être présent en premier dans l'ordre du document. Ces éléments ont souvent besoin d'un conteneur autrement ils pourraient créer un décalage et apparaître après le contenu.

```html
<div class="parent">
  <div class="float">Float</div>
  <div class="contenu">
    <!-- ... -->
  </div>
</div>
```


<a name="floats-clearing"></a>
### Éléments flottant et clear
Si vous appliquez la propriété `float`, vous aurez *probablement* besoin d'appliquer `clear` sur un élément adjacent. Tout contenu positionné après un élément flottant l'entourera jusqu'à ce qu'il soit interrompu (avec `clear`).

Utilisez [la technique du clearfix](http://nicolasgallagher.com/micro-clearfix-hack/) pour rétablir le rendu normal avec une classe séparée.

```css
.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both;
}
```

Autrement vous pouvez aussi définir la propriété `overflow` sur le parent avec pour valeur `auto` ou `hidden`.

```css
.parent {
  overflow: auto; /* clearfix */
}
.autre-parent {
  overflow: hidden; /* clearfix */
}
```

Soyez conscient que la propriété `overflow` peut créer d'autres effets inattendus, généralement autour des éléments positionnés à l'intérieur d'un parent.

**Astuce Pro!** *Gardez votre futur vous et vos collègues heureux en ajoutant un commentaire comme `/* clearfix */` puisque que cette propriété peut être utilisée pour autre chose.*


<a name="floats-computed-height"></a>
### Flottants et hauteur calculée
Un élément parent qui contient uniquement des flottants aura une hauteur calculée de `height: 0;`. Ajoutez un `clearfix` sur le parent pour forcer le navigateur à calculer la hauteur réelle.

<a name="floats-block-level"></a>
### Les flottants sont des blocs
Les éléments avec `float` seront automatiquement définis comme étant `display: block;`. Il est donc inutile de définir les deux vu que le `float` annulera votre `display`.

```css
.element {
  float: left;
  display: block; /* Inutile */
}
```

**Fait marrant :** *Quelques années auparavant, nous devions définir `display: inline;` sur la plupart des flottants pour que ça marche correctement sous IE6 afin d'éviter le [bug de la double marge](http://www.positioniseverything.net/explorer/doubled-margin.html). Toutefois, ces jours sont passés depuis longtemps*


<a name="vertical-margins-collapse"></a>
### Fusion des marges verticales
Sur des éléments adjacents, les marges du haut (`top`) et du bas (`bottom`) peuvent et vont fusionner dans plusieurs situations mais jamais pour des flottants ou des éléments avec `position: absolute;`. [Lisez cet article du MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/margin_collapsing) ou la partie [Fusion des marges](http://www.w3.org/TR/CSS2/box.html#collapsing-margins) des spécifications techniques de CSS2 pour en savoir plus.

Les marges horizontales ne **fusionneront jamais**.

<a name="styling-table-rows"></a>
### Mettre en forme les lignes de tableau
Les lignes de `table` (`<tr>`) n'appliquent pas la propriété `border` à moins que vous définissiez `border-collapse: collapse;` sur l'élément `<table>` parent. De plus, si le `<tr>`et les `<td>` (ou `<th>`) qui en découlent ont le *même* `border-width`, les lignes n'auront pas leurs bordures générées. [Exemple sur JS Bin](http://jsbin.com/yabek/2/)


<a name="buttons-firefox"></a>
### Firefox et les boutons `<input>`

Pour des raisons inconnus, Firefox applique un `line-height` aux `<input>` de type *submit* et *button* qui ne peut être changé avec votre CSS. Vous avez alors 2 choix :

1. Utiliser uniquement l'élément `<button>`
2. Ne pas utiliser `line-height` sur vos boutons

Si vous choisissez la première option ( et je recommande celle-ci car les `<button>` sont top), voici ce que vous devez savoir :

```html
<!-- Pas top -->
<input type="submit" value="Enregistrer les modifications">
<input type="button" value="Annuler">

<!-- Super partout -->
<button type="submit">Enregistrer les modifications</button>
<button type="button">Annuler</button>
```

Si jamais vous vouliez choisir la seconde option, contentez-vous juste de ne pas définir `line-height` et utiliser *seulement* le `padding` vertical pour aligner le text dans le bouton. [Regardez cette example (JS Bin)](http://jsbin.com/yabek/4/) dans Firefox pour voir le problème original et comment le contourner.

**Bonne nouvelle!** *Il semblerait que Firefox 30 apportera un [correctif](https://bugzilla.mozilla.org/show_bug.cgi?id=697451#c43). C'est une bonne nouvelle pour nos projets futurs mais n'oubliez pas que le bug restera sur les anciennes versions.*


<a name="buttons-firefox-outline"></a>
### Contour intérieur des boutons dans Firefox

Firefox [ajoute un contour intérieur](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button#Notes) aux boutons (`<input>` et `<button>`) à l'état `:focus`. Apparement ce serait pour des raisons d'accessibilité mais pourtant sont positionnement semble plutôt curieux. Utilisez cette règle pour l'annuler :

```css
input::-moz-focus-inner,
button::-moz-focus-inner {
  padding: 0;
  border: 0;
}
```

Vous pouvez voir ce correctif en action dans le même [exemple (JS Bin)](http://jsbin.com/yabek/4/) cité dans la partie précédente.

**Astuce Pro!** *Pensez à ajouter un état `:focus` sur vos boutons, liens et `<input>`. Permettre une capacité d'accès est primordiale, Aussi bien pour les pro qui utilisent la touche `tab` pour naviguer que les personnes soufrants de déficience visuelle.*

<a name="buttons-type"></a>
### Toujours définir un `type` sur les `<button>`
La valeur par défaut est `submit`, ce qui veut dire que n'importe quel bouton dans un formulaire peut le soumettre. Utilisez `type="submit"` pour les boutons qui doivent soumettre le formulaire et `type="button"` pour les autres.

```html
<button type="submit">Enregistrer les modifications</button>
<button type="button">Annuler</button>
```

Pour toutes actions exigeant un `<button>` n'étant pas dans un `<form>`, utilisez le `type="button"`.

```html
<button class="fermer" type="button">x</button>
```

**Fait marrant :** *Apparement IE7 ne supporte pas correctement l'attribut `value` sur les `<button>`. À la place de lire le contenu de l'attribut, il extrait la valeur grâce à innerHTML (le contenu entre le tag d'ouverture et de fermeture de `<button>`). Néanmoins, je ne considère pas ça comme étant une grosse pré-occupation pour 2 raisons : IE7 n'est plus beaucoup utilisé et il semble plutôt peu commun de définir `value` et le "innerHTML".*

<a name="ie-selector-limit"></a>
### Limite du nombre de sélecteur sous Internet Explorer
Internet Explorer 9 (ainsi que les versions précédentes) ne permettent d'avoir qu'un maximum de 4,096 sélecteurs par feuille de style. Il y a aussi une limite du nombre d'insertion cumulée de feuille de style et de `<style></style>`. Tout ce qui se trouve après cette limite sera ignoré par le navigateur. Soit vous séparez votre CSS en plusieurs feuilles de style, soit vous le ré-organisez ! Je conseillerais le dernier.

Pour votre information, voici comment les navigateurs comptent vos sélecteurs :

```css
/* 1 sélecteur */
.element { }

/* 2 sélecteurs en plus */
.element,
.autre-element { }

/* 3 sélecteurs en plus */
input[type="text"],
.formulaire-controle,
.formulaire-groupe > input { }
```


<a name="position-explained"></a>
### Explications des positions
Les éléments avec `position: fixed;` sont positionnés par rapport au cadre du navigateur (viewport). Les éléments avec `position: absolute;` sont placés par rapport à leurs parent le plus proche ayant une position autre que `static` (e.g., `relative`, `absolute`, `fixed`). 

<a name="position-width"></a>
### Position et width
Ne pas définir `width: 100%;` sur un élément qui a `position: [absolute|fixed];`, `left` et `right`. L'utilisation de `width: 100%;` revient au même que de définir `left: 0;` et `right: 0;`. Utilisez l'un ou l'autre mais pas les deux.

<a name="position-transforms"></a>
### Position fixed et transform
Les navigateurs cassent l'effet de `position: fixed;` sur un élément quand un de ses parents possède la propriété `transform`. L'utilisation de `transform` créer un nouveau conteneur qui force le parent à avoir `position: relative;` et l'élément `fixed` à réagir comme un `position: absolute;`.

[Voir la démo](http://jsbin.com/yabek/1/) et lire [l'article d'Eric Meyer sur ce problème](http://meyerweb.com/eric/thoughts/2011/09/12/un-fixing-fixed-elements-with-css-transforms/).
