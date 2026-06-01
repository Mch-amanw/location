# Fonctionnalité : Gestion des modifications et annulations

## 1. Objectif métier

Permettre la modification et l’annulation des réservations selon des règles claires et configurables, tout en garantissant :
- La cohérence des disponibilités véhicules.
- L’application correcte des règles tarifaires.
- La conformité à la politique d’annulation (annulation gratuite jusqu’à 24h avant le départ).
- La traçabilité complète des actions.

Cette fonctionnalité est accessible :
- Par le client via le portail client (selon conditions).
- Par les utilisateurs internes (agent, responsable agence, admin, superAdmin) via le back-office.

---

## 2. Périmètre

Inclus :
- Modification d’une réservation avant transformation en contrat.
- Annulation d’une réservation avec calcul automatique des frais éventuels.
- Déclenchement automatique des remboursements ou compléments de paiement.
- Mise à jour des disponibilités véhicules.
- Journalisation complète des actions.

Exclus :
- Modification après transformation en contrat (hors périmètre, géré par le module Contrats).
- Remboursements complexes multi-moyens (hors règles définies par le module Paiement).

---

## 3. Règles de gestion – Modification

### 3.1 Conditions de modification

Une réservation peut être modifiée si :
- Son statut est "Confirmée" ou "En attente de paiement".
- Elle n’est pas encore transformée en contrat.
- La date/heure de départ n’est pas dépassée.

Les champs modifiables :
- Date et heure de départ / retour.
- Agence de départ / retour.
- Catégorie ou véhicule (selon disponibilité).
- Options.
- Type de kilométrage.

### 3.2 Vérifications automatiques

Toute modification déclenche :
- Une vérification de disponibilité en temps réel.
- Un recalcul complet du tarif via le moteur de tarification.
- Une comparaison avec le pricing snapshot initial.

### 3.3 Impact financier

Trois cas :

1. Nouveau montant = montant initial → aucune action financière.
2. Nouveau montant > montant initial → paiement complémentaire requis avant confirmation définitive.
3. Nouveau montant < montant initial → génération d’un remboursement (selon règles du module Paiement).

La réservation n’est considérée comme modifiée définitivement qu’après validation du paiement complémentaire si applicable.

---

## 4. Règles de gestion – Annulation

### 4.1 Politique d’annulation (MVP)

- Annulation gratuite jusqu’à 24h avant l’heure de départ.
- Moins de 24h avant le départ : frais d’annulation configurables.

Les frais sont paramétrables globalement (ex : pourcentage du montant total ou montant fixe).

### 4.2 Calcul des frais

Au moment de l’annulation :
- Calcul automatique du délai entre l’instant d’annulation et l’heure de départ.
- Application de la règle correspondante (gratuit ou frais).
- Calcul du montant à rembourser = montant payé – frais d’annulation.

### 4.3 Effets de l’annulation

- Changement du statut en "Annulée".
- Libération immédiate de la disponibilité du véhicule.
- Déclenchement du processus de remboursement si applicable.
- Envoi d’une notification au client (email/SMS).

Une réservation déjà transformée en contrat ne peut plus être annulée via ce mécanisme.

---

## 5. Accès et rôles

### 5.1 Client

Peut :
- Modifier ou annuler sa réservation depuis l’espace client, tant que les conditions sont respectées.
- Visualiser clairement les frais éventuels avant validation.

### 5.2 Utilisateurs internes

Peuvent :
- Modifier ou annuler une réservation selon leurs droits (RBAC multi-agences).
- Forcer une annulation si nécessaire (avec journalisation obligatoire).

---

## 6. Notifications

Déclenchement automatique de notifications :
- Confirmation de modification.
- Demande de paiement complémentaire.
- Confirmation d’annulation.
- Confirmation de remboursement (si applicable).

Templates multilingues FR/EN.

---

## 7. Journal d’audit

Doivent être tracés :
- Toute modification de dates.
- Changement de véhicule/catégorie.
- Modification du montant.
- Annulation (avec motif si saisi).
- Annulation forcée par un agent.

Les logs incluent : utilisateur, rôle, agence, date/heure, ancienne valeur, nouvelle valeur.

---

## 8. Critères d’acceptation

- Une annulation effectuée plus de 24h avant le départ ne génère aucun frais.
- Une annulation effectuée moins de 24h avant applique automatiquement les frais configurés.
- Une modification avec augmentation de prix exige un paiement complémentaire avant validation.
- Une modification avec diminution de prix déclenche un remboursement.
- Une réservation annulée libère immédiatement la disponibilité du véhicule.
- Toutes les actions sont visibles dans le journal d’audit.