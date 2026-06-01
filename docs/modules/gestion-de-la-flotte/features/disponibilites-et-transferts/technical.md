# Spécification Technique – Disponibilités et transferts

## 1. Composants impactés

Backend :
- Service VehicleService (gestion statuts et disponibilité).
- Service VehicleTransferService.
- Intégration avec ReservationService (vérification conflits dates).
- Intégration JournalAuditService.

Frontend (Back-office SPA) :
- Vue liste véhicules avec filtres avancés.
- Vue planning / indicateurs synthétiques par agence.
- Écrans de gestion des transferts.

Base de données :
- Tables Vehicle.
- Table VehicleTransfer.
- Jointures avec réservations/contrats pour validation disponibilité.

---

## 2. Logique de disponibilité

### 2.1 Règles serveur

La disponibilité est calculée côté backend via :
- Statut du véhicule.
- Vérification d’absence de réservation ou contrat actif sur la période demandée.

Les requêtes doivent :
- Filtrer par agencyId.
- Exclure statuts non compatibles.
- Vérifier conflits de dates (overlap) avec réservations confirmées.

Index requis :
- (agencyId, status).
- Index sur dates de réservations.

---

## 3. Gestion des transferts

### 3.1 Transactions

Les opérations suivantes doivent être transactionnelles :
- Démarrage transfert :
  - Mise à jour statut véhicule → "En transfert".
  - Mise à jour statut transfert → "in_progress".

- Finalisation transfert :
  - Mise à jour agencyId du véhicule.
  - Mise à jour statut véhicule → "Disponible".
  - Mise à jour statut transfert → "completed".

Toute incohérence doit déclencher un rollback.

---

## 4. API REST

Endpoints principaux (conformes au module parent) :

- GET /vehicles?agencyId=&status=&categoryId=
- GET /vehicles/availability?agencyId=&startDate=&endDate=
- POST /vehicle-transfers
- PATCH /vehicle-transfers/{id}/start
- PATCH /vehicle-transfers/{id}/complete
- PATCH /vehicle-transfers/{id}/cancel

Tous les endpoints :
- Protégés par JWT.
- Vérification RBAC.

---

## 5. Audit et traçabilité

Chaque action suivante génère une entrée dans le journal d’audit :
- Création transfert.
- Démarrage transfert.
- Finalisation transfert.
- Annulation transfert.
- Modification manuelle de statut impactant la disponibilité.

Les logs incluent :
- userId
- role
- vehicleId
- transferId (si applicable)
- horodatage

---

## 6. Performance

- Pagination obligatoire sur liste véhicules.
- Requêtes optimisées pour éviter les full scans.
- Mise en cache possible pour vues synthétiques (non critique transactionnellement).

Objectif : maintenir des temps de réponse compatibles avec 1000+ véhicules.

---

## 7. Cohérence multi-agences

- Isolation logique par agencyId.
- Vérification stricte que l’utilisateur ne peut créer un transfert que sur un véhicule appartenant à son périmètre.
- Validation backend systématique (pas uniquement frontend).

---

## 8. Conformité architecture projet

- API versionnée (/api/v1).
- Backend stateless.
- PostgreSQL avec contraintes FK.
- Respect du modèle enum Vehicle.status.
- Compatible avec montée en charge future (>1000 véhicules).