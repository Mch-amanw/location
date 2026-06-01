# Fonctionnalité : Gestion complète des clients

## 1. Objectif métier

Permettre la gestion centralisée et sécurisée des fiches clients (particuliers et professionnels), incluant leurs informations personnelles, documents justificatifs (permis, pièces d’identité), historique d’activité et statut de fidélité.

Cette fonctionnalité vise à :
- Garantir une vision 360° du client.
- Sécuriser les locations via la vérification des documents.
- Faciliter le suivi commercial et opérationnel.
- Alimenter les modules Réservations, Contrats, Facturation, Paiement, Sinistres et Reporting.

---

## 2. Périmètre fonctionnel

### 2.1 Création et gestion des fiches clients

Le système permet :
- Création manuelle d’un client depuis le back-office.
- Création automatique via inscription sur le portail client.
- Modification des informations client selon les droits.
- Désactivation ou suspension d’un client.
- Blacklistage en cas d’incidents répétés ou impayés.
- Archivage logique (pas de suppression physique si obligations légales).

Types de clients :
- Particulier.
- Professionnel (avec informations légales complémentaires).

---

### 2.2 Données gérées

Pour chaque client :
- Identité : nom, prénom ou raison sociale.
- Coordonnées : email, téléphone, adresse.
- Type : particulier / professionnel.
- Informations légales (ICE si applicable).
- Préférence linguistique (FR/EN).
- Statut : actif, suspendu, blacklisté.
- Date de création et historique des modifications.

Règles :
- L’email doit être unique.
- Un client peut être lié à plusieurs réservations et contrats.
- Toute modification sensible (statut, données légales) doit être journalisée.

---

### 2.3 Gestion des documents clients

Documents gérés :
- Permis de conduire.
- Pièce d’identité.
- Autres justificatifs si nécessaires.

Fonctionnalités :
- Upload de documents (back-office et portail client).
- Historisation des versions (pas d’écrasement sans trace).
- Visualisation et téléchargement sécurisé.
- Statut de validation (ex : en attente, validé, refusé).

Règles :
- Les documents doivent être liés à un client unique.
- Les documents sont conservés selon les obligations légales.
- Les actions d’upload, suppression logique ou validation sont tracées.

---

### 2.4 Historique client

Le back-office affiche une vue consolidée comprenant :
- Réservations passées et futures.
- Contrats.
- Factures.
- Paiements et cautions.
- Sinistres.
- Amendes.

Objectif : fournir une vision complète pour la prise de décision (ex : suspension, geste commercial, fidélisation).

---

### 2.5 Espace client (portail)

Le client authentifié peut :
- Consulter ses réservations.
- Télécharger ses contrats et factures.
- Mettre à jour certaines informations personnelles (hors données légales sensibles).
- Télécharger / mettre à jour ses documents.

Les modifications sont soumises aux règles de validation internes.

---

### 2.6 Programme de fidélité

Le système permet :
- Calcul automatique d’un indicateur de fidélité (ex : nombre de locations réalisées).
- Affichage du statut fidélité dans la fiche client.
- Paramétrage des règles au niveau société.

La fidélité peut influencer des avantages commerciaux (hors logique détaillée dans cette fonctionnalité).

---

## 3. Règles de gestion clés

- Un client blacklisté ne peut pas effectuer de nouvelle réservation.
- Un client suspendu peut être empêché de créer de nouvelles réservations selon paramétrage.
- Les données clients ne sont pas supprimées physiquement si liées à des obligations comptables ou contractuelles.
- Toutes les actions sensibles sont tracées dans le journal d’audit.
- Les données doivent être conformes au RGPD (droit d’accès, rectification, anonymisation si légalement autorisée).

---

## 4. Critères d’acceptation

- ✅ Création d’un client particulier ou professionnel depuis le back-office.
- ✅ Inscription autonome via portail client avec création automatique de fiche.
- ✅ Upload et consultation de documents sécurisés.
- ✅ Visualisation d’un historique consolidé dans la fiche client.
- ✅ Changement de statut (actif, suspendu, blacklisté) avec traçabilité.
- ✅ Calcul et affichage automatique du statut fidélité.
- ✅ Blocage de réservation pour client blacklisté.
- ✅ Conformité RGPD (export des données client possible, anonymisation si autorisée).