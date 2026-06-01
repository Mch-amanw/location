# Spécification Technique – Ticket « Journal d’audit »

## 1. Architecture

Le journal d’audit est implémenté côté backend via un composant transversal (AuditLogService) intégré à l’API REST (/api/v1).

L’écriture des logs est automatique et centralisée, via :
- Middleware ou interceptor global.
- Ou appels explicites au service d’audit dans les services métier pour les actions critiques.

Aucune logique d’audit ne doit dépendre du frontend.

---

## 2. Modèle de données

Table : `audit_logs`

Champs :
- id (UUID ou bigserial)
- user_id (FK vers users)
- role (string)
- action (string, indexé)
- entity_type (string, indexé)
- entity_id (UUID ou bigint, indexé)
- old_value (JSONB, nullable)
- new_value (JSONB, nullable)
- agency_id (FK, indexé si applicable)
- timestamp (timestamp with time zone, indexé)

### Index recommandés
- Index sur (timestamp DESC)
- Index sur (entity_type, entity_id)
- Index sur (user_id)
- Index sur (agency_id)

Utilisation du type JSONB (PostgreSQL) pour old_value et new_value.

---

## 3. Génération des logs

### 3.1 Principe

À chaque action sensible :
- Identification de l’utilisateur via JWT.
- Capture de l’état initial (si modification).
- Exécution de l’opération métier.
- Capture de l’état final.
- Insertion d’une entrée dans `audit_logs`.

L’insertion doit faire partie de la même transaction DB lorsque possible afin d’assurer la cohérence.

### 3.2 Normalisation des actions

Les actions doivent être standardisées (ex : UPDATE_CONTRACT, REFUND_DEPOSIT, UPDATE_PRICING).

Un enum applicatif ou une constante centralisée doit être utilisé pour éviter les incohérences.

---

## 4. API de consultation

Endpoint sécurisé (RBAC strict) :

- GET /api/v1/audit-logs

Paramètres possibles :
- dateFrom
- dateTo
- userId
- action
- entityType
- agencyId
- page
- limit

Réponse :
- Liste paginée.
- Métadonnées de pagination.

Endpoint détail :
- GET /api/v1/audit-logs/{id}

Aucune route POST, PUT ou DELETE publique pour cette ressource.

---

## 5. Sécurité

- Accès restreint via RBAC (superAdmin, adminSociete).
- Filtrage automatique par agency_id selon le rôle et le périmètre multi-agences.
- Aucune modification ou suppression autorisée via API.
- Protection contre l’exposition excessive de données sensibles dans old_value/new_value (filtrage si nécessaire).

---

## 6. Performance

- Requêtes paginées obligatoires.
- Indexation sur colonnes critiques.
- Possibilité d’ajouter un mécanisme d’archivage futur si volumétrie importante (> plusieurs millions d’entrées).

---

## 7. Intégration avec l’architecture globale

- Compatible architecture stateless.
- Authentification via JWT.
- Documentation via OpenAPI/Swagger.
- Logs applicatifs centralisés pour supervision.
- Sauvegarde incluse dans le backup quotidien de la base PostgreSQL.

Le composant AuditLogService doit être réutilisable et facilement extensible pour intégrer de nouvelles actions à tracer dans les évolutions futures.