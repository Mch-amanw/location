# Ticket : Workflow complet amendes

## 1. Objectif du ticket

Implémenter le **workflow complet de gestion des amendes** dans le module *Clients, sinistres et amendes*, couvrant :

- La réception d’une amende.
- L’identification du contrat concerné.
- L’affectation au client.
- La refacturation.
- Le suivi du paiement.
- La clôture du dossier.

Ce ticket couvre la mise en œuvre opérationnelle du cycle de vie des amendes conformément à la fonctionnalité parente « Workflow sinistres et amendes ».

---

## 2. Périmètre fonctionnel

### 2.1 Création / Réception d’une amende

Un utilisateur interne autorisé (agent comptoir, responsable agence, comptabilité, admin) peut créer une amende.

Informations obligatoires :
- Véhicule concerné.
- Date d’infraction.
- Type d’infraction.
- Autorité émettrice.
- Montant.
- Référence officielle (si disponible).

À la création :
- Le statut initial est **Reçue**.
- L’amende est rattachée à une agence (via le véhicule).
- L’action est journalisée.

---

### 2.2 Identification du contrat

Le système permet d’identifier le contrat couvrant la date d’infraction.

Comportement attendu :
- Recherche automatique du contrat actif pour le véhicule à la date d’infraction.
- Si un contrat unique est trouvé → passage au statut **Contrat identifié**.
- Si aucun contrat n’est trouvé → l’amende reste en statut **Reçue**.
- En cas d’ambiguïté (plusieurs contrats valides), le système bloque la transition et nécessite une intervention manuelle.

L’identification est traçable dans le journal d’audit.

---

### 2.3 Affectation au client

Une fois le contrat identifié :
- L’amende peut être affectée au client lié au contrat.
- Le statut devient **Affectée au client**.

Règles :
- L’amende doit obligatoirement être liée à un contrat valide avant affectation.
- Le client est automatiquement dérivé du contrat.
- Toute modification manuelle est journalisée.

---

### 2.4 Refacturation

Une amende affectée peut être refacturée au client.

Comportement :
- Génération d’une facture complémentaire liée au contrat et au client.
- Le montant refacturé correspond au montant de l’amende (sauf ajustement validé par rôle autorisé).
- Application de la TVA conformément aux règles du module Facturation.

Après génération :
- Le statut passe à **Refacturée**.
- L’identifiant de la facture est associé à l’amende.
- L’action est journalisée.

---

### 2.5 Suivi du paiement

Le système doit permettre :
- La consultation du statut de paiement de la facture associée.
- La mise à jour automatique du statut de l’amende lorsque la facture est payée.

Transitions :
- Si la facture est payée → statut **Payée**.
- Une amende payée peut ensuite être **Clôturée**.

---

### 2.6 Contestation

Une amende peut être mise en statut **Contestée**.

Effets :
- Blocage de la refacturation tant que le statut est contesté.
- Possibilité de revenir à un statut précédent selon droits autorisés.
- Traçabilité obligatoire.

---

### 2.7 Clôture

Une amende peut être clôturée lorsque :
- Elle est payée, ou
- Elle est classée sans suite (décision administrative).

Effets :
- Statut final **Clôturée**.
- Modification bloquée sauf droits exceptionnels.

---

## 3. Machine à états

Statuts autorisés :

1. Reçue
2. Contrat identifié
3. Affectée au client
4. Refacturée
5. Payée
6. Contestée
7. Clôturée

Les transitions doivent respecter les règles définies dans la fonctionnalité parente (impossibilité de refacturer sans contrat identifié, etc.).

---

## 4. Droits et périmètre agence

- Les utilisateurs ne peuvent accéder qu’aux amendes de leur agence (sauf rôles globaux).
- La refacturation est restreinte aux rôles autorisés (comptabilité, admin société, superAdmin).
- Toute modification de statut ou de montant est auditée.

---

## 5. Intégration avec reporting

Les amendes doivent alimenter :
- Le nombre d’amendes par agence.
- Les montants refacturés.
- Les amendes en attente (non refacturées / non payées).
- Les délais moyens de traitement.

Aucune logique spécifique de reporting n’est implémentée ici, mais les données doivent être exploitables par le module Reporting.