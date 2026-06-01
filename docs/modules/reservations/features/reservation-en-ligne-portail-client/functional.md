# Fonctionnalité : Réservation en ligne (portail client)

## 1. Objectif métier

Permettre à un client (particulier ou professionnel) d’effectuer une réservation de véhicule en ligne depuis le portail client, avec vérification de disponibilité en temps réel, calcul automatique du tarif et confirmation après paiement.

Cette fonctionnalité vise à :
- Générer des réservations 24/7 sans intervention humaine.
- Réduire la charge des agents en agence.
- Garantir la cohérence tarifaire et la disponibilité réelle des véhicules.

---

## 2. Périmètre

Inclus dans le MVP :
- Recherche de disponibilité par agence, dates, catégorie.
- Affichage des catégories/véhicules disponibles.
- Sélection d’options et d’assurances.
- Calcul automatique du prix (MAD, TVA incluse).
- Création de réservation.
- Paiement en ligne (total ou partiel selon paramétrage global).
- Passage au statut « Confirmée » après validation du paiement.
- Notifications automatiques (email/SMS).

Hors périmètre :
- Gestion des partenaires/apporteurs.
- Application mobile native (phase 2).

---

## 3. Parcours utilisateur

### 3.1 Recherche

Le client renseigne :
- Agence de départ.
- Agence de retour.
- Date et heure de départ.
- Date et heure de retour.
- Catégorie de véhicule.

Le système :
- Vérifie la cohérence des dates (fin > début).
- Interroge les disponibilités en temps réel.
- Affiche les catégories ou véhicules disponibles avec prix estimatif.

Règles de gestion :
- Une réservation n’est possible que si un véhicule est disponible sur toute la période.
- Les chevauchements avec réservations confirmées ou contrats actifs rendent le véhicule indisponible.
- Les transferts inter-agences sont pris en compte dans la logique globale de disponibilité.

---

### 3.2 Sélection et personnalisation

Le client sélectionne :
- Catégorie ou véhicule proposé.
- Options payantes (ex : GPS, siège bébé, etc.).
- Type de kilométrage (limité / illimité).
- Assurance disponible.

Le système :
- Calcule automatiquement le tarif :
  - Prix journalier.
  - Ajustement saisonnier.
  - Options.
  - Promotions éventuelles.
  - TVA.
- Affiche le total HT, TVA, TTC en MAD.

---

### 3.3 Identification client

Le client :
- Se connecte à son compte existant, ou
- Crée un compte (informations minimales requises selon module Clients).

Règles :
- Une réservation en ligne doit être rattachée à un compte client.
- Les données personnelles sont traitées conformément au RGPD.

---

### 3.4 Paiement et confirmation

Le client procède au paiement en ligne par carte bancaire.

Règles :
- La réservation passe au statut « En attente de paiement » lors de la création.
- Elle passe au statut « Confirmée » uniquement après validation du paiement requis par la passerelle.
- En cas d’échec, elle reste en attente ou expire après un délai configuré.
- Le tarif est figé au moment de la confirmation (pricing snapshot).

---

### 3.5 Notifications

En cas de confirmation :
- Email de confirmation.
- SMS (si activé).

Les notifications incluent :
- Référence de réservation.
- Détails principaux (agences, dates, catégorie).
- Montant payé.

Templates multilingues (FR/EN) selon la langue du client.

---

## 4. Règles de gestion clés

- Devise unique : MAD.
- TVA conforme aux règles marocaines.
- Annulation gratuite jusqu’à 24h avant le départ (gérée par fonctionnalité d’annulation du module).
- Toute action critique (création, confirmation automatique suite paiement) est tracée dans le journal d’audit.
- Une réservation confirmée bloque la disponibilité du véhicule.

---

## 5. Critères d’acceptation

1. Un client peut rechercher une disponibilité et obtenir uniquement des véhicules réellement disponibles.
2. Le prix affiché correspond exactement au montant facturé après paiement.
3. Une réservation n’est confirmée qu’après validation effective du paiement par la passerelle.
4. En cas d’échec de paiement, aucune réservation confirmée ne doit être créée.
5. La réservation confirmée apparaît dans l’espace client avec son statut correct.
6. Une notification email est envoyée automatiquement après confirmation.
7. Les montants sont affichés en MAD et correctement ventilés (HT, TVA, TTC).