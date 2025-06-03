# Version implémentée en 2023

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
