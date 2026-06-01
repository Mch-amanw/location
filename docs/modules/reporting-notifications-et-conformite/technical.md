# Spécification Technique – Module Reporting, Notifications et Conformité

## 1. Architecture générale du module

Le module s’appuie sur :
- L’API REST backend (/api/v1).
- La base de données relationnelle PostgreSQL.
- Les services externes d’email et SMS.
- Le système RBAC existant.

Il est composé de quatre sous-domaines techniques :
1. Reporting & agrégations.
2. Service de notifications.
3. Journal d’audit.
4. Mini CMS.

---

## 2. Reporting

### 2.1 Source des données

- Tables : réservations, contrats, factures, paiements, véhicules, agences, sinistres.
- Requêtes agrégées optimisées (SUM, COUNT, taux d’occupation).

### 2.2 Performance

- Indexation sur colonnes clés (date, agence_id, vehicule_id, statut).
- Mise en cache possible pour indicateurs fréquemment consultés.
- Requêtes paginées pour listes détaillées.

### 2.3 Sécurité

- Filtrage par agence côté backend selon JWT et rôle.
- Aucun calcul critique effectué uniquement côté frontend.

### 2.4 Exposition API

Exemples d’endpoints :
- GET /api/v1/reporting/dashboard
- GET /api/v1/reporting/ca
- GET /api/v1/reporting/occupation

Documentation via OpenAPI/Swagger.

---

## 3. Notifications

### 3.1 Architecture

- Service interne de gestion des notifications.
- Intégration avec :
  - Service email transactionnel (ex : SendGrid).
  - Fournisseur SMS local compatible Maroc.

### 3.2 Déclenchement

- Déclenchement événementiel côté backend (ex : réservation confirmée).
- Possibilité d’utilisation de webhooks pour confirmation de paiement.

### 3.3 Templates

- Stockage en base (table templates) avec version FR/EN.
- Variables dynamiques injectées côté backend.

### 3.4 Journalisation

- Table notification_logs :
  - id
  - type
  - canal
  - destinataire
  - statut (envoyé, échec)
  - date_envoi
  - reference_entite

---

## 4. Journal d’audit

### 4.1 Implémentation

- Table audit_logs dédiée.
- Écriture automatique via middleware ou interceptors backend.

Champs principaux :
- id
- user_id
- role
- action
- entity_type
- entity_id
- old_value (JSON)
- new_value (JSON)
- timestamp

### 4.2 Sécurité

- Accès en lecture restreint via RBAC.
- Aucune API publique de modification ou suppression.
- Conservation selon politique de sauvegarde (backup quotidien, rétention ≥ 30 jours minimum).

---

## 5. Mini CMS

### 5.1 Modèle de données

Table pages :
- id
- slug
- title
- content
- language (FR/EN)
- status (draft/published)
- created_at
- updated_at

### 5.2 Fonctionnement

- API CRUD sécurisée pour back-office.
- Endpoint public pour consultation côté portail client (lecture seule, pages publiées uniquement).

### 5.3 Sécurité

- Contrôle RBAC strict.
- Validation des entrées (prévention XSS).

---

## 6. Conformité et sécurité

- HTTPS obligatoire.
- Données sensibles non exposées dans les rapports au-delà des droits.
- Respect RGPD :
  - Limitation des données affichées.
  - Journalisation des accès sensibles.

---

## 7. Monitoring et logs

- Centralisation des logs applicatifs.
- Alerting en cas d’échec massif d’envoi de notifications.
- Suivi des performances des requêtes reporting.

---

## 8. Scalabilité

- Architecture stateless compatible montée en charge.
- Services notifications découplés pour éviter blocage du processus métier.
- Prévu pour extension future (intégration comptable et mobile).