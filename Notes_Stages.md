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

## Authentification - `Login.js`

### Fonctions principales :

- `useEffect` : Récupère automatiquement l’email si un ID est passé en paramètre d’URL  
- `handleSignupClick` : Gère l’inscription via POST `/auth/register`  
- `login` : Gère la connexion via `/auth/login` et stocke le token  
- `handleKeyDown` : Valide le formulaire avec la touche Entrée 

Améliorations possibles pour cette partie: 

- `Gestion d'erreurs améliorée` : Remplacer les alert par des messages d'erreur visuel
- `Créer une fonction de validation globale` : pour les formulaires 

---

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
