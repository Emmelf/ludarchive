# Règles de contribution — Ludarchive

Ce document décrit la méthode de travail suivie sur le dépôt : organisation, branches, commits, pull requests, fusion, livraison et critères de fin de tâche.

## Contexte

Ludarchive est un projet de formation individuel (IPSSI, Concepteur Développeur d'Applications). Le code est public pour consultation, mais les contributions externes ne sont pas acceptées.

## Organisation du travail

- Méthode [Scrum](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-French.pdf) adaptée au travail en solo, en sprints de **2 semaines**.
- Suivi sur le tableau GitHub Projects : [Ludarchive](https://github.com/users/Emmelf/projects/5).

Circuit d'une issue sur le tableau :

| Statut | Quand | Déplacement |
|---|---|---|
| *Backlog* | création de l'issue | automatique |
| *Ready* | sprint planning | à la main |
| *In progress* | début du travail | à la main |
| *In review* | pull request ouverte | automatique |
| *Done* | pull request fusionnée | automatique |

Une issue parente n'a pas de pull request : ses passages en *In progress* et en *Done* sont faits à la main.

## Issues

**Une tâche = une issue**, créée à partir d'un modèle : *Fonctionnalité*, *Bug* ou *Tâche*.

### Priorité

| Priorité | Sens |
|---|---|
| P0 | Critique ou bloquant : mise en place, bug qui casse le site |
| P1 | Important : nécessaire à l'objectif du produit |
| P2 | Souhaitable : peut être allégé ou repoussé en cas de retard |

Avec la méthode MoSCoW : les *Must have* (le MVP) sont en P0 ou P1, les *Should* et *Could have* en P2.

### Découpage en sous-issues

**Une pull request = un type de commit.** Une issue qui en couvre plusieurs, ou qui dépasse deux jours de travail, devient une **issue parente** découpée en **sous-issues**.

| | Issue parente | Sous-issue |
|---|---|---|
| Contenu | le besoin : user story, critères d'acceptation | un livrable ; titre = titre de sa pull request, sans le type |
| Branche et pull request | aucune | une chacune, avec `Closes #` + numéro de la sous-issue |
| Sprint | celui où elle se termine ; aucun si elle s'étale sur plusieurs sprints (*epic*) | celui de sa parente |
| Priorité | selon le tableau ci-dessus | celle de sa parente |
| Statut | déplacé à la main : *In progress* à la première sous-issue commencée, *Done* après la dernière | circuit normal |

Une sous-issue de code est **verticale** : utilisable de bout en bout (données, API, interface, tests). Les travaux de documentation ou d'outillage dont la parente a besoin (maquette, modèle de données, configuration) sont des sous-issues à part.

Exemple :

```
Importer un jeu depuis IGDB par son nom (parente, Feature, sprint 1)
├─ Documenter les données IGDB utilisées    → docs: documenter les données IGDB utilisées
├─ Concevoir le MCD du catalogue            → docs: concevoir le MCD du catalogue
├─ Importer un jeu principal par son nom    → feat: importer un jeu principal par son nom
└─ Rattacher les portages et les éditions   → feat: rattacher les portages et les éditions au jeu de base
```

## Branches

Modèle de branches inspiré de [GitFlow](https://nvie.com/posts/a-successful-git-branching-model/).

| Branche | Rôle |
|---|---|
| `main` | Versions stables uniquement |
| `develop` | Intégration du travail en cours (branche par défaut) |
| `type/numero-description` | Une branche par issue, créée depuis `develop` (depuis `main` pour un correctif urgent, voir *Livraison*) |

Format des branches de travail : `type/numero-description`, en minuscules, mots séparés par des tirets. Le `type` reprend ceux des commits ; le `numero` est celui de l'issue traitée (attribué par GitHub à sa création).

Exemples : `docs/1-contributing`, `feat/14-wishlist`, `fix/31-note-moyenne`.

`main` et `develop` sont protégées : aucun push direct, tout passe par une pull request.

## Commits

Convention [Conventional Commits](https://www.conventionalcommits.org/fr/), sans portée, messages en français :

```
type: description courte
```

- **type** : la nature du changement (tableau ci-dessous) ;
- **description** : à l'infinitif, en minuscules, sans point final.

Quand la convention s'applique :

- **obligatoire pour le titre de chaque pull request** : à la fusion, ce titre devient le message du commit enregistré dans la branche cible (voir *Fusion*) ;
- **libre pour les commits d'une branche de travail** : ils restent visibles dans la pull request mais n'arrivent pas dans l'historique de `develop`.

Pas de portée (`type(portée): …`) : la plupart des issues touchent à la fois l'API et le front, et le numéro de pull request ajouté au titre mène déjà à l'issue et à tout son contexte.

### Types

| Type | Usage | Exemple |
|---|---|---|
| `feat` | Nouvelle fonctionnalité | `feat: ajouter le bouton wishlist` |
| `fix` | Correction de bug | `fix: corriger le calcul de la note moyenne` |
| `docs` | Documentation uniquement | `docs: ajouter le cahier des charges` |
| `test` | Ajout ou correction de tests | `test: tester la fusion des comptes Google` |
| `refactor` | Réorganisation du code sans changer le comportement | `refactor: extraire le service d'import IGDB` |
| `style` | Mise en forme sans effet sur le code | `style: appliquer le formatage` |
| `ci` | Pipelines GitHub Actions | `ci: lancer PHPUnit à chaque push` |
| `build` | Dépendances, Docker, outils de build | `build: ajouter le service PostgreSQL` |
| `chore` | Maintenance diverse | `chore: mettre à jour le .gitignore` |

## Pull requests

1. Ouvrir la pull request de la branche de travail **vers `develop`** (vers `main` pour un correctif urgent, voir *Livraison*).
2. **Titre** au format des commits : `type: description` (le numéro de la PR est ajouté automatiquement à la fusion).
3. **Description** : remplir le modèle : section *Description*, `Closes #N` (numéro de l'issue), uniquement la catégorie de Definition of Done de la PR. L'issue se ferme automatiquement à la fusion dans `develop`, la branche par défaut.
4. **Definition of Done** : avant la review, l'auteur coche chaque case dont il a vérifié le critère (la case des tests une fois la CI verte). Les cases se cochent directement dans la pull request.
5. **Review** : relire le code dans l'onglet *Files changed*. En équipe, le relecteur termine par **Review changes** : *Approve*, ou *Request changes* avec des commentaires ; l'auteur corrige sur sa branche puis clique sur *Re-request review*. En solo, l'auteur fait lui-même cette relecture.
6. Fusionner une fois toutes les cases cochées et la review validée.

## Fusion

| Pull request | Méthode |
|---|---|
| Branche de travail → `develop` | **Squash and merge** (un commit par issue) |
| `develop` → `main` | **Create a merge commit** |
| Branche de correctif urgent → `main` | **Squash and merge** |
| `main` → `develop` (après un correctif urgent) | **Create a merge commit** |

Les fusions entre `main` et `develop` ne se font jamais en squash : les deux branches doivent garder un historique commun, sinon elles divergent et provoquent des conflits.

## Livraison

Les versions suivent le [versionnage sémantique](https://semver.org/lang/fr/) `MAJEUR.MINEUR.CORRECTIF` :

| Changement | Quand | Exemples |
|---|---|---|
| MINEUR + 1 | Livraison de fin de sprint (nouvelles fonctionnalités) | `0.1.0` → `0.2.0`, `1.1.0` → `1.2.0` |
| CORRECTIF + 1 | Correctif urgent entre deux sprints | `0.2.0` → `0.2.1` |
| MAJEUR + 1 | Première version stable, puis changement incompatible | `0.6.0` → `1.0.0` |

Quand un chiffre augmente, ceux à sa droite repassent à 0. Les versions `0.x` désignent le produit en développement, même mis en production ; la livraison finale du projet est la version `1.0.0`.

### Fin de sprint

Après la sprint review, si l'incrément respecte la *Definition of Done* :

1. Ouvrir une pull request `develop` → `main`, titre `chore: livrer la version` + numéro (par exemple `chore: livrer la version 0.2.0`), description : objectif du sprint et pull requests incluses (supprimer les sections de Definition of Done du modèle).
2. Fusionner avec **Create a merge commit**.
3. Créer la release : **Releases › Draft a new release**, tag de la nouvelle version (par exemple `v0.2.0`), cible `main`, **Generate release notes**, compléter le résumé (objectif, livré, reporté), **Publish release**.

### Correctif urgent (hotfix)

Pour corriger un bug en production sans livrer le travail en cours de `develop` :

1. Créer une issue (modèle *Bug*), puis une branche `fix/numero-description` **depuis `main`**.
2. Ouvrir une pull request vers **`main`**, titre `fix: description`, fusionner en **Squash and merge**.
3. Fermer l'issue à la main : la fermeture automatique (`Closes #N`) ne fonctionne que pour les fusions dans la branche par défaut (`develop`).
4. Créer la release de la version corrective (par exemple `v0.2.1`) sur `main`.
5. Ouvrir une pull request `main` → `develop` et fusionner avec **Create a merge commit**, pour que la correction soit aussi dans le travail en cours.

## Definition of Done

Une issue passe en *Done* uniquement si tous les critères de sa catégorie sont remplis. La liste de critères se trouve dans le [modèle de pull request](.github/pull_request_template.md) : elle est pré-remplie dans chaque PR et cochée avant la fusion.

| Catégorie | Types de commits concernés |
|---|---|
| Documentation | `docs` |
| Code | `feat`, `fix`, `refactor`, `test`, `style` |
| Outillage | `ci`, `build`, `chore` |

Dans tous les cas, la tâche n'est terminée qu'une fois la pull request fusionnée et l'issue fermée.

La pull request de livraison (`develop` → `main`) n'a pas de Definition of Done propre : chaque tâche livrée a déjà été validée dans sa propre pull request.

## Règles de code

- **PHP** : normes [PSR](https://www.php-fig.org/psr/), style PSR-12 appliqué par PHP-CS-Fixer ; analyse statique par PHPStan.
- **JavaScript et React** : mise en forme par Prettier ; analyse par ESLint.
- Ces outils tournent dans l'éditeur et dans la CI : une pull request dont la CI échoue n'est pas fusionnée.
- **Aucun secret** dans le dépôt : en local dans `.env.local` (non versionné), en production dans les variables d'environnement du serveur, pour la CI dans les secrets GitHub. Aucun secret côté front : les variables `VITE_*` sont lisibles dans le navigateur.
