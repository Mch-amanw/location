## Spécification Fonctionnelle – Ticket : Mini CMS (CGU, FAQ, assurances)

### 1. Objectif

Permettre aux utilisateurs autorisés d’administrer les pages légales et informatives affichées sur le portail client, notamment :
- CGU (Conditions Générales d’Utilisation / de Location)
- Informations sur les assurances
- FAQ
- Pages informatives diverses

Le mini CMS doit permettre une gestion simple, sécurisée et multilingue (FR/EN) des contenus, avec distinction entre brouillon et contenu publié.

---

### 2. Périmètre fonctionnel

Le ticket couvre :
- La gestion des pages via le back-office.
- L’affichage des pages publiées sur le portail client.
- La gestion multilingue FR/EN.
- La gestion des statuts (brouillon / publié).

Ne couvre pas :
- Gestion avancée de versions (versioning complet).
- Workflow de validation multi-étapes.

---

### 3. Gestion des pages (Back-office)

#### 3.1 Création d’une page

Un utilisateur autorisé peut :
- Créer une nouvelle page.
- Définir :
  - Un slug unique (identifiant URL).
  - Un titre.
  - Un contenu riche (texte formaté).
  - Une langue (FR ou EN).
  - Un statut (brouillon ou publié).

Chaque langue est gérée comme une entrée distincte, partageant le même slug logique.

#### 3.2 Modification d’une page

Un utilisateur autorisé peut :
- Modifier le titre.
- Modifier le contenu.
- Modifier le statut (brouillon ↔ publié).

Toute modification d’une page publiée rend immédiatement la nouvelle version visible sur le portail.

#### 3.3 Suppression d’une page

Un utilisateur autorisé peut supprimer une page.
La suppression rend la page inaccessible côté portail.

---

### 4. Statuts des pages

Deux statuts sont disponibles :
- **Brouillon** : visible uniquement en back-office.
- **Publié** : visible sur le portail client.

Règles :
- Seules les pages en statut "publié" sont accessibles publiquement.
- Une page brouillon ne doit jamais être accessible via l’API publique.

---

### 5. Multilingue (FR / EN)

- Chaque page doit pouvoir exister en français et en anglais.
- Le portail client affiche la version correspondant à la langue active.
- Si une page n’existe pas dans la langue demandée, elle n’est pas affichée.

---

### 6. Droits d’accès

Selon la politique RBAC définie au niveau du projet :

- Super Admin : gestion complète (création, modification, suppression, publication).
- Admin Société : gestion complète au niveau société.
- Responsable d’agence : pas de gestion CMS (lecture uniquement si prévu globalement).
- Agent comptoir : aucun accès au CMS.

Les opérations d’administration sont strictement réservées aux rôles autorisés.

---

### 7. Affichage côté portail client

Le portail client doit :
- Permettre l’accès aux pages via une URL basée sur le slug.
- Afficher uniquement les pages publiées.
- Respecter la langue active (FR/EN).

Les pages concernées incluent notamment :
- CGU
- Assurances
- FAQ
- Autres pages informatives définies par l’administrateur

---

### 8. Conformité et traçabilité

- Les modifications importantes (création, mise à jour, suppression) peuvent être tracées via le journal d’audit.
- Les contenus publiés doivent être conformes aux obligations légales applicables (RGPD, réglementation marocaine).

Ce mini CMS constitue la base de gestion des contenus légaux et informatifs du portail client dans le cadre du MVP.