## Ticket : Gestion empreinte bancaire et restitution – Spécification technique

### 1. Modèle de données

Utilisation de l’entité existante `SecurityDeposit`.

Champs concernés :

- id
- contractId (FK)
- provider
- providerTransactionId
- amount
- currency (MAD)
- status (authorized, captured_partial, captured_full, released)
- authorizedAt
- closedAt
- capturedAmount (nullable)
- captureReason (nullable)
- createdAt
- updatedAt

Contraintes :
- Unicité : un seul `SecurityDeposit` actif (status = authorized) par `contractId`.
- Index sur `contractId`, `providerTransactionId`, `status`.

---

### 2. Prise d’empreinte (pré-autorisation)

#### 2.1 Endpoint

`POST /api/v1/contracts/{contractId}/security-deposits`

Actions :
- Vérification RBAC.
- Vérification que le contrat est actif.
- Vérification qu’aucune caution active n’existe.
- Appel API provider pour pre-authorization.
- Enregistrement en base avec status = "authorized".
- authorizedAt = now().
- Enregistrement audit log.

Gestion d’erreur :
- En cas d’échec provider, aucune création en base.
- Log technique détaillé.

---

### 3. Capture de caution

#### 3.1 Endpoint capture

`POST /api/v1/security-deposits/{id}/capture`

Payload :
- amount (optionnel ; si absent = capture totale)
- reason (obligatoire)

Règles :
- Vérifier status = "authorized".
- Si amount < amount autorisé → capture partielle.
- Si amount = amount autorisé → capture totale.
- Refuser si amount > amount autorisé.

Actions :
- Appel API provider (capture).
- Mise à jour :
  - status = captured_partial ou captured_full
  - capturedAmount
  - captureReason
  - closedAt = now()
- Transaction DB.
- Journalisation.
- Déclenchement notification.

---

### 4. Libération de caution

#### 4.1 Endpoint release

`POST /api/v1/security-deposits/{id}/release`

Règles :
- Vérifier status = "authorized".

Actions :
- Appel API provider (release/void authorization).
- Mise à jour :
  - status = released
  - closedAt = now()
- Journalisation.
- Déclenchement notification.

---

### 5. Sécurité

- RBAC strict (Agent comptoir, Responsable agence, Admin selon périmètre agence).
- Vérification que l’utilisateur appartient à l’agence du contrat.
- Vérification signature provider lors des confirmations si nécessaire.
- Aucune donnée carte stockée.
- providerTransactionId chiffré au repos.

---

### 6. Journal d’audit

Pour chaque action :
- entityType = "SecurityDeposit"
- entityId
- actionType (authorize, capture_partial, capture_full, release)
- metadata : montant, motif, providerTransactionId
- userId
- timestamp

---

### 7. Cohérence et fiabilité

- Utilisation transactions DB lors des changements de statut.
- Vérification idempotence côté backend (empêcher double capture via contrôle status).
- Logs techniques détaillés pour erreurs provider.
- Gestion asynchrone des appels si nécessaire mais cohérence forte exigée.

---

### 8. Intégration Notifications

Après succès opération :
- Appel service Notifications.
- Template selon type (prise empreinte, capture partielle, capture totale, libération).
- Paramètres : montant, contrat, agence, date.