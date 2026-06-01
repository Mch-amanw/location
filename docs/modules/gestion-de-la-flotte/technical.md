## 1. Architecture et positionnement

Le module Gestion de la flotte est implémenté dans le backend via des endpoints REST versionnés (/api/v1) et exposé au frontend SPA (back-office).

Il repose sur une base de données relationnelle (PostgreSQL) avec isolation logique par agence.

---

## 2. Modèle de données (conceptuel)

### 2.1 Entités principales

**VehicleCategory**
- id (UUID)
- nameFr
- nameEn
- descriptionFr
- descriptionEn
- imageUrl
- isActive
- createdAt / updatedAt

**Vehicle**
- id (UUID)
- internalCode
- registrationNumber (unique index)
- categoryId (FK)
- agencyId (FK)
- brand
- model
- year
- status (enum)
- isActive
- createdAt / updatedAt

**VehicleTransfer**
- id (UUID)
- vehicleId (FK)
- originAgencyId (FK)
- destinationAgencyId (FK)
- departureDate
- expectedArrivalDate
- actualArrivalDate
- status (planned, in_progress, completed, cancelled)
- createdBy
- createdAt / updatedAt

### 2.2 Contraintes
- Index sur registrationNumber.
- Index sur agencyId + status.
- Contraintes FK avec intégrité référentielle stricte.

---

## 3. API REST (exemples)

### Catégories
- GET /vehicle-categories
- POST /vehicle-categories
- PUT /vehicle-categories/{id}
- PATCH /vehicle-categories/{id}/deactivate

### Véhicules
- GET /vehicles (filtres : agence, statut, catégorie)
- GET /vehicles/{id}
- POST /vehicles
- PUT /vehicles/{id}
- PATCH /vehicles/{id}/status
- PATCH /vehicles/{id}/deactivate

### Transferts
- POST /vehicle-transfers
- GET /vehicle-transfers
- PATCH /vehicle-transfers/{id}/start
- PATCH /vehicle-transfers/{id}/complete

Toutes les routes sont protégées par JWT + RBAC.

---

## 4. Gestion des statuts

Le champ status de Vehicle est un enum contrôlé côté backend.
Les transitions critiques doivent être validées par des règles métier côté serveur (ex : impossibilité de transférer un véhicule en location).

Les changements de statut déclenchent :
- Mise à jour des index de disponibilité.
- Écriture dans le journal d’audit.

---

## 5. Intégration disponibilité

Le module expose un service interne utilisé par le module Réservations pour :
- Vérifier la disponibilité d’un véhicule.
- Exclure les véhicules avec statuts non compatibles.

Une indexation spécifique (agencyId, status) est requise pour optimiser les requêtes fréquentes.

---

## 6. Stockage des médias

- Photos véhicules stockées en object storage (S3 compatible).
- URL stockée en base.
- Accès sécurisé via URLs signées si nécessaire.

---

## 7. Sécurité et audit

- RBAC strict appliqué à chaque endpoint.
- Journalisation des actions :
  - Création véhicule.
  - Modification statut.
  - Transfert.
  - Désactivation.
- Les logs incluent : utilisateur, rôle, agence, horodatage.

---

## 8. Performance et scalabilité

- Index DB sur registrationNumber, agencyId, status.
- Pagination obligatoire sur les listes.
- Architecture stateless compatible montée en charge (>1000 véhicules).

---

## 9. Internationalisation

- Champs catégories multilingues (FR/EN).
- Les labels statuts sont traduits côté frontend via système i18n.

---

## 10. Sauvegardes et intégrité

- Sauvegardes quotidiennes incluses dans la stratégie globale projet.
- Contraintes transactionnelles lors des transferts pour garantir la cohérence (transaction DB lors du changement d’agence + statut).