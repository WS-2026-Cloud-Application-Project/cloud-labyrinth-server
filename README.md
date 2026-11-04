# Cloud-Labyrinth – Server

[![pr-checks](https://github.com/WS-2026-Cloud-Application-Project/cloud-labyrinth-server/actions/workflows/pr-checks.yml/badge.svg)](https://github.com/WS-2026-Cloud-Application-Project/cloud-labyrinth-server/actions/workflows/pr-checks.yml)
<!-- TODO DevOps: Badges für ci.yml und release-deploy.yml ergänzen, sobald die Workflows existieren -->
![Java](https://img.shields.io/badge/Java-17-blue)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.5-green)

Spring-Boot-WebSocket-Server für **„Das verrückte Labyrinth“**, betrieben als verteiltes System auf **Apache CloudStack**
im Rahmen des *Cloud Application Project* (MCI, WS 2026/27, Gruppe 3).
Ausgangspunkt ist der Server aus dem Advanced Integrative Project (AIP), Stand `afd32b8`.

> **Lebendes Dokument:** Bei jeder Änderung an Setup, Konfiguration oder Betrieb wird diese README im selben PR angepasst
> (siehe Definition of Done in [QUALITY.md](QUALITY.md)).

---

## Inhalt

1. [Projektstatus](#projektstatus)
2. [Architektur](#architektur)
3. [Schnellstart](#schnellstart)
4. [Konfiguration](#konfiguration)
5. [Entwicklung](#entwicklung)
6. [Tests](#tests)
7. [Umgebungen und Deploy](#umgebungen-und-deploy)
8. [Dokumentation](#dokumentation)
9. [Team](#team)

---

## Projektstatus

| Stufe | Inhalt | Abgabe / Präsentation | Status |
|---|---|---|---|
| 1 – Run it in the Cloud | Server auf CloudStack-VM, kontrollierter Zugriff | 23.11. / 26.11.2026 | 🟨 in Arbeit |
| 2 – Engineer it for the Cloud | Container, IaC, CI/CD, Monitoring, Backup | 14.12. / 17.12.2026 | ⬜ offen |
| 3 – Scale it in the Cloud | Mehrere Instanzen, Load Balancing, Autoscaling, Failover | 01.02. / 04.02.2027 | ⬜ offen |

Erreichungsgrad der 11 Systemziele: [docs/zielmatrix.md](docs/zielmatrix.md)

**Aktuelle Version:** _siehe [Releases](https://github.com/WS-2026-Cloud-Application-Project/cloud-labyrinth-server/releases)_

## Architektur

<!-- TODO: Architekturdiagramm einfügen (z. B. docs/architektur.png) und bei jeder Stufe aktualisieren -->

```text
 Spieler (JavaFX-Client) ──WebSocket ws://<host>:8397/game──▶ Labyrinth-Server (Container auf CloudStack-VM)
                                                                    │
                                                                    └─HTTPS Heartbeat──▶ Server-Registry (mgmt.dvl.spalx.dev)
```

| Komponente | Technologie |
|---|---|
| Anwendung | Java 17, Spring Boot 3.5, WebSocket |
| Spielzustand | In-Memory (geht bei Neustart verloren) – _Entscheidung für Stufe 3 offen_ |
| Container | Docker (Multi-Stage-Build, läuft ohne Root) |
| Registry | GitHub Container Registry (GHCR) |
| Infrastruktur | Apache CloudStack (`cloud.dibse.at`) |
| CI/CD | GitHub Actions |

Wichtige Entscheidungen sind als ADRs dokumentiert: [docs/adr/](docs/adr/)

## Schnellstart

### Voraussetzungen

- JDK 17 und Maven 3.9 (der Maven Wrapper ist noch unvollständig, siehe [Offene Punkte](#offene-punkte))
- Optional: Docker Desktop

### Lokal mit Maven

```bash
cd app
cp .env.example .env          # OPENAI_API_KEY eintragen (nur für KI-Spieler nötig)
mvn spring-boot:run
```

Ohne gesetzten Port wählt der Server automatisch einen freien Port zwischen 8397 und 8405 (steht im Log).

### Mit Docker

```bash
docker build -t labyrinth-server app
docker run --rm -p 8397:8397 --env-file app/.env labyrinth-server
```

Im Container läuft der Server fest auf Port **8397**. WebSocket-Endpoint: `ws://localhost:8397/game`

## Konfiguration

Konfiguration über Umgebungsvariablen (Vorlage: [`app/.env.example`](app/.env.example)) oder `app/src/main/resources/application.*`.
Echte Werte gehören **nie** ins Repo, sondern in `.env` (lokal) bzw. GitHub Environments (Server).

| Variable | Bedeutung | Standard |
|---|---|---|
| `OPENAI_API_KEY` | API-Key für KI-Spieler | – |
| `SERVER_PORT` | fester Port statt automatischer Suche | im Container `8397` |
| `GAME_SERVER_HOST` | öffentliche IP/Domain, wird an die Server-Registry gemeldet | `localhost` |
| `GAME_SERVER_NAME` | Anzeigename in der Server-Registry (Test/Prod unterscheiden) | `Gruppe 3` |
| `SERVER_REGISTRY_BASE_URL` | Adresse der Server-Registry | `https://mgmt.dvl.spalx.dev/` |

## Entwicklung

Kurzfassung – vollständig in [QUALITY.md](QUALITY.md) und im [GitHub-Konzept](docs/github-konzept.md):

1. Einmalig: `pip install pre-commit` und `pre-commit install --hook-type pre-commit --hook-type commit-msg`
2. Branch von `dev` abzweigen: `feature/<issue>-<kurzname>`, `fix/…`, `chore/…`, `docs/…`
3. Commits und PR-Titel nach Conventional Commits (`feat: …`, `fix: …`) – der PR-Titel bestimmt die Version
4. PR nach `dev`, 2 Approvals, Squash-Merge → automatischer Deploy auf den Testserver
5. Release-PR `dev` → `main` → Deploy in Produktion

Codestyle: [app/CODESTYLE.md](app/CODESTYLE.md)

## Tests

```bash
cd app
mvn test
```

| Testart | Wo | Status |
|---|---|---|
| Unit-Tests | `app/src/test` | ✅ vorhanden |
| WebSocket-Integrationstest | – | ⬜ geplant |
| Lasttest (k6) | `tests/load/` | ⬜ geplant (Stufe 2) |
| Ausfall-/Restore-Tests | `tests/chaos/` | ⬜ geplant (Stufe 2/3) |

## Umgebungen und Deploy

| Umgebung | Branch | Adresse | Deploy |
|---|---|---|---|
| `development` (Testserver) | `dev` | `ws://<TODO>:8397/game` | automatisch bei Merge nach `dev` |
| `production` | `main` | `ws://<TODO>:8397/game` | automatisch bei Merge nach `main` |

Ablauf, Versionierung und Rollback: [GitHub-Konzept, Abschnitte 6–7](docs/github-konzept.md) ·
Betriebsanleitungen: [docs/runbook/](docs/runbook/)

## Dokumentation

| Thema | Ort |
|---|---|
| Qualitätsrichtlinien, Definition of Done | [QUALITY.md](QUALITY.md) |
| GitHub- und CI/CD-Konzept | [docs/github-konzept.md](docs/github-konzept.md) |
| Systemziele und Nachweise | [docs/zielmatrix.md](docs/zielmatrix.md), [docs/evidence/](docs/evidence/) |
| Architekturentscheidungen | [docs/adr/](docs/adr/) |
| Runbooks (Betrieb) | [docs/runbook/](docs/runbook/) |
| Protokolle | [docs/protokolle/](docs/protokolle/) |
| Details zur Anwendung (aus AIP) | [app/README.md](app/README.md) |
| Protokoll-Spezifikation | [labyrinth-interface](https://github.com/thomasklammer/labyrinth-interface) |

## Offene Punkte

<!-- Laufend pflegen; Erledigtes entfernen -->
- [ ] Maven Wrapper vervollständigen (`mvn wrapper:wrapper`, Ordner `.mvn/` committen)
- [ ] Health-Endpoint (`spring-boot-starter-actuator`, `/actuator/health`)
- [ ] Architekturdiagramm
- [ ] Adressen von Test- und Produktivserver eintragen

## Team

Gruppe 3 · Betreuung: _TODO_

| Name | Rolle |
|---|---|
| David Strauß | Gruppenmanagement |
| Simon Raass | Schriftführung |
| Rene Stockinger | Qualitätsmanagement |

Client: [cloud-labyrinth-client](https://github.com/WS-2026-Cloud-Application-Project/cloud-labyrinth-client)

---

Studienprojekt im Kurs *Cloud Application Project*, MCI Management Center Innsbruck.
