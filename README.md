# BAC SPACE TN — plateforme collaborative

Cette version transforme le prototype HTML en application multi-utilisateurs basée sur **Vite + Supabase**.

## Fonctionnalités préparées

- Inscription et connexion par e-mail.
- Profil élève avec prénom/pseudo et section.
- Dépôt de PDF (10 Mo maximum) avec statut `pending`.
- Bibliothèque filtrable par section, matière et type.
- Téléchargement par URL temporaire depuis un bucket privé.
- Signalement des documents.
- Interface Français / Derja tunisienne.
- Espace de modération pour les comptes ayant le rôle `moderator` ou `admin`.

> Le site n'est pas encore en ligne et aucune base Supabase n'a été créée ou connectée. Les comptes, le stockage et les publications ne fonctionneront qu'après les étapes ci-dessous.

## 1. Créer le projet Supabase

1. Crée un projet sur https://supabase.com/dashboard.
2. Dans **SQL Editor**, colle et exécute `supabase/schema.sql`.
3. Dans **Project Settings → API**, récupère l'URL du projet et la clé publique `anon` / publishable.
4. Copie `.env.example` vers `.env`, puis renseigne :
   - `VITE_SUPABASE_URL`
   - `VITE_SUPABASE_ANON_KEY`

**Ne mets jamais une clé `service_role` dans le navigateur, dans `.env` avec un préfixe `VITE_`, ni dans GitHub.** La clé publique est prévue pour le client ; la sécurité dépend des politiques RLS installées par le SQL.

## 2. Lancer le site en local

Installe Node.js 20 ou une version compatible récente, puis dans ce dossier :

```bash
npm install
npm run dev
```

Vite affiche l'adresse locale à ouvrir dans le navigateur. Pour vérifier la version de production :

```bash
npm run build
npm run preview
```

## 3. Créer le premier compte administrateur

1. Inscris-toi depuis le site.
2. Confirme l'adresse e-mail si Supabase le demande.
3. Dans Supabase → Table Editor → `profiles`, retrouve ton profil et copie son `id`.
4. Dans SQL Editor, exécute la commande en remplaçant l'UUID :

```sql
update public.profiles
set role = 'admin'
where id = 'UUID_DU_COMPTE';
```

Ne donne jamais le rôle administrateur à un utilisateur inconnu. Le rôle n'est pas modifiable depuis l'interface élève.

## 4. Déployer gratuitement avec Netlify

1. Crée un dépôt Git privé/public et envoie les fichiers du projet (sans `.env`).
2. Dans Netlify, importe le dépôt.
3. Build command : `npm run build` ; Publish directory : `dist`.
4. Dans **Site configuration → Environment variables**, ajoute `VITE_SUPABASE_URL` et `VITE_SUPABASE_ANON_KEY`.
5. Lance le déploiement.
6. Dans Supabase → Authentication → URL Configuration, ajoute l'URL Netlify dans **Site URL** et **Redirect URLs**.

Guide officiel : https://vite.dev/guide/static-deploy#netlify

## Sécurité et modération

- Le bucket `bac-documents` est privé.
- Les élèves ne peuvent soumettre que des documents en attente.
- Seuls les documents approuvés sont visibles dans la bibliothèque des autres élèves.
- Les URLs de téléchargement expirent après 60 secondes.
- Les profils ne peuvent modifier que leur nom et leur section via l'application ; les rôles ne sont pas modifiables depuis le client.
- Les élèves ne peuvent pas changer le statut de validation d'un document ; seuls les modérateurs peuvent le faire.
- La suppression des fichiers est limitée aux fichiers en attente appartenant à l'élève (ou au personnel de modération).
- Les rôles staff sont attribués manuellement dans Supabase.
- Les signalements sont réservés aux utilisateurs connectés.

Avant un lancement public, teste les politiques RLS avec deux comptes étudiants et un compte modérateur. Prévois aussi une procédure de suppression des contenus illégaux, protégés par droit d'auteur ou contenant des données personnelles.

## Limites / coûts

Les offres gratuites de Supabase et Netlify peuvent suffire pour un premier essai, mais elles ont des quotas et conditions susceptibles d'évoluer. Vérifie les tarifs et limites avant d'ouvrir le site à beaucoup d'utilisateurs :
- https://supabase.com/pricing
- https://www.netlify.com/pricing/

## État de cette livraison

- [x] Prototype précédent retrouvé et pris en compte dans le nouveau design.
- [x] Application front-end connectable à Supabase.
- [x] Schéma SQL, profils, RLS, bucket privé et modération préparés.
- [x] Filtres et interface bilingue préparés.
- [ ] Créer et configurer le projet Supabase.
- [ ] Renseigner les clés locales.
- [ ] Tester inscription, dépôt, validation et téléchargement.
- [ ] Déployer sur Netlify et configurer les URL d'authentification.
