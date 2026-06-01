## Spécification Technique – Moteur de disponibilité

### 1. Positionnement architectural

Le moteur de disponibilité est implémenté sous forme de service métier centralisé côté backend :

- Service : `AvailabilityService`
- Utilisé par :
  - ReservationService
  - VehicleService
  - VehicleTransferService
  - Endpoints REST dédiés

Il ne doit pas y avoir de logique de disponibilité implémentée côté frontend.

---

### 2. Méthodes principales

#### 2.1 Vérification unitaire

`isVehicleAvailable(vehicleId, startDate, endDate): boolean`

Vérifie :
- Vehicle.isActive = true
- Vehicle.status = AVAILABLE
- Absence de réservation confirmée avec overlap.
- Absence de contrat actif avec overlap.
- Absence de transfert en cours sur la période.

---

#### 2.2 Recherche globale

`getAvailableVehicles({ agencyId, categoryId?, startDate, endDate })`

Logique :
1. Filtrer véhicules par :
   - agencyId
   - isActive = true
   - status = AVAILABLE
   - categoryId si fourni
2. Exclure les véhicules ayant :
   - Réservations confirmées avec chevauchement.
   - Contrats actifs avec chevauchement.
   - Transferts en cours.

---

### 3. Gestion des chevauchements (overlap)

Condition standard SQL :

Deux périodes [A_start, A_end] et [B_start, B_end] se chevauchent si :

```
A_start < B_end AND A_end > B_start
```

Cette logique doit être appliquée aux tables :
- reservations
- contracts
- vehicle_transfers (si période en cours)

---

### 4. Requêtes et performance

#### 4.1 Index requis

- Index sur Vehicle (agencyId, status, isActive).
- Index sur Reservation (vehicleId, startDate, endDate, status).
- Index sur Contract (vehicleId, startDate, endDate, status).
- Index sur VehicleTransfer (vehicleId, status, departureDate, expectedArrivalDate).

#### 4.2 Optimisation

- Requête basée sur sous-requêtes NOT EXISTS plutôt que filtrage en mémoire.
- Pagination obligatoire sur résultats.
- Aucune récupération massive non filtrée.

---

### 5. Endpoint REST

Endpoint conforme à la spec parent :

`GET /api/v1/vehicles/availability`

Query params :
- agencyId (required)
- startDate (required)
- endDate (required)
- categoryId (optional)
- page / limit

Réponse :
- Liste paginée de véhicules disponibles.
- Métadonnées de pagination.

---

### 6. Cohérence transactionnelle

Le moteur ne modifie pas les données.

Toute opération impactant la disponibilité (création réservation, changement statut, démarrage transfert) doit être transactionnelle au niveau de son propre service afin que le moteur retourne toujours un état cohérent post-transaction.

---

### 7. Multi-agences et sécurité

- Filtrage obligatoire par agencyId.
- Vérification RBAC côté contrôleur avant appel du service.
- Un utilisateur ne peut consulter que les agences de son périmètre.

---

### 8. Scalabilité

- Implémentation stateless.
- Compatible >1000 véhicules.
- Possibilité future de mise en cache pour vues synthétiques (non inclus dans ce ticket).

---

### 9. Tests techniques attendus (niveau service)

- Cas sans réservation.
- Cas avec réservation chevauchante.
- Cas avec réservation adjacente (sans overlap).
- Cas statut non disponible.
- Cas transfert en cours.

Les tests doivent couvrir les règles métier centrales afin de garantir la fiabilité du moteur.