# repowarden-demo

🇫🇷 Français · [🇬🇧 English](README.en.md)

Projet de démonstration de [repowarden](https://github.com/simdevWarden/repowarden) : une petite application Python dont tout le cycle de vie (commits, branches, formatage, tests, code mort, releases) est encadré par repowarden, configuré **uniquement avec les modèles publiés** (`templates/project/`).

## Le projet

`calc` est une bibliothèque Python minimale, volontairement simple pour que l'attention reste sur l'outillage :

```text
src/calc/__init__.py    add(a, b) et mul(a, b)
tests/test_calc.py      tests pytest
pyproject.toml          projet Python (version tenue à jour par les releases)
CHANGELOG.md            généré à chaque release
```

```bash
pip install pytest && pytest -q     # lancer les tests (pythonpath réglé dans pyproject.toml)
```

Les versions sont publiées automatiquement : [releases](https://github.com/simdevWarden/repowarden-demo/releases).

## Configuration repowarden

| Fichier | Rôle |
|---|---|
| `.github/workflows/repowarden.yml` | CI : action `simdevWarden/repowarden@v4`, mode strict : messages, branche, secrets, formatage (ruff), tests (pytest), nouveau code mort (ruff, vulture) |
| `.github/workflows/release.yml` | releases, flux `develop` → `main` : préversion `vX.Y.Z-next.N` le soir si `develop` a changé, PR de livraison `develop` → `main` ; son merge publie le tag et la release |
| `.github/workflows/nettoyage.yml` | branches de PR supprimées après merge (`main` et `develop` conservées) |
| `.repowarden.conf` | réglages partagés : version minimale de repowarden attendue sur les postes (`version`), langue des messages de la CI (`lang = fr`), exemples commentés |

## Essayer

```bash
npm install -g repowarden && repowarden install --global   # une fois par poste
gh repo fork simdevWarden/repowarden-demo --clone && cd repowarden-demo
# ou : bouton Fork sur GitHub, puis git clone <toi>/repowarden-demo
```

Dans le fork, onglet **Actions** → **« I understand my workflows, go ahead and enable them »** : une fois, sinon la CI ne tourne pas.

```bash
git switch -c feat/division
# … modifier src/calc/__init__.py, puis :
git add src/ && git cc             # assistant de commit
# ou : git commit -am "division" → préfixé en « feat(division): division », code formaté par ruff
git push -u origin feat/division   # tests (pytest) avant l'envoi
gh pr create --repo <toi>/repowarden-demo --base develop   # PR dans le fork, CI `repowarden`
```

Sans les hooks, la CI refait les mêmes vérifications et bloque la PR.

**Dans un fork** : seule la CI `repowarden` tourne (jeton par défaut) ; préversions, livraisons et suivi des tickets passent par la GitHub App du dépôt d'origine et ne tournent pas.

## Technologies prises en charge par repowarden

Ce projet est en Python, mais la même configuration fonctionne pour :

| Domaine | Technologies |
|---|---|
| **Java / JVM** | Maven, Gradle — Spring Boot, Quarkus, Android |
| **JavaScript / TypeScript** | npm, pnpm, yarn, bun, Deno — React, Next.js, Vue, Angular, Nest |
| **Python** | pip, uv, poetry, pipenv — Django, FastAPI, Flask |
| **Autres langages** | Go · Rust · PHP (Laravel, Symfony) · Ruby (Rails) · .NET · Dart / Flutter · Swift · Elixir · C / C++ · Shell |
| **Infrastructure** | Terraform / OpenTofu · Packer · Ansible · Helm · Kubernetes · Docker · GitHub Actions |
| **Hébergement / CI** | Hooks : tout dépôt Git · CI : GitHub Actions, GitLab CI |
| **Systèmes** | Linux, macOS, Windows |

Détails (formateurs, commandes de test) : [repowarden — Technologies](https://simdevwarden.github.io/repowarden/technologies/).

## Protection

Le travail entre dans `develop` (branche par défaut) ; **seul `develop` entre dans `main`**, par la PR de livraison. Une PR de travail ouverte vers `main` est reciblée vers `develop` par la CI. Les deux branches sont protégées : merge uniquement par PR, check `repowarden` obligatoire.

Les PR de démonstration montrent ce qui passe et ce qui est bloqué :

- [#1 — conforme](https://github.com/simdevWarden/repowarden-demo/pull/1) : faite avec les hooks, mergée ;
- [#2 — non conforme](https://github.com/simdevWarden/repowarden-demo/pull/2) : faite sans les hooks, bloquée par la CI (message, branche, formatage), puis fermée ; le log du blocage reste consultable.

Les branches mergées sont supprimées automatiquement (serveur et postes).
# panier

## Licence

Logiciel libre, distribué sous la [GNU General Public License version 3](LICENSE) (`GPL-3.0-only`).
