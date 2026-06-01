# Spécification Fonctionnelle – Ticket : Tableaux de bord KPI

## 1. Objectif du ticket

Ce ticket a pour objectif de développer les indicateurs clés (KPI) du tableau de bord principal du module « Reporting et Tableaux de Bord ».

Les KPI concernés sont :
- Chiffre d’affaires (CA).
- Taux d’occupation.
- Rentabilité par véhicule.
- Retards de restitution.
- Sinistres (ouverts / clôturés).

Ces indicateurs doivent fournir une vue synthétique et exploitable de l’activité du réseau multi-agences.

---

## 2. Périmètre fonctionnel

Le ticket couvre :
- Le calcul métier des indicateurs.
- L’exposition via API.
- L’affichage synthétique dans le tableau de bord (cartes KPI et données structurées).

Il ne couvre pas :
- Les exports avancés.
- Les indicateurs futurs (phase 2).

---

## 3. Indicateurs à implémenter

### 3.1 Chiffre d’affaires (CA)

#### Définition
Montant total généré par les locations sur une période donnée.

#### Règles métier
- Basé sur les factures validées et/ou paiements confirmés.
- Calculé sur la période sélectionnée.
- Affichage en MAD.
- Respect de la configuration TVA existante.
- Filtrable par agence selon le rôle.

#### Affichage
- Carte KPI affichant :
  - Montant total.
  - Variation éventuelle sur période précédente (si disponible ultérieurement).

---

### 3.2 Taux d’occupation

#### Définition
Pourcentage d’utilisation des véhicules sur une période donnée.

#### Règles métier
- Calcul basé sur les périodes effectives de location.
- Rapport entre durée totale louée et durée totale disponible.
- Exprimé en pourcentage.
- Disponible en vue globale et par agence.

#### Affichage
- Carte KPI affichant le pourcentage moyen.

---

### 3.3 Rentabilité par véhicule

#### Définition
Revenus générés par véhicule sur la période sélectionnée.

#### Règles métier
- Agrégation des revenus par véhicule.
- Nombre de locations associé.
- Consultation possible selon périmètre agence.

#### Affichage
- Liste triable affichant pour chaque véhicule :
  - Identifiant ou immatriculation.
  - Nombre de locations.
  - Revenus générés.

---

### 3.4 Retards de restitution

#### Définition
Contrats dont la date/heure de fin est dépassée sans restitution enregistrée.

#### Règles métier
- Un retard est actif si :
  - date_fin < date/heure actuelle.
  - Statut du contrat ≠ clôturé.
- Mise à jour dynamique.
- Filtrage par agence selon rôle.

#### Affichage
- Carte KPI avec nombre de retards en cours.
- Liste détaillée avec :
  - Client.
  - Véhicule.
  - Agence.
  - Durée estimée du retard.

---

### 3.5 Sinistres

#### Définition
Nombre de sinistres enregistrés sur la période.

#### Règles métier
- Distinction entre :
  - Sinistres ouverts.
  - Sinistres clôturés.
- Filtrage par période et agence.

#### Affichage
- Carte KPI affichant :
  - Total.
  - Répartition ouvert / clôturé.

---

## 4. Filtres applicables

Tous les indicateurs doivent supporter :
- Filtre par période :
  - Jour.
  - Mois.
  - Année.
  - Plage personnalisée.
- Filtre par agence (selon rôle).

Les responsables d’agence ne doivent voir que les données de leur agence.

---

## 5. Droits d’accès

- Super Admin / Admin Société : accès global multi-agences.
- Responsable d’agence : accès restreint à son agence.
- Comptabilité : accès aux indicateurs financiers (notamment CA).
- Agent comptoir : accès limité selon configuration.

Les données affichées ne doivent pas exposer d’informations personnelles sensibles au-delà du nécessaire.

---

## 6. Contraintes transverses

- Cohérence stricte avec les données réelles (réservations, contrats, factures, sinistres).
- Respect du modèle multi-agences.
- Affichage en devise MAD.
- Compatible multilingue (libellés gérés côté frontend).
- Performance adaptée à un usage simultané par 50–200 utilisateurs internes.