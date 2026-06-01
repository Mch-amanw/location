## Spécification Technique – Ticket : Mini CMS (CGU, FAQ, assurances)

### 1. Modèle de données

Table principale : `pages`

Champs :
- id (UUID ou serial)
- slug (varchar, indexé)
- title (varchar)
- content (text)
- language (enum: FR, EN)
- status (enum: draft, published)
- created_at (timestamp)
- updated_at (timestamp)

Contraintes :
- Index sur (slug, language).
- Unicité recommandée sur (slug, language).
- Index sur status pour optimisation des requêtes publiques.

---

### 2. API Back-office (protégée JWT + RBAC)

Endpoints :

- POST /api/v1/cms/pages  
  Création d’une page.

- PUT /api/v1/cms/pages/{id}  
  Mise à jour d’une page existante.

- GET /api/v1/cms/pages  
  Liste paginée des pages (filtres possibles : status, language).

- GET /api/v1/cms/pages/{id}  
  Détail d’une page.

- DELETE /api/v1/cms/pages/{id}  
  Suppression d’une page.

Sécurité :
- Accès restreint aux rôles autorisés via middleware RBAC.
- Vérification du JWT obligatoire.

---

### 3. API publique (lecture seule)

Endpoint :

- GET /api/v1/public/pages/{slug}?lang=FR|EN

Règles :
- Retourne uniquement une page avec status = 'published'.
- Si aucune page correspondante n’est trouvée (slug + language + published), retourner 404.
- Aucun champ interne (ex : created_at, updated_at) exposé inutilement.

---

### 4. Validation et sécurité

- Validation serveur des champs obligatoires (slug, title, content, language, status).
- Validation stricte des valeurs enum (language, status).
- Protection contre XSS :
  - Sanitization du contenu HTML si éditeur riche.
  - Validation côté backend.
- Protection CSRF côté back-office si nécessaire selon architecture.

---

### 5. Intégration RBAC

- Vérification des rôles via middleware.
- Rôles autorisés : superAdmin, adminSociete.
- Les autres rôles reçoivent une erreur 403 sur endpoints CMS protégés.

---

### 6. Intégration avec le journal d’audit

Lors des actions suivantes :
- Création d’une page.
- Modification d’une page.
- Suppression d’une page.

Le système doit :
- Enregistrer une entrée dans `audit_logs` avec :
  - user_id
  - role
  - action (create_page, update_page, delete_page)
  - entity_type = 'page'
  - entity_id
  - old_value (JSONB, si applicable)
  - new_value (JSONB)
  - timestamp

---

### 7. Performance et scalabilité

- Volume attendu faible (quelques dizaines de pages).
- Requêtes simples indexées.
- Compatible architecture stateless.

---

### 8. Documentation et conformité

- Endpoints documentés via OpenAPI/Swagger.
- Respect des règles RGPD :
  - Pas de stockage de données personnelles dans les pages CMS.
- Sauvegarde incluse dans la stratégie globale de backup PostgreSQL (backup quotidien, rétention ≥ 30 jours).

Ce ticket implémente le mini CMS minimal requis pour le MVP, conforme à l’architecture REST, au modèle RBAC et au système d’audit existant.