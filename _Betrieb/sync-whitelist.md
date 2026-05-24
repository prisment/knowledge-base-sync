---
typ: manifest
titel: "Sync-Whitelist (GitHub → Projekt-Wissen)"
stand: 2026-05-24
gepflegt_von: "Claude Code"
---

# Sync-Whitelist

Definiert, WAS via GitHub ins Projekt-Wissen des Chats gespiegelt wird. Whitelist-Prinzip: explizit auflisten, was rein darf — alles andere bleibt draußen. Kundendaten und Secrets werden NIE gespiegelt.

## Mechanik (wichtig)

- GitHub-Sync ist KEIN Live-Zugriff. Es ist ein Import zu einem Zeitpunkt in den Projekt-Wissensspeicher; der Chat lädt diesen Speicher beim Sitzungsstart. Zwei Versatz-Stellen, kein lebender Draht.
- Gespiegelt wird der **stabile Unterbau** (langsam alternd, Versatz verkraftbar). Die **flüchtige Arbeitsschicht** (Specs, Reports, Abschluss-Dokus des laufenden Zyklus) wird NICHT gespiegelt — sie kommt frisch über den Gesprächskanal (Claude Code nennt den Dateinamen, der Mensch bringt sie ein).
- Der **Systemzustand insgesamt** wird NICHT gespiegelt (Volumen, Volatilität, Mandantennähe). Stattdessen leben in `<Bereich>/Systemzustand/Uebersicht/` kuratierte Übersichten (MD + SVG), die der Chat-Architekt im Projekt-Wissen sieht. Detail-Wissen wird gezielt über den Gesprächskanal eingebracht (Claude Code nennt den Pfad).
- Datenmenge bleibt klein → alle Geltungsbereiche werden mit ihren Übersichten gespiegelt. Fokus entsteht nicht durch selektives Laden, sondern durch das konkrete Problem + Claude Codes Prompt mit Dateinamen.

## Gespiegelt (stabiler Unterbau)

```
_Betrieb/CLAUDE.md
_Betrieb/sync-whitelist.md       # diese Datei selbst (Kontroll-Manifest sichtbar im Chat)
_Betrieb/Verfassung/**           # alle 6 Verfassungsdateien
_Betrieb/Templates/**            # Dokument-Templates
_Betrieb/Logbuch/**              # übergreifende Entscheidungs-Historie (E1–E14+)
_Betrieb/Backlog/**              # zentraler Backlog + Seed-Dateien (Planungsüberblick)

CLAUDE.md                        # Root-Wegweiser

Plattform/CLAUDE.md
Plattform/Schritt-Log.md
Plattform/Systemzustand/Uebersicht/**     # nur kuratierte Übersichten (MD + SVG), nicht der ganze Systemzustand

Prisment/CLAUDE.md
Prisment/Schritt-Log.md
Prisment/Systemzustand/Uebersicht/**

Intern/CLAUDE.md
Intern/Schritt-Log.md
Intern/Systemzustand/Uebersicht/**
```

## NICHT gespiegelt

```
**/Arbeitsgedaechtnis/**         # flüchtig → Gesprächskanal
**/Archiv/**                     # abgeschlossen, nicht aktiv nötig
**/Migration/**                  # zerstörungsfreier Migrations-Korridor, transient
10_Kunden/**                     # personenbezogen/sensibel — NIE
**/*.env, **/secrets/**, Tokens, Keys   # Secrets — NIE
```

## Aktualitäts-Sicherung

Jede gespiegelte Datei trägt `stand:` im Front Matter. Der Chat nennt zu Sitzungsbeginn ungefragt die Stände der Kern-Dateien, damit der Mensch erkennt, ob ein Zyklus den Stand seither überholt hat.
