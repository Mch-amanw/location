## Ticket : Configuration des tarifs et saisons – Spécification technique

### 1. Composants impactés

- Backend : module Tarification et assurances.
- API REST (/api/v1).
- Base de données PostgreSQL.
- Back-office (SPA React/Vue).
- Journal d’audit global.

---

## 2. Modèle de données

### 2.1 Table Tariff

Champs utilisés :

- id (UUID ou bigint)
- category_id (nullable)
- vehicle_id (nullable)
- agency_id (nullable)
- price_per_day (numeric)
- mileage_policy_id (FK)
- valid_from (date)
- valid_to (date)
- created_at
- updated_at
- created_by
- updated_by

Contraintes :

- CHECK (price_per_day >= 0)
- CHECK (valid_to > valid_from)
- CHECK (category_id IS NOT NULL OR vehicle_id IS NOT NULL)
- Index sur (category_id, agency_id, valid_from, valid_to)
- Index sur (vehicle_id, agency_id, valid_from, valid_to)

Validation applicative :

- Vérification absence de chevauchement pour une même combinaison logique.

---

### 2.2 Table Season

Champs :

- id
- name
- date_from
- date_to
- adjustment_type (enum: override | surcharge)
- value (numeric)
- value_type (enum: fixed | percentage)
- created_at
- updated_at
- created_by
- updated_by

Contraintes :

- CHECK (date_to > date_from)
- CHECK (value >= 0)

Index :

- Index sur (date_from, date_to)

---

## 3. API REST

### Tariffs

- GET /api/v1/tariffs
  - Filtres : categoryId, vehicleId, agencyId, active

- POST /api/v1/tariffs
- PUT /api/v1/tariffs/{id}
- DELETE /api/v1/tariffs/{id}

### Seasons

- GET /api/v1/seasons
- POST /api/v1/seasons
- PUT /api/v1/seasons/{id}
- DELETE /api/v1/seasons/{id}

Toutes les routes :

- Protégées par JWT.
- Contrôlées via RBAC (superAdmin, adminSociete en écriture).

---

## 4. Logique métier backend

### 4.1 Validation des chevauchements (Tariff)

Lors de la création/modification :

- Recherche des tarifs existants avec :
  - Même catégorie ou véhicule.
  - Même agence (ou global).
  - Plage de dates intersectant la nouvelle plage.
- Si conflit → rejet avec message métier explicite.

### 4.2 Saisons

- Les saisons peuvent être créées même en cas de chevauchement.
- Aucune suppression logique des tarifs existants.

---

## 5. Journal d’audit

Pour chaque création/modification/suppression :

- Enregistrement dans la table d’audit globale :
  - entityType (Tariff | Season)
  - entityId
  - action (CREATE | UPDATE | DELETE)
  - oldValue (JSON)
  - newValue (JSON)
  - performedBy
  - performedAt

---

## 6. Frontend (Back-office)

### 6.1 Écrans

- Page liste Tarifs.
- Formulaire création/édition Tariff.
- Page liste Saisons.
- Formulaire création/édition Season.

### 6.2 Comportements

- Validation côté client (dates, champs requis, valeurs >= 0).
- Affichage des erreurs backend.
- Indicateur visuel statut (actif / expiré / futur).

Internationalisation via système i18n global.

---

## 7. Performance

- Requêtes paginées pour listing.
- Index DB optimisés pour recherche par période.
- Objectif temps réponse < 300 ms en environnement nominal.

---

## 8. Sécurité

- Aucun stockage de données sensibles.
- Toutes les opérations protégées via RBAC.
- Validation serveur obligatoire même si validé côté client.

---

## 9. Non-régression

- Ne pas impacter le moteur de calcul existant.
- Les nouvelles règles doivent être immédiatement disponibles pour /pricing/simulate.
- Aucune modification rétroactive des PricingSnapshot existants.