# Ticket : Upload photos et signature état des lieux

## 1. Objectif

Ce ticket couvre l’implémentation complète de :

- L’ajout et la gestion des photos dans un état des lieux (départ ou retour).
- La signature électronique de l’état des lieux par le client (et éventuellement l’agent).

Il s’inscrit dans la fonctionnalité « États des lieux numériques » du module « Contrats et états des lieux ».

---

## 2. Périmètre fonctionnel

### 2.1 Ajout de photos

L’utilisateur (Agent comptoir ou rôle autorisé) peut :

- Ajouter une ou plusieurs photos à un état des lieux en statut "draft".
- Visualiser la liste des photos déjà ajoutées.
- Supprimer une photo tant que l’état des lieux n’est pas signé.

Chaque photo :
- Est associée exclusivement à un état des lieux (départ ou retour).
- Est horodatée automatiquement par le système.
- Est visible dans le détail de l’état des lieux.
- Est intégrée au PDF final après signature.

Les photos sont consultables :
- Depuis le back-office.
- Depuis l’espace client après signature.

Aucune modification (ajout/suppression) n’est autorisée après signature.

---

### 2.2 Signature électronique

L’état des lieux doit être signé électroniquement par le client.

Fonctionnement :

- La signature est réalisée via une interface tactile (tablette, mobile ou souris).
- La signature est associée à :
  - L’identité du client liée au contrat.
  - La date et l’heure serveur.
- Après signature :
  - L’état des lieux passe en statut "signed".
  - Il devient non modifiable.
  - Un PDF est généré automatiquement.

Optionnellement, l’agent peut également signer si le processus métier l’exige.

---

## 3. Règles de gestion

- Les photos ne peuvent être ajoutées que si l’état des lieux est en statut "draft".
- La signature est impossible si l’état des lieux est déjà signé.
- Une fois signé :
  - Les champs (kilométrage, carburant, checklist, commentaires) sont verrouillés.
  - Les photos sont verrouillées.
- La signature déclenche automatiquement :
  - La génération du PDF.
  - L’écriture dans le journal d’audit.
  - L’émission de l’événement métier "InspectionCompleted".

Si l’état des lieux est de type "departure" et signé :
- Il peut permettre au contrat de passer en statut "En cours" (via ContractService).

Si l’état des lieux est de type "return" et signé :
- Il peut permettre au contrat de passer en statut "Clôturé" (via ContractService).

---

## 4. Droits et accès

- Super Admin / Admin société : gestion complète.
- Responsable d’agence : gestion pour son agence.
- Agent comptoir :
  - Ajout/suppression photos avant signature.
  - Signature.
- Comptabilité : consultation uniquement.
- Client : consultation et téléchargement du PDF signé depuis l’espace client.

Les permissions sont restreintes par agence (multi-agences).

---

## 5. Contraintes

- Horodatage serveur obligatoire pour les photos et signatures.
- Conformité RGPD (données personnelles et conservation des preuves de signature).
- Documents disponibles en FR/EN selon la langue du contrat.
- Intégrité documentaire garantie après signature (document figé).