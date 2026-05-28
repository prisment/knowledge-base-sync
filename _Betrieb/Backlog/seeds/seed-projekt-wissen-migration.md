---
typ: backlog_seed
titel: "Projekt-Wissen-Migration: GitHub-Sync auflösen, statische Verfassungs-Snapshots als Anker"
geltungsbereich: intern
risikoklasse: sicher
status: offen
klasse: prozess
zugkraft: bald
mission: ""
stufe: schritt
beruehrt: ["_Betrieb/Verfassung/", "_Betrieb/Arbeitsplatz-Setup/"]
autonom_ziehbar: nein
stand: 2026-05-28
erzeugt_durch: "Chat-Architekt-Session 2026-05-27/28 MCP-Setup"
---

## Anlass

Seit Einrichtung der MCP-Server (`prisment-knowledge` + `prisment-git`) hat
der Chat-Architekt Live-Zugriff auf das `knowledge-base`-Repo. Damit ist
das bisherige Projekt-Wissen in claude.ai (GitHub-Sync auf das Repo) als
Kontext-Träger redundant — was die Verfassung sagt, lese ich live; was im
Arbeitsgedächtnis steht, lese ich live; was das Logbuch erzählt, lese ich live.

Aktuell ist die Projekt-Wissen-Konfiguration noch unangetastet (GitHub-Sync
läuft, Snapshot wird beim Chat-Start geladen). Das ist Übergangs-State:
funktioniert, kostet aber Tokens und schafft die alte Inkonsistenz-Falle
(Projekt-Wissen-Snapshot veraltet → Chat-Architekt liest Repo live →
zwei Wahrheits-Schichten).

## Soll-Zustand

Projekt-Wissen wird auf ein definiertes Minimal-Set reduziert, GitHub-Sync
aufgelöst. Die behaltenen Inhalte werden manuell aktualisiert (selten —
nur bei Verfassungs-Edits, MCP-Setup-Änderungen).

Soll-Inhalt des Projekt-Wissens nach Migration:

- Verfassung Module 00-06 (statische Snapshots, manuell aktualisiert)
- MCP-Setup-Doku (`_Betrieb/Arbeitsplatz-Setup/mcp-claude-desktop.md`)
- Übergreifendes Logbuch als Such-Cache (statisch, manuell aktualisiert)

Soll-Inhalt **nicht mehr** im Projekt-Wissen (wird live via MCP gelesen):

- Arbeitsgedächtnis
- Einzelne Specs
- Systemzustände der Geltungsbereiche
- Backlog
- Templates

## Auslöser für die Mini-Spec

Wenn der MCP-basierte Workflow 1-2 Wochen ohne Aussetzer läuft (keine
MCP-Crashes, keine Lese-Fehler, keine Race-Conditions zwischen lokalem
Repo-Stand und Chat-Architekt-Erwartung), eröffnen wir den Migrations-
Zyklus.

## Offene Fragen

- Gibt es im claude.ai-Projekt eine Whitelist-Option für GitHub-Sync
  (nur bestimmte Pfade syncen)? Falls ja, könnten wir statt manueller
  Pflege auch einfach den Sync auf die drei behaltenen Pfade einengen.
- Wie oft werden die behaltenen Verfassungs-Module praktisch geändert?
  Falls "selten" zutrifft (1-2 mal pro Monat), ist manuelle Pflege okay.
  Falls häufiger, brauchen wir Auto-Sync oder mindestens eine Erinnerungs-
  Mechanik ("Verfassung im Projekt-Wissen ist 3 Wochen alt").
- Soll der Sitzungs-Start-Anker (Modul 06 „Sitzungsbeginn-Routine") noch
  zusätzlich im Projekt-Wissen liegen, oder reicht es, dass der
  Systemprompt darauf verweist und ich es via MCP lese?
- Fallback-Frage: Wenn MCP mal ausfällt, soll das Projekt-Wissen genug
  enthalten, dass ich noch grob orientiert bin und den Architekten
  anleiten kann, MCP zu reparieren? Falls ja: MCP-Setup-Doku ist
  ohnehin schon im Plan.

## Nicht Teil dieses Seeds

- Migration des `knowledge-base`-Repos selbst (Pfade, Struktur). Repo
  bleibt unverändert.
- Änderungen an Claude Code's CLAUDE.md. Diese Migration ist rein
  Chat-Architekt-/Projekt-Wissen-seitig.
- Änderungen an der Verfassung. Modul 06 deckt den neuen Workflow bereits ab.

---

## 📝 Eigene Notizen

<!-- Mensch-Sperrzone. Claude Code liest hier, schreibt NICHT hinein.
     Inhalt überlebt jeden Re-Write des Seeds. -->
