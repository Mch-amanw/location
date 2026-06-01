# Fonctionnalité : Génération de contrats

## 1. Objectif métier

Permettre la création automatique d’un contrat de location à partir d’une réservation validée, avec génération d’un document contractuel conforme à la réglementation marocaine, signature électronique du client et production d’un PDF officiel archivé.

Cette fonctionnalité sécurise juridiquement la location, formalise les conditions tarifaires et constitue la base de la facturation et du suivi opérationnel.

---

## 2. Périmètre

Inclus dans la fonctionnalité :
- Création automatique du contrat à partir d’une réservation.
- Pré-remplissage des données contractuelles (client, véhicule, agence, dates, tarifs, assurances, options).
- Gestion des statuts du contrat.
- Signature électronique du contrat.
- Génération et archivage du PDF contractuel.
- Mise à disposition du contrat dans le back-office et l’espace client.

Hors périmètre :
- Génération des états des lieux (gérés par une autre fonctionnalité du module).
- Paiement en ligne (géré par le module Paiement, mais conditionnant le passage en statut "En cours").

---

## 3. Déclenchement

La génération du contrat est possible lorsque :
- Une réservation est validée.
- Un véhicule est attribué.
- Les conditions tarifaires sont figées.

La création peut être :
- Automatique (selon configuration).
- Déclenchée manuellement par un utilisateur interne autorisé (agent comptoir, responsable agence, admin).

---

## 4. Contenu du contrat

Le contrat inclut au minimum :
- Informations client (identité, coordonnées).
- Informations véhicule (catégorie, modèle, immatriculation).
- Agence de départ et de retour.
- Dates et heures de location.
- Détail tarifaire :
  - Prix journalier.
  - Saisonnalité.
  - Options.
  - Promotions.
  - Kilométrage (limité/illimité).
- Assurances et franchises appliquées.
- Montant estimatif total en MAD.
- Conditions générales (CGU, assurances) issues du mini CMS.
- Numéro unique de contrat.

Le contrat est généré dans la langue sélectionnée par le client (FR ou EN).

---

## 5. Statuts du contrat

Le contrat suit les statuts suivants :
- Brouillon : généré mais non soumis à signature.
- En attente de signature : prêt à être signé.
- Signé : signature électronique effectuée.
- En cours : location démarrée (signature + conditions de paiement remplies).
- Clôturé : location terminée.
- Annulé : contrat annulé avant démarrage.

Règles :
- Un seul contrat actif par location.
- Toute modification majeure (dates, véhicule, tarif) entraîne une nouvelle version.
- Les modifications après signature sont tracées dans le journal d’audit.

---

## 6. Signature électronique

- Signature électronique du client via interface tactile (tablette ou écran).
- Horodatage serveur.
- Association à l’identité du client.
- Une fois signé, le contrat est verrouillé (lecture seule).

Toute tentative de modification après signature nécessite :
- Création d’une nouvelle version.
- Traçabilité dans le journal d’audit.

---

## 7. Génération et archivage du PDF

- Génération automatique d’un PDF non modifiable après signature.
- Numérotation cohérente et séquentielle.
- Archivage sécurisé.
- Téléchargement disponible :
  - Back-office (selon droits).
  - Espace client.

Le PDF constitue la version contractuelle officielle.

---

## 8. Règles de gestion clés

- Devise unique : MAD.
- Conformité aux mentions légales marocaines.
- Multilingue FR/EN.
- Toute action sensible (création, modification, signature, annulation) est journalisée.
- Conservation des documents conformément aux obligations légales.

---

## 9. Critères d’acceptation

- ✅ Un contrat est automatiquement généré à partir d’une réservation validée.
- ✅ Le document contient toutes les données issues de la réservation.
- ✅ La signature électronique verrouille le contrat.
- ✅ Un PDF est généré et archivé après signature.
- ✅ Le contrat est visible dans le back-office et l’espace client.
- ✅ Toute modification après signature crée une nouvelle version et une trace d’audit.
- ✅ Le contrat respecte la langue sélectionnée (FR/EN).