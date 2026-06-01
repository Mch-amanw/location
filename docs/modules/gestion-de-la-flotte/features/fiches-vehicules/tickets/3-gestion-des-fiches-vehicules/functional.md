# Ticket : Gestion des fiches véhicules

## 1. Objectif du ticket

Mettre en place la gestion complète des **fiches véhicules** dans le back-office afin de permettre la création, la consultation, la modification et la désactivation des véhicules exploités par le réseau multi-agences.

Ce ticket couvre l’implémentation des champs principaux d’un véhicule (catégorie, immatriculation, statut, agence, etc.) et leur gestion conforme aux règles métier définies dans le module Gestion de la flotte.

---

## 2. Périmètre fonctionnel

Le ticket couvre :

- Création d’une fiche véhicule.
- Affichage de la liste des véhicules avec filtres.
- Consultation d’une fiche véhicule détaillée.
- Modification des informations d’un véhicule.
- Gestion du statut opérationnel.
- Désactivation (mise hors flotte).

La gestion des transferts inter-agences et des catégories est hors périmètre direct de ce ticket (elles sont gérées par leurs fonctionnalités dédiées), mais la fiche véhicule doit s’y intégrer correctement.

---

## 3. Données d’une fiche véhicule

Chaque véhicule doit comporter les informations suivantes :

### 3.1 Identification
- Identifiant interne unique (généré automatiquement).
- Code interne (optionnel).
- Immatriculation (obligatoire, unique au niveau global).

### 3.2 Classification
- Catégorie (obligatoire, sélection parmi les catégories actives).

### 3.3 Informations descriptives
- Marque.
- Modèle.
- Année.

### 3.4 Rattachement organisationnel
- Agence d’affectation actuelle (obligatoire).

### 3.5 Statut opérationnel
Statut sélectionné parmi les valeurs suivantes :
- Disponible.
- Réservé.
- En location.
- En transfert.
- En maintenance / indisponible.
- Hors flotte.

### 3.6 État d’activation
- Indicateur actif / inactif.
- La désactivation correspond fonctionnellement au passage en « Hors flotte ».

### 3.7 Métadonnées
- Date de création.
- Date de dernière modification.

---

## 4. Règles de gestion

### 4.1 Création

- L’immatriculation doit être unique dans l’ensemble du système.
- La catégorie sélectionnée doit être active.
- Une agence d’affectation initiale est obligatoire.
- Le statut initial par défaut est « Disponible ».
- La création d’un véhicule est tracée dans le journal d’audit.

### 4.2 Modification

- L’identifiant interne n’est jamais modifiable.
- Toute modification du statut, de l’agence ou de l’état d’activation est considérée comme sensible et doit être journalisée.
- Un véhicule ne peut appartenir qu’à une seule agence à un instant donné.

### 4.3 Gestion des statuts

- Un véhicule en location ne peut pas être passé manuellement en transfert.
- Un véhicule en maintenance est exclu des disponibilités.
- Le statut « Hors flotte » rend le véhicule indisponible pour toute nouvelle réservation.
- Toute modification de statut doit être immédiatement prise en compte dans la disponibilité exploitable par le module Réservations.

### 4.4 Désactivation

- La désactivation correspond à une mise « Hors flotte ».
- Un véhicule désactivé reste visible dans l’historique et le reporting.
- Les données historiques (réservations, contrats, états des lieux) restent accessibles.

---

## 5. Interface back-office

### 5.1 Liste des véhicules

- Vue paginée.
- Recherche par immatriculation.
- Recherche par marque ou modèle.
- Filtres : agence, catégorie, statut.
- Affichage synthétique : immatriculation, catégorie, agence, statut.

### 5.2 Fiche détail

- Affichage complet des informations du véhicule.
- Historique des modifications sensibles (via journal d’audit si accessible).
- Accès aux actions : modifier, changer statut, désactiver.

---

## 6. Droits et permissions

- Super Admin : gestion complète multi-agences.
- Admin société : gestion complète flotte.
- Responsable d’agence : gestion des véhicules rattachés à son agence.
- Agent comptoir : consultation et modifications selon configuration RBAC.
- Comptabilité : consultation seule.

Les restrictions par agence doivent être respectées conformément au modèle RBAC du projet.

---

## 7. Intégrations

- Module Réservations : consommation du statut pour déterminer la disponibilité.
- Module Transferts : mise à jour de l’agence via workflow dédié.
- Module Reporting : exploitation des données véhicule.
- Journal d’audit : traçabilité des actions critiques.

Ce ticket constitue la base opérationnelle permettant aux autres modules de fonctionner correctement.