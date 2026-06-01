# Spécification Fonctionnelle – Module Reporting, Notifications et Conformité

## 1. Rôle du module

Le module « Reporting, notifications et conformité » regroupe :
- Les tableaux de bord et indicateurs de pilotage.
- Le système de notifications automatiques (email/SMS).
- Le journal d’audit obligatoire.
- La gestion des contenus légaux via le mini CMS (CGU, assurances, FAQ, pages informatives).

Il contribue au pilotage opérationnel et financier du réseau multi-agences, à l’automatisation des communications, et à la conformité réglementaire (RGPD et législation marocaine).

---

## 2. Reporting et tableaux de bord

### 2.1 Objectifs
Fournir des indicateurs fiables et exploitables pour :
- La direction (superAdmin, adminSociete).
- Les responsables d’agence.
- La comptabilité.

### 2.2 Indicateurs principaux (MVP)

Les tableaux de bord doivent permettre la visualisation des indicateurs suivants :

- Chiffre d’affaires (global et par agence).
- Taux d’occupation des véhicules.
- Rentabilité par véhicule.
- Nombre de réservations (période donnée).
- Retards de restitution.
- Nombre et statut des sinistres.

### 2.3 Filtres et périmètre

- Filtres par période (jour, mois, année, plage personnalisée).
- Filtre par agence (selon rôle).
- Accès restreint aux données selon le rôle et le périmètre (multi-agences).

### 2.4 Règles de gestion

- Les données doivent refléter l’état réel des réservations, contrats, paiements et sinistres.
- Les calculs doivent être cohérents avec les règles de facturation (TVA incluse/exclue selon configuration).
- Les responsables d’agence ne visualisent que les données de leur agence.
- Les exports (si activés) doivent respecter les droits d’accès.

---

## 3. Notifications automatiques (Email / SMS)

### 3.1 Objectifs
Automatiser la communication avec les clients et, le cas échéant, les utilisateurs internes.

### 3.2 Événements déclencheurs (MVP)

Notifications automatiques notamment pour :
- Confirmation de réservation.
- Rappels avant départ.
- Confirmation de paiement.
- Information liée au contrat.
- Notification de retard.

### 3.3 Canaux

- Email transactionnel.
- SMS via fournisseur compatible Maroc.

### 3.4 Templates

- Templates multilingues (FR/EN).
- Variables dynamiques (nom client, dates, agence, véhicule, montant, etc.).
- Activation/désactivation par type d’événement (selon configuration).

### 3.5 Règles de gestion

- Une notification est envoyée uniquement si les coordonnées nécessaires sont disponibles.
- Les envois doivent être journalisés (date, canal, statut).
- Les erreurs d’envoi doivent être tracées.
- Respect des règles RGPD (consentement et gestion des préférences si applicable).

---

## 4. Journal d’audit

### 4.1 Objectif
Garantir la traçabilité des actions sensibles réalisées dans le système.

### 4.2 Actions à tracer (MVP)

Inclut notamment :
- Modification de contrat.
- Restitution de caution.
- Modification tarifaire.
- Actions critiques sur réservations, factures et sinistres.

### 4.3 Données enregistrées

Pour chaque événement :
- Identifiant utilisateur.
- Rôle.
- Date et heure.
- Type d’action.
- Entité concernée (contrat, réservation, facture, etc.).
- Valeurs avant/après si pertinent.

### 4.4 Règles de gestion

- Le journal d’audit est non modifiable par les utilisateurs standards.
- Consultation restreinte aux rôles autorisés (ex : superAdmin, adminSociete).
- Conservation conforme aux obligations légales et aux exigences RGPD.

---

## 5. Mini CMS et contenus légaux

### 5.1 Objectif
Permettre l’administration des contenus visibles sur le portail client.

### 5.2 Types de contenus (MVP)

- CGU.
- Informations sur les assurances.
- FAQ.
- Pages informatives.

### 5.3 Fonctionnalités

- Création / modification / publication de pages.
- Gestion multilingue FR/EN.
- Gestion du statut (brouillon / publié).

### 5.4 Règles de gestion

- Seuls les rôles autorisés peuvent modifier les contenus.
- L’historique des modifications importantes peut être tracé via le journal d’audit.
- Les versions publiées sont celles affichées sur le portail client.

---

## 6. Conformité RGPD et législation marocaine

Le module contribue à la conformité via :
- Journalisation des actions sensibles.
- Gestion structurée des communications.
- Publication des CGU et informations légales à jour.
- Accès restreint aux données selon les rôles.

Les données personnelles utilisées dans les notifications et rapports doivent être exploitées uniquement dans le cadre des finalités prévues.

---

## 7. Profils concernés

- Super Admin : accès global à tous les reportings, audit et CMS.
- Admin Société : accès global société.
- Responsable d’agence : reporting agence.
- Comptabilité : reporting financier.
- Agent comptoir : accès limité (pas d’accès complet aux rapports stratégiques ni à l’audit complet).

---

# Conclusion

Ce module centralise les fonctions de pilotage, de communication automatisée et de conformité réglementaire, essentielles au bon fonctionnement et à la gouvernance du réseau multi-agences « location 3 ».