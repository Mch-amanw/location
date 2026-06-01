## Spécification Fonctionnelle – Moteur de disponibilité

### 1. Objectif du ticket

Le moteur de disponibilité a pour objectif de fournir un calcul fiable, cohérent et temps réel de la disponibilité des véhicules en tenant compte :

- Du statut opérationnel du véhicule.
- De son état d’activation (actif / hors flotte).
- Des réservations existantes.
- Des contrats actifs.
- Des transferts en cours.

Ce moteur constitue le socle métier utilisé par :
- Le module Réservations (vérification avant création/modification).
- Le module Gestion de la flotte (vue synthétique par agence).
- Le portail client (affichage des véhicules disponibles).
- Le reporting (taux d’occupation).

---

### 2. Périmètre fonctionnel

Le moteur doit permettre :

1. De déterminer si un véhicule est disponible sur une période donnée.
2. De retourner la liste des véhicules disponibles selon :
   - Une agence.
   - Une catégorie (optionnelle).
   - Une période (date/heure de début et de fin).
3. De fournir des indicateurs synthétiques de disponibilité par agence.

---

### 3. Règles métier de disponibilité

Un véhicule est considéré comme **disponible** sur une période donnée uniquement si :

1. Il est actif (non désactivé, non hors flotte).
2. Son statut est "Disponible".
3. Il n’a aucune réservation confirmée chevauchant la période demandée.
4. Il n’est associé à aucun contrat actif chevauchant la période demandée.
5. Il n’est pas en transfert (statut véhicule "En transfert" ou transfert en cours sur la période).
6. Il n’est pas en maintenance / indisponible.

---

### 4. Gestion des conflits de dates

Deux périodes sont considérées en conflit si leurs intervalles se chevauchent.

Le moteur doit :
- Détecter tout chevauchement entre la période demandée et :
  - Les réservations existantes.
  - Les contrats actifs.
- Exclure automatiquement le véhicule si un conflit est détecté.

---

### 5. Cas spécifiques

- Un véhicule avec une réservation future ne doit pas être proposé comme disponible sur la période réservée.
- Un véhicule dont le statut change (ex : passage en maintenance) doit être immédiatement exclu des résultats.
- Un véhicule en transfert est considéré indisponible pendant toute la durée du transfert.
- Un véhicule désactivé ou hors flotte ne doit jamais apparaître comme disponible.

---

### 6. Utilisation dans le système

Le moteur doit pouvoir être utilisé :

- En vérification unitaire (vérifier si un véhicule précis est disponible).
- En recherche globale (retourner une liste de véhicules disponibles).
- Pour alimenter une vue synthétique par agence (nombre total, disponibles, indisponibles par statut).

Le calcul de disponibilité doit être centralisé afin d’éviter toute divergence de logique entre modules.

---

### 7. Traçabilité et cohérence

Le moteur n’effectue pas directement de journalisation, mais toute action modifiant les données impactant la disponibilité (changement de statut, création de réservation, transfert, etc.) doit déclencher une mise à jour immédiate et cohérente des résultats retournés par le moteur.

La logique doit être unique, centralisée et non dupliquée dans plusieurs services.