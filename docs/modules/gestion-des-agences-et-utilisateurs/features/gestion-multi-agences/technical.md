# Fonctionnalité : Gestion multi-agences – Spécification technique

## 1. Intégration dans l’architecture

La fonctionnalité s’appuie sur :
- L’entité `Agency`.
- Le module RBAC.
- Le module Journal d’audit.
- Le système d’authentification JWT.

Elle est exposée via l’API REST versionnée `/api/v1`.

---

## 2. Modèle de données

### 2.1 Table `agencies`

Champs principaux :
- id (UUID, PK)
- name (string, not null)
- address (string)
- city (string)
- phone (string)
- email (string)
- status (enum: active, inactive)
- createdAt (timestamp)
- updatedAt (timestamp)

Contraintes :
- Index sur `status`.
- Soft delete logique via champ `status` (pas de suppression physique).

Relations :
- 1-N avec `users`.
- 1-N avec véhicules, réservations, contrats, factures (dans leurs modules respectifs).

---

## 3. API REST

Préfixe : `/api/v1`

### 3.1 Endpoints

- `GET /agencies`
  - Liste des agences.
  - Filtrage possible par statut.
  - Résultats filtrés selon rôle (global vs agence spécifique).

- `GET /agencies/{id}`
  - Détail d’une agence.

- `POST /agencies`
  - Création d’une agence.
  - Autorisé : Super Admin, Administrateur société.

- `PUT /agencies/{id}`
  - Mise à jour des informations.

- `PATCH /agencies/{id}/status`
  - Activation / désactivation.

Toutes les routes sont protégées par :
- Authentification JWT.
- Middleware RBAC.
- Contrôle de périmètre agence.

---

## 4. RBAC et contrôle d’accès

- Middleware vérifie :
  - Validité du token.
  - Rôle utilisateur.
  - Périmètre agence.

Règles :
- Rôles globaux : accès complet.
- Rôles agence : accès en lecture seule à leur agence.
- Refus (403) si tentative d’accès hors périmètre.

Le filtrage par `agencyId` doit être appliqué côté backend.

---

## 5. Intégration avec les autres modules

- Réservations :
  - Validation que `agency.status = active` avant création d’une réservation.

- Véhicules :
  - Interdiction d’affecter un véhicule à une agence inactive.

- Reporting :
  - Inclusion des agences inactives pour les données historiques.

---

## 6. Journal d’audit

À chaque action :
- Insertion dans `audit_log` avec :
  - userId
  - action (CREATE_AGENCY, UPDATE_AGENCY, CHANGE_AGENCY_STATUS)
  - entityType = "Agency"
  - entityId
  - timestamp
  - metadata (ancien / nouveau statut si applicable)

Implémentation via interceptor ou service centralisé.

---

## 7. Sécurité et conformité

- Accès uniquement via HTTPS.
- Validation des entrées (DTO + validation serveur).
- Protection contre injection SQL via ORM.
- Journalisation conforme RGPD.

---

## 8. Tests

- Tests unitaires :
  - Création agence.
  - Changement de statut.
  - Refus d’accès pour rôle non autorisé.

- Tests d’intégration :
  - Vérification blocage création réservation si agence inactive.
  - Vérification audit log généré.

---

# Conclusion technique

La gestion multi-agences repose sur une entité centrale `Agency`, un contrôle RBAC strict et une désactivation logique garantissant la conservation des données historiques. Elle s’intègre transversalement à tous les modules métier pour assurer le cloisonnement et la cohérence du système multi-agences.