# Spécification Fonctionnelle – Fonctionnalité « Notifications, audit et CMS »

## 1. Objectif métier

Cette fonctionnalité regroupe trois piliers essentiels du module :
- L’automatisation des notifications email et SMS.
- La traçabilité des actions sensibles via un journal d’audit.
- La gestion des contenus légaux et informatifs via un mini CMS.

Elle vise à :
- Améliorer la communication client.
- Garantir la conformité réglementaire (RGPD et législation marocaine).
- Assurer la transparence et la traçabilité des actions critiques.

---

## 2. Notifications automatiques (Email / SMS)

### 2.1 Périmètre

Envoi automatique de notifications liées aux événements clés du cycle de location.

Événements concernés (MVP) :
- Confirmation de réservation.
- Rappel avant départ.
- Confirmation de paiement.
- Informations liées au contrat.
- Notification de retard.

Canaux supportés :
- Email transactionnel.
- SMS.

### 2.2 Règles de gestion

- Une notification est envoyée uniquement si les coordonnées nécessaires (email ou numéro mobile) sont disponibles.
- Les templates sont multilingues (FR/EN) et utilisent des variables dynamiques (nom, dates, agence, véhicule, montant, etc.).
- L’activation/désactivation des notifications peut être configurée par type d’événement.
- Chaque envoi est journalisé (date, canal, statut, entité liée).
- En cas d’échec d’envoi, l’erreur est tracée.
- Le traitement respecte les règles RGPD (utilisation des données limitée aux finalités prévues).

### 2.3 Critères d’acceptation

- Lorsqu’une réservation est confirmée, un email de confirmation est automatiquement envoyé au client.
- Si un numéro mobile valide est présent, un SMS peut être envoyé selon la configuration.
- Les messages envoyés apparaissent dans un historique consultable en back-office.
- Les contenus sont affichés dans la langue du client si disponible.

---

## 3. Journal d’audit

### 3.1 Périmètre

Le journal d’audit trace les actions sensibles effectuées par les utilisateurs internes.

Actions concernées (MVP) :
- Modification de contrat.
- Restitution de caution.
- Modification tarifaire.
- Actions critiques sur réservations, factures et sinistres.

### 3.2 Données enregistrées

Pour chaque action :
- Utilisateur (identifiant).
- Rôle.
- Date et heure.
- Type d’action.
- Entité concernée (contrat, réservation, facture, sinistre, etc.).
- Valeurs avant/après si pertinent.

### 3.3 Règles de gestion

- Les entrées du journal d’audit sont non modifiables par les utilisateurs standards.
- L’accès en lecture est restreint aux rôles autorisés (superAdmin, adminSociete).
- Les données sont conservées conformément aux obligations légales et aux politiques internes.

### 3.4 Critères d’acceptation

- Toute modification d’un contrat génère automatiquement une entrée dans le journal d’audit.
- La restitution d’une caution crée une trace détaillée incluant l’utilisateur et l’horodatage.
- Un utilisateur non autorisé ne peut ni consulter ni modifier les logs.

---

## 4. Mini CMS – Contenus légaux et informatifs

### 4.1 Périmètre

Gestion des contenus affichés sur le portail client :
- CGU.
- Informations sur les assurances.
- FAQ.
- Pages informatives.

### 4.2 Fonctionnalités

- Création, modification et suppression de pages (selon droits).
- Gestion du statut : brouillon / publié.
- Gestion multilingue (FR/EN).
- Visualisation des pages publiées sur le portail client.

### 4.3 Règles de gestion

- Seuls les rôles autorisés peuvent créer ou modifier les contenus.
- Seules les pages avec statut « publié » sont visibles côté portail client.
- Les modifications importantes peuvent être tracées dans le journal d’audit.
- Les contenus doivent être conformes aux obligations légales en vigueur.

### 4.4 Critères d’acceptation

- Une page en statut « brouillon » n’est pas visible sur le portail.
- La publication d’une nouvelle version la rend immédiatement accessible aux clients.
- Les contenus sont disponibles en FR et EN.

---

## 5. Profils concernés

- Super Admin : gestion complète (notifications, audit, CMS).
- Admin Société : gestion complète au niveau société.
- Responsable d’agence : accès limité (lecture partielle des notifications liées à son agence).
- Comptabilité : accès aux notifications et audit liés aux opérations financières.
- Agent comptoir : pas d’accès au journal d’audit complet ni au CMS.

---

## 6. Conformité

La fonctionnalité contribue à la conformité :
- RGPD (traçabilité, usage maîtrisé des données, publication des CGU).
- Législation marocaine (transparence, conservation des informations liées aux transactions).