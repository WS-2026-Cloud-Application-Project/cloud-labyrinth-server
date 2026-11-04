# ADR-0001: Architekturentscheidungen als ADRs dokumentieren

| | |
|---|---|
| Status | Angenommen |
| Datum | 2026-11-XX |
| Entscheider:innen | Team |
| Betroffene Ziele | 10 dokumentiert |

## Kontext
Der Auftraggeber verlangt, dass Entscheidungen nachvollziehbar begründet werden. Die Statusberichte enthalten ein Kapitel „Umsetzung und Entscheidungen“.

## Optionen
| Option | Vorteile | Nachteile |
|---|---|---|
| ADRs als Markdown im Repo | versioniert, reviewbar, nah am Code | Disziplin nötig |
| Wiki / Google Doc | einfach zu bearbeiten | nicht versioniert mit dem Code, geht verloren |

## Entscheidung
Wir dokumentieren jede Architektur- und Tool-Entscheidung als ADR in `docs/adr/`, nummeriert, nach Vorlage `0000-vorlage.md`. ADRs werden per PR reviewed.

## Konsequenzen
- Berichtskapitel 2 lässt sich direkt aus den ADRs ableiten.
- Abgelöste Entscheidungen werden nicht gelöscht, sondern auf „Abgelöst“ gesetzt.
