## Module : Facturation et paiements

### 1. Rôle du module dans le projet

Le module **Facturation et paiements** couvre l’ensemble des processus financiers liés aux réservations et contrats de location :
- Génération des factures conformes à la législation marocaine (TVA, numérotation légale, devise MAD).
- Encaissement des paiements en ligne par carte bancaire.
- Gestion des cautions via empreinte bancaire (pré-autorisation).
- Suivi des statuts de paiement et des régularisations.

Il s’intègre étroitement avec les modules Réservations, Contrats, Clients, Sinistres/Amendes, Reporting et Journal d’audit.

---

### 2. Gestion de la facturation

#### 2.1 Génération des factures

Le système doit permettre :
- La génération automatique de factures à partir d’un contrat de location.
- La prise en compte des éléments suivants :
  - Prix de location (par jour, selon saison).
  - Options payantes.
  - Assurances complémentaires.
  - Frais additionnels (retard, dépassement kilométrique, pénalités).
  - Sinistres et amendes refacturées.
- Le calcul automatique de la TVA selon le paramétrage en vigueur.
- L’affichage des montants en devise MAD.

Chaque facture comporte :
- Un numéro unique séquentiel conforme aux obligations légales.
- Les informations société (raison sociale, identifiants fiscaux).
- Les informations client.
- Le détail des lignes facturées.
- Le montant HT, TVA, TTC.
- Le statut (brouillon, émise, payée, partiellement payée, annulée).

#### 2.2 Avoirs et annulations

Le module permet :
- L’émission d’avoirs en cas d’annulation ou de régularisation.
- L’annulation d’une facture selon les règles comptables définies.
- La traçabilité complète de toute modification (via journal d’audit).

---

### 3. Paiement en ligne

#### 3.1 Paiement par carte bancaire

Le système permet :
- Le paiement en ligne lors de la réservation via le portail client.
- Le paiement depuis le back-office par un agent.
- Le paiement total ou partiel selon les règles définies.

Chaque paiement est associé à :
- Une réservation et/ou une facture.
- Un statut de transaction (en attente, autorisé, confirmé, échoué, annulé).

Une confirmation automatique est envoyée au client (email/SMS).

#### 3.2 Gestion des statuts

Le module gère les statuts suivants :
- En attente de paiement.
- Payé partiellement.
- Payé intégralement.
- Échoué.
- Remboursé (si applicable selon passerelle).

Les statuts sont synchronisés avec la passerelle de paiement via mécanisme de notification (webhook).

---

### 4. Gestion des cautions (empreinte bancaire)

#### 4.1 Pré-autorisation

Le système permet :
- La prise d’une empreinte bancaire (pré-autorisation) au moment du départ.
- L’enregistrement du montant de la caution selon la catégorie de véhicule ou assurance.

#### 4.2 Restitution

Après retour du véhicule :
- La caution peut être :
  - Restituée intégralement.
  - Partiellement capturée (en cas de frais).
  - Totalement capturée (sinistre grave).
- La restitution est déclenchée manuellement par un utilisateur habilité.
- Toute action sur la caution est enregistrée dans le journal d’audit.

---

### 5. Règles de gestion

- Toute facture émise est figée (non modifiable directement) ; les corrections passent par un avoir.
- La numérotation des factures est séquentielle et non réutilisable.
- Les montants sont exprimés en MAD.
- Les taux de TVA sont paramétrables.
- Une facture ne peut être marquée "payée" que si les transactions correspondantes sont confirmées.
- Les opérations sensibles (annulation facture, capture caution, remboursement) sont soumises à contrôle de rôle et journalisées.

---

### 6. Droits et rôles

- SuperAdmin / Admin Société : accès complet (paramétrage TVA, supervision globale).
- Responsable agence : gestion des factures et cautions de son agence.
- Agent comptoir : création factures liées aux contrats, encaissements.
- Comptabilité : accès aux exports, suivi des paiements, émission d’avoirs.
- Client : consultation et téléchargement de ses factures depuis l’espace client.

---

### 7. Exports et reporting

Le module alimente le reporting avec :
- Chiffre d’affaires par période.
- CA par agence.
- CA par véhicule.
- Taux de paiement.

Export possible des factures et paiements (format non précisé).