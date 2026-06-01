# Fonctionnalité : Réservation en ligne – Spécification Technique

## 1. Architecture générale

La fonctionnalité s’appuie sur :
- Frontend SPA (portail client).
- API REST `/api/v1/reservations`.
- Services internes : Disponibilité, Tarification, Paiement, Clients, Notifications, Audit.

Flux principal :
1. Recherche disponibilité (API).
2. Simulation tarifaire.
3. Création réservation (status = EN_ATTENTE_PAIEMENT).
4. Création intention de paiement.
5. Redirection passerelle.
6. Webhook paiement → mise à jour statut.

---

## 2. Endpoints principaux

### Recherche disponibilité
- `GET /api/v1/availability`
  - Paramètres : agencyStartId, agencyEndId, startDateTime, endDateTime, category.
  - Retour : liste catégories/véhicules disponibles + estimation tarifaire.

### Simulation tarifaire
- `POST /api/v1/pricing/simulate`
  - Payload : dates, catégorie, options, assurance, kilométrage.
  - Retour : détail HT / TVA / TTC.

### Création réservation
- `POST /api/v1/reservations`
  - Authentification client obligatoire (JWT).
  - Status initial : EN_ATTENTE_PAIEMENT.

### Confirmation (via webhook)
- `POST /api/v1/payments/webhook`
  - Vérification signature passerelle.
  - Mise à jour transaction.
  - Si succès → update réservation status = CONFIRMEE.
  - Enregistrement pricingSnapshot (JSON figé).

---

## 3. Gestion de la concurrence

Problème : double réservation simultanée.

Mesures :
- Vérification disponibilité avant création.
- Transaction DB lors de la création.
- Revalidation disponibilité avant confirmation finale.
- Contrainte logique basée sur chevauchement dates + véhicule.

Isolation transactionnelle recommandée pour éviter les conflits (niveau approprié PostgreSQL).

---

## 4. Pricing snapshot

Au moment de la confirmation :
- Appel final au moteur de tarification.
- Sauvegarde dans `reservation.pricingSnapshot` (JSON) :
  - Détail journalier.
  - Options.
  - Promotions.
  - Paramètres TVA.

Garantit l’intégrité si règles tarifaires changent ultérieurement.

---

## 5. Sécurité

- Authentification JWT côté client.
- Accès limité aux réservations du client connecté.
- Validation stricte des entrées (dates ISO, cohérence temporelle).
- Protection CSRF côté frontend.
- Vérification signature webhook paiement.
- Journal d’audit pour :
  - Création réservation.
  - Confirmation automatique suite paiement.

---

## 6. Performance

- Index sur (vehicleId, startDateTime, endDateTime).
- Mise en cache court terme des résultats de disponibilité (si pertinent).
- Pagination si liste volumineuse.
- Temps de réponse cible recherche < 2 secondes.

---

## 7. Internationalisation

- Textes UI gérés par système i18n frontend.
- Langue déterminée par préférence utilisateur ou navigateur.
- Montants stockés en MAD (pas de conversion multi-devise).

---

## 8. Logs & monitoring

- Log des erreurs paiement.
- Log des échecs de création réservation.
- Monitoring spécifique sur taux d’échec paiement.

---

## 9. Tests

Tests à prévoir :
- Cas nominal complet (recherche → paiement succès).
- Paiement refusé.
- Double tentative simultanée.
- Modification règles tarifaires après réservation (vérification snapshot).
- Charge sur endpoint disponibilité.