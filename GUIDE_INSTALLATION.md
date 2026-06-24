# Comptage Caisse — La Marmite Bleue

## Mise en route (5 minutes)

### 1. Créer le projet Firebase
1. Va sur https://console.firebase.google.com
2. Crée un nouveau projet, par exemple `caisse-marmite-bleue`
3. Dans le menu de gauche → **Compilation > Firestore Database** → "Créer une base de données"
   - Choisis le mode **production**
   - Région : `eur3 (europe-west)` recommandé
4. Une fois créée, va dans **Règles** et remplace temporairement par :
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
}
```
   ⚠️ Ces règles ouvrent l'accès à tout le monde qui connaît l'URL. C'est suffisant pour un usage interne avec une URL non partagée, mais si tu veux sécuriser davantage plus tard (ex: avec un code PIN ou une authentification), dis-le-moi et je l'ajoute.

### 2. Récupérer la config
1. Dans la console Firebase → icône ⚙️ (Paramètres du projet)
2. Descends jusqu'à "Vos applications" → clique sur l'icône **Web** `</>`
3. Donne un nom (ex: "Comptage Caisse"), pas besoin de Firebase Hosting
4. Copie l'objet `firebaseConfig` qui s'affiche

### 3. Coller la config dans le fichier
Ouvre `app.js`, tout en haut (SECTION 1), et remplace :
```javascript
const firebaseConfig = {
  apiKey: "VOTRE_API_KEY",
  authDomain: "caisse-marmite-bleue.firebaseapp.com",
  projectId: "caisse-marmite-bleue",
  storageBucket: "caisse-marmite-bleue.appspot.com",
  messagingSenderId: "VOTRE_SENDER_ID",
  appId: "VOTRE_APP_ID"
};
```
par les vraies valeurs copiées à l'étape 2.

### 4. Héberger sur GitHub Pages (comme tes autres apps)
1. Crée un nouveau repo, ex: `cevapizza11/comptage-caisse`
2. Mets `index.html` et `app.js` à la racine
3. Active GitHub Pages dans Settings > Pages > Branch `main` / `root`
4. Ton app sera accessible à `https://cevapizza11.github.io/comptage-caisse`

Ajoute-la à l'écran d'accueil de ton iPhone (Safari > Partager > Sur l'écran d'accueil) pour l'utiliser comme une vraie appli.

---

## Comment fonctionne l'app

### Écran "Nouveau"
- Choisis **Ouverture** (fond de caisse en début de service) ou **Clôture** (fin de service)
- Sélectionne la caisse/poste et le service
- Pour une clôture, tu peux renseigner le **fond de caisse de départ** et le **CA théorique** (relevé Z ou TPE) → l'écart se calcule automatiquement
- Saisis le nombre de billets et pièces avec les boutons +/− ou en tapant directement le chiffre
- Le total se met à jour en temps réel
- Enregistre : ça part dans Firestore et apparaît dans l'historique

### Écran "Historique"
- Liste tous les comptages, filtrable par caisse et par période
- Un badge coloré indique l'écart (vert = juste, jaune = petit écart, rouge = écart important)
- Clique sur un comptage pour voir le détail, le modifier ou le supprimer

### Écran "Stats"
- Vue d'ensemble des écarts sur les clôtures avec rapprochement
- Moyenne, cumul, répartition (juste/petit écart/écart important)
- Détail par caisse — utile pour repérer si une caisse en particulier a souvent des écarts

### Écran "Réglages"
- Gère la liste des caisses/postes et des services (ajout/modification/suppression)
- Définit le seuil au-delà duquel un écart est considéré "important" (rouge). Par défaut 5€.

---

## Notes techniques
- Firebase SDK en mode **compat** (comme SCI Thomarion) pour éviter les soucis de scope avec les `onclick`
- Fonctionne hors-ligne grâce à la persistance Firestore activée (`enablePersistence`) — utile si le réseau du restaurant est instable, la saisie se synchronise dès que la connexion revient
- Single fichier HTML + un fichier JS, pas de build, pas de dépendances autres que le SDK Firebase chargé depuis un CDN
- Code organisé en sections numérotées (SECTION 1 à 14) dans `app.js` pour que je puisse te faire des patchs ciblés plus tard sans tout réécrire
