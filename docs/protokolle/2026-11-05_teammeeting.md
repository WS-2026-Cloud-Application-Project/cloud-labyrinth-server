# Protokoll: Teammeeting 2 – GitHub-Workflow und Infrastruktur

| | |
|---|---|
| Datum / Uhrzeit | 2026-11-05, 19:23–20:05 |
| Teilnehmende | Rene Stockinger, David Strauß, Andreas Rofner, Manuel Kirchebner, Simon Raass |
| Abwesend | – |
| Moderation | David Strauß |
| Quelle | Google-Meet-Zusammenfassung (Gemini), nachbearbeitet von Rene Stockinger; nicht projektbezogene Gesprächsteile entfernt |

## Status seit letztem Termin
- Repositories `cloud-labyrinth-server` und `cloud-labyrinth-client` angelegt, AIP-Code übernommen
- QM-Grundlagen (`QUALITY.md`, pre-commit, PR-Vorlage, Doku-Vorlagen) im Repo
- GitHub- und CI/CD-Konzept als Vorschlag erstellt (David Strauß, `docs/github-konzept.md`)
- Terraform-Prototyp für die Infrastruktur (Andreas Rofner, Manuel Kirchebner)

## Besprochene Themen
1. **GitHub-Workflow und pre-commit:** Nach einmaliger Einrichtung (Befehle in `QUALITY.md`) laufen bei jedem lokalen Commit automatische Checks. Bei jedem Push folgen Build, Unit-Tests, Codestyle und Testabdeckung.
2. **Pull-Request-Prozess:** Arbeit nur in Feature-Branches; `dev` und `main` sind für direkte Pushes gesperrt. PRs werden von Reviewern geprüft und freigegeben. Im PR laufen dieselben Checks wie bei pre-commit plus Docker-Build und Sicherheitsscans. Schlägt ein Deploy fehl, wird automatisch auf die vorherige Version zurückgerollt.
3. **Commit-Konventionen:** Commit-Messages und PR-Titel folgen Conventional Commits (`feat`, `fix`, `chore`, `docs`, `test` …), weil die Versionsnummer daraus berechnet wird. Der pre-commit-Hook blockiert abweichende Commit-Messages bereits.
4. **Infrastruktur mit Terraform:** Terraform erstellt Instanzen, Netzwerke und Firewall-Regeln. Auto-Scaling-Gruppe und Load Balancing sind prototypisch umgesetzt: standardmäßig 2 Instanzen, insgesamt stehen 6 VMs zur Verfügung. Terraform ist nur für die Infrastruktur zuständig, nicht für GitHub-Prozesse.
5. **Automatisierung von Terraform:** Die Terraform-Konfiguration soll im Repository liegen und über eine Pipeline ausgerollt werden. Aktuell wird sie noch lokal per PowerShell gestartet.
6. **Q&A-Termin:** Unstimmigkeiten im Kalender zum Termin am 09.11. (18:00–20:30).
7. **IAM:** weiterhin offen, ob eine Benutzerverwaltung im Programm nötig ist oder nur Zugriffskontrolle in der Cloud. Offene Fragen werden zentral gesammelt (Google-Dokument, Discord).
8. **Zeitplan Stufe 1:** Präsentation am 26.11. (Termin evtl. verschoben). Pipelines und Deployment bis dahin technisch aufbauen, Präsentation in der Woche davor vorbereiten.
9. **Code-Stand in `dev`:** Die jüngsten Änderungen von Manuel (u. a. Generierung der Spieler-ID) sind im übernommenen Code noch nicht enthalten. Wird bei Bedarf von Manuel übernommen. Wichtig ist die klare Trennung von `dev` (Testumgebung) und `main` (Produktion).
10. **Umgebungskennung beim Serverstart:** Beim Start muss übergeben werden, ob Test- oder Produktivumgebung, damit parallele Instanzen korrekt bei der Server-Registry angemeldet werden (unterschiedlicher Name).

## Entscheidungen
| # | Entscheidung | Begründung | ADR |
|---|---|---|---|
| E1 | GitHub-Workflow: lokale pre-commit-Checks, Feature-Branches, Pull Requests nach `dev` und `main`, `dev`/`main` gesperrt | Ziel 7 (kontrolliert ausgeliefert), siehe [GitHub-Konzept](../github-konzept.md) | ausstehend |
| E2 | Server bekommt beim Start einen Parameter zur Unterscheidung von Test- und Produktivumgebung | Test- und Produktivserver in der Registry unterscheidbar | – |
| E3 | Autoscaling, Auto-Healing und Zustandssynchronisation über mehrere Instanzen vorerst zurückgestellt, Klärung mit Betreuer | betrifft Stufe 3, Anforderungen unklar | – |

## Aufgaben
| # | Aufgabe | Owner | Frist | Issue |
|---|---|---|---|---|
| A1 | GitHub-Konzept an die Gruppe senden | David Strauß | – | – |
| A2 | Beim Betreuer klären, ob der Q&A-Termin am 09.11. stattfindet | David Strauß | zeitnah | – |
| A3 | Offene Fragen für das Q&A im Google-Dokument bzw. Discord sammeln | alle | 09.11.2026 | – |
| A4 | Vollständige Automatisierung von Infrastruktur und Deployment über Terraform und GitHub prüfen | Andreas Rofner, Manuel Kirchebner | – | – |
| A5 | Parameter zur Umgebungskennung (Test/Produktion) beim Serverstart umsetzen | Simon Raass | - | – |

## Offene Fragen (für Betreuer)
- Wie weit müssen Autoscaling, Auto-Healing und Zustandssynchronisation über mehrere Instanzen umgesetzt werden?
- IAM: Benutzerverwaltung in der Anwendung oder reine Zugriffskontrolle in der Cloud?
- Findet der Q&A-Termin am 09.11. (18:00–20:30) statt?

## Nächster Termin
offen
