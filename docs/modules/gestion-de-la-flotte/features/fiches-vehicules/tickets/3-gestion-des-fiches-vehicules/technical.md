# Ticket : Gestion des fiches véhicules – Spécification Technique

## 1. Positionnement dans l’architecture

Implémentation backend via API REST versionnée `/api/v1` et consommation par le back-office SPA.

Toutes les routes sont protégées par :
- Authentification JWT.
- Contrôle RBAC côté serveur.

---

## 2. Modèle de données

### 2.1 Table `Vehicle`

Champs :

- `id` (UUID, PK)
- `internalCode` (string, nullable)
- `registrationNumber` (string, NOT NULL, unique index)
- `categoryId` (UUID, FK → VehicleCategory, NOT NULL)
- `agencyId` (UUID, FK → Agency, NOT NULL)
- `brand` (string)
- `model` (string)
- `year` (integer)
- `status` (enum contrôlé côté backend)
- `isActive` (boolean, default true)
- `createdAt` (timestamp)
- `updatedAt` (timestamp)

Contraintes :
- Index unique sur `registrationNumber`.
- Index composite sur (`agencyId`, `status`).
- Contraintes FK avec intégrité référentielle stricte.

### 2.2 Enum `VehicleStatus`

Valeurs autorisées :
- AVAILABLE
- RESERVED
- RENTED
- IN_TRANSFER
- MAINTENANCE
- OUT_OF_FLEET

Les libellés sont traduits côté frontend (i18n FR/EN).

---

## 3. API REST

### 3.1 Liste

`GET /vehicles`

Query params :
- `agencyId`
- `categoryId`
- `status`
- `search` (immatriculation, marque, modèle)
- `page`
- `limit`

Pagination obligatoire.

---

### 3.2 Détail

`GET /vehicles/{id}`

Retourne :
- Données complètes du véhicule.

---

### 3.3 Création

`POST /vehicles`

Validations serveur :
- Unicité `registrationNumber`.
- Existence et activation de la catégorie.
- Existence de l’agence.
- Attribution par défaut du statut `AVAILABLE` si non fourni.

Création dans une transaction.

---

### 3.4 Modification

`PUT /vehicles/{id}`

- Mise à jour complète sauf `id`.
- Validation des règles métier (ex : cohérence statut).

---

### 3.5 Changement de statut

`PATCH /vehicles/{id}/status`

- Validation des transitions autorisées.
- Vérification des contraintes métier (ex : refuser transfert si statut RENTED).
- Exécution transactionnelle.

---

### 3.6 Désactivation

`PATCH /vehicles/{id}/deactivate`

Effets :
- `isActive = false`
- `status = OUT_OF_FLEET`

Transaction obligatoire.

---

## 4. Règles métier backend

Centralisation dans un service métier (ex : `VehicleService`) :

- Validation unicité immatriculation.
- Validation catégorie active.
- Contrôle transitions de statut.
- Synchronisation immédiate avec mécanisme de disponibilité utilisé par le module Réservations.

Toute modification critique doit :
- Être exécutée dans une transaction DB.
- Générer une entrée dans le journal d’audit.

---

## 5. Sécurité

- Filtrage automatique par `agencyId` pour rôles non globaux.
- Vérification des permissions avant toute mutation.
- Journalisation des actions : création, modification, changement de statut, désactivation.

Les logs incluent : `userId`, rôle, agence, horodatage, ancienne et nouvelle valeur si applicable.

---

## 6. Performance

- Pagination obligatoire.
- Index sur `registrationNumber`.
- Index composite sur (`agencyId`, `status`).
- Requêtes optimisées pour support > 1000 véhicules.

---

## 7. Cohérence et intégrité

- Aucune suppression physique (soft delete via `isActive`).
- Intégrité référentielle stricte avec `VehicleCategory` et `Agency`.
- Utilisation de transactions pour toute modification sensible.

Ce ticket constitue le socle technique permettant aux modules Réservations, Transferts, États des lieux et Reporting de s’appuyer sur des données véhicules cohérentes et fiables.