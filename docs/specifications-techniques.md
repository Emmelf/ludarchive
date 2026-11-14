# Spécifications techniques — Ludarchive

Ce document décrit **comment** Ludarchive est réalisé : l'architecture, les technologies retenues et leur justification. Il répond aux contraintes de la section 4 du cahier des charges (`docs/Ludarchive-CDC.pdf`), qui reste la référence pour le **quoi**.

Il est tenu à jour pendant le développement : chaque choix nouveau ou modifié y est ajouté.

## 1. Architecture

Ludarchive suit une architecture **API + SPA** :

- une **API** (interface de programmation : le serveur ne renvoie que des données, au format JSON) développée avec Symfony ;
- une **SPA** (Single Page Application, application monopage : les pages s'affichent sans rechargement complet) développée avec React, qui gère toute l'interface.

**Justification.** Ludarchive est une application grand public, avec des carrousels, une recherche et des pages très dynamiques : une interface séparée permet une expérience réactive. React, avec son approche par composants, convient à une interface faite de listes, de cartes et de carrousels. La séparation facilite aussi les tests de bout en bout.

**Coût assumé.** Cette architecture demande plus de travail qu'une application Symfony monolithique (avec Twig) : deux projets, une authentification par API, une gestion des erreurs des deux côtés. Ce coût est pris en compte dans le découpage en deux lots du cahier des charges.

## 2. Composants et versions

| Rôle | Choix | Justification |
|---|---|---|
| Langage back | PHP 8.5 | Version stable la plus récente |
| Framework back | Symfony 7.4 LTS | Imposé ; LTS = support long |
| Front | React 19 | Voir section 1 |
| Base de données | PostgreSQL 19 | Recherche textuelle intégrée, relations entre fiches, type JSONB (voir section 3) |
| Conteneurs | Docker et Docker Compose | Imposé ; même environnement en développement et en production |
| Envoi d'emails | Brevo (offre gratuite, 300 emails par jour) | Service d'emails transactionnels, suffisant pour le volume attendu |
| Données des jeux | API IGDB (Twitch) | Seule base de jeux complète et gratuite pour un usage non commercial |

## 3. Stockage des données

- **Données relationnelles** : comptes, ludothèques, notes, commentaires, signalements et fiches de jeux, dans des tables PostgreSQL au schéma normalisé (conception Merise).
- **Données brutes d'IGDB** : conservées dans une colonne **JSONB** (format JSON binaire de PostgreSQL, indexable et interrogeable). Elles évitent de rappeler IGDB pour relire un jeu, et permettent d'exploiter un nouveau champ sans modifier le schéma. Ce stockage orienté documents couvre l'attendu NoSQL du projet, tout en gardant la base relationnelle imposée.
- **Recherche par titre** : recherche textuelle intégrée à PostgreSQL, pour tenir l'exigence de moins d'une seconde.
- **Images des membres** (avatars, bannières) : fichiers stockés sur le serveur de l'application. Les jaquettes ne sont pas téléchargées : ce sont des liens vers les images d'IGDB.
- **Signalements** : chaque signalement garde une copie du texte signalé.

## 4. Gestion du code et intégration continue

- **Normes de code** : PSR-12 (PHP Standards Recommendations, conventions d'écriture du PHP publiées par le PHP-FIG), détaillées dans `CONTRIBUTING.md`.
- **Dépôt** : GitHub, branches `main` et `develop`, fusions par pull request. Les règles complètes sont dans `CONTRIBUTING.md`.
- **Intégration continue** : GitHub Actions, avec trois jobs de tests lancés à chaque modification envoyée :
  - **back-end** : PHPUnit (tests unitaires et fonctionnels de l'API) ;
  - **front-end** : Vitest (tests des composants React) ;
  - **bout en bout** : Playwright (un navigateur automatisé qui simule un utilisateur).

## 5. Hébergement et mise en production

- **Serveur** : VPS (serveur privé virtuel) OVH.
- **Déploiement** : automatisé depuis GitHub Actions, par connexion SSH au serveur.
- **Adresse** : `ludarchive.com`, en HTTPS avec un certificat gratuit Let's Encrypt.
- **Sauvegardes** : sauvegarde quotidienne de la base et des images, conservée 7 jours et copiée hors du serveur.
- **Compte administrateur** : créé par une commande console Symfony (un script lancé dans le terminal du conteneur, par exemple `php bin/console app:create-admin`), jamais par un formulaire du site. C'est la pratique montrée par la documentation de Symfony et son application de démonstration.
- **Secrets** : clés d'API et mots de passe dans des variables d'environnement (`.env.local` en local, secrets GitHub pour la CI et le déploiement), jamais dans le dépôt.

## 6. Correspondance avec le cahier des charges

| Contrainte du cahier des charges | Réponse technique |
|---|---|
| 4.1 Framework Symfony | Symfony 7.4 LTS en API |
| 4.1 Base relationnelle | PostgreSQL 19 |
| 4.1 Docker | Docker Compose en développement et en production |
| 4.1 Git, branches, pull requests | GitHub, voir `CONTRIBUTING.md` |
| 4.1 Intégration continue | GitHub Actions : PHPUnit, Vitest, Playwright |
| 4.1 API externe | IGDB, appelée uniquement depuis le serveur |
| 4.1 HTTPS | Let's Encrypt sur le VPS OVH |
| 4.4 Recherche en moins d'une seconde | Recherche textuelle PostgreSQL |
| 4.6 Code conforme aux conventions du langage | PSR-12 pour le PHP, vérifié par un linter |

## 7. À compléter au fil du développement

- Authentification de l'API (jeton ou session) et protection CSRF.
- Organisation du code en couches (contrôleurs, services, dépôts).
- Outils de qualité : linter PHP et JavaScript, outil d'audit d'accessibilité.
- Schéma d'architecture déployée (conteneurs et leurs échanges).
