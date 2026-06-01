# Spécification Technique – Fonctionnalité « Notifications, audit et CMS »

## 1. Architecture générale

La fonctionnalité s’appuie sur :
- L’API REST versionnée (/api/v1).
- La base PostgreSQL.
- Les services externes email et SMS.
- Le système RBAC existant.

Elle est composée de trois sous-composants :
1. NotificationService.
2. AuditLogService.
3. CmsService.

---

## 2. Notifications

### 2.1 Déclenchement

- Déclenchement événementiel côté backend (ex : réservation confirmée, paiement validé).
- Intégration avec les webhooks de la passerelle de paiement pour confirmation de transaction.

### 2.2 Modèle de données

Table `notification_logs` :
- id
- type (ex : reservation_confirmation)
- canal (email, sms)
- destinataire
- statut (pending, sent, failed)
- date_envoi
- reference_entite (type + id)
- erreur (nullable)

Table `notification_templates` :
- id
- type_evenement
- language (FR/EN)
- sujet (email)
- contenu
- actif (boolean)
- updated_at

### 2.3 Intégrations

- Service email transactionnel (ex : SendGrid).
- Fournisseur SMS local compatible Maroc.
- Injection des variables dynamiques côté backend.

### 2.4 Sécurité et performance

- Aucun envoi direct depuis le frontend.
- Journalisation systématique des résultats d’envoi.
- Possibilité de traitement asynchrone pour éviter de bloquer les transactions métier.

---

## 3. Journal d’audit

### 3.1 Implémentation

- Middleware ou interceptor backend capturant les actions sensibles.
- Écriture automatique dans la table `audit_logs`.

Table `audit_logs` :
- id
- user_id
- role
- action
- entity_type
- entity_id
- old_value (JSONB)
- new_value (JSONB)
- timestamp

### 3.2 Sécurité

- Aucune API de modification ou suppression des logs.
- Endpoint de consultation protégé par RBAC.
- Filtrage possible par entité, date, utilisateur.

### 3.3 Conformité

- Données conservées selon politique de sauvegarde (backup quotidien, rétention minimale 30 jours).
- Accès restreint aux rôles autorisés.

---

## 4. Mini CMS

### 4.1 Modèle de données

Table `pages` :
- id
- slug
- title
- content
- language (FR/EN)
- status (draft, published)
- created_at
- updated_at

### 4.2 API

Back-office (protégé RBAC) :
- POST /api/v1/cms/pages
- PUT /api/v1/cms/pages/{id}
- GET /api/v1/cms/pages
- DELETE /api/v1/cms/pages/{id}

Portail client (public lecture seule) :
- GET /api/v1/public/pages/{slug}?lang=FR|EN

### 4.3 Sécurité

- Validation des entrées pour prévenir XSS.
- Seules les pages avec status=published sont accessibles publiquement.
- Contrôle d’accès strict via JWT pour les opérations d’administration.

---

## 5. Monitoring et logs

- Centralisation des logs applicatifs.
- Alerting en cas d’échec massif d’envoi de notifications.
- Surveillance des performances des requêtes liées à l’audit.

---

## 6. Cohérence avec l’architecture globale

- Respect de l’architecture stateless.
- Utilisation de JWT pour contrôle d’accès.
- API documentée via OpenAPI/Swagger.
- Données stockées en base relationnelle PostgreSQL avec indexation sur champs critiques (entity_id, user_id, date).