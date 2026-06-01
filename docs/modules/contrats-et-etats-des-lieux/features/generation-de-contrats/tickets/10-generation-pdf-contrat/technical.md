# Spécification technique : Génération PDF contrat

## 1. Composants impliqués

- ContractService
- SignatureService
- PdfGenerationService
- AuditService
- Object Storage (S3-compatible)

---

## 2. Déclenchement technique

Lors de l’appel :

POST /api/v1/contracts/{id}/sign

Après :
- Validation de la signature.
- Mise à jour du statut → SIGNED.
- Enregistrement signedAt (UTC).

Le backend déclenche :
- Génération du PDF (synchrone ou asynchrone selon configuration).

---

## 3. Source des données

Le PDF est généré à partir d’un **ContractSnapshot** figé comprenant :
- Données réservation.
- Données client.
- Données véhicule.
- Tarification détaillée.
- Assurances.
- Contenus CGU (mini CMS).
- Signature (image stockée).

Le snapshot est basé sur la version du contrat au moment de la signature.

---

## 4. Génération du PDF

### 4.1 Mécanisme

- Template HTML multilingue (FR/EN).
- Moteur de rendu HTML → PDF côté serveur.
- Intégration de la signature (image stockée en object storage).
- Intégration des mentions légales.

### 4.2 Sécurité et intégrité

- Ajout d’un identifiant unique (contractNumber + version).
- Possibilité d’ajout d’un hash d’intégrité.
- PDF non modifiable.

---

## 5. Stockage

- Upload du fichier PDF vers object storage.
- Chemin structuré par :
  - agencyId/
  - contractId/
  - version/
- Mise à jour du champ :
  - pdfUrl

Les anciennes versions ne sont pas supprimées.

---

## 6. Modèle de données impacté

Table Contract :
- pdfUrl (string)
- version (int)
- signedAt (timestamp)
- status (enum)

Index recommandé sur :
- reservationId
- contractNumber

---

## 7. API d’accès

Consultation :
- GET /api/v1/contracts/{id}
- GET /api/v1/contracts/{id}/pdf

Contrôles :
- JWT obligatoire.
- Vérification RBAC (rôle + agence).
- Vérification ownership pour client.

---

## 8. Gestion des versions

En cas de modification après signature :
- version++.
- Nouveau snapshot.
- Nouveau PDF généré.
- Audit log enregistré.

---

## 9. Audit

Événement enregistré :
- ContractPdfGenerated

Payload minimal :
- contractId
- version
- userId (si déclenché manuellement)
- timestamp

---

## 10. Performance

- Génération asynchrone recommandée pour éviter blocage UI.
- File d’attente possible (job queue).
- Timeout contrôlé.

Le service reste stateless et compatible avec montée en charge.