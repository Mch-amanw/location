# Spécification technique – Espace client en ligne

## 1. Architecture

Composant frontend intégré au portail client (SPA React/Vue).

Backend exposé via API REST sécurisée sous `/api/v1/client-portal`.

Authentification via JWT + refresh token.

Isolation stricte des données par `clientId` issu du token.

---

## 2. Sécurité et authentification

- Authentification par email + mot de passe.
- JWT contenant :
  - userId
  - clientId
  - role = `client`
- Middleware backend vérifiant :
  - Validité du token.
  - Correspondance entre `clientId` du token et ressources demandées.

Protection contre :
- Accès horizontal (IDOR).
- Téléchargement direct non autorisé de fichiers.

Téléchargement de documents via URL signée temporaire (object storage S3-compatible).

---

## 3. Endpoints API

Base path : `/api/v1/client-portal`

### 3.1 Profil
- `GET /me` : retourne informations du client.
- `PUT /me` : mise à jour des champs autorisés (telephone, adresse, langue).
- `PUT /me/password` : changement mot de passe.

### 3.2 Réservations
- `GET /me/reservations` : liste paginée.
- `GET /me/reservations/{id}` : détail (vérification ownership obligatoire).

### 3.3 Contrats
- `GET /me/contracts` : liste.
- `GET /me/contracts/{id}/download` : téléchargement PDF (URL signée).

### 3.4 Factures
- `GET /me/invoices` : liste.
- `GET /me/invoices/{id}/download` : téléchargement PDF.

### 3.5 Documents
- `GET /me/documents` : liste.
- `POST /me/documents` : upload document.

Upload :
- Vérification type MIME.
- Taille maximale configurable.
- Stockage sur object storage.
- Enregistrement en base `client_documents` avec `validation_status = en_attente`.

---

## 4. Modèle de données

Réutilisation des tables existantes :
- `clients`
- `reservations`
- `contrats`
- `factures`
- `client_documents`

Aucune nouvelle entité métier majeure.

Index requis :
- `reservations.client_id`
- `contrats.client_id`
- `factures.client_id`
- `client_documents.client_id`

---

## 5. Journal d’audit

Événements à tracer :
- Modification profil (`PUT /me`).
- Changement mot de passe.
- Upload document.

Enregistrement :
- userId (client)
- action
- date
- ancienne valeur / nouvelle valeur si applicable

---

## 6. Internationalisation

- Utilisation système i18n frontend.
- Backend retourne messages traduisibles.
- Champ `langue` du client utilisé pour préconfigurer l’interface.

---

## 7. Performance

- Pagination obligatoire sur listes (réservations, factures, contrats).
- Limitation nombre de résultats par défaut.
- Génération d’URL signées à la demande.

---

## 8. RGPD

- Endpoint futur compatible avec export des données client.
- Accès strictement limité aux données personnelles du client authentifié.
- Aucune donnée sensible exposée inutilement.

---

## 9. Tests techniques attendus

- Vérification isolation multi-tenant (impossible d’accéder à une ressource d’un autre client).
- Validation upload sécurisé.
- Vérification expiration URL signée.
- Vérification traçabilité audit.

Ce composant respecte l’architecture REST versionnée `/api/v1` et s’intègre dans la stratégie de sécurité globale (JWT, RBAC, stockage sécurisé).