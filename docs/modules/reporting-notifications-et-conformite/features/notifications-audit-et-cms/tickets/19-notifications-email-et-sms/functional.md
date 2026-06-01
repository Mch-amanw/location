# Spécification Fonctionnelle – Ticket « Notifications email et SMS »

## 1. Objectif du ticket

Mettre en place le système de notifications automatiques multilingues (FR/EN) par email et SMS pour les événements clés du cycle de location, conformément à la fonctionnalité « Notifications, audit et CMS ».

Ce ticket couvre :
- L’envoi automatique des notifications.
- La gestion des templates multilingues.
- La journalisation des envois.
- La configuration d’activation/désactivation par type d’événement.

---

## 2. Périmètre fonctionnel

### 2.1 Événements concernés (MVP)

Les notifications doivent être envoyées automatiquement pour les événements suivants :

- Confirmation de réservation.
- Rappel avant départ.
- Confirmation de paiement.
- Informations liées au contrat.
- Notification de retard.

Les événements sont déclenchés par les actions métier correspondantes (réservation confirmée, paiement validé, contrat généré, etc.).

---

## 3. Canaux de communication

### 3.1 Email

- Envoi d’emails transactionnels.
- Utilisation d’un sujet et d’un contenu dynamique.
- Envoi uniquement si l’adresse email du client est disponible.

### 3.2 SMS

- Envoi de SMS via fournisseur compatible Maroc.
- Envoi uniquement si un numéro mobile valide est présent.
- Possibilité d’activer/désactiver le SMS indépendamment de l’email selon le type d’événement.

---

## 4. Multilingue

- Les notifications doivent être disponibles en français (FR) et en anglais (EN).
- La langue utilisée dépend :
  - De la langue du client si renseignée.
  - À défaut, de la langue par défaut du système.
- Chaque type d’événement dispose d’un template par langue.

---

## 5. Templates de notifications

Chaque notification repose sur un template configurable comprenant :

- Type d’événement.
- Canal (email / SMS).
- Langue (FR / EN).
- Sujet (email uniquement).
- Contenu avec variables dynamiques.
- Statut actif / inactif.

### 5.1 Variables dynamiques

Les templates peuvent inclure des variables telles que :
- Nom du client.
- Dates de location.
- Agence.
- Véhicule.
- Montant.
- Référence de réservation ou contrat.

Les variables sont injectées automatiquement au moment de l’envoi.

---

## 6. Règles de gestion

- Une notification est envoyée uniquement si :
  - Le template correspondant est actif.
  - Les coordonnées nécessaires sont disponibles.
- Chaque envoi (réussi ou échoué) est enregistré dans un historique consultable en back-office.
- En cas d’échec d’envoi, le statut et le message d’erreur sont enregistrés.
- Les notifications sont liées à une entité métier (réservation, contrat, paiement, etc.).
- Les données personnelles utilisées dans les notifications sont limitées aux finalités prévues (conformité RGPD).

---

## 7. Historique des notifications

Le back-office doit permettre :
- La consultation de l’historique des notifications.
- Le filtrage par :
  - Type d’événement.
  - Canal.
  - Statut (envoyé, échec).
  - Période.
  - Entité liée.

L’accès à cet historique est soumis au RBAC :
- Super Admin et Admin Société : accès global.
- Responsable d’agence : accès limité aux notifications liées à son agence.

---

## 8. Conformité

- Respect du RGPD :
  - Utilisation des données uniquement dans le cadre des opérations de location.
  - Journalisation des envois.
- Aucun envoi massif marketing (hors périmètre MVP).
- Les notifications sont strictement transactionnelles.

---

## 9. Hors périmètre du ticket

- Gestion avancée des préférences marketing.
- Campagnes marketing.
- Automatisations complexes conditionnelles (scénarios multi-étapes).