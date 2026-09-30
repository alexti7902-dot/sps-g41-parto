# Projet partogramme

Application web pour une salle de naissance : suivre le travail d'une patiente
(courbe de dilatation), gérer les patientes, leurs grossesses, les enfants,
les praticiens, et exporter le suivi en Excel. La maquette à respecter est
`maquette.png`.

## Comment c'est fait

- Site statique sans étape de build : `index.html` + `style.css` + fichiers JS.
- Une seule page avec 5 onglets : Suivi du travail, Patientes, Grossesses,
  Enfants, Praticiens.
- Bibliothèques depuis le CDN (dans `index.html`) : Supabase JS, Chart.js
  (courbe de dilatation), ExcelJS (export Excel), police Inter.
- Rôle de chaque fichier JS :
  - `config.js` : URL et clé Supabase (écrit par Connecter-Supabase.bat).
  - `app.js` : client Supabase, navigation entre onglets, page Patientes,
    fonctions partagées (`afficherPage`, `afficherMessage`, `formaterDate`,
    `cellule`).
  - `grossesses.js`, `examens.js`, `enfants.js`, `praticiens.js` : la page du
    même nom. `examens.js` gère aussi le tableau de bord du suivi.
  - `export.js` : export Excel du suivi (bouton « Exporter en Excel »).

## Base de données Supabase

Projet : `kgbbbzawcjwvogengalv` (connecté au MCP `supabase`). RLS activée sur
toutes les tables, ouverte en lecture/ajout/modification (données fictives du TP).

6 tables dans le schéma `public` (clés primaires en `id_...`) :

- `patiente` : nom, nom_marital, prenom, gs_rh, date_naissance.
- `praticien` : nom, prenom, specialite.
- `grossesse` : id_patiente → patiente, debut, parite, hiv, toxo,
  duree_travail et duree_expulsion (type `time`), et 4 praticiens référents
  (id_sage_femme, id_obstetricien, id_anesthesiste, id_pediatre → praticien).
- `enfant` : id_grossesse → grossesse, prenom, sexe, date_heure_naissance,
  poids_naissance_kg, taille_cm.
- `examen` : id_grossesse → grossesse, date_heure, dilatation (0–10),
  presentation_hauteur (-5 à 5), id_orientation → orientation, remarque.
- `orientation` : nom_orientation, fichier_pictogramme (9 lignes, table de
  référence, pas modifiée par le site).

Listes de valeurs fixes définies comme types enum dans la base :
`groupe_sanguin` (A+ … inconnu), `specialite` (Sage-femme, Anesthésiste,
Obstétricien, Pédiatre), `sexe` (Garçon, Fille).

## Point à connaître (état actuel, 30/09/2026)

- `config.js` : réparé le 30/09/2026. Il ne contient plus que l'URL du projet
  Supabase et la clé publishable `sb_publishable_r1BD4JuHfx6SoUM5JguQyw_Kd_c5Osm`.
  Ne jamais y remettre une clé secrète. Avant, plusieurs clés étaient collées
  ensemble (dont `sb_secret_...`) : cette clé secrète ayant été exposée dans un
  fichier, il faudrait la révoquer dans le tableau de bord Supabase (pas encore fait).
- Le dossier n'est pas relié à GitHub (pas de `.git`, et `gh` n'est pas
  connecté depuis la réinitialisation du PC) : le site en ligne
  https://sps-g41-parto.professeurpetitchat.com/ renvoie 404. Pour la mise en
  ligne : `gh auth login`, puis suivre la section « Mise en ligne » du
  CLAUDE.md du dossier parent.
- Pour tester en local : Python n'est pas installé sur ce PC ; lancer un petit
  serveur avec Node depuis le dossier `Projets/` (par exemple
  `node -e "…"` sur un port libre), puis ouvrir
  `http://127.0.0.1:<port>/partogramme/index.html`. Le navigateur MCP
  Playwright refuse les adresses `file:`. Un ancien navigateur de test peut
  bloquer le profil Playwright (erreur « browser already in use ») : fermer
  les processus `msedge` lancés avec le dossier `ms-playwright-mcp`.