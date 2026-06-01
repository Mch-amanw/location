## Ticket : Gestion empreinte bancaire et restitution

### 1. Objectif du ticket

Implémenter la gestion opérationnelle de l’empreinte bancaire (pré-autorisation) et de sa restitution manuelle dans le cadre d’un contrat de location.

Ce ticket couvre :
- La prise d’empreinte bancaire (pré-autorisation) associée à un contrat.
- La visualisation du statut de la caution.
- La restitution manuelle (libération) ou la capture (partielle ou totale) après restitution du véhicule.
- La traçabilité complète via le journal d’audit.

Il s’inscrit dans la fonctionnalité **Paiement en ligne et caution** du module **Facturation et paiements**.

---

### 2. Périmètre fonctionnel

#### 2.1 Prise d’empreinte bancaire

Au moment du départ du véhicule (création ou activation du contrat), un utilisateur habilité peut :

- Déclencher une pré-autorisation bancaire correspondant au montant de la caution.
- Visualiser le montant calculé (défini selon catégorie véhicule et/ou assurance configurée).
- Confirmer l’opération.

Résultat attendu :
- Une empreinte bancaire est créée auprès de la passerelle.
- Le statut initial est "autorisé".
- La caution est liée au contrat.
- Le montant est exprimé en MAD.

Le système doit afficher :
- Montant de la caution.
- Statut (autorisé, capturé partiellement, capturé totalement, libéré).
- Date d’autorisation.

---

#### 2.2 Restitution manuelle de la caution

Après retour du véhicule, un utilisateur habilité (agent comptoir ou responsable agence) peut déclencher manuellement une action sur la caution :

1. **Libération totale** :
   - Annulation de la pré-autorisation.
   - Aucun débit définitif.

2. **Capture partielle** :
   - Débit d’un montant inférieur à la caution autorisée.
   - Motif obligatoire (ex : réparation, nettoyage, retard).

3. **Capture totale** :
   - Débit de la totalité du montant autorisé.
   - Motif obligatoire.

Une fois une capture ou une libération effectuée :
- L’opération est définitive.
- Le statut de la caution est mis à jour.
- La date de clôture est enregistrée.

---

### 3. Règles de gestion

- Une caution ne peut être prise que si le contrat est actif.
- Une seule empreinte bancaire active est autorisée par contrat.
- Une caution doit être au statut "autorisé" pour permettre une capture ou une libération.
- Une caution ne peut être capturée qu’une seule fois (totale ou partielle).
- Une caution déjà libérée ou capturée ne peut plus être modifiée.
- Les montants sont obligatoirement en MAD.
- Toute action (prise d’empreinte, capture, libération) est journalisée.
- Seuls les rôles autorisés peuvent effectuer ces opérations (RBAC strict).

---

### 4. Notifications

Le client doit être notifié automatiquement :

- Lors de la prise d’empreinte (information).
- Lors d’une capture partielle ou totale.
- Lors d’une libération.

Les notifications sont envoyées via le module Notifications (email/SMS) en respectant la langue du client (FR/EN).

---

### 5. Visibilité et interface

Dans le back-office (fiche contrat) :

- Bloc "Caution" affichant :
  - Montant autorisé.
  - Statut.
  - Historique des actions.
  - Boutons d’action (capturer, libérer) conditionnés par le statut et les droits.

Dans l’espace client :

- Affichage du statut de la caution.
- Historique des opérations liées à la caution.

---

### 6. Journal d’audit

Les actions suivantes doivent être tracées :

- Prise d’empreinte bancaire.
- Capture partielle (avec montant et motif).
- Capture totale.
- Libération.

Chaque entrée d’audit doit inclure :
- Utilisateur.
- Agence.
- Date/heure.
- Identifiant du contrat.
- Montant concerné.