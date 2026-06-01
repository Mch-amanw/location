# Fonctionnalité : Gestion multi-agences

## 1. Objectif métier

La fonctionnalité **Gestion multi-agences** permet d’administrer l’ensemble des agences du réseau de location dans le cadre du projet "location 3". Elle garantit une organisation claire, un cloisonnement des données par agence et une configuration locale adaptée à chaque entité opérationnelle.

Elle s’adresse principalement aux rôles **Super Admin** et **Administrateur société**.

---

## 2. Périmètre fonctionnel

### 2.1 Création d’une agence

Le système permet :
- La création d’une nouvelle agence.
- La saisie des informations administratives et de contact.

Données gérées :
- Nom de l’agence.
- Adresse complète.
- Ville.
- Téléphone.
- Email.
- Statut (active / inactive).

Règles de gestion :
- Le nom de l’agence est obligatoire.
- Une agence nouvellement créée est active par défaut (sauf configuration contraire).
- Seuls les rôles autorisés (Super Admin, Administrateur société) peuvent créer une agence.

---

### 2.2 Modification des informations d’une agence

Le système permet :
- La modification des informations générales.
- La mise à jour des coordonnées.

Règles de gestion :
- Les modifications sont historisées via le journal d’audit.
- Les modifications n’impactent pas les données historiques (réservations, contrats, factures).

---

### 2.3 Activation / désactivation d’une agence

Le système permet de :
- Désactiver une agence (soft disable).
- Réactiver une agence désactivée.

Règles de gestion :
- Une agence inactive ne peut plus :
  - Être sélectionnée pour de nouvelles réservations.
  - Recevoir de nouveaux véhicules.
- Les données existantes (réservations, contrats, factures, sinistres) restent accessibles en consultation.
- La désactivation ne supprime aucune donnée (pas de suppression physique).

---

### 2.4 Paramétrage local de l’agence

Chaque agence peut disposer de paramètres propres (dans le cadre du MVP) :
- Statut d’activation.
- Informations d’affichage (nom, coordonnées) utilisées dans les documents (contrats, factures).

Règles de gestion :
- Les paramètres locaux s’appliquent uniquement aux données rattachées à l’agence.
- Les rôles non globaux ne peuvent consulter et modifier que leur propre agence.

---

## 3. Règles de sécurité et d’accès

- Seuls les rôles globaux (Super Admin, Administrateur société) peuvent créer ou désactiver une agence.
- Un Responsable d’agence peut consulter les informations de son agence mais ne peut pas créer ou désactiver d’autres agences.
- Toute tentative d’accès à une agence hors périmètre doit être bloquée.

---

## 4. Journal d’audit

Les actions suivantes doivent être tracées :
- Création d’agence.
- Modification d’agence.
- Changement de statut (activation / désactivation).

Chaque entrée d’audit inclut :
- L’utilisateur initiateur.
- La date et l’heure.
- Le type d’action.
- L’identifiant de l’agence concernée.

---

## 5. Critères d’acceptation

- ✅ Un Super Admin peut créer une nouvelle agence et la voir apparaître dans la liste.
- ✅ Une agence désactivée ne peut plus être sélectionnée lors de la création d’une réservation.
- ✅ Les données historiques d’une agence désactivée restent consultables.
- ✅ Un Responsable d’agence ne peut consulter que son agence.
- ✅ Toute action de création, modification ou désactivation génère une entrée dans le journal d’audit.

---

## 6. Hors périmètre

- Gestion avancée de configuration financière spécifique par agence (ex : TVA différenciée).
- Paramétrage comptable spécifique (prévu en phase 2 via intégration comptable).

---

# Conclusion fonctionnelle

La fonctionnalité de Gestion multi-agences structure le réseau et garantit une administration centralisée avec cloisonnement strict des données. Elle constitue un élément fondamental de la gouvernance et de la sécurité du système multi-agences.