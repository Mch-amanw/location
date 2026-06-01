# Fonctionnalité : Fiches véhicules

## 1. Objectif métier

La fonctionnalité **Fiches véhicules** permet de créer, consulter, modifier et désactiver les véhicules exploités par le réseau multi-agences.

Elle constitue la base opérationnelle de la gestion de la flotte : chaque réservation, contrat, état des lieux, transfert ou reporting repose sur des données véhicules fiables et à jour.

---

## 2. Périmètre

La fonctionnalité couvre :

- Création d’une fiche véhicule.
- Consultation détaillée d’un véhicule.
- Modification des informations.
- Gestion du statut opérationnel.
- Rattachement à une agence.
- Désactivation (mise hors flotte).
- Consultation de l’historique des affectations (agences).
- Gestion des photos du véhicule.

Elle s’applique à toutes les catégories définies dans le module (Voitures, SUV, Utilitaires, Premium).

---

## 3. Données d’une fiche véhicule

Chaque fiche véhicule comprend au minimum :

- Identifiant interne unique (généré automatiquement).
- Code interne (si utilisé par l’exploitation).
- Immatriculation (unique).
- Catégorie (obligatoire).
- Marque.
- Modèle.
- Année.
- Agence d’affectation actuelle (obligatoire).
- Statut opérationnel (enum : Disponible, Réservé, En location, En transfert, En maintenance/indisponible, Hors flotte).
- Indicateur d’activation (actif / hors flotte).
- Photos (une ou plusieurs).
- Dates de création et dernière modification.

---

## 4. Règles de gestion

### 4.1 Création

- L’immatriculation doit être unique dans tout le système.
- La catégorie sélectionnée doit être active.
- Une agence d’affectation initiale est obligatoire.
- Le statut initial par défaut est « Disponible » (sauf décision métier contraire).
- Toute création est enregistrée dans le journal d’audit.

### 4.2 Modification

- Les champs modifiables excluent l’identifiant interne.
- Toute modification sensible (statut, agence, désactivation) est journalisée.
- Un véhicule ne peut appartenir qu’à une seule agence à un instant donné.

### 4.3 Gestion des statuts

- Un véhicule en location ne peut pas être passé manuellement en transfert.
- Un véhicule en maintenance est exclu des disponibilités.
- Le passage en « Hors flotte » rend le véhicule indisponible pour toute nouvelle réservation.
- Les changements de statut mettent à jour immédiatement la disponibilité exploitable par le module Réservations.

### 4.4 Désactivation

- La désactivation correspond à une mise « Hors flotte ».
- Un véhicule désactivé ne peut plus être réservé.
- Les données historiques (réservations, contrats, états des lieux) restent accessibles.

### 4.5 Photos

- Plusieurs photos peuvent être associées à un véhicule.
- Les photos doivent être rattachées à la fiche et consultables depuis le back-office.

---

## 5. Recherche et consultation

Le back-office doit permettre :

- Recherche par immatriculation.
- Recherche par marque ou modèle.
- Filtres par agence, catégorie et statut.
- Vue liste paginée.
- Accès à une vue détaillée complète.

---

## 6. Droits et permissions

- Super Admin : accès complet multi-agences.
- Admin société : accès complet flotte.
- Responsable d’agence : gestion des véhicules de son agence.
- Agent comptoir : consultation et modifications limitées selon configuration RBAC.
- Comptabilité : consultation seule.

Toutes les actions respectent le modèle RBAC défini au niveau projet.

---

## 7. Critères d’acceptation (exemples)

- ✅ Impossible de créer deux véhicules avec la même immatriculation.
- ✅ Un véhicule désactivé n’apparaît plus dans les listes de disponibilité pour réservation.
- ✅ Le changement de statut est immédiatement reflété dans la disponibilité.
- ✅ Toute modification sensible génère une entrée dans le journal d’audit.
- ✅ Un responsable d’agence ne peut pas modifier un véhicule rattaché à une autre agence.

---

## 8. Intégrations

- Module Réservations : consommation de la disponibilité véhicule.
- Module Transferts : modification d’agence via processus contrôlé.
- Module États des lieux : rattachement des constats au véhicule.
- Reporting : calcul rentabilité et taux d’occupation par véhicule.
- Journal d’audit : traçabilité complète des opérations critiques.