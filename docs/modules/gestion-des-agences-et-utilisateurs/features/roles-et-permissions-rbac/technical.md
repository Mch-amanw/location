# Spécification technique : Rôles et permissions (RBAC)

## 1. Architecture générale

La gestion RBAC est implémentée côté backend et appliquée sur tous les endpoints de l’API REST.

Elle repose sur :
- Authentification JWT.
- Middleware d’autorisation.
- Vérification du périmètre agence.
- Journalisation via le module Audit.

Le contrôle d’accès ne doit jamais dépendre uniquement du frontend.

---

## 2. Modélisation

### 2.1 Role

Pour le MVP :
- Rôles définis via enum en code (recommandé).

Exemple :
- SUPER_ADMIN
- ADMIN_SOCIETE
- RESPONSABLE_AGENCE
- AGENT_COMPTOIR
- COMPTABILITE

Les permissions sont codées dans des règles métier associées à ces rôles.

---

## 3. JWT et contexte utilisateur

Le token JWT contient au minimum :
- userId
- role
- agencyId (nullable)

À chaque requête :
1. Vérification de la validité du token.
2. Injection du contexte utilisateur dans la requête.
3. Passage par le middleware RBAC.

---

## 4. Middleware RBAC

### 4.1 Vérification rôle

Chaque endpoint définit :
- Les rôles autorisés.

Exemple conceptuel :
- POST /agencies → SUPER_ADMIN, ADMIN_SOCIETE
- GET /reservations → selon rôle + filtrage agence

Si le rôle n’est pas autorisé → HTTP 403 Forbidden.

---

### 4.2 Vérification périmètre agence

Pour toute entité contenant un champ agencyId :
- Si utilisateur rôle global → accès autorisé.
- Si utilisateur rattaché à une agence :
  - Comparaison entre agencyId de la ressource et agencyId du token.
  - Refus si mismatch.

Ce contrôle doit être appliqué côté service ou repository (et non uniquement dans le contrôleur).

---

## 5. Journal d’audit

Les actions suivantes déclenchent un log automatique :
- Modification du rôle d’un utilisateur.
- Tentative d’accès non autorisé.

Structure audit_log :
- id
- userId
- action (ROLE_CHANGE, ACCESS_DENIED, etc.)
- entityType
- entityId
- timestamp
- metadata (ancien rôle, nouveau rôle, endpoint ciblé, etc.)

---

## 6. Sécurité

- Les permissions sont validées exclusivement côté backend.
- Les rôles ne sont jamais modifiables via le frontend sans endpoint sécurisé.
- Les endpoints sensibles (ex : restitution caution) doivent exiger explicitement les rôles autorisés.
- Protection contre élévation de privilèges (vérification stricte lors des modifications d’utilisateurs).

---

## 7. Performance

- Index DB sur user.role et user.agencyId.
- Filtrage automatique via requêtes SQL avec clause WHERE agency_id = :agencyId.
- Compatible avec 50–200 utilisateurs simultanés.

---

## 8. Tests

### 8.1 Tests unitaires
- Vérification mapping rôle → permissions.
- Vérification refus accès non autorisé.

### 8.2 Tests d’intégration
- Cas utilisateur agence A tentant d’accéder aux données agence B.
- Cas changement de rôle et vérification effet immédiat.

---

# Conclusion technique

Le RBAC repose sur une implémentation backend stricte via JWT et middleware d’autorisation, avec contrôle systématique du périmètre agence. Il constitue un pilier de sécurité et d’isolation multi-agences du projet "location 3".