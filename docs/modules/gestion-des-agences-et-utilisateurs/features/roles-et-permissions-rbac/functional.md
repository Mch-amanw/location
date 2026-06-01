# Fonctionnalité : Rôles et permissions (RBAC)

## 1. Objectif métier

La fonctionnalité **Rôles et permissions (RBAC)** permet de contrôler précisément l’accès aux fonctionnalités et aux données du système "location 3" selon le profil utilisateur.

Elle garantit :
- La séparation des responsabilités entre les différents profils.
- Le cloisonnement des données par agence.
- La sécurisation des opérations sensibles (contrats, facturation, cautions, paramétrage).
- La conformité RGPD via le principe du moindre privilège.

Cette fonctionnalité est centrale pour un fonctionnement multi-agences sécurisé.

---

## 2. Périmètre

### 2.1 Rôles prédéfinis (MVP)

Les rôles suivants sont disponibles dans le MVP :

- **Super Admin**
- **Administrateur société**
- **Responsable d’agence**
- **Agent comptoir**
- **Comptabilité**

Les rôles sont prédéfinis et non modifiables dynamiquement dans le MVP.

---

## 3. Description des rôles

### 3.1 Super Admin
- Accès global à toutes les agences.
- Gestion complète des agences.
- Gestion complète des utilisateurs et des rôles.
- Accès à tous les modules (flotte, réservations, contrats, facturation, reporting, CMS, etc.).
- Accès au reporting consolidé multi-agences.

### 3.2 Administrateur société
- Accès global opérationnel.
- Création et modification des agences.
- Gestion des utilisateurs.
- Gestion flotte, tarification, réservations.
- Accès global au reporting.
- Pas d’accès aux paramètres techniques sensibles réservés au Super Admin (si existants).

### 3.3 Responsable d’agence
- Accès complet aux données de son agence uniquement.
- Gestion des véhicules de son agence.
- Gestion des réservations, contrats, états des lieux.
- Accès aux reportings de son agence.
- Gestion des utilisateurs rattachés à son agence (selon politique définie par le système).

### 3.4 Agent comptoir
- Accès aux réservations et contrats de son agence.
- Création et modification des réservations.
- Réalisation des états des lieux.
- Consultation des clients.
- Pas d’accès au paramétrage global ni au reporting consolidé.

### 3.5 Comptabilité
- Accès aux factures et paiements.
- Consultation des cautions et restitutions.
- Accès au reporting financier.
- Pas d’accès à la gestion des agences ou des utilisateurs.

---

## 4. Règles de gestion

### 4.1 Attribution des rôles
- Chaque utilisateur interne possède un seul rôle actif.
- Le rôle est obligatoire.
- Le rôle détermine automatiquement les permissions applicables.

### 4.2 Périmètre agence
- Un utilisateur peut être :
  - Global (Super Admin, Administrateur société).
  - Rattaché à une agence spécifique.
- Un utilisateur rattaché à une agence ne peut consulter ou modifier que les données de cette agence.
- Toute tentative d’accès hors périmètre doit être bloquée.

### 4.3 Principe du moindre privilège
- Chaque rôle ne dispose que des accès strictement nécessaires.
- Les actions sensibles (ex : restitution de caution, modification de contrat, changement de rôle) sont limitées à des rôles autorisés.

### 4.4 Traçabilité
Les actions suivantes doivent être journalisées :
- Création/modification/suppression logique d’un rôle utilisateur.
- Changement de rôle d’un utilisateur.
- Tentative d’accès non autorisé (log de sécurité).

---

## 5. Cas d’usage principaux

- Un Responsable d’agence consulte uniquement les réservations de son agence.
- Un Agent comptoir ne peut pas accéder au module de gestion des agences.
- Un utilisateur Comptabilité peut consulter les factures multi-agences si rôle global.
- Un Super Admin peut désactiver un utilisateur quelle que soit son agence.

---

## 6. Critères d’acceptation

- ✅ Un utilisateur ne peut accéder qu’aux modules autorisés par son rôle.
- ✅ Les données sont automatiquement filtrées selon l’agence de rattachement.
- ✅ Un accès interdit retourne une erreur d’autorisation.
- ✅ Les changements de rôle sont tracés dans le journal d’audit.
- ✅ Les rôles prédéfinis correspondent aux règles décrites dans la spécification du module parent.

---

## 7. Contraintes

- Respect strict du cloisonnement multi-agences.
- Conformité RGPD (accès limité aux données nécessaires).
- Cohérence avec le module "Gestion des agences et utilisateurs".

---

# Conclusion fonctionnelle

La fonctionnalité RBAC structure l’ensemble des accès du système. Elle garantit la sécurité, l’isolation des agences et la répartition claire des responsabilités au sein du réseau de location.