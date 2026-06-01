# Spécification Fonctionnelle – Ticket : Gestion des transferts inter-agences

## 1. Objectif du ticket

Ce ticket vise à implémenter la gestion complète des transferts inter-agences d’un véhicule, incluant :

- La création d’un transfert.
- Le suivi de son cycle de vie (planifié, en cours, terminé, annulé).
- La gestion des dates prévues et effectives.
- La mise à jour automatique du statut et de l’agence d’affectation du véhicule.
- La conservation d’un historique consultable.

Cette fonctionnalité s’inscrit dans le module **Gestion de la flotte** et contribue à garantir la cohérence des disponibilités en environnement multi-agences.

---

## 2. Création d’un transfert

Un utilisateur autorisé peut créer un transfert depuis la fiche véhicule ou via un écran dédié.

### 2.1 Informations obligatoires
- Véhicule concerné.
- Agence d’origine (préremplie depuis le véhicule).
- Agence de destination.
- Date de départ prévue.
- Date d’arrivée prévue.

### 2.2 Règles métier
- Le véhicule doit être au statut **Disponible**.
- Le véhicule doit être actif.
- Aucune réservation ou contrat actif ne doit exister sur la période de transfert.
- L’agence de destination doit être différente de l’agence d’origine.

À la création :
- Le transfert est au statut **Planifié**.
- Le véhicule reste au statut **Disponible** tant que le transfert n’est pas démarré.

---

## 3. Cycle de vie du transfert

### 3.1 Statuts du transfert
- Planifié
- En cours
- Terminé
- Annulé

### 3.2 Démarrage du transfert

Action disponible pour un utilisateur autorisé.

Effets :
- Le transfert passe au statut **En cours**.
- Le véhicule passe au statut **En transfert**.
- Le véhicule devient immédiatement indisponible à la réservation.

Une date de départ effective peut être enregistrée si différente de la date prévue.

---

### 3.3 Finalisation du transfert

Action déclenchée par l’agence de destination.

Effets :
- Enregistrement de la date d’arrivée effective.
- Le transfert passe au statut **Terminé**.
- L’agence d’affectation du véhicule est mise à jour vers l’agence de destination.
- Le véhicule passe au statut **Disponible**.

Un transfert ne peut pas être finalisé si :
- Le véhicule est associé à un contrat actif.
- Le transfert n’est pas au statut **En cours**.

---

### 3.4 Annulation d’un transfert

- Possible uniquement si le transfert est au statut **Planifié**.
- Le statut devient **Annulé**.
- Aucun impact sur l’agence ou le statut du véhicule.

Toute tentative d’annulation après démarrage est refusée.

---

## 4. Historique et traçabilité

Chaque transfert doit :
- Être visible dans l’historique du véhicule.
- Conserver les dates prévues et effectives.
- Conserver les statuts successifs.
- Conserver l’utilisateur ayant créé et modifié le transfert.

Les informations doivent être consultables en lecture seule après finalisation.

---

## 5. Impact sur la disponibilité

Pendant toute la durée du statut **En cours** :
- Le véhicule est exclu des résultats de disponibilité.
- Il ne peut pas être sélectionné pour une nouvelle réservation.

La finalisation du transfert réintègre automatiquement le véhicule dans le parc disponible de l’agence de destination.

---

## 6. Droits et permissions

Conformément au modèle RBAC du projet :

- Super Admin : gestion globale multi-agences.
- Admin société : gestion globale flotte.
- Responsable d’agence : création et gestion des transferts pour les véhicules relevant de son périmètre.
- Agent comptoir : création et consultation si autorisé.
- Comptabilité : consultation uniquement.

Les validations de périmètre agence doivent être appliquées côté backend.