# Fonctionnalité : Assurances et franchises configurables

## 1. Objectif métier

Permettre à l’administrateur de configurer les formules d’assurance et les montants de franchise applicables aux locations, afin de :

- Définir les couvertures incluses ou optionnelles.
- Paramétrer les montants de franchise par catégorie ou véhicule.
- Proposer des options de réduction de franchise payantes.
- Garantir la transparence tarifaire vis-à-vis du client (portail et contrat).

Cette fonctionnalité alimente le moteur de calcul tarifaire et le contrat de location.

---

## 2. Périmètre

Inclus dans le MVP :

- Création, modification et désactivation de formules d’assurance.
- Paramétrage des franchises par catégorie ou véhicule.
- Lien entre assurance, franchise et option de réduction.
- Intégration automatique dans le calcul de prix.
- Affichage des informations d’assurance sur le portail client et dans le contrat.
- Journalisation des modifications.

Hors périmètre :

- Gestion de sinistre détaillée (traitée dans le module Sinistres).
- Intégration avec assureur externe.

---

## 3. Gestion des assurances

### 3.1 Création d’une assurance

Pour chaque assurance, l’utilisateur autorisé peut définir :

- Nom (FR / EN).
- Description (FR / EN).
- Type :
  - Incluse dans le tarif.
  - Optionnelle (en supplément).
- Prix (si optionnelle) en MAD.
- Mode de facturation :
  - Par jour.
  - Forfait.
- Catégories de véhicules concernées.
- Agence concernée (ou globale).
- Statut : active / inactive.

### 3.2 Règles de gestion

- Une assurance incluse ne génère pas de ligne tarifaire supplémentaire visible comme option payante, mais doit apparaître dans le détail du contrat.
- Une assurance optionnelle sélectionnée doit être intégrée au calcul total.
- Une assurance inactive ne peut plus être sélectionnée pour de nouvelles réservations.
- Les textes doivent être affichables dans la langue choisie (FR/EN).

---

## 4. Gestion des franchises

### 4.1 Paramétrage de la franchise

Pour chaque catégorie ou véhicule, il est possible de définir :

- Montant de franchise en MAD.
- Assurance associée (si applicable).
- Agence (ou globale).

Règles :

- Une catégorie doit avoir au maximum une franchise active par période donnée.
- Le montant de franchise doit être clairement communiqué lors de la réservation.
- Toute modification n’impacte pas les réservations déjà confirmées (snapshot conservé).

### 4.2 Réduction de franchise

Le système permet d’associer à une franchise :

- Une option payante de réduction.
- Un nouveau montant de franchise réduit.

Règles :

- La réduction de franchise est proposée comme option sélectionnable.
- Si l’option est choisie, le montant de franchise applicable au contrat est remplacé par le montant réduit.
- Le détail doit apparaître dans le contrat et dans le calcul tarifaire.

---

## 5. Intégration au parcours de réservation

### 5.1 Portail client

- Affichage des assurances incluses dans l’offre.
- Proposition des assurances optionnelles.
- Affichage clair du montant de franchise standard.
- Affichage du montant de franchise réduit si option sélectionnée.

### 5.2 Back-office

- Visualisation des assurances sélectionnées.
- Modification possible avant validation finale (selon droits).
- Recalcul automatique du total.

---

## 6. Règles de conformité et transparence

- Tous les montants sont en MAD.
- Les informations d’assurance doivent être visibles avant paiement.
- Les conditions doivent être cohérentes avec le contrat généré.
- Les modifications sont tracées dans le journal d’audit.

---

## 7. Critères d’acceptation

- ✅ Un administrateur peut créer, modifier et désactiver une assurance.
- ✅ Une assurance optionnelle sélectionnée impacte correctement le total de la réservation.
- ✅ La franchise standard s’affiche correctement sur le portail client.
- ✅ La sélection d’une réduction de franchise modifie le montant de franchise contractuel.
- ✅ Les modifications de paramètres sont journalisées.
- ✅ Les réservations existantes conservent les valeurs initiales même après modification des règles.

---

## 8. Droits d’accès

- Création / modification : superAdmin, adminSociete.
- Consultation : responsables d’agence (selon périmètre).
- Aucun accès client à la configuration.

Toutes les actions de configuration sont soumises au journal d’audit.