# Spécification Fonctionnelle – Ticket « Journal d’audit »

## 1. Objectif du ticket

Mettre en place un journal d’audit centralisé permettant de tracer toutes les actions sensibles réalisées par les utilisateurs internes dans le système « location 3 », avec un historique consultable selon les droits d’accès.

Ce journal constitue un élément clé de conformité (RGPD et exigences de traçabilité), de sécurité et de gouvernance du réseau multi-agences.

---

## 2. Périmètre fonctionnel

Le ticket couvre :
- La génération automatique d’entrées d’audit lors d’actions sensibles.
- Le stockage persistant et non modifiable des logs.
- La consultation sécurisée de l’historique via le back-office.

Il ne couvre pas :
- La suppression ou modification des logs (interdit en MVP).
- Les exports avancés (si non définis ailleurs).

---

## 3. Actions à tracer (MVP)

Le système doit tracer au minimum les actions suivantes :

- Modification de contrat.
- Restitution de caution.
- Modification tarifaire.
- Actions critiques sur :
  - Réservations (modification, annulation forcée, changement de statut sensible).
  - Factures.
  - Sinistres.

De manière générale, toute action impactant :
- Un montant financier.
- Un engagement contractuel.
- Le statut juridique ou opérationnel d’une entité clé.

---

## 4. Données enregistrées pour chaque entrée

Chaque entrée du journal d’audit doit contenir :

- Identifiant de l’utilisateur.
- Rôle au moment de l’action.
- Date et heure (horodatage précis).
- Type d’action (ex : UPDATE_CONTRACT, REFUND_DEPOSIT).
- Type d’entité concernée (contrat, réservation, facture, sinistre, tarif, etc.).
- Identifiant de l’entité.
- Valeur avant modification (si applicable).
- Valeur après modification (si applicable).

Les valeurs avant/après sont requises pour les opérations de modification.

---

## 5. Consultation du journal d’audit

### 5.1 Accès

L’accès à la consultation du journal est restreint :

- Super Admin : accès global.
- Admin Société : accès global société.
- Autres rôles : accès restreint ou non autorisé selon configuration définie au niveau module.

Un utilisateur ne peut consulter que les logs correspondant à son périmètre (multi-agences).

### 5.2 Fonctionnalités de consultation

Le back-office doit permettre :

- Liste paginée des entrées.
- Tri par date (descendant par défaut).
- Filtres :
  - Par période.
  - Par utilisateur.
  - Par type d’action.
  - Par type d’entité.
  - Par agence (selon rôle).
- Accès au détail d’une entrée (visualisation complète des données avant/après).

---

## 6. Règles de gestion

- Les logs sont générés automatiquement, sans intervention manuelle.
- Les entrées du journal d’audit sont non modifiables et non supprimables via l’interface standard.
- Toute action sensible doit générer exactement une entrée cohérente.
- L’horodatage doit refléter la date réelle de l’action côté serveur.
- Les données personnelles présentes dans les valeurs avant/après doivent rester limitées au strict nécessaire.

---

## 7. Contraintes réglementaires

- Contribuer à la conformité RGPD (traçabilité des actions sur données personnelles).
- Conservation conforme à la politique de sauvegarde (minimum défini au niveau projet).
- Le journal ne doit pas exposer d’informations sensibles à des rôles non autorisés.

---

## 8. Impacts transverses

Le journal d’audit est transversal et doit s’intégrer aux modules suivants :
- Réservations.
- Contrats.
- Facturation.
- Paiements et cautions.
- Sinistres.
- Tarification.

Toute évolution future sur ces modules devra prendre en compte l’ajout éventuel de nouvelles actions à tracer.