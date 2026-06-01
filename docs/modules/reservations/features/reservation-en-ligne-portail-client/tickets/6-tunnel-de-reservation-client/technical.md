# Ticket : Tunnel de réservation client – Spécification Technique

## 1. Positionnement technique

Implémentation côté **Frontend SPA (React ou Vue)** du parcours complet de réservation.

Le tunnel consomme exclusivement l’API REST `/api/v1` définie dans le module Réservations.

Aucune logique métier critique ne doit être implémentée uniquement côté frontend (toujours validée par l’API).

---

## 2. Architecture Frontend

### 2.1 Structure des pages / composants

Proposition de structure :

- `ReservationSearchPage`
- `ReservationResults`
- `ReservationCustomization`
- `ReservationSummary`
- `ReservationAuthStep`
- `ReservationPaymentRedirect`

Gestion d’état :
- Store global (Redux, Pinia, ou équivalent).
- État local du tunnel persisté en mémoire.
- Optionnel : persistance temporaire en sessionStorage pour éviter perte accidentelle.

---

## 3. Intégration API

### 3.1 Recherche disponibilité
`GET /api/v1/availability`

Paramètres :
- agencyStartId
- agencyEndId
- startDateTime (ISO 8601)
- endDateTime (ISO 8601)
- category

Gestion :
- Timeout configurable.
- Affichage loader.
- Gestion erreurs réseau.

---

### 3.2 Simulation tarifaire
`POST /api/v1/pricing/simulate`

Payload :
- Dates.
- Catégorie.
- Options.
- Assurance.
- Kilométrage.

Retour : détail HT / TVA / TTC.

Le frontend ne calcule jamais lui-même les montants.

---

### 3.3 Création réservation
`POST /api/v1/reservations`

Préconditions :
- JWT valide.

Payload :
- Données sélectionnées.

Réponse attendue :
- id réservation.
- statut.
- URL ou informations nécessaires au paiement.

Protection contre double soumission :
- Désactivation du bouton après clic.
- Idempotency côté backend (si prévu).

---

## 4. Gestion de la concurrence

Le frontend :
- Revalide la disponibilité implicitement via la création réservation.
- Gère le cas où le backend retourne une erreur d’indisponibilité (ex : véhicule réservé entre-temps).
- Redirige l’utilisateur vers l’étape résultats si nécessaire.

---

## 5. Sécurité

- Authentification JWT stockée de manière sécurisée.
- Aucune donnée sensible stockée en localStorage non chiffré.
- Validation stricte des inputs avant envoi.
- Protection CSRF selon configuration globale.
- Aucune logique de validation métier critique côté client uniquement.

---

## 6. Internationalisation

- Utilisation du système i18n global.
- Toutes les chaînes externalisées.
- Formatage des dates selon locale.
- Montants formatés en MAD.

---

## 7. Gestion des erreurs

Types d’erreurs à gérer :
- Indisponibilité véhicule.
- Erreur API.
- Session expirée.
- Paiement refusé (retour depuis passerelle).

Comportement :
- Messages clairs.
- Pas d’exposition d’erreurs techniques internes.
- Logs d’erreurs côté monitoring frontend si configuré.

---

## 8. Performance

- Temps cible < 2 secondes pour affichage résultats (hors latence réseau externe).
- Lazy loading des composants.
- Debounce sur requêtes répétées si nécessaire.
- Optimisation des re-renders.

---

## 9. Responsive & UX

- Design mobile-first.
- Stepper adaptatif.
- Boutons accessibles.
- Conformité basique accessibilité (labels, aria si applicable).

---

## 10. Tests techniques à prévoir

- Tests unitaires composants critiques.
- Tests d’intégration API mockée.
- Test parcours complet simulé.
- Gestion double clic / double soumission.
- Gestion expiration session pendant tunnel.