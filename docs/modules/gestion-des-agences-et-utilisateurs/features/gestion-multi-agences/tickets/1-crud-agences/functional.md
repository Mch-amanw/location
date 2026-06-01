# Ticket : CRUD agences

## 1. Objectif du ticket

Ce ticket a pour objectif d’implémenter les opérations de **création, consultation, modification et désactivation (CRUD logique)** des agences dans le cadre de la fonctionnalité *Gestion multi-agences*.

Il permet aux rôles autorisés d’administrer le réseau d’agences tout en garantissant :
- Le cloisonnement des données.
- La conservation de l’historique métier.
- La traçabilité des actions via le journal d’audit.

---

## 2. Périmètre fonctionnel

### 2.1 Création d’une agence

Les rôles **Super Admin** et **Administrateur société** peuvent créer une nouvelle agence.

Informations à renseigner :
- Nom de l’agence (obligatoire)
- Adresse complète
- Ville
- Téléphone
- Email
- Statut (active / inactive)

Règles métier :
- Le nom de l’agence est obligatoire.
- Une agence est active par défaut si le statut n’est pas précisé.
- L’agence devient immédiatement disponible pour rattachement de véhicules et d’utilisateurs si elle est active.
- La création génère une entrée dans le journal d’audit.

---

### 2.2 Consultation des agences

Le système permet :
- La consultation de la liste des agences.
- La consultation du détail d’une agence.

Règles d’accès :
- Les rôles globaux voient toutes les agences.
- Les rôles rattachés à une agence (Responsable d’agence, Agent comptoir, Comptabilité non globale) ne peuvent consulter que leur propre agence.
- Les agences inactives restent visibles pour consultation (historique et reporting).

La liste peut être filtrée par statut (active / inactive).

---

### 2.3 Modification d’une agence

Les rôles **Super Admin** et **Administrateur société** peuvent modifier :
- Nom
- Adresse
- Ville
- Téléphone
- Email

Règles métier :
- Les modifications n’affectent pas les données historiques (réservations, contrats, factures, sinistres).
- Les nouvelles informations sont utilisées dans les futurs documents générés (contrats, factures).
- Toute modification génère une entrée dans le journal d’audit.

Un Responsable d’agence peut consulter les informations mais ne peut pas les modifier (sauf évolution ultérieure non incluse dans ce ticket).

---

### 2.4 Désactivation / réactivation d’une agence

Le système permet de :
- Désactiver une agence.
- Réactiver une agence inactive.

Règles métier :
- La désactivation est logique (aucune suppression physique).
- Une agence inactive :
  - Ne peut plus être sélectionnée lors de la création d’une réservation.
  - Ne peut plus recevoir de nouveaux véhicules.
- Les données historiques restent accessibles en consultation.
- La réactivation restaure les capacités opérationnelles.
- Chaque changement de statut est journalisé.

---

## 3. Paramètres locaux de l’agence

Les informations de l’agence (nom, adresse, coordonnées) sont utilisées :
- Dans les contrats de location.
- Dans les factures.
- Dans les documents PDF générés.

Les modifications doivent être prises en compte pour les documents générés après la mise à jour.

---

## 4. Règles de sécurité

- Seuls les rôles globaux peuvent créer, modifier ou désactiver une agence.
- Toute tentative d’accès hors périmètre doit être bloquée.
- Les contrôles d’accès sont appliqués côté backend.
- Toutes les actions sensibles sont tracées dans le journal d’audit.

---

## 5. Hors périmètre du ticket

- Paramétrage financier spécifique par agence (TVA personnalisée, comptes comptables).
- Suppression physique d’une agence.
- Paramétrage avancé (horaires, règles tarifaires spécifiques).