## Module : Contrats et états des lieux

### 1. Rôle du module
Le module « Contrats et états des lieux » gère l’ensemble du cycle documentaire lié à une location : génération du contrat de location, réalisation des états des lieux (départ et retour), signature électronique et production des documents PDF officiels.

Il s’inscrit dans le processus métier entre la réservation validée et la clôture de la location, avec impact sur la facturation, la caution et la gestion des sinistres.

---

### 2. Génération des contrats de location

#### 2.1 Création du contrat
Le contrat est généré automatiquement à partir :
- De la réservation validée.
- Des données client.
- Du véhicule attribué.
- Des conditions tarifaires (prix, options, promotions).
- Des assurances et franchises configurées.

Le contrat inclut notamment :
- Identité du client.
- Détails du véhicule.
- Agence de départ et de retour.
- Dates et heures de location.
- Conditions tarifaires détaillées.
- Montant estimatif total.
- Conditions générales (CGU, assurances).

#### 2.2 Statuts du contrat
Le contrat dispose de statuts :
- Brouillon.
- En attente de signature.
- Signé.
- En cours.
- Clôturé.
- Annulé.

Le passage en statut « En cours » est conditionné à :
- Signature du contrat.
- Paiement ou pré-autorisation (selon configuration).

#### 2.3 Règles de gestion
- Un contrat est unique par location.
- Toute modification majeure (dates, véhicule, tarif) entraîne une mise à jour versionnée du contrat.
- Les modifications après signature sont tracées dans le journal d’audit.
- Le contrat final est figé sous forme de PDF non modifiable.

---

### 3. États des lieux numériques

#### 3.1 Types d’états des lieux
- État des lieux de départ.
- État des lieux de retour.

Chaque état des lieux est lié à un contrat.

#### 3.2 Contenu
Un état des lieux comprend :
- Kilométrage.
- Niveau de carburant.
- Checklist de contrôle (intérieur/extérieur).
- Signalement des dommages.
- Ajout de photos horodatées.
- Commentaires libres.

#### 3.3 Règles de gestion
- L’état des lieux de départ est obligatoire avant mise à disposition du véhicule.
- L’état des lieux de retour est obligatoire pour clôturer la location.
- Les photos sont associées au contrat et horodatées.
- Toute différence entre départ et retour peut alimenter le workflow sinistres.

---

### 4. Signature électronique

- Signature électronique du client sur :
  - Contrat.
  - État des lieux de départ.
  - État des lieux de retour.
- Signature possible sur tablette ou interface tactile.
- Horodatage et association à l’identité du signataire.
- Une fois signé, le document est verrouillé.

---

### 5. Génération et gestion des PDF

#### 5.1 Documents générés
- Contrat de location.
- État des lieux départ.
- État des lieux retour.

#### 5.2 Règles
- Génération automatique en PDF côté serveur.
- Numérotation cohérente avec le contrat.
- Téléchargement disponible :
  - Back-office.
  - Espace client.
- Archivage sécurisé.

---

### 6. Intégrations métier

- Interaction avec le module Réservations (source des données).
- Interaction avec le module Paiement (validation avant démarrage).
- Interaction avec Facturation (base de facturation finale).
- Interaction avec Sinistres (dommages détectés au retour).
- Journal d’audit pour toute action sensible (signature, modification, clôture).

---

### 7. Contraintes réglementaires

- Conformité légale marocaine (mentions obligatoires sur contrat).
- Conservation des documents contractuels.
- Respect RGPD (accès, suppression selon règles légales de conservation).
- Multilingue FR/EN selon langue choisie par le client.