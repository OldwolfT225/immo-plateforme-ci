# RUNBOOK — Lancement avec Claude Code

## 1. Prérequis

Machine Windows :
- privilégier WSL2 ou Git Bash selon l’environnement choisi ;
- Git ;
- Node.js ;
- compte GitHub ;
- compte Anthropic/Claude Code.

La documentation Anthropic indique actuellement un support de Windows via WSL ou Git for Windows et un prérequis Node.js 18+ pour Claude Code.

## 2. Initialiser le dépôt

```bash
mkdir immo-platform-ci
cd immo-platform-ci
git init
```

Ajouter :
- `CLAUDE.md`
- `docs/PRD_V1.md`
- `docs/SECURITY_AND_COMPLIANCE.md`

Puis :

```bash
git add .
git commit -m "docs: initial product specification"
```

## 3. Premier lancement Claude

Démarrer Claude dans le dossier du projet :

```bash
claude
```

Pour la première session, ne pas lui demander de coder tout le produit immédiatement.

## 4. Prompt de session 0

Copier le prompt suivant :

---

Tu es l’architecte logiciel principal et le lead developer du projet « Immo Platform CI ».

Avant d’écrire du code :
1. lis `CLAUDE.md` ;
2. lis tous les documents dans `docs/` ;
3. inspecte le dépôt ;
4. construis un plan de travail V1 ;
5. propose l’architecture technique ;
6. propose le schéma PostgreSQL ;
7. propose la matrice détaillée des rôles/permissions ;
8. propose les règles RLS/tenant isolation ;
9. propose l’architecture des jobs et notifications ;
10. propose les tests de sécurité et E2E ;
11. identifie toute ambiguïté ou contradiction dans les documents.

Ne développe rien de substantiel avant d’avoir produit ces artefacts :
- `docs/ARCHITECTURE.md`
- `docs/DATABASE_SCHEMA.md`
- `docs/PERMISSIONS_MATRIX.md`
- `docs/NOTIFICATIONS.md`
- `docs/JOBS.md`
- `docs/TEST_PLAN.md`
- `docs/THREAT_MODEL.md`

Ensuite, arrête-toi et présente :
- ce qui est certain ;
- les hypothèses réversibles ;
- les décisions à prendre ;
- l’ordre d’implémentation recommandé.

Important :
- la sécurité prime sur la rapidité ;
- aucune donnée ne doit traverser un tenant ;
- aucune autorisation ne doit dépendre de l’interface seule ;
- aucun secret dans Git ;
- ne supprime pas de données ;
- ne désactive pas les contrôles de sécurité pour faire passer les tests.

---

## 5. Phase 1 après validation de l’architecture

Demander à Claude :
- initialiser le projet ;
- configurer lint/typecheck/tests ;
- configurer CI ;
- créer la base/migrations ;
- créer seed de développement ;
- créer Auth ;
- créer modèle d’organisation/tenant ;
- créer RLS et tests inter-tenant ;
- construire le layout et design system.

## 6. Construction par vertical slices

Ordre recommandé :

### Slice 1
Auth + organisations + rôles + permissions + sécurité.

### Slice 2
Propriétaire particulier → biens → unités.

### Slice 3
Locataire → invitation → activation → dossier.

### Slice 4
Contrat → calendrier de loyers → échéances.

### Slice 5
Paiements → allocations → reçus → audit.

### Slice 6
Impayés → notifications.

### Slice 7
Maintenance → techniciens → SLA → validation.

### Slice 8
Documents + galerie.

### Slice 9
Messagerie + notifications.

### Slice 10
Rapports mensuels.

### Slice 11
Agence → propriétaires → relations de gestion → gestionnaires.

### Slice 12
Support + IA.

### Slice 13
Offline/PWA.

### Slice 14
Admin plateforme + observabilité.

## 7. Règle de livraison d’une slice

Chaque slice doit livrer :
- code ;
- migration ;
- tests ;
- UI ;
- règles d’autorisation ;
- audit ;
- documentation ;
- seed de test si nécessaire.

Après chaque slice :
```bash
npm run lint
npm run typecheck
npm test
npm run test:e2e
```
Adapter les commandes au projet si Claude choisit un autre outillage et documenter la décision.

## 8. Git

Branches :
- `main` = stable ;
- `develop` = intégration ;
- `feature/...` = fonctionnalité.

Commits :
- petits ;
- explicites ;
- un sujet par commit.

Ne jamais faire de force-push destructif sans nécessité et contrôle.

## 9. Gestion des permissions Claude Code

Commencer avec le mode plan/lecture autant que possible.
Accorder uniquement les outils nécessaires.
Examiner les commandes shell avant validation.
Ne pas utiliser `--dangerously-skip-permissions`.

Anthropic documente un modèle de permissions explicites dans Claude Code et recommande de contrôler les actions, notamment pour les commandes shell et les serveurs MCP.

## 10. Gate de pré-production

Ne pas déployer avec de vraies données tant que :
- revue sécurité passée ;
- tests cross-tenant passés ;
- sauvegarde/recovery testée ;
- stockage privé vérifié ;
- MFA comptes sensibles vérifié ;
- journalisation vérifiée ;
- conformité Côte d’Ivoire revue ;
- flux WhatsApp/email documentés ;
- politique de confidentialité finalisée ;
- support et incidents documentés ;
- monitoring actif.

