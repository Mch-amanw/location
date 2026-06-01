# Ticket : Espace client en ligne

## 1. Objectif

Mettre à disposition un **espace client sécurisé en ligne** permettant aux clients (particuliers et professionnels) d’accéder à leurs documents contractuels et financiers, ainsi que de gérer certaines informations de leur profil.

Cet espace est accessible via le portail web et constitue la partie authentifiée dédiée aux clients finaux.

---

## 2. Périmètre fonctionnel

### 2.1 Authentification et accès sécurisé

Le client doit pouvoir :
- Se connecter à son espace personnel via email + mot de passe.
- Accéder uniquement à ses propres données.
- Se déconnecter.

L’accès est strictement limité aux données associées à son `clientId`.

---

### 2.2 Tableau de bord client

Après connexion, le client accède à un tableau de bord synthétique affichant :
- Ses réservations à venir.
- Ses dernières réservations passées.
- Son statut (actif, suspendu, blacklisté si applicable — en lecture seule).
- Son indicateur de fidélité (si activé).

Objectif : offrir une vision rapide de son activité.

---

### 2.3 Consultation des réservations

Le client peut :
- Consulter la liste de ses réservations (passées et futures).
- Visualiser le détail d’une réservation :
  - Dates.
  - Agence de départ et de retour.
  - Véhicule (ou catégorie selon disponibilité).
  - Statut.

Aucune modification de réservation n’est incluse dans ce ticket (hors consultation).

---

### 2.4 Accès aux contrats

Pour chaque contrat lié à une réservation :
- Consultation des informations principales.
- Téléchargement du contrat au format PDF.

Les contrats affichés doivent être ceux générés par le module Contrats.

---

### 2.5 Accès aux factures

Le client peut :
- Consulter la liste de ses factures.
- Visualiser les informations principales (date, montant, statut).
- Télécharger les factures au format PDF.

Les factures respectent la réglementation marocaine (TVA incluse) et proviennent du module Facturation.

---

### 2.6 Gestion du profil

Le client peut :
- Consulter ses informations personnelles.
- Modifier certaines données :
  - Téléphone.
  - Adresse.
  - Préférence linguistique (FR/EN).
- Mettre à jour son mot de passe.

Les champs sensibles ou légaux (ex : type client, ICE, statut) ne sont pas modifiables par le client.

Toute modification doit être tracée.

---

### 2.7 Gestion des documents

Le client peut :
- Consulter la liste de ses documents (permis, pièce d’identité, autres).
- Télécharger ses documents.
- Ajouter un nouveau document (upload).

Les documents uploadés sont soumis au workflow de validation interne (statut : en attente, validé, refusé).

---

### 2.8 Contraintes métier

- Un client blacklisté peut accéder à son espace mais ne peut pas effectuer de nouvelles réservations (géré par module Réservations).
- Toutes les données affichées doivent correspondre strictement au client authentifié.
- Les téléchargements doivent être sécurisés et non accessibles publiquement.
- Les actions sensibles (modification profil, upload document) doivent être journalisées.

---

### 2.9 Internationalisation

- Interface disponible en français et anglais.
- La langue par défaut correspond à la préférence enregistrée du client.
- Les documents générés (contrats, factures) respectent la langue définie lors de leur génération.

---

## 3. Hors périmètre du ticket

- Modification ou annulation de réservation.
- Paiement en ligne depuis l’espace client (déjà couvert par module Paiement).
- Gestion avancée du programme de fidélité.

---

## 4. Impacts transverses

Ce ticket interagit avec :
- Module Clients (données profil, documents).
- Module Réservations.
- Module Contrats.
- Module Facturation.
- Module Paiement (lecture des statuts).
- Module Journal d’audit.

Il constitue la brique centrale du portail client authentifié.