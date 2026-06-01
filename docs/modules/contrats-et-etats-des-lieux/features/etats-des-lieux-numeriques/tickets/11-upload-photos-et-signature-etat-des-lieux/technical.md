# Ticket : Upload photos et signature état des lieux – Spécification technique

## 1. Entités impactées

### 1.1 Inspection

Utilisation des champs existants :
- id
- status (draft | signed)
- signedAt
- signedByUserId
- pdfUrl

Aucune modification structurelle majeure si déjà conforme à la spec parente.

### 1.2 InspectionPhoto

Champs :
- id (UUID)
- inspectionId (FK)
- fileUrl (string)
- createdAt (timestamp serveur)
- metadata (JSON : taille, mimeType, éventuellement dimensions)

Index sur inspectionId.

### 1.3 Signature

Deux options selon implémentation :

- Stockage comme fichier image lié à Inspection (recommandé).
- Ou stockage base64 temporaire puis conversion en image stockée sur object storage.

Champs supplémentaires possibles dans Inspection :
- signatureFileUrl (string)
- signatureIp (string)

---

## 2. API REST

### 2.1 Upload photo

POST /api/v1/inspections/{id}/photos

Contraintes :
- JWT requis.
- Vérification RBAC + agence.
- Vérifier inspection.status == draft.
- Vérifier taille et type MIME (image uniquement).

Traitement :
- Compression serveur.
- Upload vers object storage.
- Création InspectionPhoto en base.
- Log audit (ajout photo).

---

### 2.2 Suppression photo

DELETE /api/v1/inspections/{photoId}

Contraintes :
- Inspection associée en draft.
- RBAC valide.

Suppression logique ou physique selon stratégie stockage.

---

### 2.3 Signature

POST /api/v1/inspections/{id}/sign

Payload :
- signature (image base64 ou fichier)

Étapes :
1. Vérifier RBAC.
2. Vérifier inspection.status == draft.
3. Enregistrer signature (upload object storage).
4. Mettre à jour :
   - status = signed
   - signedAt = now()
   - signedByUserId = utilisateur courant
   - signatureIp = IP requête
5. Appel PdfGenerationService.
6. Stocker pdfUrl.
7. Écrire entrée audit log.
8. Émettre événement InspectionCompleted.

Transaction DB atomique pour éviter incohérence.

---

## 3. Génération PDF

Le PdfGenerationService :

- Charge les données Inspection + Contract + Photos.
- Injecte :
  - Signature.
  - Horodatage.
  - Photos compressées.
- Template multilingue (FR/EN).
- Génère PDF via moteur HTML → PDF.
- Stocke PDF sur object storage.
- Retourne URL.

Possibilité d’exécution asynchrone si volume élevé.

---

## 4. Sécurité

- Validation stricte MIME type (image/jpeg, image/png).
- Limite taille fichier configurable (ex: 5MB par photo).
- TLS obligatoire.
- Stockage hors base (object storage).
- URLs privées (accès via API sécurisée ou URL signée).
- Journalisation :
  - Ajout photo.
  - Suppression photo.
  - Signature.

---

## 5. Verrouillage logique

Dans InspectionService :

- Toute tentative d’update si status == signed → rejet.
- Toute tentative d’ajout/suppression photo si status == signed → rejet.

Contrôle également au niveau API.

---

## 6. Performance

- Compression automatique des images.
- Index sur inspectionId.
- Pagination éventuelle si grand nombre de photos.

Architecture stateless compatible montée en charge.

---

## 7. Intégrations

- ContractService : vérification transitions après signature.
- Module Notifications : envoi éventuel PDF signé.
- Module Sinistres : exploitation des photos retour.
- Journal d’audit : enregistrement systématique des actions.

Documentation mise à jour via OpenAPI/Swagger.