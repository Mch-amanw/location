# Spécification Fonctionnelle – Disponibilités et transferts

## 1. Objectif métier

La fonctionnalité **Disponibilités et transferts** a pour objectif de :

- Garantir une vision fiable et en temps réel de la disponibilité des véhicules.
- Permettre la planification et le suivi des transferts inter-agences.
- Assurer la cohérence entre les statuts véhicules, les réservations et les opérations logistiques.

Elle constitue un élément critique pour la gestion des réservations, la satisfaction client et l’optimisation du taux d’occupation.

---

## 2. Périmètre

La fonctionnalité couvre :

- Le calcul et l’affichage des disponibilités des véhicules.
- L’exclusion automatique des véhicules indisponibles (réservés, en location, en maintenance, en transfert, hors flotte).
- La planification d’un transfert inter-agences.
- Le suivi des transferts (création, démarrage, réception).
- La mise à jour automatique du statut et de l’agence d’affectation.

Elle s’intègre avec :
- Le module Réservations (blocage et vérification de disponibilité).
- Le module Contrats (véhicule en location).
- Le module Reporting (taux d’occupation).
- Le Journal d’audit.

---

## 3. Gestion des disponibilités

### 3.1 Vision temps réel

Le système doit permettre :

- La consultation de la disponibilité d’un véhicule par agence.
- Le filtrage par catégorie et statut.
- Une vue synthétique de la flotte par agence (nombre total, disponibles, en location, en maintenance, en transfert).

### 3.2 Règles de gestion

Un véhicule est considéré comme **disponible** uniquement si :
- Son statut est "Disponible".
- Il est actif.
- Il n’est associé à aucune réservation ou contrat en cours sur la période demandée.

Un véhicule est automatiquement exclu de la disponibilité si son statut est :
- Réservé.
- En location.
- En transfert.
- En maintenance / indisponible.
- Hors flotte.

Toute modification de statut met à jour immédiatement la disponibilité.

---

## 4. Transferts inter-agences

### 4.1 Objectif

Permettre la réaffectation d’un véhicule d’une agence à une autre tout en garantissant l’indisponibilité temporaire pendant le transfert.

### 4.2 Création d’un transfert

Un utilisateur autorisé peut créer un transfert en renseignant :
- Agence d’origine.
- Agence de destination.
- Date de départ prévue.
- Date d’arrivée prévue.

Règles :
- Le véhicule doit être au statut "Disponible".
- Il ne doit pas avoir de réservation ou contrat actif sur la période de transfert.

---

### 4.3 Cycle de vie d’un transfert

États possibles d’un transfert :
- Planifié.
- En cours.
- Terminé.
- Annulé.

Transitions :
- Passage à "En cours" → statut véhicule devient "En transfert".
- Passage à "Terminé" →
  - Mise à jour de l’agence d’affectation.
  - Statut véhicule devient "Disponible".
- Annulation possible uniquement avant démarrage.

Pendant toute la période "En transfert", le véhicule est indisponible à la réservation.

---

## 5. Cas particuliers

- Un véhicule en location ne peut pas être transféré.
- Un transfert ne peut pas être finalisé si le véhicule est associé à un contrat actif.
- Toute modification ou annulation est journalisée.

---

## 6. Droits et permissions

- Super Admin : gestion globale multi-agences.
- Admin société : gestion globale flotte.
- Responsable d’agence : création et validation des transferts pour son agence.
- Agent comptoir : consultation, création si autorisé.
- Comptabilité : consultation uniquement.

Le contrôle d’accès respecte le modèle RBAC du projet.

---

## 7. Critères d’acceptation

- ✅ Un véhicule en statut "Disponible" apparaît comme réservable.
- ✅ Un véhicule en transfert n’est pas proposé à la réservation.
- ✅ La finalisation d’un transfert met à jour automatiquement l’agence et le statut.
- ✅ Les règles empêchent tout transfert d’un véhicule en location.
- ✅ Toute opération de transfert est tracée dans le journal d’audit.
- ✅ La vue synthétique reflète en temps réel les changements de statut.