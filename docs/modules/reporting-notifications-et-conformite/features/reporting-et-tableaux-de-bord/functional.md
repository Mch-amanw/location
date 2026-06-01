# Spécification Fonctionnelle – Reporting et Tableaux de Bord

## 1. Objectif métier

La fonctionnalité « Reporting et tableaux de bord » a pour objectif de fournir aux différents profils internes (direction, responsables d’agence, comptabilité) des indicateurs fiables et synthétiques pour piloter l’activité du réseau multi-agences.

Elle permet :
- Le suivi du chiffre d’affaires (CA).
- L’analyse du taux d’occupation des véhicules.
- L’évaluation de la rentabilité par véhicule.
- Le suivi des retards de restitution.
- Le suivi des sinistres.

Les données doivent refléter fidèlement l’activité réelle issue des réservations, contrats, paiements et sinistres.

---

## 2. Périmètre fonctionnel

### 2.1 Tableaux de bord globaux

Un tableau de bord principal présente une vue synthétique des indicateurs clés :

- Chiffre d’affaires (global et par agence).
- Nombre total de réservations sur la période.
- Taux d’occupation moyen.
- Nombre de retards en cours.
- Nombre de sinistres (ouverts / clôturés).

Les indicateurs sont présentés sous forme de :
- Cartes synthétiques (KPI).
- Graphiques (évolution sur période).
- Listes détaillées (si applicable).

---

### 2.2 Chiffre d’affaires (CA)

#### Objectif
Permettre le suivi du chiffre d’affaires généré par les locations.

#### Règles de gestion
- Le CA est calculé à partir des factures et/ou paiements validés.
- Il peut être consulté :
  - Globalement (toutes agences).
  - Par agence.
  - Sur une période donnée.
- Les montants sont affichés en MAD.
- La cohérence avec les règles de TVA configurées doit être respectée.

#### Critères d’acceptation
- L’utilisateur peut filtrer par période (jour, mois, année, plage personnalisée).
- Un responsable d’agence ne voit que les données de son agence.
- Les montants correspondent aux données présentes dans les factures validées.

---

### 2.3 Taux d’occupation

#### Objectif
Mesurer le niveau d’utilisation de la flotte.

#### Règles de gestion
- Le taux d’occupation est calculé à partir des périodes de location des véhicules sur une période donnée.
- Il peut être visualisé :
  - Globalement.
  - Par agence.
  - Par véhicule (vue détaillée).

#### Critères d’acceptation
- Le taux est exprimé en pourcentage.
- Les périodes sans contrat ne sont pas comptabilisées comme occupées.
- Les filtres par période et agence sont appliqués correctement.

---

### 2.4 Rentabilité par véhicule

#### Objectif
Identifier les véhicules les plus et les moins performants.

#### Règles de gestion
- La rentabilité est basée sur les revenus générés par véhicule sur la période sélectionnée.
- Affichage sous forme de liste triable (du plus rentable au moins rentable).
- Consultation possible par agence selon le rôle.

#### Critères d’acceptation
- Chaque véhicule affiche au minimum :
  - Immatriculation ou identifiant.
  - Nombre de locations.
  - Revenus générés.
- Le tri fonctionne correctement.

---

### 2.5 Retards de restitution

#### Objectif
Suivre les retards en cours afin d’améliorer le contrôle opérationnel.

#### Règles de gestion
- Un retard est identifié lorsqu’un véhicule n’est pas restitué à la date/heure prévue.
- Liste des retards en cours avec :
  - Client.
  - Véhicule.
  - Agence.
  - Durée estimée du retard.

#### Critères d’acceptation
- Les retards disparaissent de la liste dès régularisation.
- Les responsables d’agence ne voient que les retards de leur agence.

---

### 2.6 Suivi des sinistres

#### Objectif
Suivre le nombre et le statut des sinistres.

#### Règles de gestion
- Affichage du nombre total de sinistres sur la période.
- Distinction entre sinistres ouverts et clôturés.
- Filtre par agence et période.

#### Critères d’acceptation
- Les données correspondent aux enregistrements du module sinistres.
- Les filtres par rôle sont respectés.

---

## 3. Filtres et droits d’accès

### 3.1 Filtres disponibles
- Période : jour, mois, année, plage personnalisée.
- Agence (selon rôle).

### 3.2 Gestion des rôles
- Super Admin / Admin Société : accès global multi-agences.
- Responsable d’agence : accès restreint à son agence.
- Comptabilité : accès aux indicateurs financiers.
- Agent comptoir : accès limité (selon configuration, pas d’accès aux indicateurs stratégiques globaux).

---

## 4. Contraintes et conformité

- Données affichées cohérentes avec les règles de facturation (TVA marocaine).
- Respect du périmètre multi-agences.
- Aucune donnée personnelle sensible exposée au-delà des droits définis.
- Consultation des tableaux de bord potentiellement tracée via le journal d’audit si considérée comme action sensible.

---

# Critères d’acceptation globaux

- Les indicateurs sont calculés à partir des données réelles (réservations, contrats, factures, sinistres).
- Les filtres (période, agence) fonctionnent de manière cohérente sur tous les indicateurs.
- Les droits d’accès sont strictement respectés selon le rôle.
- Les performances sont compatibles avec un usage par 50–200 utilisateurs internes simultanés.