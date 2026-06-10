# 1. Vision et objectifs

## Objectif du produit
Mettre à disposition une plateforme SaaS permettant la mesure standardisée de la performance des systèmes d’IA (LLM, RAG, agents), afin de :
- Détecter les hallucinations, biais et failles de sécurité.
- Vérifier la conformité réglementaire (AI Act, RGPD, ISO 42001).
- Réaliser des benchmarks multi-modèles.
- Suivre les performances dans le temps.
- Mesurer le ROI et réduire les risques liés à l’IA.

Le produit fournit un scoring global sur 100 basé sur un framework d’évaluation pondéré.

---

# 2. Utilisateurs cibles

## Rôles métiers
- **Chief AI Officer (CAIO)** : gouvernance IA, conformité, supervision globale.
- **Data Scientist** : optimisation des modèles, analyse détaillée des performances.
- **DSI** : architecture, sécurité, coûts et intégration technique.
- **Direction Générale** : vision synthétique, suivi ROI.
- **Utilisateurs SaaS authentifiés** : accès aux fonctionnalités selon leur rôle et leur plan.

---

# 3. Périmètre fonctionnel (MVP et socle produit)

## 3.1 Dashboard central
- Visualisation des modèles évalués.
- Affichage des scores détaillés et score global.
- Suivi des coûts associés.
- Alertes éventuelles.
- Vue comparative (benchmark).

## 3.2 Évaluation automatique
Évaluation des systèmes IA selon plusieurs axes :
- Accuracy
- Robustesse
- Hallucination
- Toxicité
- Autres axes définis dans le framework

Chaque évaluation :
- Est liée à un modèle et à une entreprise.
- Produit des métriques détaillées.
- Contribue au calcul d’un Score Global sur 100.

## 3.3 Framework de scoring
- Pondération fixe des axes d’évaluation : 30 / 20 / 15 / 15 / 10 / 10.
- Calcul automatique d’un Score Global.
- Score exploitable dans les dashboards et rapports.

## 3.4 Benchmark multi-modèles
- Comparaison de plusieurs modèles.
- Visualisation comparative des scores.
- Historisation des performances.

## 3.5 Monitoring continu
- Suivi dans le temps des performances.
- Détection d’écarts significatifs.
- Classification AI Act.
- Mécanismes d’explicabilité.

## 3.6 Rapports d’audit
- Génération de rapports PDF.
- Exploitables pour audit interne et conformité.
- Intégration des scores, méthodologie et résultats.

## 3.7 API REST
Endpoints principaux :
- `evaluate`
- `results`
- `benchmark`
- `report`

L’API permet l’intégration avec des systèmes tiers.

---

# 4. Règles de gestion

## 4.1 Multi-tenancy
- Base de données partagée.
- Isolation logique via `tenant_id`.
- Chaque évaluation est rattachée à une entreprise.

## 4.2 Plans tarifaires
- Limites sur le nombre de modèles.
- Limites sur le nombre d’évaluations.
- Accès restreint à certaines fonctionnalités.
- SSO réservé à l’offre Enterprise.

## 4.3 Sécurité et traçabilité
- Authentification obligatoire.
- Logs d’audit pour traçabilité complète des actions.

## 4.4 Critères d’acceptation fonctionnels
- Calcul fiable du Score Global selon les pondérations définies.
- Benchmarks comparatifs opérationnels.
- Génération de rapports PDF exploitables.
- API conforme aux endpoints définis.
- Authentification sécurisée et traçabilité complète.

---

# 1. Architecture globale

## 1.1 Typologie
Plateforme SaaS avec :
- Modèle mutualisé par défaut (multi-tenant).
- Option de déploiement dédié (single-tenant) pour clients Enterprise.

## 1.2 Composants principaux
- **Frontend** : Next.js, React, TypeScript.
- **Backend** : Python, FastAPI.
- **API Gateway** : point d’entrée centralisé.
- **Evaluation Engine** : moteur de calcul des métriques et du score.
- **Reporting Service** : génération de rapports PDF.
- **Data Lake** : stockage des données d’évaluation.

## 1.3 IA et orchestration
- LangChain
- LlamaIndex
- OpenAI SDK

---

# 2. Infrastructure et déploiement

## 2.1 Cloud
- Déploiement sur un cloud principal par client (AWS, Azure ou GCP).
- Conteneurisation via Docker.
- Orchestration via Kubernetes.

## 2.2 Modèle de déploiement
- SaaS mutualisé (multi-tenant, isolation logique via `tenant_id`).
- Option single-tenant pour Enterprise.

---

# 3. Intégrations

## 3.1 LLM externes
- GPT-4
- Claude
- Gemini
- Mistral

## 3.2 SSO
- Azure AD
- Google Workspace
- Réservé à l’offre Enterprise.

---

# 4. Sécurité

## 4.1 Authentification et autorisation
- OAuth2.
- SSO (Enterprise).
- Gestion des rôles.

## 4.2 Protection des données
- Chiffrement AES-256.
- TLS 1.3 pour les communications.
- Isolation logique des données par `tenant_id`.

## 4.3 Audit et traçabilité
- Logs d’audit complets.
- Traçabilité des actions utilisateurs.

---

# 5. Contraintes techniques

- Respect des pondérations fixes pour le scoring.
- API REST conforme aux endpoints définis.
- Architecture compatible multi-cloud (un cloud principal par déploiement).
- Compatibilité multi-LLM.
- Génération fiable et reproductible des rapports.

---

# 6. Critères d’acceptation techniques

- Calcul exact et reproductible du Score Global.
- Isolation correcte des données multi-tenant.
- API sécurisée et fonctionnelle.
- Génération de rapports PDF conforme.
- Intégrations LLM et SSO opérationnelles.

---

"contextSummary": "Le projet consiste à développer une plateforme SaaS d’évaluation et de benchmark de systèmes d’IA (LLM, RAG, agents). Elle permet de mesurer la performance, détecter hallucinations et biais, assurer la conformité (AI Act, RGPD, ISO 42001) et produire un score global pondéré sur 100. La solution inclut un dashboard central, un moteur d’évaluation, un benchmark multi-modèles, un monitoring continu et des rapports PDF d’audit. L’architecture repose sur Next.js (frontend) et FastAPI (backend), avec un moteur d’évaluation basé sur LangChain et intégrations multi-LLM. Le modèle est SaaS multi-tenant avec isolation logique via tenant_id, et option single-tenant pour les clients Enterprise, déployé sur un cloud principal au choix (AWS, Azure ou GCP)."}