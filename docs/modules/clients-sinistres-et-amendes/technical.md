# Spécification Technique – Module Clients, sinistres et amendes

## 1. Architecture et positionnement

Module backend exposé via API REST `/api/v1/clients`, `/api/v1/sinistres`, `/api/v1/amendes`.

Accessible depuis :
- Back-office (SPA).
- Portail client (partie restreinte clients).

Protégé par RBAC selon les rôles (superAdmin, adminSociete, responsableAgence, agentComptoir, comptabilite).

---

## 2. Modèle de données (conceptuel)

### 2.1 Entité Client

Champs principaux :
- id
- type (particulier/professionnel)
- nom, prenom / raisonSociale
- email, telephone, adresse
- ice (si applicable)
- langue
- statut (actif, suspendu, blacklisté)
- createdAt, updatedAt

Relations :
- 1:N avec réservations
- 1:N avec contrats
- 1:N avec sinistres
- 1:N avec amendes
- 1:N avec documents

### 2.2 Entité DocumentClient

- id
- clientId
- typeDocument (permis, CIN, autre)
- urlStockage
- dateUpload
- statutValidation

Stockage sur object storage S3-compatible.

### 2.3 Entité Sinistre

- id
- contratId
- clientId
- vehiculeId
- description
- dateIncident
- coutEstime
- coutReel
- franchiseAppliquee
- statut
- createdAt, updatedAt

Photos stockées en object storage.

### 2.4 Entité Amende

- id
- vehiculeId
- contratId
- clientId
- dateInfraction
- typeInfraction
- autorite
- montant
- statut
- referenceOfficielle
- createdAt, updatedAt

---

## 3. Règles techniques spécifiques

- Indexation DB sur :
  - clientId
  - contratId
  - vehiculeId
  - dateInfraction
- Contraintes d’intégrité référentielle strictes (FK).
- Soft delete recommandé pour clients.

---

## 4. Sécurité et RGPD

- Accès restreint aux données clients selon agence (multi-tenant logique).
- Données sensibles chiffrées au repos si nécessaire.
- Upload sécurisé (contrôle type fichier, taille max).
- Journalisation obligatoire des actions sensibles :
  - Modification statut client.
  - Clôture sinistre.
  - Refacturation amende.

---

## 5. Intégrations

### 5.1 Facturation

- Création automatique de lignes de facturation via appel interne au module Facturation.
- Synchronisation des statuts de paiement.

### 5.2 Paiement

- Possibilité de rattacher un paiement existant à une amende ou un sinistre.
- Support de paiement en ligne si activé.

### 5.3 Reporting

- Exposition de vues agrégées ou endpoints spécifiques pour tableaux de bord.

---

## 6. Performance

- Pagination obligatoire pour listing clients, sinistres, amendes.
- Filtres dynamiques (agence, statut, période).
- Optimisation des requêtes analytiques via index et vues matérialisées si nécessaire.

---

## 7. Audit et traçabilité

- Intégration avec le module Journal d’audit.
- Enregistrement des événements clés avec : utilisateur, date, ancienne valeur, nouvelle valeur.

---

## 8. Internationalisation

- Champs multilingues si nécessaire (types d’infractions affichés).
- Messages API compatibles i18n.

---

## 9. Sauvegarde et conformité

- Inclus dans la stratégie globale de backup quotidien.
- Conservation des données alignée avec obligations légales marocaines.
- Procédures d’anonymisation possibles sur demande RGPD si autorisé légalement.