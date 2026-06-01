## Module : Facturation et paiements – Spécification technique

### 1. Architecture et intégration

Le module est implémenté dans le backend API REST (/api/v1) et expose des endpoints sécurisés pour :
- Gestion des factures.
- Gestion des paiements.
- Gestion des cautions.

Il interagit avec :
- Module Réservations (source des montants initiaux).
- Module Contrats.
- Module Clients.
- Module Reporting.
- Module Journal d’audit.
- Passerelle de paiement externe.

---

### 2. Modèle de données (conceptuel)

Entités principales :
- Invoice
  - id
  - invoiceNumber (séquentiel)
  - agencyId
  - clientId
  - contractId
  - status
  - totalHt
  - totalTva
  - totalTtc
  - currency (MAD)
  - issuedAt
- InvoiceLine
  - invoiceId
  - label
  - quantity
  - unitPriceHt
  - tvaRate
  - totalLineHt
- Payment
  - id
  - invoiceId
  - reservationId
  - provider
  - providerTransactionId
  - amount
  - status
  - type (paiement, remboursement)
- SecurityDeposit
  - contractId
  - amount
  - providerTransactionId
  - status (autorisé, capturé partiellement, capturé totalement, libéré)

Indexation recommandée sur :
- invoiceNumber
- agencyId
- clientId
- status
- issuedAt

---

### 3. Numérotation légale

- Génération côté serveur.
- Séquence garantie transactionnellement en base (verrouillage ou séquence DB PostgreSQL).
- Non réutilisable même en cas d’annulation.

---

### 4. Génération PDF

- Génération serveur (backend).
- Template conforme aux exigences légales marocaines.
- Stockage dans object storage (S3 compatible).
- Lien sécurisé accessible depuis espace client et back-office.

---

### 5. Intégration paiement

- Intégration avec une passerelle compatible Maroc (ex : CMI ou Stripe si autorisé).
- Tokenisation des cartes (aucune donnée carte stockée localement).
- Gestion des flux :
  - Payment intent.
  - Pre-authorization (empreinte bancaire).
- Endpoint webhook sécurisé pour mise à jour des statuts.
- Vérification signature webhook.

---

### 6. Gestion des cautions

- Utilisation des mécanismes de pre-authorization de la passerelle.
- Capture totale ou partielle via API provider.
- Libération via API provider.
- Synchronisation des statuts en base.

---

### 7. Sécurité

- RBAC strict sur endpoints financiers.
- Journalisation obligatoire pour :
  - Annulation facture.
  - Émission avoir.
  - Capture/libération caution.
  - Remboursement.
- Données sensibles chiffrées au repos (identifiants transaction).
- HTTPS obligatoire.

---

### 8. Performance et fiabilité

- Gestion idempotente des webhooks paiement.
- Transactions DB pour garantir cohérence facture/paiement.
- Logs détaillés des erreurs paiement.
- Support charge cible 50–200 utilisateurs internes simultanés.

---

### 9. Conformité

- Devise forcée en MAD.
- Paramétrage des taux TVA.
- Conservation des factures selon politique de rétention.
- Respect RGPD (accès, suppression données personnelles hors obligations légales de conservation).