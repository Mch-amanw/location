# Fonctionnalité : Paiement en ligne et caution

## 1. Objectif métier

Permettre l’encaissement sécurisé des paiements par carte bancaire (CB) et la gestion des cautions via empreinte bancaire (pré-autorisation) dans le cadre des réservations et contrats de location.

Cette fonctionnalité vise à :
- Sécuriser les transactions financières liées aux réservations.
- Réduire les risques d’impayés.
- Garantir une gestion maîtrisée des cautions en cas de sinistre, dommage ou frais supplémentaires.
- Assurer la conformité aux règles légales marocaines et aux exigences de sécurité (RGPD, sécurité des paiements).

Elle s’inscrit dans le module **Facturation et paiements** et interagit avec les modules Réservations, Contrats, Clients, Sinistres/Amendes et Journal d’audit.

---

## 2. Périmètre fonctionnel

### 2.1 Paiement en ligne par carte bancaire

Le système permet :

- Le paiement en ligne lors d’une réservation via le portail client.
- Le paiement depuis le back-office par un agent (ex : encaissement au comptoir).
- Le paiement total ou partiel d’une réservation ou d’une facture.

Chaque paiement :
- Est rattaché à une réservation et/ou une facture.
- Est exprimé en MAD.
- Dispose d’un statut (en attente, autorisé, confirmé, échoué, remboursé).

Un email/SMS de confirmation est envoyé automatiquement au client après confirmation du paiement.

---

### 2.2 Gestion des statuts de paiement

Le système doit gérer les statuts suivants :

- **En attente** : transaction initiée, en attente de confirmation.
- **Autorisé** : montant autorisé par la banque.
- **Confirmé / Payé** : paiement validé.
- **Échoué** : refus ou erreur.
- **Remboursé** : remboursement total ou partiel effectué.

Les statuts sont mis à jour automatiquement via notification de la passerelle de paiement.

Une facture ne peut être marquée "payée" que si les transactions associées sont confirmées.

---

### 2.3 Empreinte bancaire (caution)

#### 2.3.1 Prise d’empreinte

Le système permet la prise d’une empreinte bancaire (pré-autorisation) au moment du départ du véhicule.

Caractéristiques :
- Le montant de la caution est défini selon la catégorie du véhicule et/ou l’assurance choisie.
- L’empreinte ne débite pas immédiatement le client.
- Le statut initial est "autorisé".

---

#### 2.3.2 Gestion après restitution

Après retour du véhicule, un utilisateur habilité (agent comptoir, responsable agence) peut :

- Libérer intégralement la caution.
- Capturer partiellement la caution (ex : frais de réparation, nettoyage).
- Capturer totalement la caution (sinistre grave).

Toute action :
- Est déclenchée manuellement.
- Est soumise aux droits utilisateurs.
- Est tracée dans le journal d’audit.

Le client est notifié en cas de capture totale ou partielle.

---

## 3. Règles de gestion

- Tous les montants sont exprimés en MAD.
- Aucune donnée carte bancaire n’est stockée localement (tokenisation obligatoire).
- Une caution ne peut être capturée qu’après la prise d’empreinte valide.
- Une caution ne peut être capturée qu’une seule fois (totale ou partielle).
- Toute opération sensible (capture, libération, remboursement) nécessite un rôle autorisé et est journalisée.
- Les statuts locaux doivent être synchronisés avec ceux de la passerelle de paiement.

---

## 4. Critères d’acceptation

- ✅ Un client peut payer sa réservation en ligne par CB et recevoir une confirmation.
- ✅ Un agent peut enregistrer un paiement depuis le back-office.
- ✅ Les statuts de paiement sont automatiquement mis à jour via webhook.
- ✅ Une empreinte bancaire peut être prise lors du départ.
- ✅ Une caution peut être libérée ou capturée (partiellement ou totalement) après retour.
- ✅ Toute action sur paiement ou caution est visible dans le journal d’audit.
- ✅ Une facture ne passe au statut "payée" que si les paiements confirmés couvrent le montant TTC.

---

## 5. Droits et rôles

- **SuperAdmin / Admin Société** : supervision globale.
- **Responsable agence** : gestion paiements et cautions de son agence.
- **Agent comptoir** : encaissements, prise et restitution caution.
- **Comptabilité** : suivi paiements et remboursements.
- **Client** : paiement en ligne, consultation statut paiement.

L’accès est contrôlé par RBAC strict.