## Spécification technique – Moteur de facturation TVA Maroc

### 1. Architecture

Implémentation côté backend dans l’API REST versionnée `/api/v1`.

Composants :
- InvoiceService (logique métier principale).
- InvoiceNumberService (gestion séquence légale).
- VatService (récupération taux TVA actifs).
- PdfGenerationService.
- Intégration JournalAuditService.

Le calcul et la validation financière sont exclusivement réalisés côté serveur.

---

### 2. Flux principal

1. Création facture en statut `draft` à partir d’un `contractId`.
2. Génération des `InvoiceLine` à partir des données du contrat.
3. Calcul backend des montants HT/TVA/TTC.
4. Passage au statut `issued` :
   - Début transaction DB.
   - Attribution numéro via séquence PostgreSQL.
   - Recalcul et validation cohérence montants.
   - Mise à jour statut + `issuedAt`.
   - Commit transaction.
5. Génération PDF.
6. Enregistrement entrée journal d’audit.

---

### 3. Numérotation légale

- Utilisation d’une séquence PostgreSQL dédiée (ex : `invoice_number_seq`).
- Attribution via `nextval()` dans une transaction atomique.
- Contrainte unique sur `invoiceNumber`.
- Aucun rollback ne doit permettre la réutilisation du numéro.

Option recommandée :
- Table de configuration par agence si évolution future, mais MVP séquence globale conforme au module parent.

---

### 4. Calculs financiers

- Types numériques : `numeric(15,2)`.
- Arrondi à 2 décimales (mode standard financier).
- Calcul par ligne :
  - `totalLineHt = quantity × unitPriceHt`
  - `totalLineTva = totalLineHt × tvaRate`
  - `totalLineTtc = totalLineHt + totalLineTva`
- Totaux facture = somme agrégée des lignes.

Validation obligatoire avant émission :
- Somme lignes HT = totalHt.
- Somme lignes TVA = totalTva.
- totalHt + totalTva = totalTtc.

---

### 5. Gestion TVA

- Table `VatRate` (ou équivalent paramétrage).
- Vérification que chaque ligne utilise un taux valide.
- Le taux est stocké dans la ligne pour garantir l’intégrité historique.

---

### 6. Génération PDF

- Génération serveur via template HTML → PDF.
- Injection des données issues de la base.
- Stockage dans object storage S3-compatible.
- Enregistrement du chemin/URL dans la table `Invoice`.
- Accès sécurisé via contrôle RBAC.

Régénération interdite si facture déjà émise (sauf action spécifique future).

---

### 7. Sécurité et audit

- Endpoint protégé par RBAC.
- Journalisation obligatoire :
  - Création facture.
  - Émission.
  - Attribution numéro.
- Logs techniques en cas d’échec génération PDF.
- HTTPS obligatoire.

---

### 8. Performance et robustesse

- Transaction DB pour éviter incohérences.
- Gestion concurrence forte lors émission (verrouillage transactionnel).
- Compatible charge cible 50–200 utilisateurs internes.
- Idempotence recommandée sur endpoint d’émission pour éviter double numérotation.

---

### 9. Contraintes projet

- Devise forcée à "MAD" côté backend.
- API documentée via OpenAPI.
- Aucun stockage de données carte bancaire.
- Respect RGPD (les données client incluses dans facture sont celles nécessaires à l’obligation légale de conservation).