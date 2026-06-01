# 1. Présentation générale

## 1.1 Objectif du projet
Mettre en place une solution SaaS multi-tenant de gestion de location de voitures permettant :
- La gestion complète d’une flotte de véhicules.
- La réservation en ligne par les clients finaux.
- La gestion opérationnelle via un back-office.
- Le suivi administratif, financier et comptable.

La solution couvre l’ensemble du cycle de location : réservation → contrat → état des lieux → facturation → caution → restitution → gestion des sinistres et pénalités.

## 1.2 Périmètre
Le système inclut :
- Une plateforme web SaaS multi-tenant.
- Un back-office pour les sociétés de location.
- Une interface de réservation en ligne pour les clients.
- Un super-admin global pour la gestion de la plateforme.

Hors périmètre MVP :
- Application mobile (prévue en phase 2).
- API publique (prévue en phase 2).
- Self-onboarding des sociétés (phase 2).

---

# 2. Utilisateurs

## 2.1 Super-admin (plateforme)
- Création manuelle des sociétés (MVP).
- Gestion globale des abonnements.
- Supervision globale.

## 2.2 Administrateur (par société)
- Gestion des agences.
- Gestion des véhicules.
- Gestion des tarifs.
- Gestion des utilisateurs internes.
- Accès aux actions sensibles.

## 2.3 Agent d’agence
- Gestion des réservations.
- Création et gestion des contrats.
- États des lieux départ/retour.
- Encaissements.

Certaines actions sensibles sont réservées aux administrateurs.

## 2.4 Comptable
- Accès aux factures.
- Suivi des paiements.
- Accès aux rapports financiers.

## 2.5 Client final
- Consultation des véhicules disponibles.
- Réservation en ligne.
- Paiement.

---

# 3. Gestion multi-tenant

- Cloisonnement logique des données par société.
- Chaque société peut créer ses propres administrateurs et agents.
- Modèle économique : abonnement mensuel selon nombre de véhicules et/ou d’agences.

---

# 4. Gestion des véhicules

## 4.1 Catégories
- Économiques
- Berlines
- SUV
- Utilitaires
- Électriques

## 4.2 Fiche véhicule
- Caractéristiques.
- Kilométrage.
- Statut (disponible, loué, immobilisé).

## 4.3 Maintenance et conformité
- Suivi entretien.
- Assurance.
- Contrôle technique.
- Immobilisation.

---

# 5. Réservation et disponibilité

## 5.1 Calendrier
- Calendrier en temps réel par véhicule.
- Gestion multi-agences.

## 5.2 Processus de réservation
- Réservation en ligne.
- Confirmation automatique après paiement.
- Validation manuelle possible par un agent.

## 5.3 Tarification
- Tarification à la journée.
- Saisonnière.
- Promotions.
- Options supplémentaires.

## 5.4 Options et assurances
- Rachat de franchise.
- Conducteur additionnel.
- GPS.
- Siège bébé.

---

# 6. Contrat et états des lieux

## 6.1 Contrat
- Génération du contrat de location.

## 6.2 États des lieux
- Photos.
- Signature électronique.
- Comparaison automatique départ/retour.

---

# 7. Gestion financière

## 7.1 Paiements
- Carte bancaire.
- Espèces.
- Virement.
- Paiement mobile.

## 7.2 Caution
- Empreinte bancaire.
- Saisie manuelle possible.
- Débit ou ajustement en cas de sinistre.

## 7.3 Facturation
- Génération automatique de factures PDF.
- Numérotation légale.
- Gestion de TVA.

## 7.4 Sinistres
- Génération de facture complémentaire.
- Débit de caution.

## 7.5 Retards
- Calcul automatique des pénalités.

---

# 8. Reporting et tableaux de bord

- Chiffre d’affaires.
- Taux d’occupation.
- Rentabilité par véhicule.
- Performance par agence.
- Dashboards adaptés selon le rôle.

---

# 9. Notifications

- Emails automatiques.
- SMS automatiques.
- Confirmation de réservation.
- Rappels.
- Retards.
- Impayés.

---

# 10. Internationalisation

- Multilingue.
- Multi-devises.

---

# 11. Conformité

- RGPD.
- Archivage légal.
- Journal d’audit des actions sensibles.

---

# 12. Roadmap

## MVP
- SaaS multi-tenant.
- Onboarding manuel.
- Back-office complet.
- Réservation en ligne.

## Phase 2
- Application mobile.
- API publique.
- Self-onboarding.


---

# Technical Specification

## 1. Architecture

### 1.1 Type d’application
- Application web en mode SaaS.
- Architecture multi-tenant avec cloisonnement logique des données.
- Super-admin global.

### 1.2 Multi-tenancy
- Isolation logique par identifiant de société.
- Gestion des rôles par société.

---

## 2. Modules applicatifs

- Module Authentification & gestion des rôles.
- Module Gestion des sociétés (super-admin).
- Module Gestion flotte.
- Module Réservation & planning temps réel.
- Module Contrat & états des lieux.
- Module Paiement & caution.
- Module Facturation (PDF + TVA).
- Module Reporting.
- Module Notifications (Email/SMS).
- Module Audit & conformité.

---

## 3. Paiements

- Intégration paiement carte bancaire.
- Gestion empreinte bancaire.
- Gestion paiements manuels (espèces, virement, mobile).

---

## 4. Documents

- Génération automatique de PDF (factures).
- Numérotation légale.
- Archivage conforme.

---

## 5. Sécurité

- Gestion fine des rôles.
- Journal d’audit des actions sensibles.
- Conformité RGPD.
- Cloisonnement strict des données par société.

---

## 6. Reporting

- Agrégation des données par agence.
- Calcul automatique taux d’occupation.
- Calcul rentabilité par véhicule.

---

## 7. Internationalisation

- Support multi-langue.
- Support multi-devise.

---

## 8. Scalabilité

- Conçu pour plusieurs sociétés clientes.
- Modèle d’abonnement basé sur nombre de véhicules/agences.

---

## 9. Roadmap technique

### MVP
- Web app SaaS.
- Onboarding manuel.

### Phase 2
- API publique.
- Application mobile.
- Self-service onboarding.


---

# Context Summary

Le projet consiste à développer une solution SaaS multi-tenant de gestion de location de voitures destinée à plusieurs sociétés de location. La plateforme comprend un back-office complet et une interface de réservation en ligne pour les clients finaux. Elle gère l’ensemble du cycle de location : disponibilité en temps réel, contrats, états des lieux avec photos et signature électronique, paiements multi-moyens, cautions par empreinte bancaire, facturation légale avec TVA, sinistres et pénalités de retard. Le système intègre également la gestion de flotte (maintenance, assurance, immobilisation), des dashboards par rôle, des notifications automatiques, ainsi que la conformité RGPD et un journal d’audit. Le modèle économique repose sur un abonnement mensuel basé sur le nombre de véhicules ou d’agences. Une phase 2 prévoit une API publique, une application mobile et le self-onboarding des sociétés.