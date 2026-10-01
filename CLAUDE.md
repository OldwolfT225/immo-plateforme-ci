# CLAUDE.md — Projet plateforme de gestion immobilière

## 1. Mission

Construire une plateforme SaaS de gestion immobilière pour la Côte d’Ivoire, mobile-first, en français et en XOF, destinée :
- aux propriétaires particuliers ;
- aux agences immobilières ;
- aux gestionnaires, responsables, comptables et techniciens ;
- aux locataires.

La V1 est une plateforme de gestion immobilière. La marketplace de recherche/location de biens est hors périmètre V1 et doit seulement être anticipée architecturalement.

## 2. Règles absolues

1. Ne jamais mélanger les données entre organisations, propriétaires, agences, biens, unités ou utilisateurs.
2. Ne jamais considérer l’interface comme une barrière de sécurité : toutes les autorisations doivent être contrôlées côté serveur et au niveau de la base de données.
3. Ne jamais supprimer silencieusement les données financières ou historiques. Utiliser l’archivage, l’annulation contrôlée et l’audit.
4. Toute annulation de paiement doit être soumise à approbation :
   - propriétaire particulier : propriétaire ;
   - agence : administrateur d’agence.
5. Une unité ne peut avoir qu’un seul locataire actif en V1.
6. Un propriétaire peut posséder plusieurs biens.
7. Un même propriétaire peut confier différents biens à plusieurs agences en même temps.
8. Une agence ne voit et ne gère que les biens explicitement confiés par le propriétaire.
9. Les gestionnaires peuvent être autorisés au niveau agence, bien ou unité.
10. Les paiements et opérations sensibles doivent être idempotents et auditables.
11. Les opérations financières sensibles sont bloquées hors connexion ; la maintenance peut fonctionner en mode brouillon/hors connexion.
12. L’IA est limitée au support client en V1. Elle ne prend pas de décisions financières.
13. Ne jamais utiliser `--dangerously-skip-permissions` pour le développement normal.
14. Ne jamais mettre de secrets/API keys dans le dépôt, les fichiers source, les prompts ou les logs.
15. Chaque fonctionnalité importante doit avoir des tests automatisés et des critères d’acceptation.

## 3. Stack cible

Proposer et documenter avant implémentation une stack stable et maintenable :
- TypeScript strict.
- Next.js / React pour l’application web responsive mobile-first.
- PostgreSQL.
- Authentification et stockage privé compatibles avec un modèle SaaS multi-tenant.
- PWA pour installation sur téléphone.
- Architecture permettant une future application Android/iOS sans refaire le domaine métier.
- Jobs planifiés pour échéances, rappels, rapports, contrats et SLA.
- API/serveur avec contrôles d’autorisation côté serveur.
- Stockage privé des documents/photos/vidéos avec URL temporaires/signées.
- Tests unitaires, intégration et E2E.
- CI/CD et migrations versionnées.

Le fournisseur cloud final ne doit pas être figé avant validation de la stratégie de protection des données et des transferts internationaux.

## 4. Modèle métier central

Organisation/tenant → Propriétaire → Bien → Unité → Location → Locataire

Relations complémentaires :
- Agence ↔ Propriétaire via une relation de gestion acceptée.
- Agence → Bien(s) explicitement confiés.
- Utilisateur → organisation(s) et rôles.
- Gestionnaire → périmètre agence/bien/unité.
- Technicien → interventions assignées seulement.

## 5. Rôles V1

### Plateforme
- PLATFORM_ADMIN

### Agence
- AGENCY_ADMIN
- RESPONSIBLE
- MANAGER
- ACCOUNTANT
- TECHNICIAN

### Externes
- OWNER
- TENANT

Les rôles doivent être combinés avec des permissions et un scope de ressources. Prévoir une architecture de type RBAC + règles contextuelles/attributaires.

## 6. Permissions et portée

Un utilisateur ne doit accéder qu’aux ressources autorisées par :
- son identité ;
- son organisation ;
- son rôle ;
- son périmètre ;
- son lien avec le propriétaire/bien/unité.

Toute requête sensible doit vérifier ces éléments côté serveur.

## 7. Règles financières

### Loyers
Périodicités :
- mensuelle ;
- trimestrielle ;
- semestrielle ;
- annuelle ;
- personnalisée.

La date d’échéance est définie par le propriétaire/contrat.

Un loyer devient automatiquement « en retard » le lendemain de l’échéance si le montant n’est pas soldé.

### Paiements
Moyens V1 :
- espèces ;
- virement bancaire ;
- chèque ;
- Orange Money ;
- MTN Money ;
- Moov Money ;
- Wave ;
- autre.

Le justificatif est configurable par moyen de paiement et peut être obligatoire.

Après chaque paiement :
- générer un reçu unique ;
- afficher dans l’application ;
- permettre PDF ;
- permettre envoi WhatsApp ;
- permettre envoi email.

Paiement unique couvrant plusieurs échéances :
- allocation automatique par ordre chronologique des échéances ouvertes ;
- conserver une seule opération de paiement ;
- conserver le détail des allocations.

Paiement anticipé :
- peut solder l’échéance future correspondante lorsque les échéances antérieures sont soldées.

Surplus :
- ne pas transformer automatiquement en crédit ;
- signaler le surplus au gestionnaire pour décision.

Correction :
- modification autorisée selon permissions ;
- conserver ancienne et nouvelle valeur ;
- journaliser acteur, date/heure, motif si configuré.

Annulation :
- demande d’annulation ;
- validation requise ;
- audit complet ;
- pas de suppression physique de la trace.

## 8. Contrats

Le logiciel ne génère pas les contrats en V1.
Le gestionnaire téléverse le contrat existant.

Association :
Propriétaire → Bien → Unité → Locataire → Contrat.

7 jours avant la date de fin :
- alerte automatique.

Le renouvellement V1 se fait par création d’un nouveau contrat et téléversement du nouveau document.
Le nouveau loyer du contrat actif remplace le loyer actif ; l’ancien reste dans l’historique.

## 9. Caution

Suivre :
- montant demandé ;
- montant payé ;
- solde ;
- dates de versement ;
- retenues éventuelles ;
- montant restitué ;
- historique.

## 10. Occupation des unités

Une unité ne peut avoir qu’un seul locataire actif en V1.
Pas de gestion des occupants associés en V1.

Statuts d’unité :
- OCCUPEE
- LIBRE
- EN_NETTOYAGE
- EN_REPARATION
- EN_RENOVATION
- DISPONIBLE

## 11. Gestion d’un bien

À la création, les champs métier définis sont obligatoires :
- nom ;
- type ;
- pays ;
- ville ;
- commune ;
- quartier ;
- adresse/indication ;
- propriétaire ;
- photos ;
- description ;
- nombre d’unités.

Le nom est librement choisi par le propriétaire ou l’agence.
La plateforme génère une référence unique et stable.

Création des unités :
- génération en masse ;
- création/modification individuelle.

Les références d’unités peuvent être générées automatiquement puis personnalisées. Conserver un identifiant technique immuable.

## 12. Fiche unité

Champs obligatoires :
- référence ;
- type personnalisé ;
- étage ;
- superficie ;
- nombre de pièces ;
- description ;
- photos/vidéos ;
- état ;
- statut ;
- loyer actuel ;
- date d’échéance ;
- locataire actuel ;
- caution ;
- contrat.

Chaque unité possède sa galerie propre.

## 13. Maintenance

Locataire :
- crée une demande ;
- décrit ;
- ajoute photo/vidéo ;
- choisit/indique une priorité.

Priorités :
- FAIBLE
- NORMALE
- ELEVEE
- URGENTE

Le système gère un SLA configurable, avec valeurs par défaut proposées :
- urgente : 24 h ;
- élevée : 48 h ;
- normale : 3 jours ;
- faible : 7 jours.

Workflow :
Nouvelle → Prise en charge → Technicien affecté → Intervention → Validation gestionnaire → Confirmation locataire → Résolue/Fermée.

Si le locataire indique que le problème persiste :
- rouvrir automatiquement ;
- alerter le gestionnaire ;
- permettre une nouvelle intervention.

Techniciens :
- internes + externes ;
- accès très limité aux interventions assignées.

Validation des coûts :
- gestionnaire ;
- responsable agence ;
- propriétaire.
Le paiement du technicien peut être effectué par l’agence ou le propriétaire.
Si l’agence avance les frais, suivre l’avance, le remboursement et le solde à récupérer.

## 14. Documents

Documents :
- contrats ;
- pièces d’identité ;
- reçus/caution ;
- quittances ;
- état des lieux ;
- factures/travaux ;
- documents propriétaires ;
- photos/vidéos ;
- autres documents autorisés.

Stockage privé, contrôle d’accès avant délivrance, URLs temporaires si nécessaire.

## 15. Messagerie / notifications

Canaux :
- application ;
- WhatsApp ;
- email.

Modes :
- conversation individuelle ;
- message groupé ;
- annonce à tous les locataires d’un périmètre.

Conserver l’historique dans la plateforme.
Prévoir un adaptateur WhatsApp séparé du domaine métier.

Notifications automatiques :
- rappel avant échéance ;
- notification à l’échéance ;
- retard dès le lendemain ;
- impayé ;
- confirmation de paiement ;
- maintenance ;
- documents ;
- contrat à 7 jours de l’expiration ;
- invitations ;
- rapports mensuels ;
- sécurité/action nécessitant validation.

L’utilisateur personnalise ses préférences, mais certaines notifications critiques restent obligatoires.

## 16. Rapports

Rapports automatiques mensuels pour propriétaire et agence selon permissions.
Inclure au minimum :
- biens/unités ;
- occupés/libres ;
- loyers attendus ;
- encaissés ;
- restant ;
- impayés ;
- maintenance ;
- dépenses si activées ;
- activité.

Génération PDF et diffusion email.

## 17. Dépenses

Module optionnel.
Le propriétaire peut choisir de l’utiliser ou non.

Pas de calcul automatique des commissions/frais d’agence en V1.

## 18. Centre de support

Ticketing :
Nouveau → En cours → En attente → Résolu → Fermé

Assistant IA V1 :
- FAQ ;
- guidage dans l’application ;
- aide à créer un ticket ;
- préqualification ;
- transfert vers humain.

Ne pas autoriser l’IA à prendre des décisions financières ou de sécurité.

## 19. Hors connexion

Autorisé :
- lecture de données déjà synchronisées ;
- consultation des biens/unités accessibles ;
- brouillon de maintenance ;
- photos/vidéos ;
- file d’attente de certaines opérations non sensibles.

Bloqué hors connexion :
- validation définitive d’un paiement ;
- annulation de paiement ;
- correction financière sensible ;
- opérations nécessitant une autorisation forte.

Synchronisation automatique au retour du réseau avec stratégie de conflit explicite.

## 20. Sécurité obligatoire

- MFA/authentification renforcée pour comptes sensibles.
- Contrôle d’accès serveur.
- Isolation multi-tenant forte.
- RLS ou équivalent au niveau base de données si adapté.
- Tests systématiques anti cross-tenant.
- Journal d’audit append-only pour événements sensibles.
- Chiffrement en transit et au repos selon les capacités de la stack.
- Hashage sécurisé des mots de passe.
- Rate limiting.
- Protection contre IDOR/BOLA.
- CSRF/XSS/injection.
- Validation serveur de tous les champs.
- Vérification de type/taille des fichiers.
- Antivirus/scanning des uploads si disponible.
- Limitation de taille et type pour photos/vidéos/PDF.
- Secrets hors Git.
- Environnements dev/staging/prod séparés.
- Sauvegardes et tests de restauration.
- Monitoring et alerting.
- Journaliser authentification, refus d’autorisation et accès sensibles sans exposer les données sensibles.

## 21. Confidentialité / Côte d’Ivoire

Le produit traite des données personnelles (identité, téléphone, email, pièces, contrats, paiements, messages, etc.).

Avant toute mise en production avec de vraies données :
- réaliser une revue juridique et conformité Côte d’Ivoire ;
- documenter finalités, catégories de données, destinataires, durée de conservation et droits des personnes ;
- préparer les formalités applicables auprès de l’ARTCI ;
- analyser les transferts de données vers des pays tiers ;
- documenter les sous-traitants ;
- définir une politique de conservation et d’archivage ;
- désigner le responsable/correspondant protection des données selon les obligations applicables.

Ne pas considérer cette checklist technique comme un avis juridique.

## 22. Confidentialité applicative

Un administrateur plateforme n’a pas un accès libre aux données privées.
Toute intervention exceptionnelle doit être :
- autorisée ;
- justifiée ;
- limitée ;
- journalisée.

## 23. Marketplace

Hors V1.
Prévoir seulement :
- modèle extensible pour annonce ;
- statut de disponibilité ;
- galerie ;
- localisation.
Ne pas implémenter les fonctions marketplace avant validation V1.

## 24. Méthode de travail de Claude Code

Avant de coder :
1. inspecter le dépôt ;
2. lire tous les docs de `docs/` ;
3. proposer l’architecture ;
4. produire le schéma de données ;
5. produire la matrice de permissions ;
6. produire les tests de sécurité critiques ;
7. demander/attendre les validations uniquement lorsque nécessaire.

Ensuite :
- construire par tranches verticales ;
- une feature = code + migration + tests + UI + documentation ;
- lancer lint/typecheck/tests après chaque tranche ;
- ne jamais masquer les erreurs ;
- ne jamais supprimer de données de test ou de production sans autorisation explicite ;
- utiliser Git et faire des commits petits et compréhensibles.

## 25. Definition of Done globale

La V1 n’est pas considérée prête tant que :
- tous les rôles fonctionnent ;
- les règles financières passent les tests ;
- les permissions passent les tests positifs et négatifs ;
- aucun test cross-tenant ne passe ;
- les fichiers privés ne sont pas publics ;
- les audit logs sont présents ;
- la sauvegarde/restauration a été testée ;
- les notifications critiques fonctionnent ;
- les rapports mensuels fonctionnent ;
- les tests E2E principaux passent ;
- aucune migration destructive non approuvée n’existe ;
- la documentation d’installation est complète ;
- la checklist conformité a été revue avant données réelles.

## 26. Règle de comportement de Claude

Si une spécification semble ambiguë :
- choisir l’interprétation la plus sûre et la plus réversible ;
- documenter l’hypothèse ;
- ne pas inventer une règle métier sensible ;
- ne jamais sacrifier la sécurité pour accélérer le développement.

