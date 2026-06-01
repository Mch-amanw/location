# Spécification technique – Paiement en ligne et caution

## 1. Intégration passerelle de paiement

### 1.1 Provider

Intégration d’une passerelle compatible Maroc (ex : CMI ou Stripe si autorisé).

Contraintes :
- Tokenisation obligatoire.
- Aucune donnée carte stockée en base.
- Communication HTTPS uniquement.

---

## 2. Flux de paiement

### 2.1 Paiement réservation / facture

1. Création d’une intention de paiement via API backend.
2. Redirection ou utilisation d’un composant sécurisé du provider.
3. Retour utilisateur (success/fail).
4. Confirmation définitive via webhook sécurisé.
5. Mise à jour du statut en base (Payment + Invoice).

Endpoints typiques :
- `POST /api/v1/payments`
- `POST /api/v1/payments/webhook`

Le webhook :
- Vérifie la signature du provider.
- Est idempotent.
- Met à jour `Payment.status`.

---

## 3. Gestion des cautions (pré-autorisation)

### 3.1 Prise d’empreinte

- Utilisation du mécanisme de pre-authorization du provider.
- Création d’un enregistrement `SecurityDeposit` avec :
  - contractId
  - amount
  - providerTransactionId
  - status = "authorized"

### 3.2 Capture / Libération

Actions disponibles via API provider :
- Capture totale.
- Capture partielle.
- Release (annulation autorisation).

Endpoints internes :
- `POST /api/v1/security-deposits/{id}/capture`
- `POST /api/v1/security-deposits/{id}/release`

Chaque action :
- Vérifie les droits RBAC.
- Enregistre un log d’audit.
- Met à jour le statut (`captured_partial`, `captured_full`, `released`).

---

## 4. Modèle de données (complément)

### Payment
- id
- invoiceId (nullable)
- reservationId (nullable)
- provider
- providerTransactionId
- amount
- currency (MAD)
- status
- type (payment, refund)
- createdAt

### SecurityDeposit
- id
- contractId
- provider
- providerTransactionId
- amount
- currency (MAD)
- status
- authorizedAt
- closedAt

Indexation recommandée :
- providerTransactionId
- status
- contractId

---

## 5. Sécurité

- Vérification signature webhook.
- Idempotency key pour éviter double traitement.
- Transactions DB lors de mise à jour facture + paiement.
- Journalisation obligatoire des actions sensibles.
- Données sensibles chiffrées au repos.

---

## 6. Notifications

- Déclenchement automatique via service Notifications.
- Templates multilingues (FR/EN).
- Envoi lors :
  - Confirmation paiement.
  - Échec paiement.
  - Capture partielle/totale caution.

---

## 7. Cohérence et performance

- Mise à jour cohérente Invoice.status selon somme des paiements confirmés.
- Gestion asynchrone des webhooks.
- Logs détaillés en cas d’erreur provider.
- Support charge cible 50–200 utilisateurs internes simultanés.