# Sécurité & conformité — V1

## 1. Objectif

La plateforme traite des données personnelles et financières. La sécurité doit être conçue comme une propriété du produit, pas comme une couche ajoutée en fin de projet.

## 2. Menaces principales

- fuite inter-tenant ;
- IDOR/BOLA ;
- escalade de privilèges ;
- compromission de compte ;
- abus d’API ;
- accès public aux fichiers privés ;
- modification/annulation frauduleuse de paiements ;
- falsification de justificatifs ;
- fuite de secrets ;
- attaques via uploads ;
- erreurs de synchronisation offline ;
- abus de l’API WhatsApp/email ;
- accès excessif de l’admin plateforme ;
- mauvais paramétrage de cache ;
- jobs asynchrones hors scope.

## 3. Isolation multi-tenant

Chaque requête tenant-scoped doit dériver son contexte depuis l’identité authentifiée et vérifier l’appartenance/autorisation côté serveur.

Ne jamais faire confiance à un `tenant_id` fourni uniquement par le client.

Préférer :
- PostgreSQL RLS ou équivalent ;
- politiques de stockage ;
- scopes serveur ;
- tests cross-tenant.

La documentation OWASP actuelle recommande de tester explicitement les limites inter-tenant, y compris sur les requêtes, caches, stockage, files asynchrones et restaurations. [Référence : OWASP Multi-Tenant Security Cheat Sheet]

## 4. Autorisation

Appliquer le principe :
Rôle + organisation + portée + ressource.

Exemples :
- gestionnaire limité à certaines unités ;
- technicien limité à ses interventions ;
- locataire limité à ses données ;
- admin plateforme limité en accès privé.

Les contrôles doivent exister dans l’API/backend et la base, pas uniquement dans l’UI.

## 5. Paiements

Toute opération sensible doit être :
- idempotente ;
- auditée ;
- liée à un utilisateur ;
- liée à un périmètre autorisé.

Annulation :
- workflow de demande ;
- approbation ;
- journal.

## 6. Audit

Journaliser au minimum :
- connexions réussies/échouées ;
- refus d’autorisation ;
- accès à données sensibles ;
- créations/modifications/annulations de paiements ;
- changement de permissions ;
- invitations ;
- approbations ;
- actions admin ;
- événements de sécurité.

Un log doit permettre de comprendre quand, où, qui et quoi s’est produit sans exposer inutilement les données sensibles.

## 7. Authentification

- mots de passe hachés avec mécanisme robuste ;
- MFA pour comptes sensibles ;
- OTP à usage unique si utilisé ;
- expiration des sessions selon le risque ;
- rotation/révocation ;
- rate limiting ;
- protection brute force.

## 8. Fichiers

Tous les documents, photos et vidéos sensibles sont privés par défaut.

- pas de bucket public ;
- autorisation avant accès ;
- URLs temporaires ;
- type MIME et extension contrôlés ;
- taille maximale ;
- scan malware si disponible ;
- métadonnées minimisées.

## 9. Secrets

- `.env` hors Git ;
- secret manager en production ;
- aucune clé dans logs ;
- clés séparées dev/staging/prod.

## 10. CI/CD

Bloquer les merges si :
- tests échouent ;
- lint/typecheck échoue ;
- migrations invalides ;
- scan dépendances critique ;
- secrets détectés ;
- tests d’autorisation échouent.

## 11. Tests de sécurité

Créer au minimum :
- tenant A ne lit pas tenant B ;
- tenant A ne modifie pas tenant B ;
- gestionnaire hors scope refusé ;
- locataire ne lit pas autre locataire ;
- technicien ne lit pas les finances ;
- admin agence ne lit pas les données d’une autre agence ;
- admin plateforme n’a pas d’accès privé par défaut ;
- suppression directe interdite ;
- annulation de paiement sans approbation refusée ;
- fichier privé inaccessible sans autorisation ;
- token expiré refusé ;
- replay d’un OTP refusé.

## 12. Hors connexion

Aucune validation financière sensible offline.
Toutes les mutations offline doivent avoir :
- identifiant idempotent ;
- horodatage ;
- version ou séquence ;
- règle de conflit.

## 13. Côte d’Ivoire — conformité

La Loi ivoirienne n°2013-450 encadre la collecte, le traitement, la transmission, le stockage et l’utilisation des données personnelles. Le texte prévoit notamment des formalités préalables, des obligations d’information et des règles sur les transferts vers les pays tiers. Il prévoit également un régime d’autorisation préalable pour certaines catégories de traitements, notamment certains identifiants. Voir le texte publié par l’ARTCI. 

L’ARTCI rappelle actuellement aux entités privées et startups traitant des données personnelles leurs obligations de conformité et a engagé en 2026 une étude nationale de recensement et d’évaluation de conformité. 

Avant production :
1. cartographier les données ;
2. identifier responsable de traitement/sous-traitants ;
3. définir finalités et durées ;
4. préparer les formalités ARTCI applicables ;
5. analyser les hébergeurs et sous-traitants hors Côte d’Ivoire ;
6. analyser les flux WhatsApp/email ;
7. documenter les droits des personnes ;
8. préparer politique de confidentialité ;
9. préparer procédure incidents ;
10. valider avec un conseil juridique compétent.

Ce document n’est pas un avis juridique.

