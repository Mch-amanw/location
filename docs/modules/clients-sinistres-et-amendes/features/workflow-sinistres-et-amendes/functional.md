# Fonctionnalité : Workflow sinistres et amendes

## 1. Objectif métier

Mettre en place un **workflow structuré et traçable** pour la gestion des sinistres et des amendes liés aux locations, depuis leur réception ou déclaration jusqu’à leur clôture et, le cas échéant, leur refacturation au client.

Cette fonctionnalité vise à :
- Sécuriser le traitement des incidents.
- Garantir l’imputabilité correcte au contrat et au client.
- Automatiser la refacturation conforme à la législation marocaine (TVA).
- Assurer un suivi clair des statuts et des paiements.
- Alimenter le reporting et le journal d’audit.

---

## 2. Périmètre

La fonctionnalité couvre :
- Le cycle de vie complet d’un sinistre.
- Le cycle de vie complet d’une amende.
- L’affectation automatique ou assistée au contrat concerné.
- La génération de facturation complémentaire.
- Le suivi des paiements associés.
- La traçabilité des changements de statut.

Hors périmètre : gestion des relations assureur externes ou intégrations officielles avec autorités (non prévues au MVP).

---

## 3. Workflow des sinistres

### 3.1 Déclenchement

Un sinistre peut être :
- Déclaré lors de l’état des lieux retour.
- Enregistré manuellement par un utilisateur interne.

Il doit être obligatoirement rattaché à :
- Un contrat de location.
- Un véhicule.
- Un client.

### 3.2 Étapes du workflow

Statuts possibles :
1. **Ouvert** – sinistre enregistré.
2. **En évaluation** – estimation des coûts et vérification franchise.
3. **Décision refacturation** – validation du montant refacturable.
4. **Refacturé** – facture complémentaire générée.
5. **Clôturé** – dossier finalisé (réparations réalisées et paiement traité si applicable).

### 3.3 Règles de gestion

- Le montant refacturable dépend de la franchise et des conditions d’assurance configurées dans le contrat.
- Le coût réel peut être supérieur au montant refacturé (reste à charge société).
- Une facture complémentaire peut être générée automatiquement.
- Toute modification du montant ou du statut doit être journalisée.
- Un sinistre clôturé ne peut plus être modifié (sauf par rôle autorisé avec traçabilité).

---

## 4. Workflow des amendes

### 4.1 Réception

Une amende peut être :
- Saisie manuellement.
- Enregistrée après réception d’un document officiel.

Elle doit être liée à :
- Un véhicule.
- Une date d’infraction.

Le système doit permettre d’identifier le contrat actif couvrant la date d’infraction.

### 4.2 Étapes du workflow

Statuts possibles :
1. **Reçue** – amende enregistrée.
2. **Contrat identifié** – contrat correspondant trouvé.
3. **Affectée au client** – validation de l’imputation.
4. **Refacturée** – facture générée.
5. **Payée** – paiement enregistré.
6. **Contestée** – en cas de litige.
7. **Clôturée** – traitement finalisé.

### 4.3 Règles de gestion

- Une amende ne peut être refacturée qu’après identification d’un contrat valide.
- Le montant refacturé correspond au montant officiel (sauf règles internes spécifiques).
- La génération de facture respecte la TVA marocaine si applicable.
- Les changements de statut sont obligatoirement journalisés.
- Une amende payée ou clôturée devient non modifiable (hors droits exceptionnels).

---

## 5. Refacturation et paiements

- La refacturation génère une facture complémentaire liée au client et au contrat.
- La facture peut être réglée :
  - Manuellement (back-office).
  - Via paiement en ligne si activé.
- Le statut du sinistre ou de l’amende doit être synchronisé avec le statut de paiement.

---

## 6. Droits et responsabilités

Selon RBAC :
- Agent comptoir / responsable agence : création et mise à jour (dans leur périmètre agence).
- Comptabilité : validation des montants et refacturation.
- SuperAdmin / Admin société : accès global et corrections exceptionnelles.

Les données sont cloisonnées par agence (multi-agences).

---

## 7. Critères d’acceptation

- ✅ Un sinistre peut être créé uniquement s’il est lié à un contrat valide.
- ✅ Une amende doit être automatiquement associée au contrat couvrant la date d’infraction.
- ✅ La génération d’une facture complémentaire crée une écriture conforme TVA.
- ✅ Tout changement de statut est visible dans le journal d’audit.
- ✅ Les statuts sont cohérents avec l’état de paiement.
- ✅ Les listings sont filtrables par agence, statut et période.

---

## 8. Impacts reporting

Les données alimentent :
- Nombre de sinistres par agence et véhicule.
- Montants refacturés.
- Amendes en attente de traitement.
- Délais moyens de clôture.