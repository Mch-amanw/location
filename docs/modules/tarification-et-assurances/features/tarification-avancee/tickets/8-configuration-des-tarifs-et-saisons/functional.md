## Ticket : Configuration des tarifs et saisons

### 1. Objectif

Permettre aux utilisateurs autorisés (superAdmin, adminSociete) de configurer :

- Les **tarifs journaliers** par catégorie ou véhicule.
- Les **périodes de validité** associées.
- Les **saisons tarifaires** applicables aux locations.

Cette interface constitue le point d’entrée principal du paramétrage de la tarification avancée et alimente directement le moteur de calcul utilisé par la simulation, la réservation et la facturation.

---

### 2. Périmètre fonctionnel

Inclus dans ce ticket :

- Écran de gestion des tarifs (CRUD).
- Écran de gestion des saisons (CRUD).
- Validation des règles métier liées aux périodes.
- Liaison avec les catégories, véhicules et agences existants.
- Journalisation des actions.

Exclus du périmètre :

- Gestion des options payantes.
- Gestion des assurances et franchises.
- Configuration des promotions.
- Paramétrage de la TVA.

---

## 3. Gestion des tarifs journaliers

### 3.1 Création / modification d’un tarif

L’interface doit permettre de définir :

- Catégorie de véhicule OU véhicule spécifique (exclusif).
- Agence (optionnelle si tarif global).
- Prix par jour (en MAD).
- Politique de kilométrage associée (sélection existante).
- Date de début de validité.
- Date de fin de validité.

Règles métier :

- Un tarif doit être rattaché soit à une catégorie, soit à un véhicule.
- Les dates de validité sont obligatoires.
- La date de fin doit être postérieure à la date de début.
- Les montants doivent être positifs ou nuls.
- Les conflits de période pour une même combinaison (catégorie/véhicule + agence) doivent être empêchés ou clairement signalés.
- La devise est obligatoirement MAD.

### 3.2 Consultation

L’interface doit proposer :

- Une liste filtrable (par catégorie, agence, période).
- Affichage clair des périodes de validité.
- Indication visuelle des tarifs actifs / expirés / à venir.

---

## 4. Gestion des saisons

### 4.1 Création / modification d’une saison

L’interface doit permettre de définir :

- Nom de la saison.
- Date de début.
- Date de fin.
- Type d’ajustement :
  - Override (remplacement du prix journalier).
  - Surcharge.
- Valeur :
  - Montant fixe (MAD), ou
  - Pourcentage (si surcharge).

Règles métier :

- Les dates sont obligatoires.
- La date de fin doit être postérieure à la date de début.
- La valeur doit être positive ou nulle.
- Les saisons peuvent se chevaucher, mais leur priorité sera gérée par la logique définie au niveau module.
- Une saison ne supprime pas les tarifs de base ; elle les ajuste.

### 4.2 Consultation

- Liste des saisons existantes.
- Affichage des périodes.
- Indication des saisons actuellement actives.

---

## 5. Droits d’accès

- superAdmin : création, modification, suppression.
- adminSociete : création, modification, suppression.
- Autres rôles : lecture seule ou aucun accès selon RBAC global.

Toute modification doit être tracée dans le journal d’audit.

---

## 6. Intégration au moteur de calcul

Les tarifs et saisons configurés via cette interface :

- Sont immédiatement pris en compte par le moteur de simulation.
- Servent de base au calcul lors de la création d’une réservation.
- Ne modifient pas rétroactivement les réservations existantes (grâce au snapshot).

---

## 7. Contraintes spécifiques

- Devise unique : MAD.
- Compatibilité multi-agences.
- Interface disponible en français et anglais.
- Transparence et clarté des informations tarifaires (conformité légale).