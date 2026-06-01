## Spécification technique – Tarification avancée

### 1. Composants impactés

- Backend : module Tarification et assurances (moteur de calcul).
- API REST (/api/v1).
- Base de données PostgreSQL.
- Portail client (simulation prix).
- Module Réservation (snapshot du calcul).
- Journal d’audit global.

---

### 2. Modèle de données (compléments spécifiques)

#### Tariff
- id
- categoryId (nullable si vehicleId défini)
- vehicleId (nullable)
- agencyId (nullable)
- pricePerDay
- validFrom
- validTo
- mileagePolicyId
- createdAt / updatedAt
- createdBy / updatedBy

#### Season
- id
- name
- dateFrom
- dateTo
- adjustmentType (override | surcharge)
- value

#### MileagePolicy
- id
- type (limited | unlimited)
- limitValue (nullable si unlimited)
- overagePricePerKm (nullable si unlimited)

#### Promotion
- id
- code (unique, indexé)
- discountType (percentage | fixed)
- value
- validFrom
- validTo
- conditions (JSON)
- maxUsage (nullable)
- usageCount

#### PricingSnapshot (stocké avec Reservation)
- basePricePerDay
- appliedSeason
- mileagePolicySnapshot
- promotionSnapshot
- breakdown (JSON détaillé)

---

### 3. Moteur de calcul

#### 3.1 Entrée
- categoryId ou vehicleId
- agencyId
- startDate / endDate
- promoCode (optionnel)

#### 3.2 Traitement
- Sélection du tarif actif correspondant.
- Application des saisons par jour ou par période.
- Calcul du total brut = somme(prix journalier ajusté × nb jours).
- Validation et application de la promotion.
- Construction d’un objet breakdown détaillant chaque étape.

Le moteur est stateless et déterministe.

---

### 4. API

- GET /tariffs
- POST /tariffs
- PUT /tariffs/{id}
- POST /seasons
- POST /pricing/simulate
- POST /promotions/validate

Toutes les routes protégées par JWT + RBAC.

POST /pricing/simulate retourne :
- breakdown
- totalBeforeTax
- totalAfterTax (si taux fourni par module facturation)

---

### 5. Contraintes techniques

- Index DB sur (categoryId, agencyId, validFrom, validTo).
- Index unique sur promotion.code.
- Validation stricte des dates (startDate < endDate).
- Tous les montants >= 0.
- Devise MAD uniquement.
- Aucune donnée bancaire stockée.

---

### 6. Performance

- Mise en cache possible des tarifs actifs.
- Optimisation requêtes sur plages de dates.
- Objectif : réponse simulation < 300 ms en charge nominale.

---

### 7. Sécurité & audit

- Journalisation des créations/modifications/suppressions de :
  - Tarifs
  - Saisons
  - Promotions
  - Politiques kilométrage
- Stockage du snapshot pour garantir la cohérence historique.

---

### 8. Tests

- Tests unitaires sur :
  - Combinaisons saisons + promotions.
  - Cas limites (durée 1 jour, chevauchements).
  - Dépassement kilométrique.
- Tests d’intégration avec Réservation.
- Tests de non-régression sur moteur de calcul.

La conception doit rester extensible pour intégration future partenaires/canaux externes.