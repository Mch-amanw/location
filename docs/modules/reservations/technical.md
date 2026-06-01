# Module : Réservations – Spécification Technique

## 1. Architecture logique

Le module Réservations est implémenté côté backend dans l’API REST et exposé via `/api/v1/reservations`.

Il interagit avec :
- Service Véhicules (disponibilité).
- Service Tarification (pricing engine).
- Service Paiement (transactions, webhooks).
- Service Clients.
- Service Contrats.
- Service Notifications.
- Service Audit.

Architecture stateless conforme à l’architecture globale.

---

## 2. Modèle de données (conceptuel)

### Entité Reservation
Champs principaux :
- id (UUID)
- reference (unique, lisible)
- clientId (FK)
- vehicleId (FK, nullable si réservation par catégorie)
- category
- agencyStartId (FK)
- agencyEndId (FK)
- startDateTime
- endDateTime
- status (enum)
- pricingSnapshot (JSON : détail du calcul figé)
- totalHt
- totalTva
- totalTtc
- currency (MAD)
- createdBy (userId ou "client")
- createdAt / updatedAt

Index :
- (vehicleId, startDateTime, endDateTime)
- status
- agencyStartId

---

## 3. Gestion des disponibilités

Algorithme :
- Vérification de chevauchement sur les réservations confirmées.
- Exclusion des réservations annulées/expirées.
- Prise en compte des contrats actifs.

Optimisations :
- Indexation sur dates.
- Cache court terme sur requêtes fréquentes de recherche.

---

## 4. Pricing snapshot

Au moment de la confirmation :
- Appel au moteur de tarification.
- Sauvegarde d’un snapshot JSON contenant :
  - Détail journalier.
  - Saisonnalité.
  - Options.
  - Promotion.
  - Paramètres kilométrage.

Objectif : garantir l’intégrité tarifaire même si les règles changent ultérieurement.

---

## 5. Paiement

Intégration via service Paiement :
- Création d’intention de paiement.
- Redirection vers passerelle.
- Traitement via webhook.
- Mise à jour atomique du statut réservation.

Gestion des cas :
- Succès.
- Échec.
- Timeout.

Transactions DB pour assurer cohérence réservation/paiement.

---

## 6. Sécurité & RBAC

Contrôles d’accès :
- Client : accès uniquement à ses réservations.
- Agent : accès limité à son agence.
- Admin société / SuperAdmin : accès global.

Validation stricte des inputs (dates, cohérence temporelle).

Toutes les opérations critiques génèrent un log dans la table d’audit.

---

## 7. API REST (exemples)

- `GET /api/v1/reservations` (filtrage par statut, agence, date).
- `POST /api/v1/reservations`
- `GET /api/v1/reservations/{id}`
- `PUT /api/v1/reservations/{id}`
- `POST /api/v1/reservations/{id}/cancel`
- `POST /api/v1/reservations/{id}/confirm`

Documentation OpenAPI obligatoire.

---

## 8. Internationalisation

- Champs traduisibles côté frontend.
- Templates de notifications liés à la langue du client.
- Montants stockés en MAD (pas de multi-devise MVP).

---

## 9. Performance & Scalabilité

- Index sur dates et véhicule.
- Pagination obligatoire sur listes.
- Architecture compatible montée en charge (>1000 véhicules).
- Tests de charge sur recherche de disponibilité.

---

## 10. Sauvegarde & traçabilité

- Inclus dans backup quotidien global.
- Journalisation centralisée.
- Conservation des données conforme RGPD (suppression/anonymisation si requise, hors obligations légales).