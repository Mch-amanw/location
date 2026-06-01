# Spécification technique – États des lieux numériques

## 1. Composants impactés

- InspectionService (logique métier principale).
- ContractService (vérification des statuts et transitions).
- PdfGenerationService.
- SignatureService.
- Module Sinistres (intégration événementielle).
- Stockage object storage (S3 compatible).
- Journal d’audit.

---

## 2. Modèle de données

### 2.1 Entité Inspection

- id (UUID)
- contractId (FK)
- type (enum: departure | return)
- mileage (integer)
- fuelLevel (string ou enum)
- checklistData (JSONB – structure flexible intérieur/extérieur)
- comments (text)
- status (draft | signed)
- signedAt (timestamp nullable)
- signedByUserId (FK)
- pdfUrl (string)
- createdAt / updatedAt

Contrainte :
- Unique (contractId, type).

### 2.2 Entité InspectionPhoto

- id (UUID)
- inspectionId (FK)
- fileUrl (string)
- createdAt (horodatage serveur)
- metadata (JSON – taille, type, éventuellement coordonnées zone)

Index sur inspectionId.

---

## 3. API REST

Routes principales :

- POST /api/v1/contracts/{id}/inspections
- GET /api/v1/contracts/{id}/inspections
- POST /api/v1/inspections/{id}/photos
- POST /api/v1/inspections/{id}/sign
- GET /api/v1/inspections/{id}/pdf

Contraintes :
- JWT obligatoire.
- Vérification RBAC + périmètre agence.
- Vérification du statut du contrat avant autorisation (ex : pas de retour si contrat pas en cours).

---

## 4. Workflow technique

### 4.1 Création

- Vérifier unicité (contractId + type).
- Vérifier statut du contrat.
- Enregistrer en statut draft.

### 4.2 Upload photos

- Upload via endpoint sécurisé.
- Compression et validation taille/type.
- Stockage sur object storage.
- Enregistrement URL en base.

### 4.3 Signature

- Capture signature (image base64 ou fichier).
- Horodatage serveur.
- Mise à jour status = signed.
- Appel PdfGenerationService.
- Génération PDF multilingue incluant données + photos.
- Stockage PDF et sauvegarde pdfUrl.
- Écriture entrée journal d’audit.
- Émission événement InspectionCompleted.

---

## 5. Intégrations

- ContractService : contrôle transitions (En cours / Clôturé).
- Module Sinistres : possibilité de créer un sinistre à partir d’un état des lieux retour.
- Module Notifications : envoi éventuel du document signé au client.

---

## 6. Sécurité

- Accès restreint par rôle et agence.
- TLS obligatoire.
- Photos et PDF stockés hors base (object storage).
- Limite de taille par photo (configurable).
- Journalisation des actions sensibles :
  - Création.
  - Signature.
  - Clôture.

---

## 7. Performance et scalabilité

- Checklist stockée en JSONB (PostgreSQL) pour flexibilité.
- Index sur contractId.
- Génération PDF potentiellement asynchrone.
- Compression images côté serveur.
- Service stateless compatible montée en charge.

---

## 8. Internationalisation

- Templates PDF FR/EN.
- Champs dynamiques traduits via système i18n backend.
- Respect devise MAD (si affichage montant lié au contrat).