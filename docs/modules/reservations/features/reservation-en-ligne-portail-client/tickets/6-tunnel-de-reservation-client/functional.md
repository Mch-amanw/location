# Ticket : Tunnel de réservation client

## 1. Objectif du ticket

Implémenter le **parcours complet de réservation côté portail client** (frontend), depuis la recherche de disponibilité jusqu’à la redirection vers le paiement en ligne.

Ce tunnel constitue l’interface principale de conversion du portail client et doit être fluide, sécurisé, multilingue (FR/EN) et conforme aux règles métier définies dans le module Réservations.

---

## 2. Périmètre fonctionnel

Le tunnel couvre les étapes suivantes :

1. Recherche de disponibilité.
2. Sélection du véhicule/catégorie.
3. Personnalisation (options, assurance, kilométrage).
4. Récapitulatif tarifaire.
5. Identification / création de compte.
6. Création de la réservation (statut : En attente de paiement).
7. Redirection vers la passerelle de paiement.

La gestion du retour webhook et du passage au statut « Confirmée » est hors périmètre direct de l’UI mais doit être compatible avec le flux global.

---

## 3. Étapes du tunnel

### 3.1 Étape 1 – Recherche

Le client renseigne :
- Agence de départ.
- Agence de retour.
- Date et heure de départ.
- Date et heure de retour.
- Catégorie de véhicule.

Règles :
- La date de fin doit être strictement postérieure à la date de début.
- Les champs obligatoires doivent être validés côté frontend.
- L’appel à l’API `/api/v1/availability` retourne uniquement les véhicules/catégories réellement disponibles.

Affichage :
- Liste des catégories/véhicules disponibles.
- Prix estimatif (MAD, TTC).

---

### 3.2 Étape 2 – Sélection et personnalisation

Le client sélectionne :
- Une catégorie ou un véhicule.
- Les options disponibles.
- Le type de kilométrage (limité / illimité).
- L’assurance proposée.

Le système :
- Appelle l’API de simulation tarifaire.
- Affiche le détail :
  - Total HT.
  - TVA.
  - Total TTC.
  - Devise : MAD.

Le prix doit être recalculé dynamiquement à chaque modification impactante.

---

### 3.3 Étape 3 – Récapitulatif

Affichage synthétique :
- Agences départ/retour.
- Dates et horaires.
- Catégorie/véhicule.
- Options.
- Assurance.
- Détail tarifaire (HT / TVA / TTC).

Le client doit valider avant de passer à l’étape suivante.

---

### 3.4 Étape 4 – Identification

Le client doit :
- Se connecter à son compte existant, ou
- Créer un compte (formulaire simplifié selon module Clients).

Règles :
- Une réservation ne peut être créée que pour un client authentifié.
- La langue du tunnel suit la préférence utilisateur ou navigateur.

---

### 3.5 Étape 5 – Création et paiement

Lors de la validation finale :
- Appel à `POST /api/v1/reservations`.
- Création d’une réservation avec statut `EN_ATTENTE_PAIEMENT`.
- Création d’une intention de paiement.
- Redirection vers la passerelle bancaire.

En cas d’erreur API :
- Message explicite affiché.
- Aucun doublon de réservation ne doit être créé côté frontend.

---

## 4. Comportement UX

- Tunnel en étapes clairement identifiées (stepper visuel).
- Possibilité de revenir à l’étape précédente sans perdre les données saisies.
- Indication de chargement lors des appels API.
- Gestion des erreurs utilisateur (dates invalides, indisponibilité, session expirée).
- Responsive (desktop, tablette, mobile).

---

## 5. Contraintes métier

- Devise unique : MAD.
- Affichage HT / TVA / TTC obligatoire.
- Aucune confirmation sans paiement validé.
- Une réservation confirmée bloque la disponibilité (géré backend).
- Conformité RGPD pour les données personnelles.

---

## 6. Journalisation

Les actions suivantes doivent déclencher des logs côté backend (via API existantes) :
- Création de réservation.
- Tentative de paiement.

Le frontend ne gère pas directement l’audit mais doit garantir l’appel correct des endpoints.