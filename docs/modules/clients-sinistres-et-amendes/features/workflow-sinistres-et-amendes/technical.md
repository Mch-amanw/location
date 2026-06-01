# Spécification Technique – Workflow sinistres et amendes

## 1. Endpoints API

### Sinistres
- `POST /api/v1/sinistres`
- `GET /api/v1/sinistres`
- `GET /api/v1/sinistres/{id}`
- `PATCH /api/v1/sinistres/{id}`
- `POST /api/v1/sinistres/{id}/refacturer`
- `POST /api/v1/sinistres/{id}/cloturer`

### Amendes
- `POST /api/v1/amendes`
- `GET /api/v1/amendes`
- `GET /api/v1/amendes/{id}`
- `PATCH /api/v1/amendes/{id}`
- `POST /api/v1/amendes/{id}/identifier-contrat`
- `POST /api/v1/amendes/{id}/refacturer`
- `POST /api/v1/amendes/{id}/cloturer`

API REST versionnée `/api/v1`.

---

## 2. Logique métier

### 2.1 Machine à états

Implémentation d’une machine à états côté backend :
- Transitions contrôlées (ex : impossible de passer à “Refacturé” sans contrat lié).
- Validation des droits utilisateur avant transition.
- Blocage des modifications si statut final.

### 2.2 Identification automatique du contrat (amendes)

Requête DB :
- Recherche du contrat actif pour le véhicule.
- Condition : dateInfraction BETWEEN dateDebut AND dateFin.
- Si plusieurs résultats → gestion d’erreur contrôlée.

Index requis sur :
- vehiculeId
- dateInfraction
- contratId

---

## 3. Intégration facturation

Lors d’un appel `refacturer` :
- Appel interne au module Facturation.
- Création d’une facture ou ligne complémentaire.
- Stockage de `factureId` dans l’entité Sinistre ou Amende.
- Synchronisation du statut via webhook interne ou événement applicatif.

La facture respecte :
- Numérotation séquentielle.
- TVA paramétrée.
- Devise MAD.

---

## 4. Intégration paiement

- Rattachement possible d’un paiement existant.
- Mise à jour automatique du statut si paiement confirmé.
- Écoute des webhooks paiement pour mise à jour des statuts.

---

## 5. Sécurité

- Contrôle RBAC sur chaque endpoint.
- Vérification du périmètre agence (multi-tenant logique).
- Journalisation automatique des événements suivants :
  - Création.
  - Changement de statut.
  - Modification montant.
  - Refacturation.

Audit log : utilisateur, timestamp, ancienne valeur, nouvelle valeur.

---

## 6. Performance et UX

- Pagination obligatoire sur listings.
- Filtres dynamiques : agence, statut, période, véhicule.
- Tri par date et montant.
- Requêtes optimisées avec index.

---

## 7. Stockage fichiers

- Photos sinistres stockées en object storage S3-compatible.
- URL sécurisées (accès contrôlé via backend).
- Validation type MIME et taille max.

---

## 8. Cohérence transactionnelle

- Utilisation de transactions DB lors de :
  - Refacturation.
  - Clôture avec paiement.
- Rollback en cas d’échec création facture.

---

## 9. Internationalisation

- Messages API compatibles i18n (FR/EN).
- Libellés statuts traduisibles côté frontend.

---

## 10. Reporting

- Exposition de vues agrégées ou endpoints spécifiques.
- Possibilité d’utiliser vues matérialisées pour calculs lourds.