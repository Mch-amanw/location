# Ticket : Fiche client et gestion documents

## 1. Objectif du ticket

Mettre en place la **fiche client détaillée en back-office**, incluant :
- L’affichage complet des informations du client (particulier ou professionnel).
- La gestion des documents (upload permis et autres justificatifs).
- L’affichage de l’historique des réservations liées au client.

Ce ticket couvre principalement l’interface et les interactions liées à la consultation et à la mise à jour de la fiche client, en cohérence avec la fonctionnalité "Gestion complète des clients".

---

## 2. Périmètre fonctionnel

### 2.1 Accès à la fiche client

La fiche client est accessible depuis :
- La liste des clients (back-office).
- Un lien direct depuis une réservation ou un contrat.

L’accès est restreint selon les rôles (RBAC) et le périmètre agence.

---

## 3. Contenu de la fiche client

La fiche client est organisée en sections.

### 3.1 Informations générales

Affichage des informations principales :
- Type : particulier / professionnel.
- Nom / prénom ou raison sociale.
- Email.
- Téléphone.
- Adresse.
- ICE (si professionnel).
- Langue préférée (FR/EN).
- Statut (actif, suspendu, blacklisté).
- Date de création.

Fonctionnalités :
- Modification des informations (selon droits).
- Changement de statut (actif, suspendu, blacklisté).

Les modifications sensibles doivent être tracées dans le journal d’audit.

---

### 3.2 Gestion des documents (permis et justificatifs)

#### Types de documents
- Permis de conduire.
- Pièce d’identité.
- Autres justificatifs.

#### Fonctionnalités
- Upload d’un nouveau document.
- Affichage de la liste des documents existants.
- Téléchargement sécurisé.
- Visualisation (si format compatible).
- Statut de validation : en attente, validé, refusé.

Règles :
- Un document est toujours rattaché à un seul client.
- L’upload ne doit pas écraser une version existante : chaque nouveau document crée une nouvelle entrée.
- Les actions d’upload et de validation sont journalisées.
- Les documents sont conservés conformément aux obligations légales.

---

### 3.3 Historique des réservations

La fiche client doit afficher un tableau listant :
- Numéro de réservation.
- Date de début.
- Date de fin.
- Agence de départ.
- Véhicule (ou catégorie).
- Statut de la réservation.

Fonctionnalités :
- Tri par date.
- Accès rapide au détail de la réservation.
- Pagination si nécessaire.

Cet historique est en lecture seule dans le cadre de ce ticket.

---

## 4. Règles de gestion clés

- Un client blacklisté reste consultable mais ne peut pas effectuer de nouvelles réservations (géré côté Réservations).
- Les documents sensibles (permis, pièce d’identité) doivent être accessibles uniquement aux rôles autorisés.
- Toute modification des données personnelles ou du statut est tracée dans le journal d’audit.
- Les données affichées doivent respecter la logique multi-agences (accès limité au périmètre autorisé).

---

## 5. Hors périmètre du ticket

- Implémentation complète du programme de fidélité (affichage simple autorisé si déjà calculé).
- Export RGPD des données client.
- Gestion des sinistres et amendes (affichage non inclus ici).

Ce ticket se concentre sur la fiche client, les documents et l’historique des réservations.