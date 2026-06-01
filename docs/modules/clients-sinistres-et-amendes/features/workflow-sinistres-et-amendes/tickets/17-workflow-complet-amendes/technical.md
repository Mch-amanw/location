# Ticket : Workflow complet amendes – Spécification Technique

## 1. Entité Amende

L’entité `Amende` doit inclure (en cohérence avec le module parent) :

- id
- vehiculeId (FK)
- contratId (FK nullable)
- clientId (FK nullable)
- dateInfraction
- typeInfraction
- autorite
- montant
- statut (enum)
- referenceOfficielle
- factureId (FK nullable)
- agenceId (dérivé du véhicule)
- createdAt
- updatedAt

### 1.1 Enum statut

Enum backend contrôlé :
- RECUE
- CONTRAT_IDENTIFIE
- AFFECTEE
- REFACTUREE
- PAYEE
- CONTESTEE
- CLOTUREE

Index requis :
- vehiculeId
- dateInfraction
- contratId
- clientId
- statut
- agenceId

---

## 2. Endpoints API

### 2.1 Création
`POST /api/v1/amendes`

- Validation des champs obligatoires.
- Affectation automatique agenceId via véhicule.
- Statut initial = RECUE.
- Journalisation événement CREATE_AMENDE.

---

### 2.2 Identification contrat
`POST /api/v1/amendes/{id}/identifier-contrat`

Logique :
- Requête : contrat où
  - vehiculeId = amende.vehiculeId
  - dateInfraction BETWEEN dateDebut AND dateFin
- Si un seul résultat → mise à jour contratId, clientId.
- Transition vers CONTRAT_IDENTIFIE.
- Sinon → erreur contrôlée.

Transaction DB requise.
Audit log : IDENTIFICATION_CONTRAT.

---

### 2.3 Affectation
`PATCH /api/v1/amendes/{id}`

- Validation que contratId existe.
- Transition vers AFFECTEE.
- Journalisation.

---

### 2.4 Refacturation
`POST /api/v1/amendes/{id}/refacturer`

Préconditions :
- statut = AFFECTEE
- contratId et clientId non null

Processus :
1. Transaction DB.
2. Appel interne module Facturation.
3. Création facture complémentaire (devise MAD, TVA paramétrée).
4. Enregistrement factureId.
5. Transition vers REFACTUREE.
6. Commit.

Rollback en cas d’erreur.
Audit log : REFACTURATION_AMENDE.

---

### 2.5 Synchronisation paiement

- Écoute événement interne ou webhook paiement.
- Si factureId payée → transition automatique vers PAYEE.
- Journalisation automatique.

---

### 2.6 Contestation
`PATCH /api/v1/amendes/{id}` avec statut CONTESTEE

- Transition contrôlée.
- Blocage de l’endpoint refacturer tant que statut CONTESTEE.

---

### 2.7 Clôture
`POST /api/v1/amendes/{id}/cloturer`

Conditions :
- statut PAYEE ou décision administrative validée.
- Transition vers CLOTUREE.
- Blocage modifications ultérieures (sauf rôle exceptionnel).

---

## 3. Machine à états backend

Implémentation d’une machine à états centralisée :

Transitions autorisées :
- RECUE → CONTRAT_IDENTIFIE
- CONTRAT_IDENTIFIE → AFFECTEE
- AFFECTEE → REFACTUREE
- REFACTUREE → PAYEE
- PAYEE → CLOTUREE
- * → CONTESTEE (si non CLOTUREE)

Validation des transitions dans un service dédié (ex : AmendeStateService).

---

## 4. Sécurité et RBAC

- Middleware RBAC sur chaque endpoint.
- Vérification périmètre agence via agenceId.
- Refacturation limitée aux rôles comptabilité / admin société / superAdmin.

---

## 5. Journal d’audit

Événements auditables :
- Création.
- Identification contrat.
- Changement statut.
- Refacturation.
- Modification montant.

Données enregistrées :
- userId
- timestamp
- ancienne valeur
- nouvelle valeur
- type action

---

## 6. Cohérence transactionnelle

Utilisation de transactions DB pour :
- Identification contrat.
- Refacturation.
- Synchronisation paiement.

Garantir atomicité entre création facture et mise à jour statut.

---

## 7. Performance

- Pagination obligatoire sur `GET /amendes`.
- Filtres : agence, statut, période, véhicule.
- Index DB pour recherche rapide par dateInfraction et vehiculeId.

---

## 8. Internationalisation

- Statuts retournés sous forme de codes (enum).
- Traduction gérée côté frontend via i18n.

---

## 9. Conformité

- Données incluses dans stratégie backup globale.
- Respect RGPD pour données client liées.
- Conformité TVA via module Facturation.