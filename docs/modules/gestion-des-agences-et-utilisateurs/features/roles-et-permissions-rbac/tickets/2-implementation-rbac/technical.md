# Ticket : Implémentation RBAC – Spécification technique

## 1. Architecture cible

L’implémentation RBAC repose sur :

- Authentification JWT existante.
- Middleware d’autorisation global.
- Vérification systématique du périmètre agence.
- Intégration avec le module Journal d’audit.

Le contrôle doit être effectué exclusivement côté backend.

---

## 2. Modélisation

### 2.1 Enum Role

Définition en code (MVP) :

```
SUPER_ADMIN
ADMIN_SOCIETE
RESPONSABLE_AGENCE
AGENT_COMPTOIR
COMPTABILITE
```

Stockage dans la table User via champ `role`.

---

## 3. JWT

Le token JWT doit contenir :
- userId
- role
- agencyId (nullable)

À chaque requête :
1. Validation du token.
2. Injection du contexte utilisateur dans la requête.
3. Passage dans le middleware RBAC.

---

## 4. Middleware RBAC

### 4.1 Vérification des rôles autorisés

Chaque endpoint doit déclarer explicitement les rôles autorisés.

Implémentation recommandée :
- Decorator ou annotation `@Roles(...)`.
- Guard ou middleware global vérifiant le rôle présent dans le JWT.

Si rôle non autorisé → HTTP 403 Forbidden.

---

### 4.2 Vérification du périmètre agence

Pour toute entité comportant `agencyId` :

Logique :
- Si rôle global (SUPER_ADMIN, ADMIN_SOCIETE) → accès autorisé.
- Sinon → comparaison entre :
  - agencyId du token
  - agencyId de la ressource demandée

En cas de mismatch → 403 Forbidden.

Le filtrage doit être appliqué :
- Au niveau service ou repository.
- Via clause SQL automatique `WHERE agency_id = :agencyId`.

Le frontend ne doit jamais fournir agencyId comme unique mécanisme de sécurité.

---

## 5. Gestion des changements de rôle

Endpoint sécurisé pour modification de rôle :
- Vérification que l’utilisateur appelant dispose des droits nécessaires.
- Interdiction d’auto-élévation de privilèges.

Après modification :
- Mise à jour immédiate en base.
- Le nouveau rôle sera pris en compte au prochain cycle de token (ou forcer refresh si nécessaire).

---

## 6. Journalisation (Audit)

Intégration avec table `audit_log`.

Événements à enregistrer :
- ROLE_CHANGE
- ACCESS_DENIED

Structure minimale :
- id
- userId
- action
- entityType
- entityId
- timestamp
- metadata (ancien rôle, nouveau rôle, endpoint, agencyId cible)

Implémentation via :
- Interceptor global.
- Hook dans le middleware RBAC.

---

## 7. Sécurité

- Vérification stricte côté backend uniquement.
- Aucun rôle modifiable via payload libre.
- Protection contre élévation de privilèges.
- Logs des accès refusés.
- Rate limiting sur endpoints sensibles.

---

## 8. Performance

- Index DB sur `user.role` et `user.agencyId`.
- Requêtes filtrées par agencyId.
- Middleware optimisé (lecture simple du JWT, pas de requête DB systématique si non nécessaire).

Compatible avec 50–200 utilisateurs simultanés.

---

## 9. Tests techniques attendus

- Tests unitaires du middleware RBAC.
- Tests d’intégration vérifiant refus inter-agence.
- Tests sur changement de rôle.
- Tests sur accès endpoints sensibles.

---

## Conclusion technique

Ce ticket met en place le cœur du système de sécurité multi-agences. Il implémente un RBAC strict via JWT et middleware backend, avec contrôle automatique du périmètre agence et journalisation des accès sensibles. Toute l’application métier devra s’appuyer sur cette couche d’autorisation.