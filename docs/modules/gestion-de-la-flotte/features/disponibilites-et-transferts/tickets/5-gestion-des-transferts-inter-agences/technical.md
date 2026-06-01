# Spécification Technique – Ticket : Gestion des transferts inter-agences

## 1. Entité et modèle de données

Table : **VehicleTransfer** (complément et finalisation du modèle existant)

Champs :
- id (UUID, PK)
- vehicleId (UUID, FK → Vehicle.id)
- originAgencyId (UUID, FK → Agency.id)
- destinationAgencyId (UUID, FK → Agency.id)
- plannedDepartureDate (timestamp)
- plannedArrivalDate (timestamp)
- actualDepartureDate (timestamp, nullable)
- actualArrivalDate (timestamp, nullable)
- status (enum : planned, in_progress, completed, cancelled)
- createdBy (UUID → User.id)
- createdAt
- updatedAt

Contraintes :
- destinationAgencyId ≠ originAgencyId
- FK strictes
- Index sur vehicleId
- Index sur status
- Index composite (originAgencyId, status)

---

## 2. Logique métier backend

Service : **VehicleTransferService**

### 2.1 Création d’un transfert

Étapes :
1. Vérifier RBAC.
2. Charger véhicule et vérifier :
   - status = AVAILABLE
   - isActive = true
3. Vérifier absence de réservation/contrat actif sur la période.
4. Créer transfert avec status = planned.
5. Écrire entrée dans JournalAuditService.

Aucune modification du statut véhicule à ce stade.

---

### 2.2 Démarrage transfert

Endpoint : PATCH /vehicle-transfers/{id}/start

Transaction DB obligatoire :
- Vérifier status = planned.
- Vérifier véhicule.status = AVAILABLE.
- Mettre à jour :
  - VehicleTransfer.status → in_progress
  - VehicleTransfer.actualDepartureDate (si fourni, sinon now())
  - Vehicle.status → IN_TRANSFER
- Commit transaction.
- Écriture audit log.

Rollback en cas d’erreur.

---

### 2.3 Finalisation transfert

Endpoint : PATCH /vehicle-transfers/{id}/complete

Transaction DB obligatoire :
- Vérifier status = in_progress.
- Vérifier absence de contrat actif.
- Mettre à jour :
  - VehicleTransfer.status → completed
  - VehicleTransfer.actualArrivalDate (now() ou valeur fournie)
  - Vehicle.agencyId → destinationAgencyId
  - Vehicle.status → AVAILABLE
- Commit transaction.
- Audit log.

---

### 2.4 Annulation transfert

Endpoint : PATCH /vehicle-transfers/{id}/cancel

Règles :
- Autorisé uniquement si status = planned.
- Mise à jour status → cancelled.
- Aucune modification du véhicule.
- Audit log obligatoire.

---

## 3. Intégration disponibilité

Le champ Vehicle.status = IN_TRANSFER doit être pris en compte dans :
- GET /vehicles
- GET /vehicles/availability

Les requêtes de disponibilité doivent exclure automatiquement ce statut.

Index requis :
- (agencyId, status)
- Index sur dates réservations pour contrôle overlap.

---

## 4. API REST

Endpoints :
- POST /api/v1/vehicle-transfers
- GET /api/v1/vehicle-transfers?agencyId=&status=&vehicleId=
- PATCH /api/v1/vehicle-transfers/{id}/start
- PATCH /api/v1/vehicle-transfers/{id}/complete
- PATCH /api/v1/vehicle-transfers/{id}/cancel

Tous les endpoints :
- Authentification JWT.
- Vérification RBAC.
- Validation systématique côté backend (jamais uniquement frontend).

---

## 5. Frontend (Back-office SPA)

Écrans concernés :
- Fiche véhicule : section “Transferts” avec historique.
- Écran liste des transferts avec filtres (statut, agence, véhicule).
- Formulaire création transfert.

Contraintes :
- Pagination obligatoire.
- Rafraîchissement automatique du statut véhicule après démarrage ou finalisation.
- Affichage des dates prévues et effectives.

---

## 6. Audit et logs

Chaque action suivante déclenche un enregistrement via JournalAuditService :
- Création transfert.
- Démarrage transfert.
- Finalisation transfert.
- Annulation transfert.

Données loggées :
- userId
- role
- vehicleId
- transferId
- ancien statut / nouveau statut
- horodatage

---

## 7. Cohérence et transactions

Toutes les opérations modifiant à la fois VehicleTransfer et Vehicle doivent être encapsulées dans une transaction PostgreSQL.

Objectif :
- Éviter toute incohérence (ex : transfert “in_progress” avec véhicule non mis à jour).
- Garantir intégrité multi-agences.

Le service doit rester stateless et conforme à l’architecture API REST versionnée (/api/v1).