# Runbook: GitHub-Repos einrichten

> Setzt das [GitHub-Konzept](../github-konzept.md) Schritt für Schritt um – für **beide** Repos
> (`cloud-labyrinth-server`, `cloud-labyrinth-client`) in der Organisation `WS-2026-Cloud-Application-Project`.
> Das Konzept beschreibt *was* und *warum*, dieses Runbook *wie*.

| | |
|---|---|
| Owner | DevOps-Rolle |
| Zuletzt getestet | YYYY-MM-DD von <Name> |
| Betroffene Ziele | 5 sicher, 7 kontrolliert ausgeliefert, 8 reproduzierbar |

## 1. Organisation
Settings → People:
- [ ] 1–2 Owner, alle anderen Mitglieder im Team mit Rolle **Maintain** (sonst kann jede:r die Rulesets abschalten)

## 2. Branch `dev` anlegen und als Default setzen
```bash
git switch main && git pull
git switch -c dev && git push -u origin dev
```
Settings → General → Default branch → `dev`

## 3. Allgemeine Einstellungen (Settings → General → Pull Requests)
- [ ] Allow merge commits: an · Allow squash merging: an · Allow rebase merging: **aus**
- [ ] Squash: Default commit message → **Pull request title**
- [ ] Automatically delete head branches: an

## 4. Sicherheit (Settings → Code security, Actions)
- [ ] Secret scanning + **Push protection**: an
- [ ] Dependabot alerts + security updates: an (`dependabot.yml` mit `target-branch: dev`)
- [ ] CodeQL: über `codeql.yml`
- [ ] Actions → General → Workflow permissions: **Read repository contents**
- [ ] Actions → General → Fork pull request workflows: **Require approval for all outside collaborators**

## 5. Rulesets (Settings → Rules → Rulesets → New branch ruleset)
Bypass list: **leer**.

| Einstellung | Ruleset `dev` | Ruleset `main` |
|---|---|---|
| Target | Branch `dev` | Branch `main` |
| Restrict deletions / Block force pushes | ✔ | ✔ |
| Require a pull request · Required approvals | ✔ · 2 | ✔ · 2 |
| Dismiss stale approvals on new push | ✔ | ✔ |
| Require approval of the most recent push | ✔ | ✔ |
| Require conversation resolution | ✔ | ✔ |
| Allowed merge methods | Squash | Merge |
| Require status checks · up to date | ✔ · ✔ | ✔ · ✔ |
| Require deployments to succeed | – | `development` |

**Pflicht-Checks** (erst auswählbar, nachdem sie einmal gelaufen sind → nach dem ersten Test-PR nachtragen):

| Check (Job-Name) | Workflow | `dev` | `main` |
|---|---|---|---|
| `pre-commit`, `secrets` | `pr-checks.yml` | ✔ | ✔ |
| `pr-title` | `pr-checks.yml` | ✔ | ✔ |
| `source-branch` | `pr-checks.yml` | – | ✔ |
| Build/Unit-Tests, Integrationstests, Docker-Build, CodeQL | `ci.yml`, `pr-checks.yml`, `codeql.yml` | ✔ | ✔ |

## 6. Environments (Settings → Environments)

| Environment | Deployment branches | Secrets | Variables |
|---|---|---|---|
| `development` | nur `dev` | `SSH_PRIVATE_KEY`, `SSH_HOST`, `SSH_USER`, `SSH_KNOWN_HOSTS`, `OPENAI_API_KEY` | `HEALTH_URL`, `GAME_SERVER_HOST`, `GAME_SERVER_NAME` |
| `production` | nur `main` | wie oben, mit Prod-Werten | wie oben (anderer Name/MOTD) |

- Ob `production` zusätzlich **Required reviewers** bekommt, ist offene Entscheidung Nr. 2 im Konzept.
- `SSH_PRIVATE_KEY`: eigener Deploy-Key pro Umgebung (`ssh-keygen -t ed25519 -C deploy-development`).
- `SSH_KNOWN_HOSTS`: Ausgabe von `ssh-keyscan <host>`, Fingerprint einmal prüfen.
- Bei Variante B (Self-hosted Runner) entfallen die SSH-Secrets – Runner darf **nur** Deploy-Jobs nach Push ausführen, nie PR-Jobs.

## 7. CODEOWNERS
`.github/CODEOWNERS`:
```
/.github/   @<github-user-devops>
```

## 8. Prüfen
- [ ] Test-PR `chore: Setup prüfen` von `chore/setup-check` nach `dev`: alle Checks laufen, Merge ohne 2 Approvals ist gesperrt
- [ ] PR von `chore/…` direkt nach `main`: `source-branch` schlägt fehl
- [ ] Direkter Push auf `dev` wird abgelehnt
- [ ] Screenshots → `docs/evidence/` (Nachweis Ziel 7)

## Wenn etwas schiefgeht
| Symptom | Ursache | Lösung |
|---|---|---|
| Pflicht-Check nicht auswählbar | Check ist noch nie gelaufen | Test-PR öffnen, danach Ruleset ergänzen |
| PR hängt bei „Expected – waiting for status“ | Job-Name im Ruleset ≠ Job-Name im Workflow | Namen abgleichen |
| Merge nach `main` gesperrt trotz grüner Checks | Deploy auf `development` noch nicht erfolgreich | Deploy-Lauf auf `dev` prüfen |
| Approvals verschwinden | neuer Push nach Review (gewollt) | erneut reviewen lassen |
