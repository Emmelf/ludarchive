# Règles de contribution — Ludarchive

Ce document décrit la méthode de travail suivie sur le dépôt : organisation, branches, commits, pull requests, fusion, livraison et critères de fin de tâche.

## Contexte

Ludarchive est un projet de formation individuel (IPSSI, Concepteur Développeur d'Applications). Le code est public pour consultation, mais les contributions externes ne sont pas acceptées.

## Organisation du travail

- Méthode [Scrum](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-French.pdf) adaptée au travail en solo, en sprints de **2 semaines**.
- Suivi sur le tableau GitHub Projects : [Ludarchive](https://github.com/users/Emmelf/projects/4).
- Circuit d'une issue : *Backlog* → *Ready* (sprint planning) → *In progress* → *In review* (pull request ouverte) → *Done* (pull request fusionnée).

## Issues

**Une tâche = une issue**, créée à partir d'un modèle : *Fonctionnalité*, *Bug* ou *Tâche*.

### Priorité

| Priorité | Sens |
|---|---|
| P0 | Critique ou bloquant : mise en place, bug qui casse le site |
| P1 | Important : nécessaire à l'objectif du produit |
| P2 | Souhaitable : peut être allégé ou repoussé en cas de retard |

### Découpage en sous-issues

**Une pull request = un type de commit.** Une tâche qui en couvre plusieurs, ou trop grosse pour une seule pull request, devient une **issue parente** découpée en **sous-issues**.

| | Issue parente | Sous-issue |
|---|---|---|
| Rôle | regroupe et mesure l'avancement | porte le travail |
| Branche et pull request | aucune | une chacune, avec `Closes #` + numéro de la sous-issue |
| Sprint | aucun | celui où elle est faite |
| Fermeture | manuelle, quand toutes les sous-issues sont terminées | automatique, à la fusion |

Une sous-issue de fonctionnalité est **verticale** : elle livre une fonctionnalité utilisable de bout en bout (API, interface, tests), faisable en un ou deux jours.

Exemple :

```
Comptes et authentification
├─ Configurer l'envoi d'emails                       → build: configurer l'envoi d'emails avec Brevo
├─ S'inscrire par email avec vérification de l'adresse → feat: ajouter l'inscription par email
├─ Se connecter et se déconnecter                     → feat: ajouter la connexion et la déconnexion
└─ Réinitialiser son mot de passe par email           → feat: ajouter la réinitialisation du mot de passe
```

## Branches

Modèle de branches inspiré de [GitFlow](https://nvie.com/posts/a-successful-git-branching-model/).

| Branche | Rôle |
|---|---|
| `main` | Versions stables uniquement |
| `develop` | Intégration du travail en cours (branche par défaut) |
| `type/numero-description` | Une branche par issue, créée depuis `develop` (depuis `main` pour un correctif urgent, voir *Livraison*) |

Format des branches de travail : `type/numero-description`, en minuscules, mots séparés par des tirets. Le `type` reprend ceux des commits ; le `numero` est celui de l'issue traitée (attribué par GitHub à sa création).

Exemples : `docs/3-cdcf`, `feat/14-wishlist`, `fix/31-note-moyenne`.

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
| `docs` | Documentation uniquement | `docs: ajouter le CDCF` |
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
| MAJEUR + 1 | Première version stable, puis changement incompatible | `0.7.0` → `1.0.0` |

Quand un chiffre augmente, ceux à sa droite repassent à 0. Les versions `0.x` désignent le produit en développement ; la livraison du dernier sprint du projet est la version `1.0.0`.

### Fin de sprint

Après la sprint review, si l'incrément respecte la *Definition of Done* :

1. Ouvrir une pull request `develop` → `main`, titre `chore: livraison du sprint N`, description : objectif du sprint et pull requests incluses (supprimer les sections de Definition of Done du modèle).
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

- **PHP** : normes [PSR](https://www.php-fig.org/psr/) (PSR-12 pour le style).
- **React** : règles du linter du projet.
- **Aucun secret** dans le dépôt : identifiants et clés d'API uniquement dans `.env.local` (non versionné) et, pour la CI, dans les secrets GitHub.