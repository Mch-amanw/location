# Spécification Technique – Ticket : Tableaux de bord KPI

## 1. Architecture et intégration

Ce ticket s’intègre dans le module Reporting et s’appuie sur :
- Backend API REST : `/api/v1/reporting`.
- Base PostgreSQL.
- Système RBAC via JWT.
- Tables existantes : reservations, contrats, factures, paiements, vehicules, agences, sinistres.

Aucun calcul métier critique ne doit être effectué uniquement côté frontend.

---

## 2. Endpoint principal – Dashboard KPI

### 2.1 Endpoint

`GET /api/v1/reporting/dashboard`

### 2.2 Paramètres

- `dateFrom` (ISO 8601)
- `dateTo` (ISO 8601)
- `agenceId` (optionnel, validé selon rôle)

Validation :
- Si rôle restreint à une agence, le backend ignore ou valide strictement agenceId.
- Vérification systématique des droits via JWT.

### 2.3 Structure de réponse (conceptuelle)

```json
{
  "ca": {
    "total": 0
  },
  "occupation": {
    "taux": 0
  },
  "rentabiliteVehicules": [
    {
      "vehiculeId": "",
      "immatriculation": "",
      "nombreLocations": 0,
      "revenus": 0
    }
  ],
  "retards": {
    "total": 0
  },
  "sinistres": {
    "total": 0,
    "ouverts": 0,
    "clotures": 0
  }
}
```

---

## 3. Logique de calcul

### 3.1 Chiffre d’affaires

- Agrégation via `SUM` sur montants des factures validées et/ou paiements confirmés.
- Filtrage par date d’émission ou date de paiement (selon règle globale du projet).
- Filtrage par agence via jointure avec contrats/réservations.
- Index requis sur : date_facture, agence_id, statut.

---

### 3.2 Taux d’occupation

- Calcul :
  - Durée totale louée sur la période.
  - Divisée par (nombre de véhicules × durée totale de la période).
- Utilisation des champs date_debut et date_fin des contrats.
- Gestion des chevauchements avec la période sélectionnée.
- Optimisation via requêtes agrégées indexées.

Index recommandés :
- contrats(date_debut, date_fin)
- contrats(agence_id)

---

### 3.3 Rentabilité par véhicule

- `GROUP BY vehicule_id`.
- `SUM(montant)` pour revenus.
- `COUNT(contrat_id)` pour nombre de locations.
- Tri côté backend (paramètre optionnel futur).
- Pagination si volume important.

Index recommandés :
- contrats(vehicule_id)
- factures(contrat_id)

---

### 3.4 Retards

Requête sur :
- contrats où :
  - `date_fin < NOW()`
  - `statut != 'cloture'`
- Filtrage par agence.
- Calcul éventuel de durée_retard = NOW() - date_fin.

Requête dynamique (pas de cache long).

---

### 3.5 Sinistres

- `COUNT(*)` groupé par statut.
- Filtrage par date_ouverture et agence.

Index recommandés :
- sinistres(date_ouverture)
- sinistres(agence_id)
- sinistres(statut)

---

## 4. Performance

- Requêtes optimisées avec agrégations SQL.
- Indexation obligatoire sur colonnes utilisées dans WHERE et GROUP BY.
- Possibilité de mise en cache court terme (ex : 1–5 minutes) pour dashboard global.
- Pagination pour listes détaillées (rentabilité véhicules).

Objectif : temps de réponse compatible avec 50–200 utilisateurs internes simultanés.

---

## 5. Sécurité et multi-agences

- Filtrage automatique par agence selon rôle extrait du JWT.
- Interdiction d’accéder aux données d’une autre agence sans droit global.
- Validation serveur des paramètres date et agenceId.

---

## 6. Internationalisation et format

- Montants retournés en MAD (format numérique brut).
- Les libellés et formats localisés sont gérés côté frontend.
- Dates en ISO 8601.

---

## 7. Logs et monitoring

- Log des erreurs de requêtes via système centralisé.
- Possibilité de journalisation des accès aux endpoints reporting.
- Surveillance des performances des requêtes lourdes.

Ce ticket doit rester extensible pour intégrer de nouveaux KPI dans le même endpoint ou via des endpoints dédiés.