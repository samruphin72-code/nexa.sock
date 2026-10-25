# Nexa Stock — mise en ligne (version hébergée)

Contenu du dossier
- `index.html` : l'application des clients (connexion, stock, opérations, impression, export Excel).
- `admin.html` : TON espace administrateur (clients, abonnements, paiements, reçus).
- `firebase-config.js` : à remplir avec les clés de ton projet Firebase.
- `firestore.rules` : règles de sécurité de la base de données (à publier).
- `manifest.webmanifest`, `sw.js`, `icon-*.png` : installation sur téléphone (PWA).
- `firebase.json` : configuration d'hébergement Firebase.

Important : ce dossier a été préparé sans pouvoir le tester sur un vrai projet Firebase. Fais les tests de l'étape 8 avant de le donner à un client.

## 1. Créer le projet Firebase (gratuit pour démarrer)
1. Va sur https://console.firebase.google.com avec un compte Google, puis « Ajouter un projet » (nom : nexa-stock).
2. Désactive Google Analytics (inutile), puis crée le projet.

## 2. Activer la connexion
Menu « Authentication » → « Commencer » → onglet « Sign-in method » → active **E-mail/Mot de passe**.

## 3. Créer la base de données
Menu « Firestore Database » → « Créer une base de données » → choisis un emplacement proche de tes clients (ex. europe-west) → mode **production**.

## 4. Publier les règles de sécurité
Firestore → onglet « Règles » → efface tout, colle le contenu de `firestore.rules` → « Publier ».

## 5. Récupérer la configuration
Paramètres du projet (roue dentée) → « Tes applications » → icône web `</>` → nom « Nexa Stock » → copie l'objet `firebaseConfig` dans `firebase-config.js`.

## 6. Créer TON compte administrateur
1. Authentication → « Utilisateurs » → « Ajouter un utilisateur » : ton e-mail + un mot de passe solide. Copie son **UID**.
2. Firestore → « Démarrer une collection » : ID `admins` ; ID du document = ton UID ; ajoute un champ `ok` (booléen, true).
Seul ce compte pourra entrer dans `admin.html`.

## 7. Mettre en ligne
Option A — Netlify (la plus simple) : https://app.netlify.com/drop, glisse-dépose le dossier. Tu obtiens une adresse https.
Option B — Firebase Hosting : installe Node.js puis, dans le dossier :
```
npm install -g firebase-tools
firebase login
firebase use --add      (choisis ton projet)
firebase deploy --only hosting,firestore:rules
```
Domaine personnel : ajoute-le dans Netlify (Domain settings) ou Firebase Hosting (Add custom domain). Puis dans Firebase → Authentication → Settings → « Domaines autorisés », ajoute ton domaine.

## 8. Tester avant de vendre
1. Ouvre `https://ton-site/admin.html`, connecte-toi avec ton compte admin.
2. Clients → Nouveau client : e-mail, mot de passe initial (6 caractères mini), offre, durée.
3. Ouvre `https://ton-site/` dans une fenêtre privée, connecte-toi avec ce client : l'écran doit être vide (Plus → Importer ton Excel ou ajoute des produits).
4. Enregistre une entrée et une sortie : Stock et Accueil changent. Ouvre l'appli sur un second appareil : mêmes données.
5. Dans l'admin, mets la date de fin d'abonnement dans le passé : le client passe en lecture seule.
6. Crée un 2ᵉ client : il ne doit voir AUCUNE donnée du premier.

## 9. Installer sur téléphone
Android (Chrome) : menu ⋮ → « Installer l'application ». iPhone (Safari) : Partager → « Sur l'écran d'accueil ».

## 10. Offre et paiement (déjà configurés)
- Offre : **14 jours d'essai gratuit**, puis **35 $ par mois**.
- Paiement : **Airtel Money au 0993302300**. Le client voit ces instructions dans l'appli (Plus → « Payer mon abonnement ») et un bouton WhatsApp pour t'envoyer la capture.
- Validation manuelle : tu reçois la capture, tu vérifies le paiement sur ton téléphone, puis tu cliques « Renouveler » dans l'admin (30 jours ajoutés, paiement enregistré, reçu imprimable). Il n'y a pas de paiement automatique.
- Pour changer le numéro ou le prix : dans `index.html`, cherche `const PAY={` ; dans `admin.html`, onglet Offres pour le prix, et cherche `0993302300` pour le message WhatsApp.
- Le bouton WhatsApp utilise `243993302300` (indicatif RD Congo +243). Si ton numéro WhatsApp est différent ou d'un autre pays, corrige `wa:` dans `const PAY`.
- Plus tard : l'encaissement automatique demande un compte marchand Airtel Money Business (API) ; c'est possible comme évolution.

## 11. Vendre au quotidien
- Nouveau client : crée-le dans l'admin, envoie-lui le lien, son e-mail et son mot de passe (il peut le changer via « Mot de passe oublié »).
- Renouvellement : bouton « Renouveler » → paiement enregistré, abonnement prolongé, reçu imprimable dans Paiements.
- Relance : bouton WhatsApp dans « À relancer ».
- Impayé : « Suspendre » → lecture seule immédiate.

## À savoir
- Les clés de `firebase-config.js` ne sont pas secrètes ; la sécurité repose sur les règles Firestore et les comptes.
- Chaque type de données d'un client est stocké dans un document (limite Firestore ≈ 1 Mo, soit environ 5 000 à 10 000 opérations). Au-delà, un stockage ligne par ligne sera nécessaire.
- Supprimer un client dans l'admin supprime sa fiche et bloque ses écritures ; supprime aussi son compte dans Authentication si besoin.
- Vérifie les tarifs actuels de Firebase/Netlify : l'offre gratuite est limitée et peut évoluer.
- Fais des sauvegardes : les clients exportent en Excel (Plus → Télécharger l'Excel) ; toi, utilise l'export Firestore de la console.
- Prévois contrat/CGV et politique de confidentialité avant de vendre.
