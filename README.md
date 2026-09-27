# PRO TECHNICA ÉCOLE — Phase 2 (Intégration Supabase complète)

Plateforme de gestion scolaire pour PRO TECHNICA ÉCOLE, connectée à un
backend Supabase de production (26 tables, RLS, storage, realtime).

## Installation rapide

```bash
npm install
cp .env.example .env.local
npm run dev
```

Voir `docs/INSTALLATION.md` pour le guide détaillé (création du premier
compte administrateur, etc.) et `docs/DEPLOIEMENT_NETLIFY.md` pour la mise
en production.

## Ce qui a été livré en Phase 2

- **Base de données** : schéma PostgreSQL complet (26 tables), clés
  étrangères, index de performance, triggers métier (recalcul automatique
  des soldes, audit trail, création de profil à l'inscription).
  → `supabase/migrations/` (14 migrations, déjà appliquées sur le projet).
- **Sécurité** : Row Level Security activée sur toutes les tables, avec une
  politique par rôle (administrateur, direction, comptable, professeur,
  étudiant, parent). Détail complet dans `docs/SCHEMA_ET_SECURITE.md`.
- **Authentification** : connexion, déconnexion, réinitialisation de mot de
  passe, gestion de session, protection des routes (`middleware.ts`).
- **Storage** : 6 buckets (photos, documents, bulletins, reçus, assets) avec
  politiques d'accès par convention de chemin.
- **Temps réel** : notifications, paiements et annonces diffusés en direct
  via Supabase Realtime.
- **Couche services** (`lib/services/`) : un module par domaine métier
  (étudiants, professeurs, notes, présences, paiements, comptabilité,
  annonces, storage, cartes QR), qui remplace entièrement les données mock.
- **Design & UX inchangés** : aucune page, aucun composant visuel n'a été
  redessiné. Seules les sources de données ont été branchées sur Supabase
  (`lib/data.ts`, `lib/store.ts`).

## Architecture des données

```
Composants React (inchangés)
        │
        ▼
lib/data.ts  ──────────────►  lib/store.ts (Zustand, état partagé + optimistic UI)
        │                              │
        ▼                              ▼
lib/services/*.service.ts  (requêtes Supabase, mapping vers les types du domaine)
        │
        ▼
Supabase (Postgres + RLS, Auth, Storage, Realtime)
```

## Où continuer

- `lib/mock-data.ts` est conservé comme référence de forme des données et
  base possible pour un script de seed d'environnement de démo.
- Les exports PDF (reçus, bulletins) utilisent déjà `jsPDF`/`jspdf-autotable`
  installés en Phase 1 ; il reste à les uploader vers les buckets `receipts`
  et `bulletins` via `lib/services/storage.service.ts`.
- Les exports Excel (rapports comptables) peuvent s'appuyer sur
  `lib/services/comptabilite.service.ts::getFinancialReport()`.

## Documentation

- `docs/INSTALLATION.md` — installation locale, premier compte admin
- `docs/DEPLOIEMENT_NETLIFY.md` — mise en production
- `docs/SCHEMA_ET_SECURITE.md` — schéma complet, matrice RLS, storage, realtime
