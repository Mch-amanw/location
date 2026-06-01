## Ticket : Moteur de facturation TVA Maroc

### 1. Objectif

Implémenter le moteur central de facturation conforme à la législation marocaine, incluant :
- Le calcul automatique de la TVA.
- L’attribution d’un numéro de facture séquentiel et non réutilisable.
- La génération d’un document PDF conforme en devise MAD.

Ce moteur constitue le cœur opérationnel de la fonctionnalité **Facturation conforme Maroc** et s’appuie sur les données issues des contrats de location.

---

### 2. Périmètre fonctionnel

Le moteur de facturation couvre :
- La transformation d’un contrat validé en facture.
- Le calcul des montants HT, TVA et TTC.
- L’application des taux de TVA paramétrés.
- La génération du numéro légal lors du passage au statut "émise".
- La production d’un PDF conforme aux mentions obligatoires.

Hors périmètre :
- Gestion des paiements (traitée par le sous-module Paiements).
- Déclarations fiscales.
- Intégration comptable externe.

---

### 3. Règles fonctionnelles

#### 3.1 Devise
- Toutes les factures générées sont exclusivement en MAD.
- Aucun paramétrage multi-devise n’est autorisé dans le MVP.

#### 3.2 Construction des lignes de facture

À partir du contrat de location, le moteur doit générer des lignes correspondant à :
- Location (calculée selon durée et tarif applicable).
- Options payantes.
- Assurances complémentaires.
- Frais additionnels (retard, dépassement kilométrique, pénalités).
- Éventuels sinistres ou amendes refacturées.

Chaque ligne comporte :
- Libellé clair.
- Quantité.
- Prix unitaire HT.
- Taux de TVA appliqué.
- Montants calculés HT, TVA et TTC.

---

#### 3.3 Calcul TVA

Le moteur doit :
- Appliquer le taux de TVA en vigueur selon le paramétrage actif.
- Calculer automatiquement :
  - Total HT (somme des lignes HT).
  - Total TVA (somme des TVA par ligne).
  - Total TTC.
- Garantir la cohérence entre les lignes et les totaux.
- Appliquer des arrondis standards à 2 décimales.

---

#### 3.4 Numérotation légale

- Le numéro de facture est généré uniquement lors du passage au statut "émise".
- Il est unique, séquentiel et non réutilisable.
- Une facture annulée conserve son numéro.
- Une facture en brouillon ne possède pas encore de numéro définitif.

---

#### 3.5 Génération PDF

Le moteur doit générer un PDF incluant :
- Informations société.
- Informations agence émettrice.
- Informations client.
- Numéro de facture.
- Date d’émission.
- Détail des lignes.
- Totaux HT, TVA, TTC.
- Devise MAD.

Le PDF est :
- Stocké de manière sécurisée.
- Téléchargeable depuis le back-office et l’espace client.

---

#### 3.6 Intangibilité

- Une facture "émise" ne peut pas être modifiée.
- Toute correction nécessite un avoir (hors périmètre direct du ticket mais doit être techniquement compatible).
- Toute émission et génération de numéro est tracée dans le journal d’audit.

---

### 4. Rôles concernés

- Agent comptoir : création et émission de facture liée au contrat.
- Responsable agence : validation et émission.
- Comptabilité : supervision.
- SuperAdmin / Admin Société : paramétrage TVA.

Le moteur applique les règles indépendamment de l’interface (back-office ou autre).