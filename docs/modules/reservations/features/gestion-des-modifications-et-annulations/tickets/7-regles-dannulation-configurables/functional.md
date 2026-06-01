# Ticket : Règles d’annulation configurables

## 1. Objectif du ticket

Implémenter le mécanisme de paramétrage et d’application des règles d’annulation pour les réservations, conformément à la politique MVP :

- Annulation gratuite jusqu’à 24h avant l’heure de départ.
- Application de frais configurables en cas d’annulation moins de 24h avant le départ.

Le système doit permettre d’adapter ces règles via un paramétrage global, tout en garantissant un calcul automatique, fiable et traçable des frais d’annulation.

---

## 2. Périmètre fonctionnel

Inclus dans ce ticket :
- Paramétrage global des règles d’annulation.
- Calcul automatique des frais au moment de l’annulation.
- Affichage transparent des frais avant confirmation d’annulation.
- Enregistrement des montants appliqués dans la réservation.
- Intégration avec le module Paiement pour déterminer le montant à rembourser.

Non inclus :
- Gestion avancée de politiques multiples par agence ou par catégorie (MVP = règle globale unique).
- Gestion de cas juridiques spécifiques hors politique standard.

---

## 3. Paramétrage des règles d’annulation

Un paramétrage global doit permettre de définir :

- Délai de gratuité (en heures) avant l’heure de départ.
  - Valeur par défaut : 24 heures.
- Type de frais en cas d’annulation hors délai :
  - Pourcentage du montant total TTC.
  - Montant fixe (en MAD).
- Valeur des frais (pourcentage ou montant).

Ces paramètres sont modifiables uniquement par un utilisateur ayant les droits d’administration globale (Admin société ou Super Admin).

Toute modification du paramétrage doit être tracée dans le journal d’audit.

---

## 4. Calcul des frais d’annulation

### 4.1 Détermination du délai

Au moment de l’annulation, le système :

1. Récupère la date/heure actuelle.
2. Calcule la différence en heures entre la date/heure d’annulation et la date/heure de départ de la réservation.

### 4.2 Cas 1 : Annulation dans le délai de gratuité

Si la différence est supérieure ou égale au délai configuré (ex : ≥ 24h) :

- Aucun frais d’annulation n’est appliqué.
- cancellationFeeAmount = 0.

### 4.3 Cas 2 : Annulation hors délai

Si la différence est inférieure au délai configuré :

- Les frais sont calculés selon la configuration :
  - Pourcentage : montantTotalTtc × (pourcentage / 100).
  - Montant fixe : valeur configurée.

Règles complémentaires :
- Les frais ne peuvent pas dépasser le montant total payé.
- Les montants sont exprimés en MAD.
- Les montants sont arrondis selon les règles financières standard (2 décimales).

---

## 5. Impact sur le remboursement

Le montant à rembourser est calculé comme suit :

montantRemboursement = montantPayé – fraisAnnulation

Cas possibles :
- Si frais = 0 → remboursement total.
- Si frais > 0 → remboursement partiel.
- Si montantPayé = 0 → aucun remboursement, mais les frais sont enregistrés à titre informatif.

Le calcul est effectué avant appel au module Paiement.

---

## 6. Expérience utilisateur

### 6.1 Côté client (portail)

Avant confirmation définitive de l’annulation :
- Affichage du délai restant.
- Indication claire si l’annulation est gratuite ou payante.
- Affichage du montant des frais.
- Affichage du montant estimé du remboursement.

Le client doit valider explicitement l’annulation après visualisation des frais.

### 6.2 Côté back-office

Les utilisateurs internes visualisent :
- Le détail des frais calculés.
- Le paramétrage en vigueur.

En cas d’annulation forcée, les frais sont calculés automatiquement selon les mêmes règles (pas de contournement sans évolution ultérieure).

---

## 7. Journalisation et traçabilité

Doivent être enregistrés dans le journal d’audit :

- Modification du paramétrage des règles d’annulation.
- Annulation d’une réservation avec :
  - Délai calculé.
  - Type de frais appliqué.
  - Montant des frais.
  - Montant du remboursement.
  - Utilisateur à l’origine de l’action.

Les informations doivent être conservées conformément aux règles RGPD et aux obligations légales de conservation liées à la facturation.

---

## 8. Contraintes spécifiques

- Devise unique : MAD.
- Règle globale unique pour l’ensemble des agences (MVP).
- Compatible environnement multi-agences.
- Cohérent avec le workflow d’annulation défini dans la fonctionnalité "Gestion des modifications et annulations".