## Architecture
- Frontend : Next.js (React, TypeScript), authentification OAuth2/OIDC, gestion des rôles (RBAC).
- Backend : FastAPI (Python), architecture modulaire.
- Composants : API Gateway, Evaluation Engine, Reporting Service, Monitoring Service, Data Lake.
- Communication : REST JSON, OpenAPI 3.
- Conteneurisation : Docker.
- Orchestration : Kubernetes.

## Multi-tenancy
- Base de données partagée.
- Isolation logique via champ obligatoire `tenantId` sur toutes les entités métier.
- Filtrage systématique côté backend.
- Tests automatiques garantissant l’absence de fuite inter-tenant.

## Modèle de déploiement
- SaaS mutualisé (cluster partagé, isolation logique).
- Option Enterprise single-tenant : cluster Kubernetes dédié + base dédiée.
- Cloud principal choisi par client (AWS, Azure ou GCP).

## Backend – Modules
1. Auth & Access Control
   - OAuth2 / OIDC.
   - SSO (Azure AD, Google Workspace) réservé Enterprise.
   - RBAC : CAIO, Data Scientist, DSI, Direction.
   - Audit logs horodatés.

2. Evaluation Engine
   - Intégrations : OpenAI, Claude, Gemini, Mistral.
   - Framework d’évaluation pondéré.
   - Calcul du Score Global sur 100 selon pondérations fixes.
   - Stockage des métriques détaillées par modèle et par exécution.

3. Benchmark Service
   - Comparaison multi-modèles sur un même dataset.
   - Historisation des résultats.
   - Classement dynamique.

4. Reporting Service
   - Génération de rapports PDF.
   - Inclusion : scores, graphiques, conformité, classification AI Act.

5. Monitoring Service
   - Évaluations planifiées.
   - Détection dérive performance.
   - Système d’alertes.

## API REST
- POST /evaluate
- GET /results/{evaluationId}
- POST /benchmark
- GET /report/{evaluationId}
- Authentification requise (Bearer JWT).

## Données
- Base relationnelle (PostgreSQL recommandé).
- Stockage objets (S3/Azure Blob/GCS) pour rapports et datasets.
- Data Lake pour historisation et analyses longitudinales.

## Sécurité
- TLS 1.3 en transit.
- AES-256 au repos.
- JWT signés.
- Journalisation complète des actions.
- Conformité RGPD (suppression, export données).

## CI/CD
- Environnements : dev, staging, prod.
- Pipeline CI : tests unitaires, intégration, sécurité.
- Déploiement automatisé sur cluster Kubernetes.

## Critères d’acceptation techniques
- Calcul exact du Score Global selon pondérations définies.
- Isolation stricte des données par tenantId.
- API conforme OpenAPI et testée.
- Génération PDF fiable et exploitable.
- Authentification et audit logs opérationnels.