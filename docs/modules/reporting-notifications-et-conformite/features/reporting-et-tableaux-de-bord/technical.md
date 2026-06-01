# Spécification Technique – Reporting et Tableaux de Bord

## 1. Architecture générale

La fonctionnalité s’appuie sur :
- L’API REST versionnée (/api/v1/reporting).
- La base PostgreSQL.
- Le système RBAC basé sur JWT.
- Les tables existantes : reservations, contrats, factures, paiements, vehicules, agences, sinistres.

Aucun calcul critique ne doit être effectué uniquement côté frontend.

---

## 2. Endpoints API

Exemples d’endpoints :

- GET /api/v1/reporting/dashboard
- GET /api/v1/reporting/ca
- GET /api/v1/reporting/occupation
- GET /api/v1/reporting/rentabilite-vehicules
- GET /api/v1/reporting/retards
- GET /api/v1/reporting/sinistres

Paramètres possibles :
- dateFrom
- dateTo
- agenceId (si autorisé par rôle)

Validation systématique des droits côté backend.

---

## 3. Calcul des indicateurs

### 3.1 Chiffre d’affaires
- Agrégation via SUM sur les montants des factures/paiements validés.
- Filtrage par date et agence.
- Respect de la configuration TVA existante.

### 3.2 Taux d’occupation
- Calcul basé sur la durée totale des locations sur la période.
- Rapport entre jours loués et jours disponibles.
- Optimisation via requêtes agrégées indexées.

### 3.3 Rentabilité par véhicule
- Agrégation des revenus par vehicule_id.
- COUNT des locations.
- Tri et pagination côté backend.

### 3.4 Retards
- Requête sur contrats actifs dont date_fin < now() et statut ≠ clôturé.
- Mise à jour en temps réel à chaque appel.

### 3.5 Sinistres
- COUNT par statut (ouvert, clôturé).
- Filtrage par date d’ouverture et agence.

---

## 4. Performance

- Indexation recommandée sur :
  - date_debut, date_fin (contrats/réservations).
  - agence_id.
  - vehicule_id.
  - statut.
- Possibilité de mise en cache pour le dashboard global (durée courte).
- Pagination obligatoire pour listes détaillées (rentabilité véhicules, retards).

Objectif : temps de réponse acceptable pour 50–200 utilisateurs simultanés.

---

## 5. Sécurité et multi-agences

- Filtrage automatique par agence selon le rôle extrait du JWT.
- Interdiction d’accéder aux données d’une autre agence sans droit global.
- Vérification serveur des paramètres agenceId.

---

## 6. Internationalisation et devise

- Montants retournés en MAD.
- Les libellés sont gérés côté frontend via système i18n (FR/EN).
- Les formats de date respectent la configuration locale.

---

## 7. Logs et audit

- Les accès aux endpoints reporting peuvent être journalisés (niveau applicatif).
- Les erreurs de requêtes sont loguées via le système centralisé de logs.

---

## 8. Documentation

- Tous les endpoints sont documentés via OpenAPI/Swagger.
- Les paramètres de filtre et formats de réponse sont explicitement décrits.

La fonctionnalité doit rester extensible pour intégrer de nouveaux indicateurs en phase 2 (ex : intégration comptable).