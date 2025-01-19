Maintenant que le concours est terminé, il est temps de partager un petit truc que je bricole depuis plusieurs mois

### **Introduction**

https://streamable.com/uurnef
(lien vidéo) `https://streamable.com/uurnef`

Ce petit bout de code propose une solution innovante pour créer des animations météorologiques dynamiques, entièrement paramétrables et générées automatiquement en HTML/CSS. Grâce à l’utilisation de macros, il est possible d’obtenir des effets visuels complexes et réalistes adaptés aux conditions météorologiques en temps réel, le tout avec un code optimisé et réutilisable.

### **Objectif principal**

L’objectif est de fournir une boîte à outils qui permet d’intégrer rapidement des animations météorologiques dans n’importe quelle carte HA, sans nécessiter de manipulation directe des styles ou du code HTML. L’automatisation du rendu réduit considérablement les efforts de développement et garantit une expérience visuelle immersive.

### **Fonctionnalités principales**

#### **1. Génération automatique basée sur des données météorologiques**

Le code utilise des données météorologiques en temps réel pour adapter dynamiquement l'apparence des animations :

* **Conditions météo variées** : pluie, neige, brouillard, nuages, éclairs, étoiles, etc.
* **Gestion nocturne et diurne** : par exemple, l’affichage des étoiles est activé uniquement lorsque le soleil est en dessous de l’horizon.
* **Adaptation aux conditions spécifiques** : même les cas particuliers comme la grêle ou les orages sont pris en compte et automatiquement reclassifiés.

#### **2. Large paramétrage pour des effets visuels variés**

Le code offre une **personnalisation approfondie**, notamment :

* **Nuages** : taille, opacité, complexité des dégradés, vitesse de déplacement.
* **Gouttes de pluie et flocons de neige** : densité, taille, vitesse de chute.
* **Éclairs** : fréquence et intensité.
* **Effets combinés** : possibilité de superposer plusieurs phénomènes (par exemple, pluie avec éclairs et brouillard).
* **Couleurs dynamiques** : les nuances des éléments visuels (comme les nuages) s’ajustent en fonction des paramètres définis, avec des dégradés réalistes et variés.

#### **3. Effets visuels immersifs et réalistes**

Le projet utilise des techniques avancées de CSS pour offrir des effets esthétiques :

* **Dégradés radiaux multiples** pour simuler des nuages réalistes.
* **Effets de flou et transparence** pour un rendu fluide et naturel.
* **Animations fluides** avec des durées et des trajectoires calculées aléatoirement pour éviter toute répétition visible.

#### **4. Structure modulaire et optimisée**

* Les macros permettent une génération **dynamique** du contenu HTML et CSS. Par exemple, en modifiant simplement un paramètre de la macro, il est possible de générer une animation entièrement différente.
* La gestion séparée des couches **background** et **foreground** facilite l’intégration des effets dans différents contextes (arrière-plan d’un site ou animations au premier plan).
* Le code est **compact et maintenable**, conçu pour être facilement extensible.
* Les CSS fonctionnent aussi bien sur mobile que sur PC.

#### **5. Optimisation des performances**

* Calculs aléatoires contrôlés : les valeurs comme les positions, tailles ou couleurs des éléments sont générées avec des contraintes pour tenter d'offrir un rendu réaliste tout en minimisant les calculs.
* Les animations CSS sont fluides grâce à une utilisation efficace des propriétés de transformation matérielle comme `translate` et `scale`.

### **Installation**

J'ai tenté de rendre l'installation simple, mais c'est un gros morceau quand même...
Prenez le temps de bien lire l'ensemble du sujet ;)
Toutes les étapes y sont décrites pour y arriver seul....

Un archive zip de la dernière version des fichiers (stable) est disponible ici:
https://github.com/Pulpyyyy/meteocss/releases

Il faut installer les élément suivants 
* HTML Jinja2 Template card - https://github.com/PiotrMachowski/Home-Assistant-Lovelace-HTML-Jinja2-Template-card
* layout-card - https://github.com/thomasloven/lovelace-layout-card

Créer un compte sur https://app.ipgeolocation.io/dashboard pour avoir les options de la lune.

Ajouter les informations suivantes dans `/config/secrets.yaml`:
```
ipgeolocation_api: xxxxx
entitie_zone_home_latitude: 47.8xxxxxxx
entitie_zone_home_longitude: 1.9xxxxxx
```

Inclure les packages dans `/config/configuration.yaml` :
```
homeassistant:
  #Packages
  packages:
    demometeo: !include packages/demometeo.yaml
    cssmeteo: !include packages/cssmeteo.yaml
```

* Copiez le fichier de `/packages/demometeo.yaml` (pour créer les entités de la démo) dans le répertoire `/config/packages/` de votre home assistant.
* Copiez le fichier de `/packages/cssmeteo.yaml` (pour créer les vraies entités) dans le répertoire `/config/packages/` de votre home assistant.
* Créez un dashboard et copiez le contenu de la carte lovelace de démonstration `/lovelace/demo.yaml`.
* Ajoutez les libraires jinja `config/custom_templates/rotation.jinja` et `/config/custom_templates/meteo.jinja` dans le répertoire `/config/custom_templates/` de votre home assistant. S'il n'existe pas, créez le !
* Importez toutes les ressources (type images) dans votre `/config/www/images`. S'il n'existe pas, créez le !

  
Quand tout est configuré, et que HA a bien redémarré, vous deviez pouvoir jouer avec le démonstrateur.


Pour utiliser le fonctionnement réel, il faut  
* Remplacer `weather.paris_1er_arrondissement` dans les premières lignes code de `meteo.jinja` et dans la carte ci-dessous par votre propre entité
* Repartir de la carte `/lovelace/real.yaml` pour exploiter les entités réelles.
* Et compléter en plaçant les éléments de votre choix entre le foreground et le background, ou au 1er plan

Et compléter en plaçant les éléments de votre choix entre le foreground et le background, ou au 1er plan. Le placement au premier plan est primordial pour le fonctionnement de tap_action.

## Utilisation

### 1. `generate_content`

#### Description
Cette fonction est responsable de générer les différentes couches de contenu pour l'affichage (arrière-plan ou premier plan). Elle appelle dynamiquement les macros nécessaires en fonction des paramètres.

#### Paramètres

| Nom        | Type    | Valeurs possibles     | Description                                                   |
|------------|---------|-----------------------|---------------------------------------------------------------|
| `layer`    | string  | `background`, `foreground` | Spécifie si le contenu concerne le fond ou le premier plan.    |
| `type`     | string  | `html`, `css`          | Spécifie si l'on génère le HTML ou le CSS.                     |
| `demo`     | bool    | `true`, `false`        | Indique si les paramètres par défaut sont utilisés pour la démo. |

#### Exemple d'appel

- **Générer une scène complète (HTML et CSS) pour l'arrière-plan, en mode démo :**

```
{%- from 'meteo.jinja' import generate_content -%}
{{ generate_content(layer='background', type='html', demo=true) }}
```
Générer une scène de premier plan avec des conditions réelles :
```
{%- from 'meteo.jinja' import generate_content -%}
{{ generate_content(layer='foreground', type='css') }}
