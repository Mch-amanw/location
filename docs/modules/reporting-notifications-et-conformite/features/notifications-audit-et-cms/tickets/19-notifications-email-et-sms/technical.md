# Spécification Technique – Ticket « Notifications email et SMS »

## 1. Architecture générale

Implémentation d’un `NotificationService` côté backend, intégré à l’API REST (/api/v1), responsable :
- De la sélection du template.
- De l’injection des variables.
- De l’appel aux fournisseurs externes (email / SMS).
- De la journalisation dans la base de données.

Le service doit être compatible avec une architecture stateless.

---

## 2. Modèle de données

### 2.1 Table `notification_templates`

Champs :
- id (UUID)
- type_evenement (string) – ex : reservation_confirmation
- canal (enum : email, sms)
- language (enum : FR, EN)
- sujet (nullable pour SMS)
- contenu (text)
- actif (boolean)
- created_at
- updated_at

Index recommandé :
- (type_evenement, canal, language, actif)

---

### 2.2 Table `notification_logs`

Champs :
- id (UUID)
- type_evenement (string)
- canal (enum : email, sms)
- destinataire (string)
- statut (enum : pending, sent, failed)
- date_envoi (timestamp)
- reference_entite_type (string)
- reference_entite_id (UUID)
- agence_id (UUID, nullable selon contexte)
- erreur (text, nullable)
- created_at

Index recommandés :
- (reference_entite_type, reference_entite_id)
- (agence_id)
- (statut)
- (date_envoi)

---

## 3. Déclenchement des notifications

### 3.1 Déclenchement événementiel

Les notifications sont déclenchées depuis les services métier existants :
- ReservationService
- PaymentService
- ContractService

Exemple :
- Après confirmation d’une réservation → appel à `NotificationService.send(type_evenement, entity)`.

---

## 4. Injection des variables

- Les variables dynamiques sont injectées côté backend.
- Implémentation via moteur simple de substitution (ex : {{client_name}}, {{start_date}}, {{amount}}).
- Validation : si variable absente, valeur vide ou fallback contrôlé.

Aucune logique d’injection côté frontend.

---

## 5. Intégration fournisseurs externes

### 5.1 Email

- Intégration avec service email transactionnel (ex : SendGrid).
- Appel API sécurisé via clé API stockée en variable d’environnement.
- Gestion des retours d’état (succès / échec).

### 5.2 SMS

- Intégration fournisseur SMS compatible Maroc.
- API REST sécurisée.
- Gestion des statuts de livraison si disponibles.

---

## 6. Traitement asynchrone

- Les envois doivent être traités de manière asynchrone (queue/job) afin de ne pas bloquer les transactions métier.
- Workflow recommandé :
  1. Création entrée `notification_logs` avec statut = pending.
  2. Envoi via worker.
  3. Mise à jour statut = sent ou failed.

---

## 7. API back-office

Endpoints sécurisés (RBAC) :

- GET /api/v1/notifications/logs
  - Filtres : type_evenement, canal, statut, date_from, date_to, agence_id.

- GET /api/v1/notifications/templates
- PUT /api/v1/notifications/templates/{id}

Aucune API publique d’envoi direct.

---

## 8. Sécurité

- Accès aux endpoints protégé par JWT.
- Filtrage des données par agence côté backend.
- Clés API email/SMS stockées en variables d’environnement.
- Validation des entrées pour éviter injection de contenu malveillant.

---

## 9. Logs et monitoring

- Centralisation des erreurs d’envoi.
- Alerting possible en cas de taux d’échec élevé.
- Logs applicatifs corrélés avec `notification_logs`.

---

## 10. Cohérence architecture globale

- API documentée via OpenAPI.
- Compatible PostgreSQL.
- Respect du RBAC existant.
- Aucun état conservé en mémoire (stateless).