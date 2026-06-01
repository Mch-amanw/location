## Fonctionnalité : Facturation conforme Maroc

### 1. Objectif métier

Garantir que l’ensemble des factures émises par la plateforme "location 3" soient conformes à la législation marocaine en matière de facturation, notamment :
- Gestion correcte de la TVA.
- Numérotation légale séquentielle et non réutilisable.
- Émission des montants en devise MAD.
- Présentation claire et complète des mentions obligatoires.

Cette fonctionnalité vise à sécuriser l’activité comptable du réseau multi-agences et à réduire les risques juridiques et fiscaux.

---

### 2. Périmètre

La fonctionnalité couvre :
- La génération des factures à partir d’un contrat de location.
- Le calcul et l’application de la TVA paramétrable.
- La génération d’un numéro de facture séquentiel conforme.
- La production d’un document PDF conforme (format exploitable et téléchargeable).
- L’émission des montants exclusivement en MAD.

Hors périmètre :
- Intégration à un logiciel comptable externe (phase 2).
- Déclarations fiscales automatiques.

---

### 3. Règles de gestion

#### 3.1 Devise
- Toutes les factures sont émises en MAD.
- Aucune autre devise n’est autorisée au niveau du MVP.

#### 3.2 TVA
- Les taux de TVA sont paramétrables par un utilisateur habilité (SuperAdmin / Admin Société).
- Chaque ligne de facture comporte :
  - Montant HT.
  - Taux de TVA appliqué.
  - Montant de TVA calculé.
- Les totaux suivants sont obligatoires :
  - Total HT.
  - Total TVA.
  - Total TTC.
- Les calculs doivent être automatiques et cohérents avec les lignes.

#### 3.3 Numérotation légale
- Chaque facture émise possède un numéro unique, séquentiel.
- La séquence est continue et non réutilisable.
- Une facture annulée conserve son numéro.
- La génération du numéro se fait uniquement au moment de l’émission officielle (statut "émise").

#### 3.4 Mentions obligatoires
Chaque facture doit contenir :
- Informations société (raison sociale, identifiants fiscaux).
- Informations agence émettrice.
- Informations client.
- Numéro de facture.
- Date d’émission.
- Détail des prestations (location, options, assurances, frais).
- Totaux HT, TVA, TTC.
- Devise MAD.

#### 3.5 Intangibilité
- Une facture émise ne peut pas être modifiée directement.
- Toute correction doit passer par l’émission d’un avoir.
- Toute action (émission, annulation, émission d’avoir) est tracée dans le journal d’audit.

---

### 4. Critères d’acceptation

- ✅ Une facture générée affiche correctement les montants HT, TVA et TTC.
- ✅ Le taux de TVA appliqué correspond au paramétrage actif.
- ✅ Le numéro de facture est unique et séquentiel.
- ✅ Une facture annulée conserve son numéro et son historique.
- ✅ Les montants sont toujours affichés en MAD.
- ✅ Le PDF généré contient toutes les mentions obligatoires.
- ✅ Toute action sensible est visible dans le journal d’audit.

---

### 5. Rôles concernés

- SuperAdmin / Admin Société : paramétrage TVA, supervision.
- Responsable agence : émission de factures pour son agence.
- Agent comptoir : génération des factures liées aux contrats.
- Comptabilité : contrôle, émission d’avoirs.
- Client : consultation et téléchargement de ses factures.