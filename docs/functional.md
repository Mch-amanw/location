# Spécification Fonctionnelle – Projet "location 3"

## 1. Contexte et objectifs

Le projet "location 3" vise à mettre en place un outil web de gestion pour un réseau de location de véhicules multi-agences au Maroc.

Objectifs principaux :
- Centraliser la gestion des agences, véhicules et réservations.
- Proposer un portail client permettant la réservation en ligne.
- Automatiser les processus métier (contrats, facturation, cautions, sinistres, amendes).
- Fournir des indicateurs de pilotage (CA, taux d’occupation, rentabilité, etc.).
- Garantir la conformité à la réglementation marocaine (TVA, facturation) et au RGPD.

Le MVP couvre un réseau de 5 à 10 agences et 100 à 500 véhicules.

---

## 2. Périmètre métier

### 2.1 Types de véhicules
- Voitures
- SUV
- Utilitaires
- Véhicules premium

### 2.2 Structure multi-agences
- Gestion de 5 à 10 agences au lancement.
- Gestion de 100 à 500 véhicules.
- Transferts de véhicules entre agences avec suivi.

---

## 3. Utilisateurs cibles

### 3.1 Utilisateurs internes
- Super Admin
- Administrateur société
- Responsable d’agence
- Agent comptoir
- Comptabilité

Chaque rôle dispose de droits spécifiques selon son périmètre (global ou agence).

### 3.2 Clients finaux
- Particuliers ou professionnels réservant des véhicules.
- Accès à un espace client dédié.

---

## 4. Fonctionnalités principales (MVP)

### 4.1 Gestion des véhicules
- Fiches véhicules (catégorie, agence, statut, etc.).
- Suivi de disponibilité.
- Gestion des transferts inter-agences.

### 4.2 Gestion des réservations
- Réservation via back-office et portail client.
- Vérification de disponibilité en temps réel.
- Modification autorisée selon disponibilité.
- Politique d’annulation :
  - Gratuite jusqu’à 24h avant le départ.
  - Frais configurables ensuite.

### 4.3 Tarification
- Prix par jour.
- Tarifs saisonniers.
- Options payantes.
- Kilométrage limité ou illimité.
- Promotions.

### 4.4 Contrats de location
- Génération automatique de contrat.
- Gestion des assurances et franchises configurables.
- Signature électronique.
- Génération automatique de PDF.

### 4.5 États des lieux
- États des lieux départ et retour.
- Ajout de photos.
- Signature électronique.
- Génération de document PDF.

### 4.6 Facturation et TVA
- Facturation conforme aux règles marocaines.
- Gestion complète de la TVA.
- Génération et export des factures.

### 4.7 Paiements et cautions
- Paiement en ligne par carte bancaire.
- Empreinte bancaire pour la caution.
- Restitution manuelle après contrôle du véhicule.

### 4.8 Gestion des clients
- Fiche client complète.
- Historique des réservations.
- Gestion des documents (permis, pièces justificatives).
- Programme de fidélité.
- Espace client :
  - Historique des réservations.
  - Téléchargement contrats et factures.
  - Gestion du profil.

### 4.9 Gestion des sinistres et amendes
- Enregistrement des sinistres.
- Workflow complet des amendes :
  - Réception.
  - Affectation au client.
  - Refacturation.
  - Suivi du paiement.

### 4.10 Notifications
- Notifications automatiques par email et SMS :
  - Confirmation de réservation.
  - Rappels.
  - Retards.
  - Informations liées aux contrats et paiements.

### 4.11 Reporting et tableaux de bord
- Chiffre d’affaires.
- Taux d’occupation.
- Rentabilité par véhicule.
- Réservations.
- Retards.
- Sinistres.

### 4.12 Journal d’audit
- Traçabilité obligatoire des actions sensibles :
  - Modification de contrat.
  - Restitution de caution.
  - Modification tarifaire.

### 4.13 Mini CMS
- Administration des contenus :
  - CGU.
  - Assurances.
  - FAQ.
  - Pages informatives.

---

## 5. Hors périmètre MVP (Phase 2)

- Application mobile dédiée.
- Intégration logiciel comptable.
- Intégration télématique / GPS.
- Gestion des partenaires / apporteurs d’affaires.

---

## 6. Contraintes réglementaires et linguistiques

- Exploitation au Maroc.
- Gestion de la devise MAD.
- Multilingue : français et anglais.
- Conformité RGPD.
- Conformité à la législation marocaine en matière de facturation et TVA.


# Spécification Technique – Projet "location 3"

## 1. Architecture générale

### 1.1 Type d’application
- Application web responsive (MVP).
- Architecture centralisée multi-agences.

### 1.2 Organisation logique
- Frontend :
  - Portail client.
  - Back-office (rôles internes).
- Backend :
  - API centralisée.
  - Gestion des règles métier (réservations, tarification, facturation, cautions, amendes).
- Base de données centralisée multi-tenant (logique par agence).

---

## 2. Gestion des accès et sécurité

- Authentification sécurisée pour utilisateurs internes et clients.
- Gestion des rôles et permissions.
- Journal d’audit des actions sensibles.
- Sécurisation des paiements en ligne.
- Protection des données personnelles (RGPD).

---

## 3. Paiement en ligne

- Intégration d’une passerelle de paiement par carte bancaire (choix non précisé).
- Gestion :
  - Paiement total ou partiel.
  - Empreinte bancaire pour caution.
- Gestion des statuts de transaction.

---

## 4. Gestion documentaire

- Génération automatique de PDF (contrats, états des lieux, factures).
- Stockage sécurisé des documents.
- Upload et stockage de documents clients.

---

## 5. Notifications

- Intégration d’un service email.
- Intégration d’un service SMS.
- Système de templates multilingues.

---

## 6. Internationalisation

- Support multilingue (FR/EN).
- Gestion de la devise MAD.
- Paramétrage des taux de TVA.

---

## 7. Reporting

- Requêtes analytiques sur :
  - CA.
  - Occupation.
  - Rentabilité par véhicule.
  - Sinistres.
- Export possible des données (format non précisé).

---

## 8. Évolutivité (Phase 2)

- API extensible pour intégration comptable.
- API pour intégration télématique / GPS.
- Support futur application mobile.


# contextSummary

Le projet "location 3" consiste à développer un outil web responsive de gestion pour un réseau de location de véhicules multi-agences au Maroc (5 à 10 agences, 100 à 500 véhicules). Il comprend un back-office complet et un portail client avec réservation en ligne, paiement par carte bancaire et gestion des cautions par empreinte. Le MVP inclut la gestion des véhicules, disponibilités, tarification avancée (saisons, options, promotions), contrats, facturation conforme aux règles marocaines (TVA), états des lieux avec photos et signature électronique, gestion des sinistres et amendes, reporting, notifications email/SMS, journal d’audit et mini CMS. L’application est multilingue (FR/EN), en devise MAD, conforme RGPD. Les intégrations comptables, télématiques et l’application mobile sont prévues en phase 2.