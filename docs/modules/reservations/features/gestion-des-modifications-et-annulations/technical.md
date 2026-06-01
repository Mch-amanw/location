# Fonctionnalité : Gestion des modifications et annulations – Spécification Technique

## 1. Composants impactés

- Module Réservations (logique principale).
- Module Tarification (recalcul).
- Module Paiement (complément et remboursement).
- Module Véhicules (mise à jour disponibilité).
- Module Notifications.
- Module Journal d’audit.

---

## 2. Modèle de données

### 2.1 Paramétrage des frais d’annulation

Ajout d’un paramétrage global (table configuration ou settings) :
- cancellationFreeDelayHours (int, défaut = 24).
- cancellationFeeType (enum: PERCENTAGE | FIXED).
- cancellationFeeValue (decimal).

### 2.2 Réservation

- status (enum existant : Confirmée, Annulée, etc.).
- cancellationFeeAmount (decimal, nullable).
- cancelledAt (timestamp).
- cancelledBy (userId ou "client").
- modificationHistory (via audit log, pas dans la table principale).

---

## 3. Workflow technique – Modification

### 3.1 Endpoint

- `PUT /api/v1/reservations/{id}`

Étapes :
1. Vérification RBAC.
2. Vérification statut et date (pas déjà démarrée, pas transformée en contrat).
3. Vérification disponibilité.
4. Appel au moteur de tarification.
5. Comparaison avec pricingSnapshot.
6. Si delta > 0 :
   - Création d’une intention de paiement (module Paiement).
   - Statut temporaire "En attente de paiement complémentaire" (technique).
   - Confirmation définitive via webhook paiement.
7. Si delta < 0 :
   - Appel service remboursement.
   - Mise à jour réservation après confirmation.
8. Mise à jour pricingSnapshot.
9. Log audit.
10. Envoi notification.

Toutes les opérations critiques doivent être encapsulées dans une transaction DB.

---

## 4. Workflow technique – Annulation

### 4.1 Endpoint

- `POST /api/v1/reservations/{id}/cancel`

Étapes :
1. Vérification RBAC.
2. Vérification que statut ∉ {Annulée, Transformée en contrat}.
3. Calcul du délai en heures entre now() et startDateTime.
4. Lecture configuration des frais.
5. Calcul cancellationFeeAmount.
6. Calcul refundAmount = amountPaid – cancellationFeeAmount.
7. Appel module Paiement pour remboursement si > 0.
8. Mise à jour statut = Annulée.
9. Libération disponibilité (suppression du blocage logique via statut).
10. Log audit.
11. Envoi notification.

Gestion transactionnelle :
- Mise à jour réservation uniquement après confirmation succès remboursement (ou enregistrement d’un statut intermédiaire si remboursement asynchrone).

---

## 5. Intégration Paiement

- Utilisation des webhooks pour confirmer paiement complémentaire ou remboursement.
- Gestion des cas d’échec :
  - Paiement complémentaire échoué → modification non validée.
  - Remboursement échoué → statut technique "Annulation en attente remboursement".

Aucune donnée carte stockée (tokenisation via passerelle).

---

## 6. Disponibilité et cohérence

La disponibilité repose sur :
- Réservations avec statut Confirmée.
- Exclusion automatique des réservations Annulées.

Index DB sur (vehicleId, startDateTime, endDateTime) utilisé pour recalcul rapide après modification.

---

## 7. Sécurité & Audit

- Vérification stricte que le client ne peut modifier/annuler que ses propres réservations.
- Contrôle agence pour utilisateurs internes.
- Enregistrement systématique dans la table d’audit avec diff JSON (ancienne vs nouvelle version).

---

## 8. Performance & contraintes

- Opérations critiques protégées par transactions.
- Timeout et retry gérés côté paiement.
- Pas de recalcul massif en batch : traitement unitaire par réservation.

Compatible avec architecture stateless et API versionnée `/api/v1`.