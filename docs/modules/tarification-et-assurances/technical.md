## 1. Positionnement dans l’architecture

Le module Tarification et assurances est un composant backend métier exposé via l’API REST (/api/v1) et consommé par :

- Le portail client (simulation et réservation).
- Le back-office (création/modification règles).
- Le module Réservations.
- Le module Facturation.

Il implémente un moteur de calcul centralisé et stateless.

---

## 2. Modèle de données (conceptuel)

Entités principales :

- Tariff
  - id
  - categoryId ou vehicleId
  - agencyId (nullable si global)
  - pricePerDay
  - validFrom / validTo
  - mileagePolicyId

- Season
  - id
  - name
  - dateFrom / dateTo
  - adjustmentType (override / surcharge)
  - value

- Option
  - id
  - name (i18n)
  - description (i18n)
  - pricingType (perDay / flat)
  - price
  - isActive

- Promotion
  - id
  - code
  - discountType (percentage / fixed)
  - value
  - validFrom / validTo
  - conditions (structure JSON si nécessaire)

- Insurance
  - id
  - name (i18n)
  - description (i18n)
  - price
  - isIncluded

- Franchise
  - id
  - categoryId
  - amount
  - reductionOptionId (nullable)

Toutes les entités incluent :
- createdAt / updatedAt
- createdBy / updatedBy (pour audit)

---

## 3. Moteur de calcul

### 3.1 Principes

Le moteur :
- Reçoit : dates, véhicule/catégorie, agence, options sélectionnées, code promo.
- Retourne :
  - Détail du calcul (ligne par ligne).
  - Total HT.
  - Total TTC (selon taux TVA fourni par module facturation).

Il doit :
- Appliquer la saisonnalité en fonction des dates.
- Appliquer les paliers durée si configurés.
- Ajouter options et assurances.
- Appliquer promotion en dernier.

### 3.2 Traçabilité

Le résultat du calcul doit être sérialisable et stocké avec la réservation :
- Snapshot des tarifs appliqués.
- Valeur des options.
- Valeur de la promotion.

Cela garantit la cohérence même si les règles changent ultérieurement.

---

## 4. API

Exemples d’endpoints :

- GET /tariffs
- POST /tariffs
- PUT /tariffs/{id}
- POST /pricing/simulate
- POST /promotions/validate

Toutes les routes sont protégées par RBAC.

Documentation via OpenAPI/Swagger.

---

## 5. Sécurité et conformité

- Accès restreint via JWT + RBAC.
- Journalisation des modifications tarifaires (journal d’audit global).
- Validation stricte des données (dates, montants >= 0).
- Aucune donnée carte bancaire stockée dans ce module.

---

## 6. Performance

- Indexation sur : categoryId, agencyId, validFrom/validTo.
- Mise en cache possible des règles actives.
- Calcul optimisé pour supporter consultation en temps réel sur portail client.

---

## 7. Internationalisation

- Champs textuels (options, assurances) compatibles i18n.
- Montants stockés en MAD.
- Aucune logique dépendante d’une devise multiple au MVP.

---

## 8. Tests

- Tests unitaires sur moteur de calcul (cas combinatoires).
- Tests d’intégration avec module Réservation.
- Tests de non-régression sur promotions et saisonnalité.

Le module doit être conçu pour être extensible en phase 2 (partenaires, canaux externes).