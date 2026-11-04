# Qualitätsrichtlinien – Cloud Application Project

> Verantwortlich: Qualitätsmanagement (Rene) · Beschlossen am: `TT.MM.2026` · Version 0.2 (Entwurf)
>
> Grundsatz: **Wenige Regeln, die alle einhalten. Was eine Maschine prüfen kann, prüft die Maschine.**
> Änderungen an diesen Richtlinien laufen selbst per Pull Request und werden im Weekly kurz bestätigt.
>
> GitHub-Einstellungen, Branch-Schutz, Versionierung und Deploy sind im
> [GitHub-Konzept](docs/github-konzept.md) beschrieben. Dieses Dokument fasst die Regeln für den Arbeitsalltag zusammen.

## 1. Git-Workflow

```
feature/42-lobby-chat ──PR (2 Reviews, Squash)──▶ dev ──Release-PR (2 Reviews, Merge-Commit)──▶ main
                                                   │                                           │
                                         Deploy Testserver                            Deploy Produktion
```

| Regel | Details |
|---|---|
| Branches | `dev` (Default, Testserver) und `main` (Produktion) sind geschützt – kein direkter Push |
| Arbeits-Branches | immer von `dev` abzweigen: `feature/<issue>-<kurzname>`, `fix/…`, `chore/…`, `docs/…` |
| Commits & PR-Titel | Commit-Messages (lokal per Hook geprüft) und PR-Titel nach [Conventional Commits](https://www.conventionalcommits.org/de/): `feat:`, `fix:`, `chore:`, `docs:`, `test:` … – **der Titel bestimmt die Versionsnummer** und wird im CI geprüft |
| Reviews | **2 Approvals** von anderen Teammitgliedern, neuer Push macht Approvals ungültig, alle Kommentare aufgelöst |
| Merge nach `dev` | Squash-Merge (ein Commit pro Feature, Message = PR-Titel) |
| Merge nach `main` | nur Release-PR aus `dev`, Merge-Commit, erst nach erfolgreichem Deploy auf dem Testserver |
| Aktuell halten | `dev` regelmäßig in den eigenen Branch mergen |
| PR-Größe | lieber klein und oft – 2 Reviews klappen nur, wenn PRs schnell lesbar sind |
| Reviews erledigen | offene Reviews innerhalb von 1 Werktag – sonst blockiert das ganze Team |

## 2. Definition of Done (DoD)

Eine Aufgabe ist **fertig**, wenn:

- [ ] der PR mit 2 Approvals nach `dev` gemerged ist und alle Pflicht-Checks grün sind
- [ ] die Änderung ihre Tests enthält (Bugfix: Test, der ohne den Fix fehlschlägt)
- [ ] der Deploy auf den Testserver inkl. Smoke-Test erfolgreich war
- [ ] keine Secrets im Code – echte Werte nur in GitHub Environments, im Repo nur `.env.example`
- [ ] Runbook bzw. README aktualisiert ist, falls sich Betrieb/Setup ändert
- [ ] bei einer Architektur- oder Tool-Entscheidung ein **ADR** in `docs/adr/` existiert
- [ ] der **Nachweis** (Screenshot, Log, Messwert) in `docs/evidence/` liegt und in der Zielmatrix verlinkt ist (falls zielrelevant)
- [ ] das Issue geschlossen ist

## 3. Automatische Prüfungen

**Lokal (pre-commit)** – schnelle Hygiene vor jedem Commit:

| Werkzeug | Prüft |
|---|---|
| gitleaks | Secrets, Passwörter, API-Keys |
| pre-commit-hooks | Whitespace, Zeilenenden, YAML/JSON-Syntax, große Dateien, private Keys, kein Commit auf `dev`/`main` |
| hadolint | Dockerfiles |
| terraform fmt / tflint | Terraform-Code (IaC) |
| shellcheck | Shell-Skripte |
| conventional-pre-commit | Commit-Message nach Conventional Commits (`feat:`, `fix:` …) |

Einrichtung (einmalig pro Person):

```bash
pip install pre-commit
pre-commit install --hook-type pre-commit --hook-type commit-msg
pre-commit run --all-files   # erster Testlauf
```

**In GitHub (Pflicht-Checks)** – Details siehe GitHub-Konzept, Abschnitt 5:

| Workflow | Wann | Inhalt |
|---|---|---|
| `ci.yml` | jeder Push | Build, Unit-Tests, Checkstyle (`CODESTYLE.md`), Testabdeckung (JaCoCo) |
| `pr-checks.yml` | jeder PR | pre-commit, gitleaks, PR-Titel, Quell-Branch (nur `dev` → `main`), Integrationstests, Docker-Build + Trivy |
| `codeql.yml` | PR + regelmäßig | statische Sicherheitsanalyse |
| `release-deploy.yml` | Push auf `dev`/`main` | Version, Image, Deploy, Smoke-Test, automatischer Rollback |

Zusätzlich in den Repo-Einstellungen: Secret Scanning mit Push Protection, Dependabot (Ziel-Branch `dev`).

## 4. Dokumentation

| Was | Wo | Vorlage |
|---|---|---|
| GitHub- und CI/CD-Konzept | `docs/github-konzept.md` | – |
| Architekturentscheidungen | `docs/adr/NNNN-titel.md` | `docs/adr/0000-vorlage.md` |
| Betrieb (Start, Stop, Deploy, Restore, Störung) | `docs/runbook/` | `docs/runbook/VORLAGE.md` |
| Besprechungsprotokolle | `docs/protokolle/` | siehe `docs/protokolle/README.md` (Google-Meet-Prozess) |
| Zielerreichung | `docs/zielmatrix.md` | – |
| Nachweise | `docs/evidence/` | Benennung siehe `docs/evidence/README.md` |

Sprache: Deutsch, Fachbegriffe englisch (Deployment, Load Balancer, …). Begriffe einheitlich verwenden.
Server-Repo ist die einzige Quelle für Doku – das Client-Repo verweist hierher.

## 5. Zielmatrix

`docs/zielmatrix.md` ist die zentrale Übersicht über die 11 Systemziele: Owner, Zielwert, Testmethode, Nachweis, Status.
Jede:r Owner hält ihre/seine Zeilen aktuell. QM prüft die Matrix wöchentlich und vor jeder Abgabe.

## 6. Quality Gate vor jeder Abgabe

| Abgabe | Interner Freeze | QM-Review bis | Offizielle Abgabe | Präsentation |
|---|---|---|---|---|
| Stufe 1 | Do 19.11.2026 | Sa 21.11.2026 | Mo 23.11.2026 | Do 26.11.2026 |
| Stufe 2 | Do 10.12.2026 | Sa 12.12.2026 | Mo 14.12.2026 | Do 17.12.2026 |
| Stufe 3 | Do 28.01.2027 | Sa 30.01.2027 | Mo 01.02.2027 | Do 04.02.2027 |

Ab dem Freeze: nur noch Bugfixes und Doku nach `dev`. Der Abgabestand ist ein Release auf `main` (Git-Tag `vX.Y.Z`),
der Bericht nennt diese Version. QM prüft anhand von `docs/qm/review-checkliste-abgabe.md`.
Gefundene Mängel werden Issues mit Owner und Frist.

## 7. Rollen im Qualitätsprozess

| Rolle | Aufgabe |
|---|---|
| Qualitätsmanagement | Pflegt diese Richtlinien, Zielmatrix und Quality Gate, führt `docs/qm/qm-log.md` |
| DevOps | Pflegt Workflows, Rulesets und Environments (in `CODEOWNERS` für `.github/` eingetragen) |
| Schriftführung | Sorgt dafür, dass jedes Meeting-Protokoll innerhalb von 24 h nachbearbeitet im Repo liegt |
| Alle | Halten DoD ein, reviewen zeitnah, liefern Nachweise für ihre Ziele |

## 8. Weekly – 5 Minuten Qualität

Fester Punkt im Weekly: Was lief gut? Wo hakt eine Regel? Offene Reviews? Offene Mängel aus dem Quality Gate?
