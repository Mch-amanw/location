# Module : Clients, sinistres et amendes

## 1. Rôle du module

Le module **Clients, sinistres et amendes** centralise la gestion des informations clients et le suivi des incidents liés aux locations (sinistres, dommages, amendes).  
Il couvre :
- La gestion complète des fiches clients (particuliers et professionnels).
- L’historique contractuel et financier.
- La gestion des documents (permis, pièces justificatives).
- Un mécanisme de fidélité.
- Le workflow de traitement des sinistres et des amendes, incluant la refacturation au client.

Ce module est transversal et interagit fortement avec les modules Réservations, Contrats, Facturation, Paiement, États des lieux, Reporting et Journal d’audit.

---

## 2. Gestion des clients

### 2.1 Création et gestion des fiches clients

Le système permet :
- Création manuelle d’un client (back-office).
- Création automatique via inscription portail client.
- Modification des informations (selon droits).
- Désactivation / archivage d’un client (sans suppression physique des données en cas d’obligations légales).

### 2.2 Données gérées

Pour chaque client :
- Informations d’identité (nom, prénom / raison sociale).
- Coordonnées (email, téléphone, adresse).
- Type : particulier ou professionnel.
- Informations légales nécessaires à la facturation (ICE si applicable).
- Documents : permis de conduire, pièce d’identité, justificatifs.
- Préférences linguistiques (FR/EN).
- Statut (actif, suspendu, blacklisté).

### 2.3 Règles de gestion

- Un client peut avoir plusieurs réservations et contrats.
- Les documents doivent être historisés (pas d’écrasement sans trace).
- Un client peut être suspendu ou blacklisté (ex : incidents répétés, impayés).
- Les modifications sensibles doivent être tracées dans le journal d’audit.

---

## 3. Historique et espace client

### 3.1 Historique interne (back-office)

Visualisation consolidée :
- Réservations passées et futures.
- Contrats.
- Factures.
- Paiements et cautions.
- Sinistres.
- Amendes.

### 3.2 Espace client (portail)

Le client peut :
- Consulter son historique de réservations.
- Télécharger contrats et factures.
- Mettre à jour certaines informations personnelles.
- Gérer ses documents (upload permis, justificatifs).

---

## 4. Programme de fidélité

Le module permet :
- Définition d’un mécanisme simple de fidélité (ex : compteur de locations).
- Calcul du statut fidélité basé sur l’historique.
- Consultation du statut dans la fiche client.

Les règles exactes (remises, avantages) sont paramétrables au niveau société.

---

## 5. Gestion des sinistres

### 5.1 Création d’un sinistre

Un sinistre peut être :
- Déclaré à la restitution du véhicule.
- Enregistré manuellement par un utilisateur interne.

Il est lié à :
- Un contrat de location.
- Un véhicule.
- Un client.

### 5.2 Données d’un sinistre

- Date de l’incident.
- Description.
- Photos (si disponibles).
- Niveau de gravité.
- Franchise applicable.
- Coût estimé et coût réel.
- Statut (ouvert, en cours, clôturé, refacturé).

### 5.3 Règles métier

- Le montant refacturable dépend de la franchise et des conditions d’assurance.
- Un sinistre peut générer une facture complémentaire.
- Les actions (modification montant, clôture, refacturation) doivent être auditées.

---

## 6. Gestion des amendes

### 6.1 Réception d’une amende

Une amende peut être enregistrée :
- Manuellement par l’administration.
- Après réception d’une notification officielle.

Elle est associée à :
- Un véhicule.
- Une période donnée.
- Un contrat actif à la date de l’infraction.

### 6.2 Workflow amende

Étapes :
1. Réception.
2. Identification du contrat concerné.
3. Affectation au client.
4. Refacturation.
5. Suivi du paiement.

### 6.3 Données d’une amende

- Type d’infraction.
- Date.
- Autorité émettrice.
- Montant.
- Statut (reçue, affectée, refacturée, payée, contestée).

### 6.4 Règles métier

- Une amende doit être rattachée à un contrat valide couvrant la date d’infraction.
- Une amende peut générer une facture additionnelle.
- Les changements de statut sont journalisés.

---

## 7. Intégration avec facturation et paiements

- Les sinistres et amendes refacturés génèrent des lignes de facturation.
- La facturation respecte la TVA marocaine.
- Les paiements peuvent être enregistrés manuellement ou via paiement en ligne si applicable.

---

## 8. Reporting

Les données du module alimentent :
- Nombre de sinistres par agence / véhicule.
- Montants refacturés.
- Amendes en attente.
- Taux de clients avec incidents.

---

## 9. Contraintes réglementaires

- Conformité RGPD : droit d’accès, rectification, suppression (dans les limites légales).
- Conservation des données conforme aux obligations légales marocaines.
- Protection des documents sensibles (permis, pièces d’identité).