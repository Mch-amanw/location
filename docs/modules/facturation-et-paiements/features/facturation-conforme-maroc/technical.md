## Spécification technique – Facturation conforme Maroc

### 1. Composants impactés

- Backend API REST (/api/v1/invoices).
- Service de génération PDF.
- Base de données PostgreSQL (tables Invoice, InvoiceLine).
- Module Journal d’audit.
- Module Contrats (source des données financières).
- Module Paramétrage (TVA, informations société).

---

### 2. Modèle de données spécifique

#### 2.1 Table Invoice
- id (UUID)
- invoiceNumber (string, unique, indexé)
- agencyId (FK)
- clientId (FK)
- contractId (FK)
- status (draft, issued, cancelled)
- totalHt (numeric)
- totalTva (numeric)
- totalTtc (numeric)
- currency (string, valeur fixe "MAD")
- issuedAt (timestamp)
- createdAt / updatedAt

#### 2.2 Table InvoiceLine
- id
- invoiceId (FK)
- label
- quantity
- unitPriceHt
- tvaRate
- totalLineHt
- totalLineTva
- totalLineTtc

Contraintes :
- invoiceNumber unique.
- currency contrôlée côté backend (valeur forcée à MAD).

---

### 3. Numérotation séquentielle

- Utilisation d’une séquence PostgreSQL dédiée (ex : invoice_number_seq).
- Attribution du numéro dans une transaction atomique lors du passage au statut "issued".
- Verrouillage transactionnel pour éviter les doublons en cas de forte concurrence.
- Numéro non modifiable après attribution.

---

### 4. Calculs financiers

- Calcul des montants côté backend uniquement.
- Arrondis conformes aux règles standards (2 décimales).
- Recalcul systématique des totaux avant émission.
- Validation de cohérence : somme des lignes = totaux facture.

---

### 5. Paramétrage TVA

- Table de configuration des taux de TVA.
- Historisation minimale recommandée si changement de taux.
- Vérification que chaque ligne référence un taux valide.

---

### 6. Génération PDF

- Génération serveur (ex : moteur PDF type HTML → PDF).
- Template intégrant :
  - Mentions légales société.
  - Numéro et date.
  - Détail lignes.
  - Totaux.
  - Devise MAD.
- Stockage dans object storage (S3 compatible).
- URL sécurisée avec contrôle RBAC.

---

### 7. Sécurité et conformité

- RBAC sur endpoints : création, émission, annulation.
- Journalisation obligatoire :
  - Changement de statut.
  - Attribution numéro.
  - Annulation.
- Données financières protégées via HTTPS.
- Conservation des factures selon politique de rétention définie au niveau projet.

---

### 8. Contraintes et cohérence projet

- Devise figée en MAD (alignée avec la contrainte projet).
- API versionnée (/api/v1).
- Documentation OpenAPI mise à jour.
- Compatible charge cible 50–200 utilisateurs internes simultanés.
- Aucune donnée carte bancaire stockée dans cette fonctionnalité (gérée par module Paiement).