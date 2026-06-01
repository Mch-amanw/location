## Module : Contrats et états des lieux – Spécification technique

### 1. Architecture

Le module est implémenté dans le backend API REST et exposé via des endpoints sécurisés.

Composants principaux :
- Service ContractService.
- Service InspectionService (états des lieux).
- Service PdfGenerationService.
- Service SignatureService.
- Stockage documentaire (object storage S3 compatible).

---

### 2. Modèle de données (conceptuel)

#### 2.1 Entités principales
- Contract
  - id
  - reservationId
  - clientId
  - vehicleId
  - agencyId
  - status
  - totalAmount
  - currency (MAD)
  - version
  - signedAt
  - pdfUrl
  - timestamps

- Inspection
  - id
  - contractId
  - type (departure | return)
  - mileage
  - fuelLevel
  - checklistData (JSON)
  - comments
  - signedAt
  - pdfUrl
  - timestamps

- InspectionPhoto
  - id
  - inspectionId
  - fileUrl
  - metadata (horodatage)

Les données checklist peuvent être stockées en JSON structuré pour flexibilité.

---

### 3. API REST (exemples)

- POST /api/v1/contracts
- GET /api/v1/contracts/{id}
- POST /api/v1/contracts/{id}/sign
- POST /api/v1/contracts/{id}/inspections
- POST /api/v1/inspections/{id}/photos
- POST /api/v1/inspections/{id}/sign
- GET /api/v1/contracts/{id}/pdf

Toutes les routes sont protégées par JWT + RBAC.

---

### 4. Génération PDF

- Génération serveur via moteur HTML → PDF.
- Templates multilingues (FR/EN).
- Intégration des photos compressées.
- Inclusion d’un hash ou identifiant unique pour intégrité.
- Stockage dans object storage.

---

### 5. Signature électronique

- Capture de signature sous forme d’image (base64 ou fichier).
- Horodatage serveur.
- Association utilisateur + IP.
- Verrouillage logique du document après signature.
- Conservation des preuves de signature.

---

### 6. Sécurité et conformité

- Accès restreint selon rôle et agence (RBAC multi-agences).
- Journal d’audit pour :
  - Création/modification contrat.
  - Signature.
  - Clôture.
- Chiffrement TLS obligatoire.
- Stockage sécurisé des documents.
- Respect RGPD : données personnelles accessibles et exportables.

---

### 7. Performance et stockage

- Compression automatique des images uploadées.
- Limitation de taille par photo.
- Index DB sur contractId et reservationId.
- Génération PDF asynchrone possible pour éviter blocage UI.

---

### 8. Scalabilité

- Service stateless.
- Stockage fichiers externalisé.
- Compatible montée en charge > 1000 véhicules.

---

### 9. Intégrations internes

- Événements déclenchés :
  - ContractSigned.
  - InspectionCompleted.
  - ContractClosed.
- Consommés par :
  - Module Facturation.
  - Module Sinistres.
  - Module Notifications.

Documentation API exposée via OpenAPI/Swagger.