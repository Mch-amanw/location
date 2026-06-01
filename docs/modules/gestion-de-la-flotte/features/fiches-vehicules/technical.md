# Spécification Technique – Fiches véhicules

## 1. Positionnement dans l’architecture

Fonctionnalité exposée via l’API REST `/api/v1` et consommée par le back-office SPA.

Toutes les routes sont protégées par :
- Authentification JWT.
- Contrôle RBAC côté backend.

---

## 2. Modèle de données

### 2.1 Table Vehicle

Champs principaux :

- id (UUID, PK)
- internalCode (string, optionnel)
- registrationNumber (string, unique index, NOT NULL)
- categoryId (UUID, FK → VehicleCategory, NOT NULL)
- agencyId (UUID, FK → Agency, NOT NULL)
- brand (string)
- model (string)
- year (integer)
- status (enum contrôlé côté backend)
- isActive (boolean)
- createdAt (timestamp)
- updatedAt (timestamp)

Contraintes :
- Index unique sur registrationNumber.
- Index composite sur (agencyId, status).
- FK avec intégrité référentielle stricte.

### 2.2 Table VehiclePhoto (si séparée)

- id (UUID)
- vehicleId (FK → Vehicle)
- fileUrl
- createdAt

Stockage des fichiers en object storage (S3 compatible). Seule l’URL est stockée en base.

---

## 3. API REST

### Véhicules

- GET `/vehicles`  
  Filtres : agencyId, categoryId, status, search (immatriculation, marque, modèle).  
  Pagination obligatoire.

- GET `/vehicles/{id}`  
  Retourne la fiche complète + photos.

- POST `/vehicles`  
  Validation : unicité immatriculation, catégorie active.

- PUT `/vehicles/{id}`  
  Mise à jour complète (hors id).

- PATCH `/vehicles/{id}/status`  
  Changement de statut avec validation des transitions.

- PATCH `/vehicles/{id}/deactivate`  
  Passe `isActive = false` et `status = Hors flotte`.

---

## 4. Règles métier côté serveur

- Validation d’unicité immatriculation avant insertion.
- Vérification que la catégorie est active.
- Contrôle des transitions de statut (ex : refus si conflit avec location active).
- Mise à jour transactionnelle lors des changements critiques.

Les transitions doivent être centralisées dans un service métier dédié (ex : VehicleService) pour éviter toute incohérence.

---

## 5. Intégration disponibilité

- Le champ `status` est utilisé par le module Réservations.
- Les statuts non compatibles avec la réservation (maintenance, transfert, hors flotte) doivent être exclus via requête optimisée.
- Index DB sur (agencyId, status) pour performance.

---

## 6. Sécurité

- RBAC appliqué à chaque endpoint.
- Filtrage automatique par agence pour les rôles non globaux.
- Journalisation des actions suivantes :
  - Création véhicule.
  - Modification fiche.
  - Changement de statut.
  - Désactivation.

Les logs incluent : userId, rôle, agence, horodatage, ancienne valeur / nouvelle valeur si pertinent.

---

## 7. Performance

- Pagination obligatoire (limit/offset ou cursor).
- Recherche textuelle simple indexée sur registrationNumber.
- Requêtes optimisées pour support 1000+ véhicules.

---

## 8. Cohérence et transactions

- Toute modification critique (statut, agence) doit être exécutée dans une transaction DB.
- Aucune suppression physique (soft delete via isActive).

---

## 9. Internationalisation

- Les statuts sont stockés en enum technique.
- Les libellés affichés sont traduits côté frontend via système i18n (FR/EN).

---

## 10. Conformité et audit

- Les données véhicules ne contiennent pas de données personnelles.
- Historique et audit conformes aux exigences projet (RGPD + traçabilité opérationnelle).