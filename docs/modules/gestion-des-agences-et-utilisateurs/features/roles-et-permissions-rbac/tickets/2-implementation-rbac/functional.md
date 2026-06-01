# Ticket : Implémentation RBAC

## 1. Objectif du ticket

Ce ticket a pour objectif de mettre en œuvre concrètement le système de **gestion des rôles et permissions (RBAC)** pour le projet "location 3", conformément aux spécifications du module *Gestion des agences et utilisateurs* et de la fonctionnalité *Rôles et permissions (RBAC)*.

L’implémentation doit permettre :
- Le contrôle strict des accès aux fonctionnalités.
- Le cloisonnement des données par agence.
- L’application du principe du moindre privilège.
- La traçabilité des actions liées aux droits d’accès.

Ce ticket est critique car il conditionne la sécurité globale de l’application.

---

## 2. Périmètre fonctionnel

### 2.1 Rôles concernés (MVP)

Les rôles prédéfinis suivants doivent être supportés :

- SUPER_ADMIN
- ADMIN_SOCIETE
- RESPONSABLE_AGENCE
- AGENT_COMPTOIR
- COMPTABILITE

Les rôles sont fixes et non configurables dynamiquement dans le MVP.

---

## 3. Contrôle d’accès aux fonctionnalités

### 3.1 Accès aux modules

Le système doit permettre de restreindre l’accès aux modules selon le rôle :

- Gestion des agences : réservé aux rôles globaux.
- Gestion des utilisateurs : réservé aux rôles autorisés (Super Admin, Administrateur société, Responsable d’agence selon périmètre).
- Réservations, contrats, états des lieux : accessibles selon rôle et agence.
- Facturation et paiements : accessibles au rôle Comptabilité (et rôles globaux).
- Reporting consolidé : réservé aux rôles globaux.

L’accès non autorisé à un module ou une action doit être bloqué.

---

## 4. Cloisonnement par agence

### 4.1 Principe

Toute donnée métier est rattachée à une agence.

Le système doit garantir que :
- Un utilisateur rattaché à une agence ne peut voir que les données de cette agence.
- Un utilisateur global peut accéder aux données de toutes les agences.

Ce cloisonnement doit s’appliquer à :
- Réservations
- Véhicules
- Contrats
- Factures
- Clients
- Sinistres
- Paiements
- Reporting

---

## 5. Gestion des changements de rôle

Le système doit permettre :
- La modification du rôle d’un utilisateur (selon permissions).
- L’application immédiate des nouveaux droits.

Les changements de rôle doivent être journalisés.

---

## 6. Tentatives d’accès non autorisé

Le système doit :
- Bloquer toute tentative d’accès à une ressource hors périmètre.
- Retourner une réponse d’autorisation refusée.
- Enregistrer l’événement dans le journal d’audit (log de sécurité).

---

## 7. Traçabilité

Les actions suivantes doivent être tracées :
- Modification du rôle d’un utilisateur.
- Tentative d’accès interdit.
- Accès à des endpoints sensibles définis comme critiques.

Chaque entrée d’audit doit contenir :
- Utilisateur
- Date/heure
- Type d’action
- Ressource ciblée
- Résultat (autorisé / refusé)

---

## 8. Contraintes fonctionnelles

- Le contrôle d’accès ne doit pas reposer uniquement sur le frontend.
- Le filtrage par agence doit être automatique.
- Le comportement doit être cohérent avec les spécifications du module parent.
- Respect du RGPD via limitation des accès aux données nécessaires.

---

## Conclusion fonctionnelle

L’implémentation RBAC doit garantir une isolation stricte des agences, une séparation claire des responsabilités et une protection forte des opérations sensibles. Elle constitue le socle de sécurité du système "location 3".