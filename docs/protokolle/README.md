# Protokolle – Prozess mit Google Meet

Wir nutzen die **automatische Zusammenfassung von Google Meet** („Notizen machen“ / Gemini) als Rohprotokoll.
Eine KI-Zusammenfassung ist aber kein fertiges Protokoll: Entscheidungen und Aufgaben werden oft ungenau oder
ohne verantwortliche Person erfasst. Deshalb gibt es einen kurzen Nachbearbeitungsschritt.

## Ablauf

| Wann | Wer | Was |
|---|---|---|
| Meeting-Start | Moderation | Notizen-Funktion starten, kurz ansagen, dass mitgeschrieben wird (alle müssen zustimmen, DSGVO) |
| Meeting-Ende (2 min) | Moderation | Entscheidungen und Aufgaben **laut zusammenfassen** – dann landen sie zuverlässig in der Zusammenfassung |
| ≤ 24 h danach | Schriftführung | Zusammenfassung prüfen, in die Vorlage `VORLAGE.md` übertragen, korrigieren, als `YYYY-MM-DD_<art>.md` ablegen |
| ≤ 24 h danach | Schriftführung | Jede Aufgabe als Issue mit Owner und Frist anlegen, Issue-Nummer im Protokoll eintragen |
| ≤ 48 h danach | Alle | Einspruch/Korrekturen per Kommentar im PR, sonst gilt das Protokoll als genehmigt |

Dateinamen: `2026-11-05_weekly.md`, `2026-11-09_qa-betreuer.md`, `2026-11-26_praesentation-stufe1.md`

## Regeln

- Die Original-Zusammenfassung wird **nicht** unverändert ins Repo kopiert, sondern in die Vorlage übertragen.
- Keine personenbezogenen Daten Dritter und keine privaten Inhalte ins Repo. Bei Unsicherheit weglassen.
- Entscheidungen mit Architekturbezug → zusätzlich ein ADR (`docs/adr/`), im Protokoll verlinken.
- QM prüft stichprobenartig, ob Aufgaben im Protokoll auch als Issue existieren.
