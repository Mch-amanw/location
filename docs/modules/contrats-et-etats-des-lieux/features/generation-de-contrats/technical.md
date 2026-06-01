# Spécification technique : Génération de contrats

## 1. Composants impactés

Backend :
- ContractService
- PdfGenerationService
- SignatureService
- AuditService
- NotificationService (déclenchement éventuel après signature)

Frontend :
- Interface back-office (création, consultation, signature).
- Interface espace client (consultation, téléchargement).

Stockage :
- Base de données PostgreSQL (entité Contract).
- Object storage S3-compatible pour stockage PDF.

---

## 2. Modèle de données (Contract)

Champs principaux :
- id (UUID)
- reservationId (FK)
- clientId (FK)
- vehicleId (FK)
- agencyId (FK)
- contractNumber (numérotation séquentielle)
- status (enum)
- totalAmount (decimal)
- currency (MAD)
- language (FR | EN)
- version (integer)
- signedAt (timestamp, nullable)
- signatureImageUrl (nullable)
- pdfUrl (nullable)
- createdAt / updatedAt

Contraintes :
- Index sur reservationId.
- Unicité logique : un contrat actif par réservation.

---

## 3. API REST

Endpoints principaux :
- POST /api/v1/contracts (création depuis réservation)
- GET /api/v1/contracts/{id}
- PUT /api/v1/contracts/{id} (modification avant signature)
- POST /api/v1/contracts/{id}/sign
- GET /api/v1/contracts/{id}/pdf

Sécurité :
- JWT obligatoire.
- RBAC multi-agences.

---

## 4. Génération du contenu contractuel

- Récupération des données depuis :
  - Reservation
  - Client
  - Vehicle
  - Paramétrage assurances/franchises
  - Contenus CGU (mini CMS)
- Construction d’un objet ContractSnapshot (données figées pour version).
- Rendu via template HTML multilingue.

---

## 5. Signature électronique

- Capture signature côté frontend (canvas → image base64).
- Upload sécurisé vers backend.
- Stockage signature dans object storage.
- Horodatage serveur (UTC).
- Mise à jour status → SIGNED.
- Écriture entrée AuditLog.

Verrouillage logique :
- Après signature, PUT interdit.
- Toute modification crée une nouvelle version (version++).

---

## 6. Génération PDF

- Déclenchée après signature.
- Conversion HTML → PDF côté serveur.
- Intégration signature et mentions légales.
- Ajout identifiant unique / hash d’intégrité.
- Upload vers object storage.
- Mise à jour champ pdfUrl.

Possibilité de traitement asynchrone pour éviter blocage UI.

---

## 7. Journal d’audit

Événements tracés :
- ContractCreated
- ContractUpdated
- ContractSigned
- ContractCancelled

Stockage via AuditService avec :
- userId
- role
- agencyId
- timestamp
- payload minimal (diff ou résumé).

---

## 8. Contraintes et conformité

- Numérotation séquentielle conforme exigences légales.
- Devise forcée à MAD.
- Templates multilingues (i18n backend).
- TLS obligatoire.
- Accès restreint aux contrats selon agence et rôle.

---

## 9. Intégrations internes

Événements émis :
- ContractSigned → module Paiement / Notifications.
- ContractStarted → module Facturation.
- ContractClosed → module Facturation / Sinistres.

API documentée via OpenAPI (/api/v1).