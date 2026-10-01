# PRD V1 — Plateforme de gestion immobilière en Côte d’Ivoire

## 1. Vision

Créer une plateforme de gestion immobilière utilisable dans toute la Côte d’Ivoire par :
- propriétaires particuliers ;
- agences immobilières ;
- gestionnaires ;
- locataires.

Le produit commence comme outil de gestion et pourra devenir plus tard une marketplace immobilière.

## 2. Positionnement fonctionnel

Le produit n’est pas une simple application de suivi de loyers.
Il gère le cycle :
Bien → Unité → Locataire → Contrat → Loyer → Paiement → Impayé → Maintenance → Documents → Rapports.

## 3. Utilisateurs

### Propriétaire particulier
Peut gérer ses propres biens directement et inviter ses locataires.

### Propriétaire géré par agence
Peut confier certains biens à une ou plusieurs agences et superviser leurs activités.

### Agence
Peut gérer plusieurs propriétaires et plusieurs biens.

### Gestionnaire
Travaille dans le périmètre autorisé.

### Comptable
Accède aux données financières autorisées.

### Responsable
Supervise certaines opérations et validations.

### Technicien
Interne ou externe, limité aux interventions assignées.

### Locataire
Accède uniquement à ses propres données.

### Admin plateforme
Supervise la plateforme sans accès libre aux données privées.

## 4. Parcours principaux

### Onboarding propriétaire particulier
Invitation/activation selon le modèle prévu.
Création du patrimoine.
Ajout d’unités.
Création/invitation de locataires.

### Onboarding agence
Demande → vérification complète → validation → activation.
Création de gestionnaires.
Invitation de propriétaires.
Relation de gestion proposée puis acceptée.
Sélection des biens confiés.

### Onboarding locataire
Invitation par WhatsApp et/ou email → activation → accès au logement/compte autorisé.

## 5. Modules V1

1. Authentification et onboarding
2. Tableau de bord propriétaire
3. Biens
4. Unités
5. Locataires
6. Contrats
7. Loyers
8. Paiements
9. Impayés
10. Caution
11. Maintenance
12. Documents
13. Messagerie
14. Notifications
15. Rapports
16. Support/tickets
17. Assistant IA de support
18. Administration plateforme
19. Gestion des rôles et permissions
20. Audit/sécurité
21. Mode hors connexion limité

## 6. Écrans V1

### Public
- Accueil
- Connexion
- Activation de compte
- Mot de passe oublié
- Aide/Support

### Propriétaire
- Dashboard
- Liste des biens
- Ajouter un bien
- Fiche bien
- Ajouter/modifier une unité
- Fiche unité
- Locataires
- Fiche locataire
- Loyers
- Échéances
- Paiements
- Ajouter un paiement
- Détail paiement
- Impayés
- Maintenance
- Demande maintenance
- Documents
- Messages
- Notifications
- Rapports
- Paramètres

### Agence
- Dashboard agence
- Propriétaires
- Demandes de relation
- Biens gérés
- Gestionnaires
- Rôles/permissions
- Locataires
- Loyers
- Paiements
- Impayés
- Maintenance
- Techniciens
- Documents
- Messages
- Rapports
- Support

### Locataire
- Accueil
- Mon logement
- Mon loyer
- Mes paiements
- Mes documents
- Maintenance
- Messages
- Notifications
- Support

### Admin plateforme
- Dashboard
- Agences
- Validation agence
- Propriétaires
- Utilisateurs
- Support
- Sécurité/audit
- Paramètres plateforme
- Statistiques

## 7. Modèle de données logique

Entités centrales recommandées :
- users
- organizations
- organization_memberships
- owner_profiles
- agency_profiles
- management_relationships
- properties
- units
- unit_assignments
- tenant_profiles
- leases
- rent_schedules
- rent_installments
- payments
- payment_allocations
- payment_adjustments
- payment_cancellation_requests
- deposits
- deposit_transactions
- maintenance_requests
- maintenance_interventions
- technicians
- maintenance_costs
- agency_owner_advances
- documents
- media_assets
- conversations
- messages
- notifications
- notification_preferences
- monthly_reports
- support_tickets
- support_messages
- audit_logs
- invitations
- security_events

Chaque entité sensible doit avoir un modèle clair de propriété/scope.

## 8. Règles d’acceptation clés

### Bien
- impossible d’activer un bien incomplet ;
- référence unique ;
- propriétaire unique ;
- accès contrôlé.

### Unité
- référence technique immuable ;
- référence affichée personnalisable ;
- un seul locataire actif ;
- statut cohérent avec la location.

### Loyer
- calendrier généré à partir du contrat ;
- échéance configurable ;
- retard automatique J+1.

### Paiement
- reçu automatique ;
- allocation automatique ;
- justificatif selon moyen de paiement ;
- audit des corrections ;
- validation des annulations.

### Maintenance
- priorité ;
- SLA ;
- technicien ;
- validation gestionnaire ;
- confirmation locataire ;
- réouverture si non résolue.

### Documents
- stockage privé ;
- accès autorisé uniquement ;
- traçabilité.

### Rapports
- mensuels ;
- calculs cohérents ;
- PDF ;
- email.

## 9. Hors périmètre V1

- marketplace de recherche/location ;
- calcul automatique des commissions d’agence ;
- colocation/multi-occupants ;
- génération de contrats ;
- IA analytique métier ;
- recommandations financières automatisées.

## 10. KPI techniques V1

- taux de succès des tests ;
- erreurs 5xx ;
- latence API ;
- temps de chargement dashboard ;
- taux d’échec des jobs ;
- taux de livraison notifications ;
- incidents sécurité ;
- taux de synchronisation offline ;
- disponibilité ;
- temps de restauration.

