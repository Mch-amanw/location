# Module : Gestion des agences et utilisateurs – Spécification technique

## 1. Architecture et intégration

Ce module est intégré au backend API REST et interagit avec :
- Tous les modules métier (réservations, flotte, facturation, reporting, etc.).
- Le système d’authentification JWT.
- Le module Journal d’audit.

Il constitue la base du contrôle d’accès global.

---

## 2. Modèle de données (conceptuel)

### 2.1 Entité Agency

Champs principaux :
- id (UUID)
- name
- address
- city
- phone
- email
- status (active / inactive)
- createdAt / updatedAt

Contraintes :
- Suppression logique (soft delete ou statut inactive).

### 2.2 Entité User

Champs principaux :
- id (UUID)
- firstName
- lastName
- email (unique, indexé)
- passwordHash
- role (enum ou relation Role)
- agencyId (nullable pour rôle global)
- status (active / inactive)
- lastLoginAt
- createdAt / updatedAt

Contraintes :
- Index unique sur email.
- Mot de passe stocké hashé (bcrypt ou équivalent).

### 2.3 Entité Role

Deux options MVP :
- Enum statique en code (recommandé pour MVP).
- Table Role avec permissions associées.

Les permissions sont gérées côté backend via middleware RBAC.

---

## 3. RBAC (Role-Based Access Control)

### 3.1 Implémentation

- Middleware d’autorisation appliqué sur chaque endpoint.
- Vérification :
  - Validité du JWT.
  - Rôle utilisateur.
  - Correspondance agence (agencyId).

### 3.2 Contrôle de périmètre agence

Pour chaque requête sur une entité rattachée à une agence :
- Injection automatique d’un filtre agencyId.
- Refus si l’utilisateur tente d’accéder à une autre agence (sauf rôle global).

Le contrôle d’accès ne doit jamais reposer uniquement sur le frontend.

---

## 4. Authentification

- JWT signé (access token court).
- Refresh token sécurisé.
- Mot de passe hashé.
- HTTPS obligatoire.

Fonctionnalités :
- Login.
- Refresh token.
- Logout (invalidation côté serveur si nécessaire).

---

## 5. API (exemples d’endpoints)

Préfixe : /api/v1

### Agences
- GET /agencies
- POST /agencies
- PUT /agencies/{id}
- PATCH /agencies/{id}/status

### Utilisateurs
- GET /users
- POST /users
- PUT /users/{id}
- PATCH /users/{id}/status
- POST /users/{id}/reset-password

Tous les endpoints sont protégés par authentification et contrôle RBAC.

---

## 6. Sécurité

- Hashage des mots de passe (bcrypt ou équivalent).
- Protection contre attaques brute force (rate limiting login).
- Logs des tentatives de connexion.
- Données personnelles chiffrées au repos si nécessaire.

Conformité RGPD :
- Journalisation des accès.
- Possibilité d’extraction des données utilisateur interne.

---

## 7. Journal d’audit

- Table audit_log.
- Enregistrement automatique via interceptor ou hook service.
- Structure : id, userId, action, entityType, entityId, timestamp, metadata.

---

## 8. Performance et scalabilité

- Index sur email, role, agencyId.
- Architecture stateless compatible montée en charge.
- Compatible avec 50–200 utilisateurs internes simultanés.

---

## 9. Tests

- Tests unitaires RBAC.
- Tests d’intégration API (contrôle accès agence).
- Tests de sécurité sur endpoints sensibles.

---

# Conclusion technique

Le module repose sur une architecture RBAC stricte, un cloisonnement logique multi-agences et une sécurisation forte de l’authentification. Il constitue le socle de sécurité et d’isolation des données pour l’ensemble du système "location 3".