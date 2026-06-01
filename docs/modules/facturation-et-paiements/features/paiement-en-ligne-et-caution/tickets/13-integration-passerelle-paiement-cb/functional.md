## Ticket : Intégration passerelle paiement CB

### 1. Objectif du ticket

Mettre en place l’intégration technique et fonctionnelle d’une passerelle de paiement par carte bancaire (CB) compatible avec le Maroc (ex : CMI ou Stripe si autorisé), afin de permettre :

- Le paiement en ligne des réservations et factures.
- La gestion des statuts de transaction.
- La synchronisation automatique des paiements via webhooks sécurisés.
- La conformité aux exigences de sécurité (tokenisation, HTTPS, RGPD).

Ce ticket constitue le socle technique indispensable pour la fonctionnalité « Paiement en ligne et caution » du module **Facturation et paiements**.

---

### 2. Périmètre fonctionnel couvert

Ce ticket couvre :

- La connexion à la passerelle de paiement choisie.
- La création d’intentions de paiement (payment intent).
- La redirection ou intégration du composant sécurisé du provider.
- La réception et le traitement des notifications serveur-à-serveur (webhooks).
- La mise à jour des statuts de paiement dans le système.

Il ne couvre pas :

- La logique métier complète de facturation.
- La gestion des cautions (traitée dans un ticket distinct, bien que s’appuyant sur la même passerelle).
- Les règles fines d’affichage frontend (hors intégration minimale nécessaire).

---

### 3. Flux fonctionnel cible

#### 3.1 Initialisation d’un paiement

1. L’utilisateur (client ou agent) déclenche un paiement.
2. Le système crée une intention de paiement via la passerelle.
3. L’utilisateur est redirigé vers une page sécurisée du provider ou utilise un composant sécurisé intégré.
4. Le client saisit ses informations bancaires directement auprès du provider.

Aucune donnée carte bancaire ne transite ni n’est stockée sur les serveurs de l’application.

---

#### 3.2 Retour utilisateur

Après tentative de paiement :

- L’utilisateur est redirigé vers une page de succès ou d’échec.
- Le statut affiché reste provisoire tant que la confirmation définitive n’est pas reçue via webhook.

---

#### 3.3 Confirmation via webhook

La passerelle envoie une notification serveur-à-serveur contenant :

- L’identifiant de transaction provider.
- Le statut final (autorisé, confirmé, échoué, annulé).
- Le montant et la devise.

Le système :

- Vérifie l’authenticité de la notification.
- Met à jour le statut du paiement correspondant.
- Déclenche, si applicable, la mise à jour du statut de la facture.
- Journalise l’événement.

---

### 4. Statuts pris en charge

Les statuts minimum à gérer côté système sont :

- en_attente
- autorise
- confirme (payé)
- echoue
- annule
- rembourse (si fourni par le provider)

La correspondance entre statuts provider et statuts internes doit être formalisée.

---

### 5. Contraintes fonctionnelles

- Tous les montants sont exprimés en MAD.
- La devise doit être validée côté backend.
- La mise à jour du statut d’une facture dépend exclusivement des paiements confirmés.
- Le traitement des webhooks doit être idempotent.
- Toute erreur de traitement doit être journalisée.

---

### 6. Journalisation et traçabilité

Les événements suivants doivent être tracés :

- Création d’une intention de paiement.
- Réception d’un webhook.
- Changement de statut d’un paiement.
- Échec de validation signature webhook.

Ces événements alimentent les logs techniques et, si applicable, le journal d’audit pour les opérations sensibles.

---

### 7. Impacts sur les modules existants

- Module Réservations : déclenchement des paiements.
- Module Facturation : mise à jour du statut des factures.
- Module Notifications : envoi des emails/SMS après confirmation.
- Module Journal d’audit : traçabilité des opérations sensibles.

Cette intégration doit rester générique afin de permettre un changement futur de provider avec un impact minimal sur la logique métier.