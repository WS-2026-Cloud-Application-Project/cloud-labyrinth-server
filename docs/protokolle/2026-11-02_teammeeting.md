# Protokoll: Teammeeting 1 – Projektsetup

| | |
|---|---|
| Datum / Uhrzeit | 2026-11-02, 17:54–19:19 |
| Teilnehmende | Rene Stockinger, David Strauß, Andreas Rofner, Manuel Kirchebner (ab ca. 18:30) |
| Abwesend | Simon Raass |
| Moderation | David Strauß |
| Quelle | Google-Meet-Zusammenfassung (Gemini), nachbearbeitet von Rene Stockinger; nicht projektbezogene Gesprächsteile entfernt |

## Status seit letztem Termin
- Kick-off-Webinar am 29.10.2026, Rollen verteilt (Gruppenmanagement: David Strauß; Schriftführung: Simon Raass; Qualitätsmanagement: Rene Stockinger)
- Code aus dem AIP-Projekt liegt noch in den alten Repositories

## Besprochene Themen
1. **Anforderungen Stufe 1:** Laut Projektvorstellung (Folie 5) ist eine über Web und Game-Client erreichbare Spieleserver-Anwendung auf CloudStack gefordert. Continuous Deployment wird voraussichtlich erst in Stufe 2 verlangt. Fokus vorerst: Cloud-Umgebung, Versionsverwaltung, Dokumentation.
2. **Qualitätsmanagement und Nachweise:** messbare Zielwerte und pre-commit-Checks zur Fehlervermeidung; Logs, Screenshots und erfolgreiche Tests werden laufend im Repository abgelegt (`docs/evidence/`).
3. **CI/CD-Pipeline und Branch-Strategie:** David stellt das in seiner Firma genutzte Modell vor – Feature-Branches, Develop-Branch mit automatischen Tests und Auto-Deploy auf einen Testserver, kontrollierter Merge nach `main`. Die Lehrenden schlagen ähnliche Ansätze (YAML-Workflows) vor.
4. **Deploy-Varianten:** Andreas hat Workflows getestet, bei denen GitHub Actions ein Docker-Image baut und die VM es per `docker pull` holt; Images über die GitHub Container Registry (GHCR). Alternativ Coolify (Open-Source-Serververwaltung, Redeploy per GitHub-Webhook) – offen, ob es per IaC auf neuen Instanzen installiert werden kann und ob eine schlankere Lösung ressourcenschonender ist. Netzwerkthemen (VPN-Zugang zur Cloud) noch zu klären.
5. **Client auf dem Server?** Offen, ob die JavaFX-Client-Anwendung ebenfalls in der Cloud bereitgestellt werden muss. Ein Load Balancer für vier Spieler ist wenig sinnvoll, wird aber in Stufe 3 relevant.
6. **Repository-Struktur:** Vorschlag mit Ordnern `infra/` (Terraform, Compose), `docs/` (Dokumentation, Protokolle) und `app/` (Anwendungscode) wird als übersichtlich bewertet.
7. **ADRs und Systemziele:** ADRs werden für zentrale Designentscheidungen angelegt (z. B. Trennung von Entwicklungs- und Produktionsserver). Die 11 Systemziele werden mit konkreten Umsetzungen verknüpft (Zielmatrix).
8. **Identity and Access Management (IAM):** Unklar, ob IAM die Anwendung (Benutzerverwaltung, z. B. über Spring Security) oder die Cloud-Infrastruktur betrifft. Laut Zielen der Stufe 1 geht es um Zugriffskontrolle in der Cloud. Vorschlag: dedizierte Benutzer – Deployment-, Admin- und Git-Benutzer.

## Entscheidungen
| # | Entscheidung | Begründung | ADR |
|---|---|---|---|
| E1 | CI/CD-Pipeline nach dem Modell Feature-Branch → Develop-Branch (Testserver) → `main` (Produktion) | bewährtes Modell, entspricht Ziel 7 (kontrolliert ausgeliefert) | ausstehend |
| E2 | Branch-Namen `dev` und `main` | klassische Bezeichnungen | – |
| E3 | Repository-Struktur mit `infra/`, `docs/`, `app/` | klare Trennung von Infrastruktur, Doku und Code | – |
| E4 | ADRs für zentrale Designentscheidungen | Nachvollziehbarkeit, Vorgabe in Sakai | [ADR-0001](../adr/0001-adrs-verwenden.md) |
| E5 | Nachweise (Logs, Screenshots, Testergebnisse) laufend im Repo ablegen | Grundlage für die Statusberichte | – |

## Aufgaben
| # | Aufgabe | Owner | Frist | Issue |
|---|---|---|---|---|
| A1 | GitHub-Pipeline entwickeln, mit bestehenden Lösungen vergleichen (Tests, Auto-Deploy) | David Strauß | – | – |
| A2 | Deploy-Optionen direkt auf dem Server ohne Zusatzprogramme prüfen (Coolify vs. schlanke Lösung) | David Strauß | – | – |
| A3 | Pipeline-Konfiguration und Einbindung von GitHub Actions gemeinsam prüfen | Rene Stockinger, David Strauß | – | – |
| A4 | Infrastruktur-Konfiguration mit Terraform recherchieren und umsetzen | Manuel Kirchebner, Andreas Rofner | – | – |
| A5 | GitHub-Repository mit Ordnerstruktur `infra/`, `docs/`, `app/` anlegen, Code übernehmen | Rene Stockinger | – | – |
| A6 | Qualitätsrichtlinien (`QUALITY.md`) überarbeiten, Leitfaden für automatisierte Prüfungen | Rene Stockinger | – | – |
| A7 | Offene Fragen für Q&A zusammenschreiben | alle | 09.11.2026 | – |
| A8 | Einladung für das nächste Treffen erstellen | David Strauß | – | – |

## Offene Fragen (für Betreuer)
- Muss die Client-Anwendung ebenfalls auf dem Server bzw. in der Cloud betrieben werden?
- Betrifft IAM die Anwendung (Benutzerverwaltung) oder nur die Zugriffskontrolle in der Cloud?
- Welcher Dokumentationsumfang wird erwartet?

## Nächster Termin
2026-11-05, 19:30
