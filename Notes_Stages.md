# Version implémentée en 2023

R3Mob (Réseau régional de recherche sur les nouvelles mobilités) est un réseau de recherche pluridisciplinaire porté par Bordeaux INP et soutenu par la région Nouvelle-Aquitaine. Il vise à fédérer les forces académiques travaillant sur les thématiques de mobilité innovante.

## Architecture MVC (Modèle / Vue / Contrôleur)

- **Vue** : Côté client  
- **Contrôleur** : Express.js avec les envois de requêtes  
- **Modèle** : Contient les données manipulées par le programme  

### Traitement des données
- Export des données à partir de la base de connaissances **The Brain**  
- Analyse des informations pour en extraire du contenu cohérent  
- Alimentation de notre propre base de données sur le site  

## Non implémenté

- Gestion des entités telles que :
  - Chercheurs
  - Partenaires
  - Stages  
- Forum de discussion entre chercheurs sur des thématiques précises  

---

# Ce que je compte faire en premier lieu

## Audit complet du site R3Mob avec Screaming Frog

### Points à vérifier :

- **Codes de réponse HTTP** :
  - Identifier les erreurs `4XX` (pages non trouvées)
  - `5XX` (erreurs serveur)
  - Redirections `3XX`  
  → *Onglet : Response Codes*

- **Liens cassés (Broken links)** :
  - Liens internes et externes menant à des erreurs  
  → *Onglets : Internal et External*

- **Titres de pages (Title tags)** :
  - Vérifier unicité, mots-clés, etc.  
  → *Onglet : Page Titles*

- **Poids des images** :
  - Identifier les images trop lourdes qui ralentissent le site  

- **Attributs ALT des images**

- **Analyse des redirections** :
  - 301, 302

---

# Version implémentée en 2024

## Fichier principal

- **`index.js`** : Point d’entrée de l’application
  - Importe le composant principal `App` (défini dans `App.js`)
  - Affiche le tout dans la balise `<div id="root">`
  - Initialise l’état global de l’authentification
  - Vérifie si l’utilisateur est connecté au démarrage
  - Affiche un écran de chargement
  - Gère la navigation entre les pages via le système de routes

- **`reportWebVitals`** : Mesure des performances :
  - `getCLS` : Stabilité visuelle
  - `getFID` : Réactivité
  - `getFCP` : Temps d’affichage du premier contenu
  - `getLCP` : Temps de chargement du contenu principal

## Page d’accueil - `Home.js`

- 5 appels API parallèles pour optimiser les performances  
- Récupération des 3 éléments les plus récents pour chaque section  
- ⚠️ Pas de gestion d’erreur dans les appels API *(point d’amélioration)*  
- **Patterns de développement** :
  - Rendu conditionnel (n’afficher que si des données existent)
  - Fallbacks pour éviter les erreurs d’affichage  
- Séparation claire : styles dans des fichiers dédiés

## Authentification + Inscription - `Login.js`

### Fonctions principales :

- `useEffect` : Récupère automatiquement l’email si un ID est passé en paramètre d’URL  
- `handleSignupClick` : Gère l’inscription via POST `/auth/register`  
- `login` : Gère la connexion via `/auth/login` et stocke le token  
- `handleKeyDown` : Valide le formulaire avec la touche Entrée 

- Point Important à noter : handling du trouver votre adresse mail a l'aide d'une popup ou on tape des lettres et ca nous affiche des mails dans la base de données qui contiennent les lettres que l'on a tapées.


Améliorations possibles pour cette partie: 

- `Gestion d'erreurs améliorée` : Remplacer les alert par des messages d'erreur visuel
- `Créer une fonction de validation globale` : pour les formulaires 


---

 ## Accès à l'Agenda + Évènements - `Agenda.js et Event.js`

 ### Fonctions principales : 
 1. La fonction `filteredEvents` applique plusieurs filtres successifs :

- **Filtre par nom** : recherche insensible à la casse  
- **Filtre par date** : comparaison exacte des dates formatées  
- **Filtre par localisation** : présentiel, distanciel ou tous  
- **Filtre par statut temporel** : événements passés, futurs ou en cours  
- **Tri** : par date croissante ou décroissante

2. Pagination

Le système de pagination divise les événements en pages de 6 éléments :

- `eventsPerPage = 6`
- Calcul du nombre total de pages
- Extraction des événements de la page courante avec `slice()`

3. Gestionnaires d'événements

Plusieurs fonctions gèrent les changements de filtres :

- `handleEventNameFilterChange` : mise à jour du filtre nom
- `handleDateFilterChange` : gestion du sélecteur de date
- `handleEventLocationFilterChange` : changement de localisation
- `handleResetFilters` : réinitialisation de tous les filtres

Dans Event.js : 
- getAllEvents() : Récupère tous les événements standards
- getAllCustomEvents() : Récupère tous les événements personnalisés

# Routes

## `GET /combined`  
Combine tous les événements (`Event` + `CustomEvent`) pour les utilisateurs authentifiés.

## `GET /public`  
Filtre uniquement les événements publics (`isPrivate === 'f'`)

## `GET /3recent`  
Retourne les 3 événements publics les plus récents

## `GET /`  
Tous les événements publics triés par date décroissante

## `GET /all`  
Tous les événements sans filtre de confidentialité


---

# Opérations

## Création

- `POST /` : Crée un nouvel événement standard  
- `POST /custom` : Crée un événement personnalisé  

## Lecture

- `GET /custom` : Récupère tous les événements personnalisés  
- `GET /lastCustom` : Retourne l'ID du dernier événement personnalisé créé  

## Mise à jour

- `PUT /:id` : Met à jour un événement standard  
- `PUT /custom/:id` : Met à jour un événement personnalisé  
- `PUT /changePrivacy/brain/:id` et `PUT /changePrivacy/custom/:id` : Modifient la confidentialité  

## Suppression

- `DELETE /custom/:id` : Supprime un événement personnalisé


-> Erreur quand on veut choisir Evenements futurs , la requete ne se finit pas d'ou le handling des erreurs (recherche indéfinie) : fonctions de filtrage à revoir

## Projets - `Projet.js + Thematique.js + sous-Thematique.js`

Le système de filtrage est sophistiqué avec plusieurs niveaux :

- **Filtrage par titre de projet** (insensible à la casse)
- **Filtrage par établissement** avec recherche partielle
- **Filtrage par thématique et sous-thématique**
- **Filtrage par état et enjeux**
- **Pagination** avec 6 éléments par page

Problèmes de performance côté frontend
  - Trop de re-rendus potentiels à cause du nombre élevé d'états
  - Filtrage côté client peut être lent avec de gros datasets
  - Appels API multiples au chargement initial


## Update des events - `UpdateEvent.js`  ⚠️ (A revoir)

 - Ce composant React gère l'affichage et la modification d'événements dans une interface d'administration
 - Il permet de visualiser, modifier, créer et supprimer des événements de deux types : les événements "brain" et les événements "custom"
 - il y a la gestion de plusieurs fonctionnalités : (en étant connecté sur le site en mode Admin)  
   -  Suppression d'évènements custom (handleDeleteEvent)
   -  Modification d'évènements (handleSaveEventChanges)
   -  Création d'évènements (handleCreateCustomEvent)
   -  Gestion de la confidentialité des événements (handleTogglePrivacyBrain et handleTooglePrivacyCustom)

   - interface Utilisateur : Le composant affiche un tableau avec les colonnes : Titre, Date, Lieu, Status, et Actions. Il utilise des boutons pour basculer entre les types d'événements et des icônes pour les actions de modification et suppression

## Liste des acteurs de R3Mob : `Chercheur.js`
- afficher, filtrer et paginer une liste de chercheurs (ou "acteurs R3MOB") selon différents critères.

- La variable `filteredPersos` applique plusieurs filtres en chaîne sur la liste des chercheurs :

 - **Filtre par nom** (recherche en texte).
 - **Filtre par établissement** (en cherchant dans les établissements associés au chercheur).
 - **Filtre par type de personnel** (tous, ou un type précis).
 - **Filtre par type de comité** (tous, comité exécutif, comité de pilotage).
 - **Filtre par thématique** (en vérifiant les thématiques associées au chercheur).
 - **Filtre par sous-thématique** (en vérifiant les sous-thématiques associées).

 - les chercheurs filtrés sont triés alphabétiquement selon le choix de l'utilisateur (décroissant ou croissant)
 - 9 chercheurs par page

 ## Liste des ressources du site : `Ressources.js`
  # Pistes d'amélioration

  1. **Optimisation des appels API**

   - **Problème :** L’appel à `axios.get('http://localhost:3001/videos')` dépend du tableau `videos` dans le `useEffect`, ce qui provoque un rechargement infini dès que `setVideos` est appelé.  
   - **Solution :** Utiliser un tableau de dépendances vide (`[]`) pour charger les vidéos une seule fois au montage.

  2. **Gestion des filtres**

  - **Problème :** Le filtrage des chercheurs dans les publications est peu robuste (découpe sur les espaces, recherche sur chaque partie du nom uniquement).  
  - **Solution :** Améliorer la logique pour permettre la recherche sur le nom complet et ignorer la casse / espaces superflus.

  3. **Performance du filtrage et du rendu**

   - **Problème :** Tous les filtres sont recalculés à chaque rendu, même si les données n’ont pas changé.  
   - **Solution :** Utiliser `useMemo` pour mémoïser le résultat du filtrage.

  4. **Structure des `useEffect`**

   - **Problème :** Certains `useEffect` ne sont pas optimisés et peuvent provoquer des appels multiples ou non nécessaires.  
   - **Solution :** Regrouper les appels, éviter les dépendances inutiles et ne pas mélanger logique et effets de bord.

  5. **Lisibilité et factorisation**

   - **Problème :** Le code des filtres et des handlers est redondant.  
   - **Solution :** Factoriser les handlers, commenter les parties complexes, et séparer la logique métier de la présentation.

# Pistes d'amélioration

- **Numéros de port** :
  - Utiliser un fichier de configuration pour définir les ports (ex. `PORT_SERVEUR = 3001`) -> afin d'améliorer la maintenabilité du site sans trop toucher au code source.
  - Importer cette variable dans le code plutôt que d’utiliser une valeur brute

- **Affichage** :
  - Certaines images apparaissent étirées
  - Certaines rubriques ne s’affichent pas
  - Des éléments dans l’entête du site sont mal alignés

- **Documentation** :
  - Ajouter des instructions sur le lancement de la base de données avec **MySQL**
  - Utiliser **Docker** pour créer un conteneur avec un port spécifique pour développement ou test

- **Langues** : 
  - Proposer une version anglaise du site pour toucher un public plus large , comme le font R3IA et R3 Tesna (vu dans l'état de l'art de 2023) : Sélecteur de langue FR/EN (en haut de page)
  - S'assurer que la traduction est de qualité et que tous les contenus sont disponibles dans les deux langues 

- **SEO (Search Engine Optimization)** : 
 - il s'agit ici d'essayer d'optimiser le référencement naturel du site en utilisant des mots-clés pertinents dans les titres, les descriptions et les contenus.

 - Créer un sitemap pour faciliter l'indexation du site par les moteurs de recherche: deux types
 de sitemap: XML(recommandé) ou HTML(plus difficile car il faut le construire manuellement)

 -> Un sitemap XML est un fichier qui liste toutes les pages importantes d'un site web pour aider les moteurs de recherche (Google, Firefox, etc.) à les découvrir et les indexer plus efficacement. C'est comme un plan du site destiné aux robots d'indexation. 

 Pour la liste des pages disponibles (ou routes) dans le site , on peut utiliser ce bout de code à copier dans la console de l'inspecteur :

 ```javascript
(function() {
  const links = Array.from(document.querySelectorAll('a[href]'));
  const internalLinks = links
    .map(a => a.href)
    .filter(href => href.includes(window.location.hostname))
    .map(href => new URL(href).pathname)
    .filter((path, index, arr) => arr.indexOf(path) === index)
    .sort();
  
  console.log('Routes trouvées:');
  internalLinks.forEach(path => console.log(path));
})();
``` 
