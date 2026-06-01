## Ticket : Intégration passerelle paiement CB – Spécification technique

### 1. Architecture d’intégration

L’intégration est réalisée dans le backend via un service dédié :

- PaymentProviderService (interface abstraite).
- Implémentation concrète : ex. CmiPaymentService ou StripePaymentService.

Le backend expose des endpoints REST versionnés sous `/api/v1`.

---

### 2. Configuration

Variables d’environnement :

- PAYMENT_PROVIDER
- PAYMENT_API_KEY
- PAYMENT_SECRET
- PAYMENT_WEBHOOK_SECRET
- PAYMENT_RETURN_URL_SUCCESS
- PAYMENT_RETURN_URL_CANCEL

Aucune clé secrète ne doit être versionnée.

---

### 3. Endpoints backend

#### 3.1 Création paiement

`POST /api/v1/payments`

Body minimal :
- amount (en MAD)
- currency (doit être "MAD")
- reservationId ou invoiceId

Traitement :
- Validation des droits (RBAC).
- Vérification cohérence montant.
- Création en base d’un enregistrement Payment avec status = "en_attente".
- Appel API provider pour créer une intention de paiement.
- Enregistrement du providerTransactionId.
- Retour des informations nécessaires au frontend (clientSecret, redirectUrl, etc.).

---

#### 3.2 Webhook provider

`POST /api/v1/payments/webhook`

Contraintes :
- Endpoint public mais sécurisé.
- Vérification signature via PAYMENT_WEBHOOK_SECRET.
- Idempotence garantie (clé basée sur providerTransactionId + eventId).

Traitement :
- Vérifier signature.
- Identifier le Payment via providerTransactionId.
- Mapper le statut provider → statut interne.
- Mettre à jour Payment.status.
- Si statut confirmé :
  - Recalculer total des paiements confirmés.
  - Mettre à jour Invoice.status dans une transaction DB.
- Journaliser l’événement.

---

### 4. Modèle de données (utilisation existante)

Table Payment :
- id
- invoiceId (nullable)
- reservationId (nullable)
- provider
- providerTransactionId (indexé)
- amount
- currency (MAD)
- status
- type (payment)
- createdAt
- updatedAt

Ajouts éventuels :
- providerRawStatus
- lastWebhookAt

Index requis sur :
- providerTransactionId (unique si applicable)
- status

---

### 5. Sécurité

- HTTPS obligatoire.
- Vérification stricte signature webhook.
- Aucune donnée carte stockée.
- Logs sans données sensibles.
- RBAC sur endpoint création paiement.
- Protection contre replay webhook via idempotency.

---

### 6. Gestion transactionnelle

Lors de la confirmation d’un paiement :

- Utiliser transaction DB.
- Mettre à jour Payment.
- Recalculer et mettre à jour Invoice.status.
- Commit atomique.

En cas d’erreur : rollback.

---

### 7. Logs et monitoring

- Log structuré pour chaque appel provider.
- Log erreur si échec appel API provider.
- Log erreur si signature webhook invalide.
- Métriques recommandées :
  - Nombre paiements initiés.
  - Taux d’échec.
  - Temps de traitement webhook.

---

### 8. Extensibilité

L’intégration doit être basée sur une abstraction (interface provider) afin de :

- Permettre le changement de passerelle sans impacter la logique métier.
- Réutiliser la même infrastructure pour la gestion des cautions (pre-authorization).

Le code doit rester conforme à l’architecture REST, stateless et scalable définie dans la spécification technique globale.