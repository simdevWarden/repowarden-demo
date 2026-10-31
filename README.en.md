# repowarden-demo

[🇫🇷 Français](README.md) · 🇬🇧 English

Demo project for [repowarden](https://github.com/simdevWarden/repowarden): a small Python application whose whole lifecycle (commits, branches, formatting, tests, dead code, releases) is guarded by repowarden, configured **only with the published templates** (`templates/project/`).

## The project

`calc` is a minimal Python library, deliberately simple so that the focus stays on the tooling:

```text
src/calc/__init__.py    add(a, b) and mul(a, b), puissance(a, b)
tests/test_calc.py      pytest tests
pyproject.toml          Python project (version kept up to date by releases)
CHANGELOG.md            generated on each release
```

```bash
pip install pytest && pytest -q     # run the tests (pythonpath set in pyproject.toml)
```

Versions are published automatically: [releases](https://github.com/simdevWarden/repowarden-demo/releases).

## repowarden configuration

| File | Role |
|---|---|
| `.github/workflows/repowarden.yml` | CI: `simdevWarden/repowarden@v4` action, strict mode: messages, branch, secrets, formatting (ruff), tests (pytest), new dead code (ruff, vulture) |
| `.github/workflows/release.yml` | releases, `develop` → `main` flow: `vX.Y.Z-next.N` prerelease in the evening if `develop` changed, delivery PR `develop` → `main`; merging it publishes the tag and the release |
| `.github/workflows/nettoyage.yml` | PR branches deleted after merge (`main` and `develop` kept) |
| `.repowarden.conf` | shared settings: minimum repowarden version expected on developer machines (`version`), CI message language (`lang = fr`), commented examples |

## Try it

```bash
npm install -g repowarden && repowarden install --global   # once per machine
gh repo fork simdevWarden/repowarden-demo --clone && cd repowarden-demo
# origin = your fork, upstream = simdevWarden/repowarden-demo
# or: Fork button on GitHub, then git clone <you>/repowarden-demo
```

In the fork, **Actions** tab → **"I understand my workflows, go ahead and enable them"**: once, otherwise the CI does not run.

```bash
git switch -c feat/division
# … edit src/calc/__init__.py, then:
git add src/ && git cc             # commit assistant
# or: git commit -am "division" → prefixed as "feat(division): division", code formatted by ruff
git push -u origin feat/division   # to your fork; tests (pytest) before sending
gh pr create --repo <you>/repowarden-demo --base develop   # PR in the fork, `repowarden` CI
```

Without the hooks, the CI runs the same checks again and blocks the PR.

**In a fork**: only the `repowarden` CI runs (default token); prereleases, deliveries and ticket tracking go through the original repository's GitHub App and do not run.

## Technologies supported by repowarden

This project is in Python, but the same configuration works for:

| Area | Technologies |
|---|---|
| **Java / JVM** | Maven, Gradle — Spring Boot, Quarkus, Android |
| **JavaScript / TypeScript** | npm, pnpm, yarn, bun, Deno — React, Next.js, Vue, Angular, Nest |
| **Python** | pip, uv, poetry, pipenv — Django, FastAPI, Flask |
| **Other languages** | Go · Rust · PHP (Laravel, Symfony) · Ruby (Rails) · .NET · Dart / Flutter · Swift · Elixir · C / C++ · Shell |
| **Infrastructure** | Terraform / OpenTofu · Packer · Ansible · Helm · Kubernetes · Docker · GitHub Actions |
| **Hosting / CI** | Hooks: any Git repository · CI: GitHub Actions, GitLab CI |
| **Systems** | Linux, macOS, Windows |

Details (formatters, test commands): [repowarden — Technologies](https://simdevwarden.github.io/repowarden/en/technologies/).

## Protection

Work goes into `develop` (default branch); **only `develop` goes into `main`**, through the delivery PR. A work PR opened against `main` is retargeted to `develop` by the CI. Both branches are protected: merge only through PRs, `repowarden` check required.

The demo PRs show what passes and what is blocked:

- [#1 — compliant](https://github.com/simdevWarden/repowarden-demo/pull/1): made with the hooks, merged;
- [#2 — non-compliant](https://github.com/simdevWarden/repowarden-demo/pull/2): made without the hooks, blocked by the CI (message, branch, formatting), then closed; the blocking log is still available.

Merged branches are deleted automatically (server and developer machines).

## License

Free software, distributed under the [GNU General Public License version 3](LICENSE) (`GPL-3.0-only`).
