# Module : Réservations

## 1. Rôle du module dans le projet

Le module **Réservations** gère l’ensemble du cycle de vie d’une réservation de véhicule, depuis la recherche de disponibilité jusqu’à l’annulation ou la transformation en contrat.

Il est central dans le système « location 3 » et interagit avec :
- Le module Véhicules (disponibilités, affectation).
- Le module Tarification (calcul du prix).
- Le module Paiement (paiement en ligne, empreinte bancaire).
- Le module Clients (données client, espace client).
- Le module Contrats (génération du contrat à la prise du véhicule).
- Le module Notifications (emails/SMS automatiques).
- Le module Journal d’audit (traçabilité des actions sensibles).

Il est accessible :
- Depuis le portail client (réservation en ligne).
- Depuis le back-office (création et gestion par les équipes internes).

---

## 2. Fonctionnalités principales

### 2.1 Recherche de disponibilité

Fonctionnalités :
- Sélection de :
  - Agence de départ.
  - Agence de retour.
  - Date et heure de départ.
  - Date et heure de retour.
  - Catégorie de véhicule.
- Vérification de disponibilité en temps réel.
- Affichage des véhicules ou catégories disponibles.

Règles de gestion :
- Une réservation ne peut être effectuée que si un véhicule est disponible sur la période demandée.
- Les transferts inter-agences doivent être pris en compte dans la disponibilité.
- Les périodes se chevauchant rendent le véhicule indisponible.

---

### 2.2 Création de réservation

Création possible :
- Par un client via le portail.
- Par un utilisateur interne via le back-office.

Données principales :
- Client (existant ou nouveau).
- Véhicule ou catégorie.
- Dates et heures.
- Agence de départ / retour.
- Options sélectionnées.
- Type de kilométrage (limité / illimité).
- Assurance sélectionnée.

Calcul automatique :
- Prix par jour.
- Ajustement selon saison.
- Ajout des options payantes.
- Application éventuelle d’une promotion.
- Calcul TVA (MAD).
- Total TTC.

Règles :
- Le tarif appliqué est figé au moment de la confirmation.
- Toute modification ultérieure peut entraîner un recalcul.

---

### 2.3 Statuts de réservation

La réservation possède un cycle de vie avec statuts :
- Brouillon (back-office uniquement).
- En attente de paiement.
- Confirmée.
- Annulée.
- Expirée (si paiement non effectué dans le délai).
- Transformée en contrat.

Règles :
- Une réservation confirmée bloque la disponibilité du véhicule.
- Une réservation annulée libère immédiatement la disponibilité.
- Toute modification de statut sensible est journalisée.

---

### 2.4 Paiement et confirmation

- Paiement en ligne par carte bancaire.
- Possibilité de paiement total ou partiel (selon paramétrage global).
- Gestion de l’empreinte bancaire pour la caution (au moment défini par le processus global).

Règles :
- La réservation est confirmée uniquement après validation du paiement requis.
- Les statuts de transaction sont synchronisés avec la passerelle de paiement.
- En cas d’échec de paiement, la réservation reste en attente ou expire.

---

### 2.5 Modification de réservation

Modifications possibles :
- Dates et horaires.
- Agences.
- Véhicule/catégorie (selon disponibilité).
- Options.

Règles :
- Toute modification déclenche une vérification de disponibilité.
- Recalcul automatique du tarif si modification impactante.
- Les différences tarifaires peuvent générer un complément ou un avoir.
- Les modifications sont interdites si la réservation est déjà transformée en contrat.
- Les actions sont tracées dans le journal d’audit.

---

### 2.6 Annulation

Règles d’annulation (MVP) :
- Annulation gratuite jusqu’à 24h avant l’heure de départ.
- Après ce délai, frais configurables.

Fonctionnalités :
- Annulation par le client (portail) selon conditions.
- Annulation par un utilisateur interne.
- Calcul automatique des éventuels frais.
- Déclenchement du processus de remboursement si applicable.

---

### 2.7 Espace client

Depuis l’espace client :
- Consultation des réservations en cours, passées, annulées.
- Téléchargement des documents liés.
- Annulation ou demande de modification selon statut.

---

### 2.8 Notifications

Envoi automatique d’email/SMS pour :
- Confirmation de réservation.
- Paiement validé.
- Rappel avant départ.
- Annulation.
- Modification.

Templates multilingues (FR/EN).

---

### 2.9 Journal d’audit

Traçabilité obligatoire pour :
- Création manuelle par un agent.
- Modification des dates.
- Changement de véhicule.
- Annulation.
- Validation manuelle.

Les logs incluent : utilisateur, date/heure, action, ancienne valeur, nouvelle valeur.

---

## 3. Contraintes spécifiques

- Devise : MAD.
- Multilingue : FR/EN.
- Conformité RGPD : gestion des données personnelles client.
- Compatible environnement multi-agences.
- Cohérence avec règles de facturation marocaine (via modules associés).