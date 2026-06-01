# Ticket : Génération PDF contrat

## 1. Objectif

Permettre la génération automatique du PDF officiel du contrat de location après signature électronique du client, conformément aux exigences légales marocaines et aux règles du module « Contrats et états des lieux ».

Le PDF généré constitue la version contractuelle définitive, figée et non modifiable.

---

## 2. Déclenchement

La génération du PDF est déclenchée automatiquement lorsque :
- Le contrat passe au statut **Signé**.
- La signature électronique est correctement enregistrée et horodatée.

La génération ne doit pas être possible si le contrat n’est pas signé.

---

## 3. Contenu du PDF

Le PDF doit inclure :

- Numéro unique du contrat.
- Version du contrat.
- Identité complète du client.
- Informations véhicule (catégorie, modèle, immatriculation).
- Agence de départ et de retour.
- Dates et heures de location.
- Détail tarifaire complet :
  - Prix journalier.
  - Saisonnalité.
  - Options.
  - Promotions.
  - Kilométrage (limité/illimité).
- Assurances et franchises appliquées.
- Montant total estimatif en MAD.
- Conditions générales (CGU) et mentions légales issues du mini CMS.
- Signature électronique du client.
- Horodatage de signature.
- Identifiant unique ou référence d’intégrité du document.

Le document est généré dans la langue du contrat (FR ou EN).

---

## 4. Règles de gestion

- Le PDF est généré côté serveur.
- Une fois généré, il est considéré comme version officielle et ne peut être modifié.
- En cas de modification contractuelle après signature :
  - Une nouvelle version du contrat est créée.
  - Un nouveau PDF est généré.
  - Les anciennes versions restent archivées.
- Le PDF est accessible :
  - Depuis le back-office (selon rôle et agence).
  - Depuis l’espace client.
- Le document est archivé de manière sécurisée conformément aux obligations légales.

---

## 5. Disponibilité et accès

- Téléchargement direct depuis la fiche contrat.
- Accès en lecture seule.
- Respect des règles RBAC multi-agences.
- Accès client limité à ses propres contrats.

---

## 6. Contraintes réglementaires

- Devise obligatoire : MAD.
- Conformité aux mentions légales marocaines.
- Conservation conforme aux exigences légales.
- Respect RGPD (accès et portabilité des documents).