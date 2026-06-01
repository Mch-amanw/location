# Ticket : Paramétrage des assurances et franchises

## 1. Objectif du ticket

Mettre en place les écrans et règles de gestion permettant aux utilisateurs autorisés de configurer :

- Les formules d’assurance (incluses ou optionnelles).
- Les montants de franchise par catégorie ou véhicule.
- Les options de réduction de franchise associées.

Ce paramétrage alimente le moteur de calcul tarifaire et impacte le parcours de réservation (portail client et back-office), ainsi que le contrat généré.

---

## 2. Périmètre fonctionnel

Le ticket couvre :

- Les interfaces de gestion back-office des assurances.
- Les interfaces de gestion back-office des franchises.
- Les règles métier associées (validation, activation/désactivation, cohérence).
- La journalisation des modifications.

Il ne couvre pas :

- La gestion des sinistres.
- L’intégration avec un assureur externe.
- Le développement du moteur de calcul (déjà défini au niveau module, uniquement consommé ici).

---

## 3. Gestion des assurances

### 3.1 Liste des assurances

Un écran liste les assurances existantes avec :

- Nom (FR).
- Type (incluse / optionnelle).
- Prix (si applicable).
- Catégories associées.
- Agence (globale ou spécifique).
- Statut (active / inactive).
- Période de validité (si définie).

Fonctionnalités disponibles :

- Créer une assurance.
- Modifier une assurance existante.
- Activer / désactiver une assurance.
- Filtrer par agence, statut, catégorie.

---

### 3.2 Création / modification d’une assurance

Champs configurables :

- Nom (FR / EN) – obligatoire.
- Description (FR / EN) – obligatoire.
- Type de tarification :
  - Incluse.
  - Par jour.
  - Forfait.
- Prix (obligatoire si par jour ou forfait, en MAD).
- Catégories de véhicules concernées (au moins une).
- Agence (globale ou agence spécifique).
- Période de validité (optionnelle).
- Statut actif/inactif.

Règles de gestion :

- Si l’assurance est "incluse", aucun prix supplémentaire ne doit être appliqué.
- Si l’assurance est optionnelle (par jour ou forfait), elle doit pouvoir être sélectionnée lors de la réservation.
- Une assurance inactive ne doit plus être proposée pour les nouvelles réservations.
- Les modifications n’affectent pas les réservations déjà confirmées.

---

## 4. Gestion des franchises

### 4.1 Liste des franchises

Un écran liste les franchises configurées avec :

- Catégorie ou véhicule concerné.
- Agence (globale ou spécifique).
- Montant standard.
- Assurance associée (si applicable).
- Présence d’une réduction configurée.
- Statut.

Fonctionnalités :

- Créer une franchise.
- Modifier une franchise.
- Activer / désactiver.
- Filtrer par agence, catégorie.

---

### 4.2 Création / modification d’une franchise

Champs configurables :

- Catégorie ou véhicule (obligatoire).
- Agence (globale ou spécifique).
- Montant de franchise standard (MAD).
- Assurance associée (optionnelle).
- Option de réduction de franchise (référence à une assurance optionnelle).
- Montant de franchise réduit (si réduction configurée).
- Période de validité (optionnelle).
- Statut actif/inactif.

Règles de gestion :

- Une seule franchise active par catégorie/véhicule/agence sur une même période.
- Le montant réduit doit être strictement inférieur au montant standard.
- Si une réduction est configurée, elle doit être liée à une assurance optionnelle active.
- Toute modification n’impacte pas les réservations existantes (snapshot conservé au moment de la confirmation).

---

## 5. Droits d’accès

- Création / modification / activation / désactivation : superAdmin, adminSociete.
- Consultation : responsables d’agence (selon périmètre).
- Aucun accès à la configuration pour les clients.

Les règles RBAC doivent être strictement appliquées.

---

## 6. Journal d’audit

Chaque action suivante doit être journalisée :

- Création d’une assurance ou franchise.
- Modification d’un champ (ancienne valeur / nouvelle valeur).
- Activation / désactivation.

Les informations journalisées incluent :

- Utilisateur.
- Date/heure.
- Type d’action.
- Détail des changements.

---

## 7. Contraintes métier et réglementaires

- Tous les montants sont exprimés en MAD.
- Les informations doivent être compatibles avec l’affichage multilingue (FR/EN).
- Les données configurées doivent être exploitables par le moteur de calcul sans transformation manuelle.
- Les informations d’assurance et de franchise doivent pouvoir être affichées clairement dans le portail client et le contrat.