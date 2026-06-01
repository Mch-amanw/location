# Ticket : Paramétrage des assurances et franchises – Spécification technique

## 1. Positionnement architectural

Ce ticket concerne :

- Les endpoints API REST `/api/v1/insurances` et `/api/v1/franchises`.
- Les écrans back-office (SPA React/Vue).
- L’intégration avec le module d’audit.

Le moteur de calcul consomme ces entités mais n’est pas modifié structurellement dans ce ticket.

---

## 2. Modèle de données

### 2.1 Insurance

Table : `insurances`

Champs :

- id (UUID, PK)
- name (JSONB { fr: string, en: string })
- description (JSONB { fr: string, en: string })
- pricing_type (ENUM: included, per_day, flat)
- price (NUMERIC(12,2), nullable si included)
- agency_id (UUID, nullable)
- is_active (BOOLEAN)
- valid_from (DATE, nullable)
- valid_to (DATE, nullable)
- created_at / updated_at (TIMESTAMP)
- created_by / updated_by (UUID)

Relation N-N :

- insurance_categories
  - insurance_id
  - category_id

Contraintes :

- CHECK (price >= 0).
- Si pricing_type = 'included' → price = 0 ou NULL.

Index recommandés :

- agency_id
- is_active
- valid_from / valid_to

---

### 2.2 Franchise

Table : `franchises`

Champs :

- id (UUID, PK)
- category_id (UUID, nullable si vehicle_id défini)
- vehicle_id (UUID, nullable si category_id défini)
- agency_id (UUID, nullable)
- amount (NUMERIC(12,2))
- insurance_id (UUID, nullable)
- reduction_insurance_id (UUID, nullable)
- reduced_amount (NUMERIC(12,2), nullable)
- is_active (BOOLEAN)
- valid_from (DATE, nullable)
- valid_to (DATE, nullable)
- created_at / updated_at
- created_by / updated_by

Contraintes :

- CHECK (amount >= 0).
- CHECK (reduced_amount IS NULL OR reduced_amount < amount).
- Contrainte logique applicative : une seule franchise active par (category_id OR vehicle_id, agency_id, période).

Index recommandés :

- category_id
- vehicle_id
- agency_id
- is_active

---

## 3. API REST

### 3.1 Insurances

- GET /api/v1/insurances
- GET /api/v1/insurances/{id}
- POST /api/v1/insurances
- PUT /api/v1/insurances/{id}
- PATCH /api/v1/insurances/{id}/status

Validations backend :

- Champs obligatoires présents.
- Cohérence pricing_type / price.
- Au moins une catégorie associée.

---

### 3.2 Franchises

- GET /api/v1/franchises
- GET /api/v1/franchises/{id}
- POST /api/v1/franchises
- PUT /api/v1/franchises/{id}
- PATCH /api/v1/franchises/{id}/status

Validations backend :

- category_id XOR vehicle_id obligatoire.
- reduced_amount < amount si défini.
- reduction_insurance_id référence une assurance active et optionnelle.
- Vérification applicative d’unicité de franchise active sur période.

---

## 4. Intégration RBAC

Middleware JWT + contrôle des rôles :

- POST/PUT/PATCH : superAdmin, adminSociete.
- GET : superAdmin, adminSociete, responsableAgence (filtré par périmètre agence).

---

## 5. Journal d’audit

À chaque création/modification/statut :

- Enregistrement dans la table d’audit globale :
  - entity_type ("insurance" | "franchise")
  - entity_id
  - action (CREATE, UPDATE, ACTIVATE, DEACTIVATE)
  - old_value (JSONB)
  - new_value (JSONB)
  - performed_by
  - performed_at

L’audit est déclenché côté backend dans la couche service.

---

## 6. Intégration moteur de calcul

Aucune logique métier lourde dans ce ticket.

Cependant :

- Les entités doivent être récupérables efficacement par le moteur.
- Les assurances actives et valides doivent être filtrées par :
  - agence
  - catégorie
  - période de location

La structure doit être compatible avec la sérialisation snapshot dans la réservation.

---

## 7. Frontend (Back-office SPA)

- Formulaires dynamiques avec validation côté client (complétée par backend).
- Sélecteurs multi-catégories.
- Gestion i18n (FR/EN) pour name et description.
- Affichage clair des montants en MAD.
- Gestion du statut via switch (actif/inactif).

Les appels API utilisent JWT stocké côté client (secure storage).

---

## 8. Tests techniques attendus

- Tests unitaires sur validations Insurance et Franchise.
- Tests sur contrainte reduced_amount < amount.
- Tests sur unicité franchise active.
- Tests RBAC (accès refusé si rôle non autorisé).
- Tests d’intégration API CRUD.

Le développement doit rester compatible avec l’architecture stateless et multi-agences du projet.