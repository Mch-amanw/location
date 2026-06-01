# Ticket : Règles d’annulation configurables – Spécification Technique

## 1. Modèle de données

### 1.1 Table de configuration (globale)

Ajout ou extension d’une table `settings` (ou équivalent) :

Champs :
- cancellationFreeDelayHours (int, default = 24, not null)
- cancellationFeeType (enum: `PERCENTAGE` | `FIXED`, not null)
- cancellationFeeValue (decimal(10,2), not null)
- updatedAt (timestamp)
- updatedBy (userId)

Contraintes :
- cancellationFreeDelayHours >= 0
- cancellationFeeValue >= 0

Accès en lecture via service de configuration centralisé (cache applicatif recommandé).

---

### 1.2 Table Reservation (extension)

Ajout ou confirmation des champs suivants :

- cancellationFeeAmount (decimal(10,2), nullable)
- cancelledAt (timestamp, nullable)
- cancelledBy (UUID ou string "client", nullable)

Ces champs sont renseignés uniquement lors du passage au statut `Annulée`.

---

## 2. Service métier : CancellationPolicyService

Création d’un service dédié responsable de :

- Charger la configuration active.
- Calculer le délai en heures entre `now()` et `startDateTime`.
- Déterminer si l’annulation est gratuite.
- Calculer le montant des frais.
- Retourner un objet structuré :

```json
{
  "isFree": boolean,
  "delayHours": number,
  "appliedRule": {
    "type": "PERCENTAGE" | "FIXED",
    "value": number
  },
  "cancellationFeeAmount": number,
  "refundAmount": number
}
```

Règles techniques :
- Calcul basé sur horodatage serveur (UTC ou timezone système cohérente).
- Arrondi à 2 décimales (round half up).
- Si frais calculés > amountPaid → plafonnement à amountPaid.

---

## 3. Intégration dans le workflow d’annulation

Endpoint existant :

`POST /api/v1/reservations/{id}/cancel`

Étapes supplémentaires spécifiques à ce ticket :

1. Récupération de la réservation.
2. Appel à `CancellationPolicyService.calculate(reservation)`.
3. Persistance :
   - cancellationFeeAmount
   - cancelledAt
   - cancelledBy
4. Calcul du refundAmount transmis au module Paiement.
5. Mise à jour du statut = `Annulée` après confirmation succès remboursement (ou statut technique si asynchrone).
6. Création d’une entrée d’audit incluant les détails du calcul.

Toutes les opérations doivent être exécutées dans une transaction DB.

---

## 4. Endpoint de consultation des frais (prévisualisation)

Pour permettre l’affichage des frais avant confirmation, ajout d’un endpoint :

`GET /api/v1/reservations/{id}/cancellation-preview`

Fonction :
- Vérifier droits d’accès.
- Appeler `CancellationPolicyService`.
- Retourner les données de calcul sans modifier la réservation.

Aucune écriture en base à ce stade.

---

## 5. Sécurité & RBAC

- Lecture configuration : utilisateurs authentifiés.
- Modification configuration : rôle Admin société ou Super Admin uniquement.
- Endpoint preview et cancel :
  - Client → uniquement ses réservations.
  - Agent → selon périmètre agence.

Validation stricte des états :
- Interdit si statut ∈ {Annulée, Transformée en contrat}.

---

## 6. Audit

Chaque modification de configuration :
- Log avec ancienne valeur / nouvelle valeur (diff JSON).

Chaque annulation :
- Log contenant :
  - delayHours
  - cancellationFreeDelayHours
  - cancellationFeeType
  - cancellationFeeValue
  - cancellationFeeAmount
  - refundAmount

Audit centralisé conformément au module Journal d’audit.

---

## 7. Performance & cache

- Mise en cache en mémoire de la configuration (TTL court ou invalidation sur mise à jour).
- Aucun recalcul massif en batch.
- Calcul unitaire par réservation.

Compatible architecture stateless et API versionnée `/api/v1`.