# TalcoMusic

Site de musique protégé par Firebase Auth (mêmes comptes que TalcoITN / TalcoExchange), installable (PWA), page de connexion indexable par Google.

## Variables GitHub (Settings → Secrets and variables → Actions → onglet Variables)
| Variable | Rôle | Défaut |
|---|---|---|
| `SITE_NAME` | nom du site (titre, en-tête, nom de l'appli installée) | `TalcoMusic` |
| `SIGNUP_URL` | adresse ouverte par « Créer un compte » (https://…) | vide → message « lien pas encore disponible » |
| `GOOGLE_SITE_VERIFICATION` | code de la balise Search Console (optionnel) | vide → balise retirée |

Settings → Pages → Source : **GitHub Actions**. Chaque push sur `main` redéploie et génère `sitemap.xml` + `robots.txt` avec l'URL réelle du site.

## Connexion (Firebase, projet exchange-f4322)
- Connexion par **pseudo** (converti en `pseudo@talcoexchange.app`, comme TalcoITN) : un compte TalcoITN fonctionne tel quel. Pas de « mot de passe oublié » (l'adresse est fictive).
- Console Firebase → Authentication → Sign-in method : **E-mail/Mot de passe** activé.
- Firestore → Rules : réserver les données aux utilisateurs connectés, par exemple :
  ```
  rules_version = '2';
  service cloud.firestore {
    match /databases/{db}/documents {
      match /clouddata/{uid}/{document=**} { allow read, write: if request.auth != null && request.auth.uid == uid; }
      match /lyrics/{id} { allow read, write: if request.auth != null; }
    }
  }
  ```
  (si les règles de TalcoITN existent déjà, les fusionner plutôt que les remplacer)
- Si la clé API est restreinte par référent HTTP (Google Cloud → Identifiants), ajouter le domaine GitHub Pages.

## Indexation Google
Seule la page de connexion est publique, donc seule elle est indexée (titre, description, Open Graph, JSON-LD). Le contenu derrière la connexion ne peut pas l'être.
Après le premier déploiement : Search Console → ajouter la propriété → soumettre `…/sitemap.xml`.

## Pistes
`const TRACKS = [];` dans `index.html` est vide : ajouter les morceaux `{ title, artist, url, year }`.
