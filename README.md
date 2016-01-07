# SMACSS (Scalable and Modular Architecture for CSS)

#### **Une méthodologie pour organiser son CSS**

#### Le coeur de SMACSS est la categorisation. Il y a 5 types de categories:
----
1. Base
2. Mise en Page
3. Module
4. Etat
5. Theme
6. Sources et liens utiles 

#### 1- Base :
----
Concerne les éléments de base html comme body ou h1 etc.
Pour ces formes, il peut être utile d'utiliser des styles récurrents.
Eviter le plus possible les CSS resets (des styles qui en annulent d'autres) ou savoir les utiliser à bon escient.

```css
body, form {
    margin: 0;
    padding: 0;
}

a {
    color: #039;
}

a:hover {
    color: #03F;    
}
```
#### 2- Mise en page
---
* Concerne des éléments de type ID.

* On définit en priorité la position des éléments.

* Lorsqu'une classe modifie la position, on la déclare avec l'ID correspondant
```css
.l-grid {
    margin: 0;
    padding: 0;
    list-style-type: none;
}

.l-grid > li {
    display: inline-block;
    margin: 0 0 10px 10px; 
    
    /* IE7 hack to mimic inline-block on block elements */
    *display: inline;
    *zoom: 1;
}
```
Lorsque l'on veut généraliser une classe, on n'utilise pas les ID mais on cible classe et élément pour pouvoir réutiliser le style et réduire de même la longueur du code CSS en évitant de le dupliquer


#### 3. Module
---
Il est à l'intérieur des composants de la mise en page. Chaque module est  "modifié" dans le but de faire exister un composant de manière autonome.

Il faut faire attention de ne pas créer plusieurs styles différents pour des modules qui peuvent avoir la même fonctionnalité au risque d'écraser leurs styles.

Partir sur une structure modulaire est une bonne pratique à appliquer pour créer et maintenir un CSS flexible et réutilisable. A titre d’exemple, les modules les plus classiques sont: la navigation et les boutons.

### 4. Etat
---
Il s’agit de classes qui viennent définir un état particulier dans lequel se trouvent les éléments.

Généralement, une règle qui se trouve dans cette catégorie correspond à une simple classe CSS.
En effet, ce genre de règles déterminent un état final du design, qui doit s’appliquer quoiqu’il advienne. Si le code est "propre", on ne doit pas avoir deux états s’appliquant sur le même élément et modifiant les mêmes attributs.

```css
.tab {
    background-color: purple;
    color: white;
}

.is-tab-active {
    background-color: white;
    color: black;
}
```
#### 5.Theme
----
Parfois dans un projet peut comporter différents thèmes qui peuvent etre changés en fonction des choix de l'utilisateur ou de l'équipe.

Alors on peut décupler l'apparence de la structure des modules pour pouvoir changer facilement de designs.

l'idée est donc de séparer les règles qui définissent le thème dans un fichier CSS relatif au thème.

Exemple: 
```css    
.theme-ocean {
    font-family: "Roboto", sans-serif;}

.theme-ocean header {
    background-color: #33F;
    color: #A66105;}
```

# Aspects de SMACCS
----
## Profondeur d'application
Dans un style commun tel que :

```css
#sidebar div {
    border: 1px solid #333;
}

#sidebar div h3 { 
    margin-top: 5px;
}

#sidebar div ul {
    margin-bottom: 5px; 
}
```
On arrive facilement à voir la structure de la page, mais en conséquence, le style ne sera pas réutilisable lorsque l'on change la structure de la page.

Sur un code comme celui-ci :

```css
#sidebar div, #footer div {
    border: 1px solid #333;
}

#sidebar div h3, #footer div h3 { 
    margin-top: 5px;
}

#sidebar div ul, #footer div ul {
    margin-bottom: 5px; 
}
```

On peut diminuer la profondeur de champ en partant de la div à laquelle on va ajouter une classe css (.pod):

```
.pod {
    border: 1px solid #333;
}

.pod > h3 { 
    margin-top: 5px;
}

.pod > ul {
    margin-bottom: 5px; 
} 
```
L'avantage de ce style est de pouvoir être réutilisable dans d'autre cas comme les templates mustache par exemple :

```
html
<div class="pod">
    <h3>{{heading}}</h3>
    <ul>
        {{#items}}
        <li>{{item}}</li>
        {{/items}}
    </ul>
</div> 
```

Il faut essayer de trouver un équilibre entre maintenance, performance et lisibilité. Le principe est de ne pas appliquer de classes inutiles et de les utiliser à des endroits pertinents. Afin de rendre le code plus ouvert et flexible.

On peut aussi dupliquer les règles :

```css
.pod > ul, .pod > ol, .pod > div {
    margin-bottom: 5px; 
} 
```

Ou simplifier en classifiant pour réutiliser le style par exemple :

```css
.pod-body {
    margin-bottom: 5px; 
}
```

À l'intérieur de ce template:

```
html
<div class="pod">
    <h3>{{heading}}</h3>
    <ul class="pod-body">
        {{#items}}
        <li>{{item}}</li>
        {{/items}}
    </ul>
</div> 
```

## Performance des sélecteurs
---
Pour optimiser la performance de rendu de la page, il faut comprendre la façon dont est lu le code par le navigateur, le HTML est évalué comme un flux de données, et chaque élément peut commencer à être traîté alors que le serveur n'a pas encore envoyé tout le document. Exemple :

```
html
<body>
   <div id="content">
      <div class="module intro">
         <p>Lorem Ipsum</p>
      </div>
      <div class="module">
         <p>Lorem Ipsum</p>
         <p>Lorem Ipsum</p>
         <p>Lorem Ipsum <span>Test</span></p>
      </div>
   </div>
</body>
```
Lorsque le navigateur arrive sur la première ligne ```<body>```, rien d'autre n'existe pour lui et il va aller chercher le style CSS qui lui correspond, puis il descend sur la ligne ```<div id="content">``` et fait pareil, il va chercher le CSS qui correspond. Et ceci jusqu'à la fin du document.

Le CSS est lu de droite à gauche par exemple :

```css
body div#content p { color: #003366; }
```
Le navigateur va chercher un élément <p>, puis va vérifier s'il est contenu dans un élément <div id="content">, puis s'il le trouve, il recherche s'il est contenu dans le body. Par la suite, il applique le CSS à l'élément correspondant.

#### Quelle règle appliquer ?
En général, pour améliorer la vitesse d'exécution de la page, il est préférable d'utiliser le moins de sélecteurs possible voire un seul, selon les recommandations de Google Page Speed.
Trois règles sont appliquées dans le cas du SMACSS :
utiliser des sélecteurs enfant (child selectors)
éviter les sélecteurs tags pour les éléments communs
utiliser les classes pour les sélecteurs le plus à droite

#### BEM : Block, Element, Modifier
----
##### Definition : 
Façon de nommer les classes en CSS   
La méthodolgie de BEM permet d'accélerer le processus de développement et de facilter le travail en équipe.

##### Block:
conteneur , ce qui englobe le tout il représente le niveau supérieur d'une abstraction ou d'un composant 

EX: ```.block{} ```

Un block c'est par exemple une en-tete, un pied de page, un bouton, la navigation...

##### Élements: 
c'est une partie d'un block / il représente un descendant de .block.

EX:  ```.block__element {}```

Un element peut etre un titre dans un block d'en-tete ou un item dans un block de navigation.

##### Modifier : 
change l'état de l'élement. Donc il peut s'appliquer autant à un block qu'à un élément./ Il représente un état ou une version différente de .block. Cette propriété sert à créer des variantes pour faire des modifications minimes comme par exemple changer des couleurs. 

EX: ```.block--modifieur {}```


Exemple:

    ```
    .personne {},
    .personne--femme {},
    .personne__main {},
    .personne__main--gauche {},
    .personne__main--droite {},
    ```

Ici notre block est une personne, nous la modifions pour indiquer qu'il s'agit d'une femme.
Partant du postulat qu'il s'agit d'une femme. Nous ajoutons ensuite l'element main de femme(personne). Puis nous modifions cet élément pour indiquer que nous parlons de la main gauche, puis en dessous de la main droite.

> Attention, l'indentation est importante en fonction du block.

Les blocks et les éléments doivent avoir un nom unique, qui sera utilisé comme class CSS.

Les sélecteurs CSS ne doivent pas utiliser les noms des éléments HTML.

##### 1. Méthodologie BEM
Il permet de faciliter le travail du développement en équipe.

##### 2. Outils BEM
Les outils BEM peuvent faire:
* créer des entités
* construire la construction finale de l'exution
* gérer des constructions par des déclarations, comme tel que fusionner, soustraire et crosier entre eux, etc...
* construire l'ensemble de projet en utilisant ## bem make ##(commande)
* lancer un serveur du développeur sur la source des projets en utilsiant ## bem server ##(commande)

#### SASS (syntactically awesome style sheets)
---
##### Présentaton de SASS
---
Sass est un langage de génération dynamique de feuille de style CSS. Il permet d'utiliser des fonctionnalités telles que : les variables, les mixins, l'héritage de sélection et différents options très utiles.
 
##### Les variables
---
Une variable permet de stocker n'importe quelles informations (couleur, taille, texte, etc.) dans un objet que l'on nomme $MonObjet.
Exemple : 

```css
$bleu : #123DD3
$vert_fluo : #6654RF4
$jaune_pipi : #999878
```
On peut ensuite réutiliser ces variables stockées dans notre fichier SASS dans notre fichier CSS : 

```
H1 {
    color : $bleu
}

H2 {
    color : $jaune_pipi
}
```

#Sass
###Présentation & installation

Installation de Sass selon l'os.

**Sur Mac OSX :**
```
sudo gem install sass
```

On utilise "sudo" car l'installation d'une gem effectue une modification sur le système, il faut donc prouver a l'ordinateur que l'on est administrateur.

**Sur linux & Windows :**

- Crée un fichier gulpfile.js a la racine de votre projet.
- A l'intèrieur du fichier coller y ce contenue https://gist.github.com/elgamine-dev/2fc42f3bb1770265cd06

- il faut ouvrir un terminal et tapez y c'es commande :
```
sudo npm install --g gulp browser-sync
npm install --save-dev gulp-sass
```

Et pour finir :
```
gulp serve
```
-Pour avoir la coloration syntaxique sur sublimeText installer SASS via Package controle : ctrl + alt + p

##### Les mixins

Les mixins restent la meilleure avancée en terme d'innovation et vous permettent de rendre votre travail encore plus efficace et plus rapide. Les mixins vont vous permettre de réutiliser des pans entiers de CSS, propriétés ou des sélecteurs. Vous pouvez même leur donner des arguments afin de créer des mises en forme complexes en utilisant seulement une seule ligne de code !

Fichier SASS

```
@mixin border-radius($radius) {
    -webkit-border-radius: $radius
    -moz-borsder-radius: all $radius
    -ms-border-radius: all $radius

.box { @include border-radius(10px); }
```

Fichier CSS

```
css
.box {
  -webkit-border-radius: 10px;
  -moz-border-radius: 10px;
  -ms-border-radius: 10px;
  border-radius: 10px;
}
```


#### Sources et Liens utiles :
    
    SASS:
    Site officiel: http://sass-lang.com/
    Tutoriel français : https://la-cascade.io/se-lancer-dans-sass/
    Autre tuto en français : http://blog.humancoders.com/pourquoi-sass-et-compass-49/
    
    BEM:
    Site oficiel: https://en.bem.info/
        
    SMACSS:
    Site officiel:  https://smacss.com/
