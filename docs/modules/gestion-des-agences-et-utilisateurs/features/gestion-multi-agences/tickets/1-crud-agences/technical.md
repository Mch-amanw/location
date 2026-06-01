# Ticket : CRUD agences – Spécification technique

## 1. Modèle de données

Table : `agencies`

Champs :
- `id` (UUID, PK)
- `name` (varchar, not null)
- `address` (varchar, nullable)
- `city` (varchar, nullable)
- `phone` (varchar, nullable)
- `email` (varchar, nullable)
- `status` (enum: active, inactive, default: active)
- `createdAt` (timestamp)
- `updatedAt` (timestamp)

Contraintes :
- Index sur `status`.
- Aucune suppression physique (pas de DELETE SQL exposé).

Relations (déjà prévues dans modules associés) :
- 1-N avec `users`.
- 1-N avec `vehicles`, `reservations`, `contracts`, `invoices`.

---

## 2. API REST

Préfixe : `/api/v1`

### 2.1 Endpoints

- `GET /agencies`
  - Liste paginée.
  - Query params : `status` (optionnel).
  - Filtrage automatique selon rôle et périmètre.

- `GET /agencies/{id}`
  - Retourne le détail.
  - Vérification périmètre agence.

- `POST /agencies`
  - Création.
  - Body validé via DTO.
  - Autorisé : Super Admin, Administrateur société.

- `PUT /agencies/{id}`
  - Mise à jour complète des champs modifiables.
  - Autorisé : rôles globaux uniquement.

- `PATCH /agencies/{id}/status`
  - Changement de statut (active/inactive).
  - Vérifie validité transition.

Toutes les routes :
- Authentification JWT obligatoire.
- Middleware RBAC.
- Validation serveur (DTO + validation schema).

---

## 3. RBAC

Implémentation via middleware d’autorisation :

- Vérification du rôle :
  - `SuperAdmin`
  - `AdministrateurSociete`

- Si rôle non global :
  - `GET` limité à `agencyId` de l’utilisateur.
  - Toute modification → 403.

Le contrôle de périmètre doit être effectué côté backend.

---

## 4. Règles métier techniques

### 4.1 Désactivation

Lors du passage à `inactive` :
- Aucun DELETE physique.
- Les futures créations de réservation doivent vérifier `agency.status = active`.
- Les affectations de véhicules doivent vérifier `agency.status = active`.

Ces validations sont réalisées dans les services des modules concernés (réservations, véhicules).

---

## 5. Journal d’audit

À chaque action :

Insertion dans `audit_log` avec :
- `userId`
- `action` :
  - `CREATE_AGENCY`
  - `UPDATE_AGENCY`
  - `CHANGE_AGENCY_STATUS`
- `entityType` = "Agency"
- `entityId`
- `timestamp`
- `metadata` (ancien et nouveau statut si applicable)

Implémentation recommandée :
- Service centralisé ou interceptor transactionnel.

---

## 6. Validation et sécurité

- Validation des champs obligatoires (`name`).
- Vérification format email si renseigné.
- Protection contre injection via ORM.
- Retour 403 en cas d’accès non autorisé.
- Retour 404 si agence inexistante.

---

## 7. Performance

- Index DB sur `status`.
- Pagination sur `GET /agencies`.
- Architecture stateless compatible montée en charge.

---

## 8. Tests à prévoir côté implémentation

- Tests unitaires service Agency.
- Tests RBAC (rôles globaux vs agence).
- Tests d’intégration API.
- Vérification génération entrée audit_log.

---

## Conclusion technique

Ce ticket implémente les opérations CRUD logiques de l’entité `Agency`, sécurisées par RBAC strict, intégrées au journal d’audit et interconnectées aux modules métier pour garantir le blocage opérationnel des agences inactives sans suppression des données historiques.