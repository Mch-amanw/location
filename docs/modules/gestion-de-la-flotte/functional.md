## 1. Rôle du module

Le module **Gestion de la flotte** permet d’administrer l’ensemble des véhicules exploités par le réseau multi-agences. Il constitue le socle opérationnel du système : chaque réservation, contrat, transfert, état des lieux ou reporting s’appuie sur les données de ce module.

Il couvre :
- La gestion des catégories de véhicules.
- La création et la mise à jour des fiches véhicules.
- La gestion des statuts opérationnels.
- Le suivi d’affectation par agence.
- Les transferts inter-agences.

---

## 2. Gestion des catégories de véhicules

### 2.1 Objectif
Structurer la flotte par catégories afin de faciliter la tarification, la recherche et le reporting.

### 2.2 Fonctionnalités
- Création / modification / désactivation d’une catégorie.
- Types supportés (MVP) :
  - Voitures
  - SUV
  - Utilitaires
  - Premium
- Paramètres associés :
  - Nom (FR/EN).
  - Description (FR/EN).
  - Image illustrative.
  - Indicateur d’activation.

### 2.3 Règles de gestion
- Une catégorie désactivée ne peut plus être sélectionnée pour un nouveau véhicule.
- La suppression physique d’une catégorie est interdite si des véhicules y sont rattachés.

---

## 3. Gestion des véhicules

### 3.1 Fiche véhicule

Chaque véhicule dispose d’une fiche complète comprenant au minimum :
- Identifiant interne unique.
- Immatriculation.
- Catégorie.
- Marque / modèle.
- Année.
- Agence d’affectation actuelle.
- Statut opérationnel.
- Informations descriptives (couleur, carburant, transmission si définies dans le projet).
- Photos.

### 3.2 Fonctionnalités
- Création d’un véhicule.
- Modification des informations.
- Désactivation (mise hors flotte).
- Consultation détaillée.
- Historique des affectations (agences).

### 3.3 Règles de gestion
- L’immatriculation doit être unique.
- Un véhicule appartient à une seule agence à un instant donné.
- Un véhicule désactivé ne peut plus être proposé à la réservation.
- Toute modification sensible (statut, agence, désactivation) est journalisée.

---

## 4. Gestion des statuts véhicules

### 4.1 Statuts principaux (MVP)
- Disponible.
- Réservé.
- En location.
- En transfert.
- En maintenance / indisponible.
- Hors flotte.

### 4.2 Règles de transition
- Un véhicule en location ne peut pas être transféré.
- Un véhicule en maintenance est exclu du calcul de disponibilité.
- Le passage en “hors flotte” le rend définitivement indisponible pour les nouvelles réservations.
- Les changements de statut déclenchent une mise à jour immédiate des disponibilités.

---

## 5. Transferts inter-agences

### 5.1 Objectif
Permettre la réallocation d’un véhicule entre agences du réseau.

### 5.2 Fonctionnalités
- Création d’un transfert :
  - Agence d’origine.
  - Agence de destination.
  - Date de départ prévue.
  - Date d’arrivée prévue.
- Passage automatique au statut “En transfert”.
- Confirmation de réception par l’agence de destination.
- Historique des transferts.

### 5.3 Règles de gestion
- Un transfert ne peut être initié que si le véhicule est disponible.
- Pendant la période de transfert, le véhicule est indisponible à la réservation.
- La réception valide le changement d’agence d’affectation.
- Toute opération de transfert est tracée dans le journal d’audit.

---

## 6. Recherche et filtres

Le module doit permettre :
- Recherche par immatriculation, marque ou modèle.
- Filtrage par :
  - Agence.
  - Catégorie.
  - Statut.
- Vue synthétique de la flotte par agence.

---

## 7. Intégration avec les autres modules

- **Réservations** : vérification de disponibilité et blocage du véhicule.
- **Contrats** : association d’un véhicule à un contrat.
- **États des lieux** : rattachement au véhicule.
- **Reporting** : rentabilité par véhicule, taux d’occupation.
- **Journal d’audit** : traçabilité des modifications critiques.

---

## 8. Droits et permissions

- Super Admin : gestion globale multi-agences.
- Admin société : gestion globale flotte.
- Responsable d’agence : gestion des véhicules de son agence.
- Agent comptoir : consultation + modifications limitées selon droits.
- Comptabilité : consultation uniquement.

Le contrôle d’accès doit respecter le modèle RBAC du projet.