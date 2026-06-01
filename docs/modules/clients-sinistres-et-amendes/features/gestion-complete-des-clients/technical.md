# Spécification technique – Gestion complète des clients

## 1. Endpoints API

Base path : `/api/v1/clients`

### 1.1 Clients
- `POST /clients` : création client.
- `GET /clients` : liste paginée avec filtres (agence, statut, type, recherche).
- `GET /clients/{id}` : détail client.
- `PUT /clients/{id}` : modification.
- `PATCH /clients/{id}/status` : changement de statut.
- `DELETE /clients/{id}` : soft delete (si autorisé).

### 1.2 Documents
- `POST /clients/{id}/documents` : upload document.
- `GET /clients/{id}/documents` : liste.
- `PATCH /documents/{id}/validation` : validation/refus.

### 1.3 Historique consolidé
- `GET /clients/{id}/history` : retourne agrégation réservations, contrats, factures, paiements, sinistres, amendes.

---

## 2. Modèle de données

### 2.1 Table `clients`

Champs principaux :
- id (UUID)
- type (enum: particulier, professionnel)
- nom
- prenom
- raison_sociale
- email (unique, indexé)
- telephone
- adresse
- ice
- langue (FR/EN)
- statut (actif, suspendu, blacklisté)
- loyaltyCounter (int)
- created_at
- updated_at
- deleted_at (soft delete)

Index :
- email (unique)
- statut
- type

### 2.2 Table `client_documents`

- id
- client_id (FK indexée)
- type_document (enum)
- storage_url
- validation_status
- created_at

Stockage : object storage S3-compatible.

---

## 3. Sécurité et RBAC

- Accès restreint selon rôle :
  - SuperAdmin / AdminSociete : accès global.
  - ResponsableAgence / AgentComptoir : accès limité à leur périmètre.
  - Comptabilité : lecture principalement.
  - Client : accès uniquement à ses propres données.

- Middleware d’authentification JWT.
- Vérification multi-tenant logique par agence.

---

## 4. Journal d’audit

Événements journalisés :
- Création / modification fiche client.
- Changement de statut.
- Upload / suppression logique document.
- Validation document.

Données enregistrées : utilisateur, date, ancienne valeur, nouvelle valeur.

---

## 5. Intégrations internes

- Réservations : vérification statut client avant confirmation.
- Facturation : récupération données légales client.
- Sinistres / Amendes : liaison par client_id.
- Reporting : agrégation via vues optimisées.

---

## 6. RGPD et conformité

- Endpoint export données client (format JSON ou PDF).
- Fonction d’anonymisation logique si autorisée.
- Données sensibles protégées via TLS et stockage sécurisé.
- Contrôle type et taille des fichiers uploadés.

---

## 7. Performance

- Pagination obligatoire sur listing.
- Filtres dynamiques (statut, période, type).
- Indexation sur email et statut.
- Endpoint historique optimisé via requêtes agrégées ou vues matérialisées si nécessaire.

---

## 8. Internationalisation

- Champs texte compatibles i18n.
- Messages API multilingues.
- Préférence linguistique utilisée pour communications (notifications).