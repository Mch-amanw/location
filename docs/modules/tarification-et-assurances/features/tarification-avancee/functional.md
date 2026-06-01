## Fonctionnalité : Tarification avancée

### 1. Objectif métier

La fonctionnalité **Tarification avancée** permet de configurer et d’appliquer les règles de calcul du prix de location d’un véhicule, en intégrant :

- Le prix par jour.
- Les règles saisonnières.
- Les politiques de kilométrage (limité / illimité).
- Les promotions.

Elle constitue le socle du calcul tarifaire utilisé lors de la simulation, de la réservation, de la génération du contrat et de la facturation.

---

### 2. Périmètre

Inclus dans cette fonctionnalité :

- Définition des tarifs de base par catégorie ou véhicule.
- Application de saisons tarifaires sur une période donnée.
- Configuration du kilométrage (plafond, illimité, coût au km supplémentaire).
- Application de codes promotionnels selon des conditions définies.
- Intégration de ces règles dans le moteur de calcul.

Exclus du périmètre :

- Gestion des options payantes.
- Configuration détaillée des assurances et franchises.
- Paramétrage de la TVA (géré par le module Facturation).

---

### 3. Règles de gestion

#### 3.1 Prix par jour

- Chaque tarif est rattaché à une catégorie de véhicule ou à un véhicule spécifique.
- Un tarif peut être global ou spécifique à une agence.
- Une période de validité (date début / date fin) est obligatoire.
- Les montants sont exprimés en MAD.
- Les conflits de période doivent être empêchés ou signalés selon la stratégie définie au niveau module.

#### 3.2 Saisonnalité

- Une saison est définie par une plage de dates.
- Elle peut :
  - Remplacer le prix journalier (override), ou
  - Appliquer une majoration (montant fixe ou pourcentage).
- Une saison s’applique en fonction des dates effectives de location.
- En cas de chevauchement, une règle de priorité cohérente avec le module parent est appliquée.

#### 3.3 Kilométrage

- Chaque tarif inclut une politique de kilométrage :
  - Illimité.
  - Limité avec plafond (ex : X km par jour ou par location).
- En cas de kilométrage limité :
  - Un coût par kilomètre supplémentaire est défini.
  - Le dépassement est calculé au retour du véhicule.
- La politique de kilométrage doit être affichée clairement au client.

#### 3.4 Promotions

- Une promotion est activée via un code.
- Types de remise :
  - Pourcentage.
  - Montant fixe.
- Conditions possibles :
  - Période de validité.
  - Catégorie ou agence.
  - Durée minimale de location.
- Une promotion ne peut pas générer un total négatif.
- La promotion est appliquée après calcul du montant brut (tarif + saison).
- L’utilisation d’un code promo est tracée dans la réservation.

---

### 4. Calcul du prix

Le calcul suit l’ordre logique suivant :

1. Détermination du tarif de base applicable (catégorie/véhicule + agence + période).
2. Application des règles saisonnières sur les dates de location.
3. Calcul du montant total brut en fonction du nombre de jours.
4. Application de la promotion si valide.
5. Transmission du total au moteur global (options, assurances, TVA).

Un détail du calcul doit être conservé (snapshot) dans la réservation.

---

### 5. Critères d’acceptation

- ✅ Un administrateur peut créer un tarif journalier avec période de validité.
- ✅ Le système empêche la création de périodes incohérentes.
- ✅ Une saison correctement configurée modifie le prix lors d’une simulation.
- ✅ Une location avec kilométrage limité génère un coût supplémentaire en cas de dépassement.
- ✅ Un code promo valide réduit le total selon les règles définies.
- ✅ Un code promo invalide est rejeté avec message explicite.
- ✅ Le détail du calcul est visible dans la réservation (pour audit et support).
- ✅ Toute modification d’un tarif ou d’une promotion est journalisée.

---

### 6. Contraintes spécifiques

- Devise unique : MAD.
- Compatible avec gestion multi-agences.
- Affichage multilingue (FR/EN) des éléments exposés au client.
- Transparence tarifaire conformément aux exigences légales marocaines et RGPD.