# Module : Gestion des agences et utilisateurs

## 1. Rôle du module

Le module **Gestion des agences et utilisateurs** structure l’organisation du réseau multi-agences du projet "location 3". Il permet :

- La gestion des agences du réseau.
- La gestion des utilisateurs internes.
- La gestion des rôles et des permissions (RBAC).
- Le cloisonnement des données par agence.

Ce module est central pour garantir la sécurité, la séparation des responsabilités et la cohérence des opérations (réservations, contrats, facturation, reporting, etc.).

---

## 2. Gestion des agences

### 2.1 Création et configuration d’une agence

Le système permet :
- Création d’une agence par un Super Admin ou Administrateur société.
- Modification des informations d’une agence.
- Désactivation (et non suppression physique) d’une agence.

Données principales d’une agence :
- Nom de l’agence.
- Adresse complète.
- Ville.
- Coordonnées (téléphone, email).
- Statut (active / inactive).

Règles de gestion :
- Une agence inactive ne peut plus recevoir de nouvelles réservations.
- Les données historiques (réservations, contrats, factures) restent accessibles.
- Les véhicules sont rattachés à une agence.

---

## 3. Gestion des utilisateurs internes

### 3.1 Création et gestion des comptes

Le module permet :
- Création d’un utilisateur interne.
- Modification des informations.
- Activation / désactivation.
- Réinitialisation du mot de passe.

Données principales :
- Nom et prénom.
- Email (identifiant unique).
- Téléphone (optionnel).
- Rôle.
- Agence de rattachement (si applicable).
- Statut (actif / inactif).

Règles de gestion :
- L’email est unique dans le système.
- Un utilisateur inactif ne peut plus se connecter.
- Un utilisateur est rattaché :
  - Soit à l’ensemble du réseau (Super Admin).
  - Soit à une agence spécifique.

---

## 4. Rôles et permissions (RBAC)

### 4.1 Rôles prédéfinis (MVP)

Le MVP inclut les rôles suivants :

- **Super Admin** : accès global à toutes les agences et paramètres.
- **Administrateur société** : gestion globale opérationnelle (agences, flotte, utilisateurs).
- **Responsable d’agence** : gestion complète sur son agence.
- **Agent comptoir** : gestion opérationnelle des réservations, contrats, états des lieux.
- **Comptabilité** : accès facturation, paiements, reporting financier.

### 4.2 Logique de permissions

- Les permissions sont associées aux rôles.
- Les droits sont appliqués par périmètre (global ou agence).
- Un utilisateur ne peut accéder qu’aux données de son agence, sauf rôle global.

Exemples de règles :
- Un Responsable d’agence ne peut voir que les véhicules, réservations et clients de son agence.
- La Comptabilité peut consulter les factures de toutes les agences si rôle global.
- Seuls Super Admin et Administrateur société peuvent créer des agences.

---

## 5. Cloisonnement multi-agences

Le système garantit :
- Isolation logique des données par agence.
- Filtrage automatique des données selon le rôle et l’agence.
- Reporting consolidé pour les rôles globaux.

Règles clés :
- Toute donnée métier (réservation, contrat, véhicule, sinistre, facture) est rattachée à une agence.
- Les contrôles d’accès sont appliqués côté backend.

---

## 6. Authentification et accès

- Authentification par email + mot de passe.
- Gestion des sessions sécurisées.
- Déconnexion manuelle.
- Blocage d’accès si compte désactivé.

Le module s’applique aux utilisateurs internes. Les clients finaux sont gérés dans le module "Gestion des clients".

---

## 7. Journal d’audit

Les actions suivantes doivent être tracées :
- Création / modification / désactivation d’agence.
- Création / modification / désactivation d’utilisateur.
- Changement de rôle.

Chaque entrée d’audit contient :
- Utilisateur ayant effectué l’action.
- Date et heure.
- Type d’action.
- Entité concernée.

---

## 8. Contraintes réglementaires

- Respect RGPD pour les données personnelles des utilisateurs internes.
- Accès limité aux données strictement nécessaires selon le rôle.
- Possibilité de désactivation sans suppression des données historiques.

---

# Conclusion fonctionnelle

Ce module constitue le socle organisationnel et sécuritaire du système "location 3". Il garantit une gestion multi-agences cohérente, un contrôle d’accès strict et la traçabilité des actions sensibles.