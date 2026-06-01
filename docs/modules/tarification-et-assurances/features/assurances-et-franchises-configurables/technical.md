# Spécification Technique – Assurances et franchises configurables

## 1. Positionnement dans le module

Cette fonctionnalité étend les entités `Insurance` et `Franchise` du module Tarification et assurances et est intégrée au moteur de calcul.

Elle est exposée via l’API REST versionnée `/api/v1`.

---

## 2. Modèle de données

### 2.1 Insurance

Champs principaux :

- id (UUID)
- name (JSON i18n : { fr, en })
- description (JSON i18n : { fr, en })
- pricingType ("included" | "perDay" | "flat")
- price (numeric, >= 0, nullable si included)
- categoryIds (array ou relation N-N)
- agencyId (nullable si global)
- isActive (boolean)
- validFrom / validTo (optionnel si gestion de période)
- createdAt / updatedAt
- createdBy / updatedBy

Contraintes :

- Si pricingType = included → price = 0 ou null.
- price >= 0.

### 2.2 Franchise

Champs principaux :

- id (UUID)
- categoryId (ou vehicleId)
- agencyId (nullable si global)
- amount (numeric, >= 0)
- insuranceId (nullable)
- reductionInsuranceId (nullable, référence vers Insurance optionnelle)
- reducedAmount (numeric, >= 0, nullable)
- isActive (boolean)
- validFrom / validTo (optionnel)
- createdAt / updatedAt
- createdBy / updatedBy

Contraintes :

- reducedAmount < amount si défini.
- Une seule franchise active par catégorie/vehicle/agence/période.

Index recommandés :

- categoryId
- agencyId
- isActive
- validFrom / validTo

---

## 3. Intégration au moteur de calcul

### 3.1 Logique

Lors d’un appel à `/pricing/simulate` :

1. Identifier les assurances incluses applicables.
2. Proposer les assurances optionnelles éligibles.
3. Ajouter au calcul :
   - Assurance perDay → price × nombre de jours.
   - Assurance flat → price.
4. Appliquer la promotion en dernier.

### 3.2 Franchise

Le moteur doit :

- Retourner le montant de franchise standard.
- Si réduction sélectionnée → retourner reducedAmount.
- Inclure dans le détail du calcul une ligne explicite pour la réduction (si payante).

### 3.3 Snapshot

Au moment de la confirmation de réservation :

- Sérialiser dans la réservation :
  - Détail des assurances sélectionnées.
  - Prix unitaire et total.
  - Montant de franchise standard.
  - Montant de franchise appliqué.

Cela garantit l’immuabilité contractuelle.

---

## 4. API

Exemples d’endpoints :

- GET /insurances
- POST /insurances
- PUT /insurances/{id}
- GET /franchises
- POST /franchises
- PUT /franchises/{id}

Toutes les routes :

- Protégées par JWT.
- Contrôlées via RBAC.
- Journalisées (création/modification/désactivation).

---

## 5. Sécurité et audit

- Journalisation dans le module global d’audit :
  - Ancienne valeur.
  - Nouvelle valeur.
  - Utilisateur.
  - Horodatage.
- Validation backend stricte (montants >= 0, cohérence reducedAmount).
- Aucune donnée bancaire stockée.

---

## 6. Internationalisation

- Champs name et description stockés en structure i18n.
- Restitués selon la langue de la requête.

---

## 7. Tests

- Tests unitaires :
  - Application correcte perDay vs flat.
  - Application réduction franchise.
  - Blocage reducedAmount >= amount.
- Tests d’intégration :
  - Simulation + réservation.
  - Snapshot conservé après modification règle.

La fonctionnalité doit rester stateless côté moteur de calcul et compatible avec l’architecture multi-agences.