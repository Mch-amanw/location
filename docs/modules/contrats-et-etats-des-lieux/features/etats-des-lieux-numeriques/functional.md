# Fonctionnalité : États des lieux numériques

## 1. Objectif métier

La fonctionnalité « États des lieux numériques » permet de formaliser l’état du véhicule au moment du départ et du retour du client, afin de :

- Sécuriser juridiquement la mise à disposition du véhicule.
- Comparer l’état initial et final pour identifier d’éventuels dommages.
- Alimenter le workflow de gestion des sinistres.
- Servir de base à la restitution de caution et à la facturation complémentaire.

Elle s’inscrit dans le module « Contrats et états des lieux » et est obligatoire dans le cycle de vie d’une location.

---

## 2. Périmètre fonctionnel

### 2.1 Types d’états des lieux

Deux types sont gérés :

- État des lieux de départ (avant remise des clés).
- État des lieux de retour (à la restitution du véhicule).

Chaque état des lieux est obligatoirement rattaché à un contrat unique.

---

## 3. Contenu d’un état des lieux

Un état des lieux comprend :

- Informations générales :
  - Contrat associé.
  - Véhicule.
  - Agence.
  - Date et heure (horodatage automatique).
- Données techniques :
  - Kilométrage.
  - Niveau de carburant.
- Checklist structurée (intérieur / extérieur).
- Signalement de dommages (zone concernée, description).
- Ajout de photos horodatées.
- Commentaires libres.
- Signature électronique du client (et éventuellement de l’agent).

---

## 4. Règles de gestion

### 4.1 Règles générales

- Un seul état des lieux de départ et un seul état des lieux de retour sont autorisés par contrat.
- L’état des lieux de départ est obligatoire pour passer le contrat en statut « En cours ».
- L’état des lieux de retour est obligatoire pour passer le contrat en statut « Clôturé ».
- Une fois signé, un état des lieux est verrouillé et non modifiable.
- Toute tentative de modification après signature est interdite et tracée dans le journal d’audit.

### 4.2 Gestion des photos

- Les photos sont obligatoirement associées à un état des lieux.
- Chaque photo est horodatée.
- Les photos sont consultables depuis le contrat et comparables entre départ et retour.

### 4.3 Détection des écarts

- Le système permet la comparaison visuelle entre l’état des lieux de départ et celui de retour.
- En cas de dommage non présent au départ, l’agent peut initier un signalement vers le module Sinistres.

### 4.4 Signature électronique

- L’état des lieux doit être signé électroniquement par le client.
- La signature inclut horodatage et association au contrat.
- Après signature, un PDF est généré et archivé.

---

## 5. Droits et accès

- Super Admin / Admin société : accès global.
- Responsable d’agence : accès aux états des lieux de son agence.
- Agent comptoir : création, modification avant signature, signature.
- Comptabilité : consultation uniquement.
- Client : consultation et téléchargement depuis l’espace client (après signature).

Les accès sont restreints au périmètre agence selon les règles multi-agences.

---

## 6. Critères d’acceptation

- ✅ Impossible de démarrer une location sans état des lieux de départ signé.
- ✅ Impossible de clôturer une location sans état des lieux de retour signé.
- ✅ Les photos sont bien horodatées et rattachées au bon état des lieux.
- ✅ Le document PDF généré correspond aux données saisies et inclut les photos.
- ✅ Toute action (création, signature, clôture) est tracée dans le journal d’audit.
- ✅ Le document est accessible en téléchargement depuis le back-office et l’espace client.

---

## 7. Contraintes réglementaires

- Conservation des documents contractuels selon la législation marocaine.
- Conformité RGPD (données personnelles et droit d’accès).
- Documents disponibles en français ou en anglais selon la langue sélectionnée.