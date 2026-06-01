## 1. Rôle du module

Le module **Tarification et assurances** permet de configurer l’ensemble des règles tarifaires et des conditions financières appliquées aux réservations dans le projet "location 3". Il centralise :

- Les prix de base par catégorie de véhicule.
- Les règles saisonnières.
- Les options payantes.
- Les promotions.
- Les assurances et franchises associées aux contrats.

Il alimente le moteur de calcul des devis, réservations, contrats et factures, aussi bien pour le back-office que pour le portail client.

---

## 2. Gestion des tarifs

### 2.1 Tarifs de base

Le système permet de définir :

- Un prix par jour.
- Des tarifs par catégorie de véhicule (voiture, SUV, utilitaire, premium).
- Une affectation par agence ou globale (selon configuration multi-agences).
- Une devise unique : MAD.

Règles de gestion :
- Un tarif doit être rattaché à une catégorie ou à un véhicule.
- Les périodes de validité doivent être définies.
- Les conflits de période doivent être empêchés ou clairement hiérarchisés.

### 2.2 Saisonnalité

Le module permet de configurer des saisons tarifaires :

- Date de début / date de fin.
- Majoration ou prix spécifique par jour.

Règles :
- Une saison s’applique en fonction des dates de location.
- En cas de chevauchement, une règle de priorité (ex : priorité à la saison la plus spécifique) doit être définie au niveau métier.

### 2.3 Durée et paliers (si applicable)

Le module peut permettre la définition de règles liées à la durée :

- Prix journalier dégressif selon le nombre de jours.
- Forfaits (ex : semaine).

Ces règles doivent être cohérentes avec le calcul global de la réservation.

---

## 3. Options payantes

Le système permet de créer des options configurables :

Exemples :
- GPS.
- Siège bébé.
- Conducteur additionnel.
- Extension kilométrique.

Pour chaque option :
- Nom (FR/EN).
- Description.
- Type de facturation :
  - Par jour.
  - Forfait.
- Prix en MAD.
- Conditions d’éligibilité (catégorie véhicule, agence).
- Activation/désactivation.

Règles :
- Une option peut être sélectionnée lors de la réservation.
- Le prix des options est intégré au calcul total.
- Certaines options peuvent être obligatoires selon la catégorie (si configuré).

---

## 4. Kilométrage

Configuration possible :

- Kilométrage limité (avec plafond).
- Kilométrage illimité.
- Coût par kilomètre supplémentaire.

Règles :
- Le type de kilométrage est défini par tarif ou catégorie.
- Les dépassements sont calculés au retour du véhicule et intégrés à la facturation.

---

## 5. Promotions

Le module permet la création de promotions :

- Code promo.
- Remise en pourcentage ou montant fixe.
- Période de validité.
- Conditions d’application (durée minimale, catégorie, agence).
- Nombre maximal d’utilisations (optionnel).

Règles :
- Une promotion ne peut être appliquée que si toutes les conditions sont respectées.
- Les promotions ne doivent pas rendre le montant total négatif.
- La traçabilité des promotions appliquées doit être conservée.

---

## 6. Assurances et franchises

### 6.1 Assurances

Le module permet de configurer :

- Types d’assurance (incluse, optionnelle).
- Description (FR/EN).
- Couverture.
- Prix (inclus dans le tarif ou en supplément).

Règles :
- Certaines assurances peuvent être obligatoires.
- Les assurances sélectionnées sont intégrées au contrat.

### 6.2 Franchises

Configuration :

- Montant de la franchise par catégorie ou véhicule.
- Réduction de franchise via option payante.

Règles :
- Le montant de la franchise doit être clairement affiché au client.
- Les modifications de franchise doivent être tracées (journal d’audit).

---

## 7. Intégration au processus de réservation

Le module fournit un moteur de calcul utilisé pour :

- Affichage du prix estimatif sur le portail client.
- Calcul du montant total (base + options + assurances − promotions).
- Transmission des montants à la facturation.
- Détermination du montant à payer en ligne et de la caution.

Le détail du calcul doit être conservé pour traçabilité (audit et support client).

---

## 8. Gestion des droits et audit

- Seuls les rôles autorisés (superAdmin, adminSociete) peuvent créer/modifier les règles tarifaires.
- Les responsables d’agence peuvent consulter et appliquer les tarifs selon leur périmètre.
- Toute modification tarifaire ou d’assurance est journalisée (date, utilisateur, ancienne valeur, nouvelle valeur).

---

## 9. Contraintes réglementaires

- Tous les montants sont en MAD.
- Les montants doivent être compatibles avec la gestion de la TVA du module de facturation.
- Les informations tarifaires et d’assurance doivent être affichées clairement pour conformité légale et RGPD (transparence tarifaire).