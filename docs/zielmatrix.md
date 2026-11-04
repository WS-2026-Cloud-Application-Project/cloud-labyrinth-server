# Zielmatrix – 11 Systemziele

> Zielwerte sind **Vorschläge** und müssen im Team beschlossen werden (und ggf. mit dem Betreuer abgestimmt).
> Status: ⬜ offen · 🟨 teilweise · ✅ erreicht · ❌ nicht erreicht
> Owner pflegen ihre Zeilen, QM prüft wöchentlich und vor jedem Quality Gate.

| # | Ziel | Stufe | Owner | Zielwert (Vorschlag) | Testmethode | Nachweis | Status |
|---|---|---|---|---|---|---|---|
| 1 | Elastizität | 3 | | Bei CPU > 70 % über 3 min wird innerhalb von 5 min eine Instanz ergänzt, bei < 30 % wieder abgebaut | Lasttest mit steigender Last, Instanzanzahl beobachten | | ⬜ |
| 2 | Performant | 2 | | p95-Antwortzeit < 200 ms bei 50 gleichzeitigen Spielen | Lasttest (k6/Locust), Grafana-Auswertung | | ⬜ |
| 3 | Hochverfügbar | 3 | | Ausfall einer App-Instanz führt zu < 30 s Unterbrechung, laufende Spiele bleiben erhalten | Instanz/VM gezielt stoppen, Verfügbarkeit messen | | ⬜ |
| 4 | Wiederherstellbar | 2 | | RPO ≤ 24 h, RTO ≤ 30 min | Restore-Test auf leerer Umgebung nach Runbook, Zeit stoppen | | ⬜ |
| 5 | Sicher | 1 | | Nur Ports 443 (+ Game-Port) öffentlich, SSH nur per Key, 0 Secrets im Repo, 0 kritische CVEs im Image | Portscan (nmap), gitleaks, Image-Scan (Trivy) | | ⬜ |
| 6 | Datenschutzkonform | 1 | | Datenverzeichnis vorhanden, nur nötige Daten, Löschfrist definiert | Review Datenverzeichnis vs. DB-Schema | | ⬜ |
| 7 | Kontrolliert ausgeliefert | 2 | | 100 % der Prod-Deployments über Pipeline, Tests + Staging vor Prod | Pipeline-Historie, Versuch eines manuellen Deployments | | ⬜ |
| 8 | Reproduzierbar | 2 | | Komplette Umgebung aus dem Repo in ≤ 60 min neu aufbaubar, ohne manuelle Schritte außer Secrets | Neuaufbau durch Person, die die IaC nicht geschrieben hat | | ⬜ |
| 9 | Beobachtbar | 2 | | Dashboard für Latenz, Fehlerrate, Auslastung; Alarm bei Ausfall in < 2 min | Dienst stoppen, Zeit bis Alarm messen | | ⬜ |
| 10 | Dokumentiert | 1 | | Teammitglied ohne Vorwissen kann Deploy + Restore allein nach Runbook durchführen | „Fremdbetrieb“-Test durch anderes Teammitglied | | ⬜ |
| 11 | Kostenbewusst | 2 | | Ressourcen ≤ Quota/Budget, jede VM/Ressource in Kostenübersicht begründet | Ressourcenliste vs. Kostenübersicht | | ⬜ |

## Änderungshistorie
| Datum | Änderung | Wer |
|---|---|---|
| 2026-11-04 | Erstfassung | Rene Stockinger |
