# Constitution SAE-Allonzy

## Principes fondamentaux

### I. Stack

- Tout code du projet est écrit en TypeScript en mode strict. Pas de fichier JavaScript pur, et pas
  de any pour contourner le typage.

- L’application est une application mobile. Tout nouvel écran ou composant est écrit en React
  Native avec Expo.

- L’API est écrite en Node.js avec Express. Toute nouvelle route REST passe par Express, sans autre
  framework serveur.

- Le temps réel (messagerie, mises à jour partagées) passe uniquement par Socket.IO.

- Les données sont stockées dans PostgreSQL. Tout accès à la base passe par Prisma, et toute
  modification du schéma se fait par une migration Prisma. Pas de SQL écrit à la main ni d’autre
  ORM.

- On n’ajoute aucune bibliothèque qui fait doublon avec une technologie de cette liste. Toute
  nouvelle dépendance est validée par le groupe avant d’être ajoutée.

- Le code est formaté avec Prettier et doit passer ESLint sans erreur avant d’être proposé en
  revue.

- Les tests sont écrits avec Jest. Toute nouvelle logique métier (par exemple le calcul des
  meilleurs créneaux ou des dépenses) est livrée avec ses tests.

### II. Architecture

- Tout le code est dans un seul dépôt : l’application mobile dans apps/mobile/, l’API dans
  apps/api/, et les types et formats de données échangés entre les deux dans packages/shared/.
  L’application n’importe jamais l’API, et l’API n’importe jamais l’application.

- Côté application, un nouvel écran est un fichier du dossier app/ qui ne contient ni logique
  métier ni appel réseau. Le code d’une fonctionnalité va dans src/features/<domaine>/. Une
  feature n’importe jamais une autre feature : le code commun va dans src/components/ ou src/lib/.

- Côté API, une nouvelle route s’écrit dans src/modules/<domaine>/, avec un fichier de routes, un
  contrôleur qui lit la requête et appelle le service, et un service qui porte les règles métier.
  Un contrôleur n’accède jamais directement à la base de données.

- Chaque feature de l’application et chaque module de l’API a un dossier __tests__/. Le test d’un
  fichier X.ts s’appelle __tests__/X.test.ts.

### III. Nommage

- Les noms de fichiers, dossiers, variables, fonctions, types, modèles Prisma et routes sont
  toujours en anglais et sans accent ; les commentaires et les textes affichés à l'utilisateur
  sont en français.

- Un mot du métier se traduit toujours de la même façon, jamais par un synonyme : voyage trip,
  groupe group, membre member, profil profile, centre d'intérêt interest, message message, sondage
  poll, dépense expense, solde balance, activité activity, proposition proposal, liste list,
  élément item, photo photo, galerie gallery, calendrier calendar, disponibilité availability,
  créneau slot, trajet itinerary, lieu place.

- Les composants, types et modèles Prisma sont en PascalCase au singulier (Expense), les fonctions
  et variables en camelCase, les constantes en UPPER_SNAKE_CASE ; un fichier porte le nom de ce
  qu'il exporte (ExpenseCard.tsx, useExpenses.ts), sauf les fichiers d'un module API, nommés
  <domaine>.<rôle>.ts.

- Une fonction commence toujours par un verbe (createExpense, removeMember), un booléen par is,
  has ou can, et un dossier de domaine est au pluriel en minuscules (expenses, groups).

### IV. Erreurs et données

- Une erreur n’est jamais ignorée : un catch renvoie l’erreur à l’appelant ou la journalise côté
  API avec son contexte (route, utilisateur, opération), et un catch vide est interdit.

- En cas d’échec, l’API répond toujours avec le bon code HTTP et un corps
  { error: { code, message } } dont le type est défini dans packages/shared, et n’envoie jamais au
  client une trace de pile ni un message de Prisma ou de PostgreSQL.

- Quand une action échoue, l’application affiche toujours à l’utilisateur un message en français
  qui dit quoi faire (réessayer, vérifier sa connexion, corriger un champ), jamais un écran vide ni
  un échec silencieux.

- Les dates sont toujours stockées et échangées en UTC au format ISO 8601, et converties en heure
  locale seulement à l’affichage ; les identifiants sont toujours des UUID générés par la base.

### V. Tests

- Toute nouvelle logique métier (par exemple le calcul des meilleurs créneaux, la répartition des
  dépenses ou le calcul des soldes) est toujours livrée avec ses tests Jest, jamais dans une
  livraison ultérieure.

- Un service d’un module de l’API et une fonction de packages/shared/ ne sont jamais livrés sans
  test.

- Le code n’est jamais livré tant qu’un test échoue.

### VI. Propre au projet

- Toute route REST et tout événement Socket.IO qui lit ou modifie un groupe vérifie toujours côté
  API que l’utilisateur connecté en est membre (et auteur ou administrateur pour une suppression),
  y compris pour servir un fichier photo, et un événement temps réel n’est jamais émis ailleurs
  que dans la room de ce groupe.

- Un montant est toujours un entier en centimes avec sa devise, jamais un nombre à virgule, la
  somme des soldes d’un groupe vaut toujours zéro, et une dépense n’est jamais effacée quand son
  payeur ou un participant quitte le groupe ou supprime son profil.

- Aucun secret (mot de passe de base, clé d’API, secret de jeton) n’est jamais écrit dans le code
  ni commité : il vient d’un fichier .env ignoré par git, décrit sans valeur dans .env.example, et
  n’est jamais placé dans l’application mobile.

- Une commande ou une migration qui efface des données (prisma migrate reset, db push
  --accept-data-loss, suppression d’une table ou d’une colonne remplie) n’est jamais lancée ni
  écrite sans l’accord explicite du groupe.

## Gouvernance

- Cette constitution prime sur toute autre pratique du projet. Toute revue de code vérifie que le
  changement la respecte.

- Toute modification de cette constitution est validée par le groupe et passe par
  /speckit-constitution, qui met à jour la version et la date de dernière modification.

- La version suit le versionnage sémantique : MAJEURE quand une règle est retirée ou redéfinie,
  MINEURE quand une règle ou une rubrique est ajoutée, CORRECTIVE pour une reformulation sans
  changement de sens.

**Version**: 1.0.0 | **Ratified**: 2026-11-20 | **Last Amended**: 2026-11-20
