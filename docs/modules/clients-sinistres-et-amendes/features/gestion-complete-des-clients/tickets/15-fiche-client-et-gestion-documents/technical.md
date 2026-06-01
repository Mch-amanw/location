# Spécification technique – Fiche client et gestion documents

## 1. Endpoints concernés

Base path : `/api/v1/clients`

### 1.1 Fiche client
- `GET /clients/{id}` : récupération des informations complètes du client.
- `PUT /clients/{id}` : mise à jour des informations générales.
- `PATCH /clients/{id}/status` : changement de statut.

### 1.2 Documents
- `POST /clients/{id}/documents` : upload document.
- `GET /clients/{id}/documents` : liste des documents.
- `GET /documents/{id}` : détail document (métadonnées).
- `GET /documents/{id}/download` : téléchargement sécurisé.
- `PATCH /documents/{id}/validation` : mise à jour du statut de validation.

### 1.3 Historique des réservations
- `GET /clients/{id}/reservations` : liste paginée des réservations du client.
  - Paramètres : `page`, `limit`, `sort`, `order`.

---

## 2. Modèle de données utilisé

### 2.1 Table `clients`

Champs utilisés :
- id (UUID)
- type
- nom
- prenom
- raison_sociale
- email (unique index)
- telephone
- adresse
- ice
- langue
- statut
- loyaltyCounter
- created_at
- updated_at
- deleted_at

Contraintes :
- Email unique.
- Soft delete via `deleted_at`.

---

### 2.2 Table `client_documents`

Champs :
- id (UUID)
- client_id (FK indexée)
- type_document (enum)
- storage_url
- validation_status (enum: en_attente, valide, refuse)
- created_at

Règles techniques :
- Aucun overwrite : chaque upload crée une nouvelle ligne.
- FK avec contrainte ON DELETE RESTRICT (soft delete côté client).

---

### 2.3 Réservations

Requête sur table `reservations` filtrée par `client_id`.
Index recommandé sur :
- client_id
- start_date

Retourner uniquement les champs nécessaires pour le tableau (projection optimisée).

---

## 3. Gestion des fichiers

- Stockage via object storage S3-compatible.
- Upload via backend (pas d’accès direct public).
- Génération d’URL signée temporaire pour téléchargement.
- Contrôles :
  - Taille maximale (configurable).
  - Types MIME autorisés (PDF, JPG, PNG).

Les fichiers ne doivent pas être accessibles publiquement sans authentification.

---

## 4. Sécurité et RBAC

### 4.1 Contrôle d’accès

- SuperAdmin / AdminSociete : accès global.
- ResponsableAgence / AgentComptoir : accès limité au périmètre agence.
- Comptabilité : accès lecture seule.
- Client (portail) : accès uniquement à sa propre fiche et ses documents.

Vérification :
- Middleware JWT.
- Vérification du périmètre agence avant retour des données.

---

## 5. Journal d’audit

Événements journalisés :
- Modification des informations client.
- Changement de statut.
- Upload document.
- Changement de validation document.

Structure audit :
- user_id
- action
- entity_type (client, document)
- entity_id
- old_value (JSON)
- new_value (JSON)
- timestamp

---

## 6. Performance

- Pagination obligatoire pour historique des réservations.
- Limitation du nombre de documents retournés par requête si volumétrie élevée.
- Sélection des champs strictement nécessaires.
- Index sur `client_id` dans `client_documents` et `reservations`.

---

## 7. Internationalisation

- Messages API compatibles i18n.
- Enum `validation_status` et `statut` mappés vers libellés traduisibles côté frontend.

---

## 8. Logs et monitoring

- Log des erreurs upload (taille, type invalide).
- Log des accès refusés (RBAC).
- Intégration avec le système global de monitoring.

Ce ticket s’intègre dans l’architecture REST `/api/v1`, respecte la logique multi-agences, le RGPD et la sécurité définis au niveau projet.