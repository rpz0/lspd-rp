# 🚔 LSPD — Guide de déploiement complet
## GitHub + Netlify + Supabase (100% gratuit)

---

## VUE D'ENSEMBLE

```
Tu modifies index.html
        ↓
Tu push sur GitHub
        ↓
Netlify détecte le changement
        ↓
Site mis à jour automatiquement ✅
        ↓
Toutes les données dans Supabase (partagées entre agents)
```

---

## ÉTAPE 1 — Créer la base de données Supabase

### 1.1 Créer un compte
1. Va sur **https://supabase.com**
2. Clique **Start your project** (en haut à droite)
3. Connecte-toi avec GitHub ou ton email

### 1.2 Créer un projet
1. Clique **New project**
2. Remplis :
   - **Name** → `lspd-rp` (ou ce que tu veux)
   - **Database Password** → mets un mot de passe fort (note-le quelque part)
   - **Region** → choisis `West EU (Ireland)` pour la latence
3. Clique **Create new project**
4. ⏳ Attends 1-2 minutes que le projet se crée

### 1.3 Créer les tables (copie-colle le SQL)
1. Dans le menu gauche, clique **SQL Editor**
2. Clique **New query**
3. Copie-colle TOUT ce bloc SQL d'un coup :

```sql
create table casiers (
  id uuid default gen_random_uuid() primary key,
  created_at timestamptz default now(),
  nom text, prenom text, date_naissance date,
  nationalite text, plaque text, danger text,
  adresse text, signalement text, notes text
);

create table ppa (
  id uuid default gen_random_uuid() primary key,
  created_at timestamptz default now(),
  casier_id uuid, casier_nom text, date date,
  agent text, motif text, lieu text,
  gav_heures int, amende numeric, prison_jours int,
  infractions text, saisies text, resume text, statut text
);

create table delits (
  id uuid default gen_random_uuid() primary key,
  created_at timestamptz default now(),
  casier_id uuid, casier_nom text, type_delit text,
  categorie text, date date, agent text,
  amende numeric, statut text, description text
);

create table vehicules (
  id uuid default gen_random_uuid() primary key,
  created_at timestamptz default now(),
  casier_id uuid, proprietaire_nom text, plaque text,
  marque text, modele text, couleur text,
  annee int, statut text, notes text
);

create table armes (
  id uuid default gen_random_uuid() primary key,
  created_at timestamptz default now(),
  casier_id uuid, casier_nom text, nom text,
  type_arme text, numero_serie text, etat text,
  date date, agent text, lieu text, notes text
);

create table recherche (
  id uuid default gen_random_uuid() primary key,
  created_at timestamptz default now(),
  casier_id uuid, nom text, danger text, motif text,
  signalement text, derniere_localisation text,
  agent text, notes text
);

create table amendes (
  id uuid default gen_random_uuid() primary key,
  created_at timestamptz default now(),
  nom text, plaque text, infraction text,
  montant numeric, agent text, date date, statut text
);

create table budget (
  id uuid default gen_random_uuid() primary key,
  created_at timestamptz default now(),
  description text, categorie text,
  type text, montant numeric, date date
);

create table salaires (
  id uuid default gen_random_uuid() primary key,
  created_at timestamptz default now(),
  grade text, niveau text,
  salaire numeric, effectifs int
);
```

4. Clique le bouton vert **Run** (ou Ctrl+Entrée)
5. Tu dois voir `Success. No rows returned` → ✅ tables créées

### 1.4 Récupérer tes clés API
1. Dans le menu gauche, clique **Settings** (icône engrenage, tout en bas)
2. Clique **API**
3. Note ces deux valeurs :
   - **Project URL** → ressemble à `https://abcdefgh.supabase.co`
   - **anon public** (sous "Project API keys") → longue chaîne commençant par `eyJ...`

---

## ÉTAPE 2 — Modifier le fichier index.html

1. Ouvre le fichier `index.html` avec **Notepad** (Windows) ou **TextEdit** (Mac)
   - Clic droit sur le fichier → "Ouvrir avec" → Bloc-notes
2. Appuie sur **Ctrl+H** (chercher/remplacer)
3. Remplace `REMPLACE_PAR_TON_URL_SUPABASE` par ton URL Supabase
4. Remplace `REMPLACE_PAR_TON_ANON_KEY` par ta clé anon
5. Tu peux aussi changer `LSPD2024` par ton propre mot de passe
6. **Sauvegarde** le fichier (Ctrl+S)

Exemple de ce que ça doit ressembler dans le fichier :
```javascript
const SUPABASE_URL = 'https://abcdefgh.supabase.co';
const SUPABASE_KEY = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...';
const PASSWORD     = 'MonMotDePasse2024';
```

---

## ÉTAPE 3 — Créer un compte GitHub

1. Va sur **https://github.com**
2. Clique **Sign up** en haut à droite
3. Entre ton email, un mot de passe, un nom d'utilisateur
4. Vérifie ton email (GitHub envoie un code)
5. Tu arrives sur ton dashboard GitHub

---

## ÉTAPE 4 — Créer un repository GitHub

1. Clique le **+** en haut à droite → **New repository**
2. Remplis :
   - **Repository name** → `lspd-rp` (ou ce que tu veux)
   - **Description** → `Système LSPD RP` (optionnel)
   - Coche **Public** (nécessaire pour Netlify gratuit)
   - Coche **Add a README file**
3. Clique **Create repository**

---

## ÉTAPE 5 — Uploader ton fichier index.html

### Méthode simple (sans ligne de commande) :

1. Dans ton repository GitHub, clique **Add file** → **Upload files**
2. Fais glisser ton fichier `index.html` dans la zone pointillée
   - OU clique "choose your files" et sélectionne-le
3. En bas, dans **Commit changes** :
   - Laisse le message par défaut ou écris "premier upload"
4. Clique le bouton vert **Commit changes**
5. Tu vois maintenant `index.html` dans ton repo ✅

---

## ÉTAPE 6 — Connecter Netlify à GitHub

### 6.1 Créer un compte Netlify
1. Va sur **https://netlify.com**
2. Clique **Sign up**
3. Choisis **Sign up with GitHub** → autorise l'accès
4. Tu arrives sur le dashboard Netlify

### 6.2 Créer le site depuis GitHub
1. Clique **Add new site** → **Import an existing project**
2. Clique **Deploy with GitHub**
3. Cherche et sélectionne ton repository `lspd-rp`
4. Sur la page de configuration :
   - **Branch to deploy** → `main`
   - **Build command** → laisse VIDE
   - **Publish directory** → laisse VIDE (ou mets `/`)
5. Clique **Deploy lspd-rp**
6. ⏳ Attends 30 secondes...
7. ✅ Ton site est en ligne ! Tu vois une URL du genre `https://amazing-archimedes-123.netlify.app`

---

## ÉTAPE 7 — Personnaliser l'URL (optionnel)

1. Dans Netlify, va dans **Site configuration** → **General**
2. Clique **Change site name**
3. Entre par exemple `lspd-monserveur` → l'URL devient `https://lspd-monserveur.netlify.app`
4. Clique **Save**

---

## COMMENT METTRE À JOUR LE SITE ?

Quand tu veux modifier quelque chose (changer le mot de passe, ajouter une section...) :

1. Modifie `index.html` sur ton ordinateur
2. Va sur GitHub → ton repo → clique sur `index.html`
3. Clique l'icône crayon ✏️ (Edit this file) en haut à droite
4. Ou reclique **Add file → Upload files** et recharge le fichier
5. Netlify détecte automatiquement le changement et redéploie en ~30 secondes

---

## PARTAGER LE SITE AVEC TES AGENTS

Envoie simplement l'URL Netlify à tes agents :
- `https://lspd-monserveur.netlify.app`
- Mot de passe : celui que tu as configuré (ex: `LSPD2024`)

Tous les agents voient les mêmes données en temps réel. ✅

---

## RÉSOLUTION DE PROBLÈMES

**❌ "Erreur Supabase" sur le site**
→ Vérifie que l'URL et la clé sont bien copiées dans index.html
→ Vérifie qu'il n'y a pas d'espace avant/après les valeurs

**❌ Le site affiche la bannière jaune de config**
→ Tu n'as pas remplacé REMPLACE_PAR_TON_URL_SUPABASE dans le fichier

**❌ Les tables n'existent pas (erreur 404 dans Supabase)**
→ Retourne dans Supabase → SQL Editor et re-exécute le SQL de l'étape 1.3

**❌ Netlify dit "Page not found"**
→ Vérifie que le fichier s'appelle exactement `index.html` (pas Index.html ou index.HTML)

**❌ Je veux changer le mot de passe**
→ Ouvre index.html, cherche `LSPD2024`, remplace par ton nouveau mot de passe, reupload sur GitHub

---

## SÉCURITÉ (IMPORTANT)

Le mot de passe dans index.html est côté client — il suffit pour un serveur RP mais ne protège pas les données à 100%. Si tu veux plus de sécurité :
- Active les **Row Level Security (RLS)** dans Supabase → Tables → chaque table → RLS
- Ou contacte-moi pour ajouter un système de login plus robuste

---

*Guide rédigé pour LSPD RP — dernière mise à jour Mars 2025*
