---
typ: manifest
titel: "Sync-Whitelist (GitHub → Projekt-Wissen)"
stand: 2026-05-28
gepflegt_von: "Claude Code"
---

# Sync-Whitelist

Definiert, WAS via GitHub ins Projekt-Wissen des Chats gespiegelt wird. Whitelist-Prinzip: explizit auflisten, was rein darf — alles andere bleibt draußen. Kundendaten und Secrets werden NIE gespiegelt.

## Mechanik (wichtig)

- Seit MCP-Setup (INT-001, 2026-05-27) hat der Chat-Architekt Live-Zugriff aufs Repo via `prisment-knowledge` + `prisment-git`. Das Projekt-Wissen ist damit kein vollständiger Wissens-Spiegel mehr, sondern nur noch der **stabile Anker** (Verfassung), der auch bei MCP-Ausfall oder beim allerersten Lese-Zugriff sofort verfügbar sein muss.
- Alles andere (Backlog, Templates, Logbuch, Systemzustand, Skills, CLAUDE.md-Dateien, Schritt-Logs) wird live via MCP gelesen — nicht mehr gespiegelt. Damit gibt es nur noch eine Wahrheits-Schicht.
- Kundendaten und Secrets bleiben hart ausgeschlossen, unabhängig vom Projekt-Wissens-Sync.

## Gespiegelt (Verfassungs-Anker)

```
_Betrieb/Verfassung/**           # alle Verfassungs-Module (00–06)
```

## NICHT gespiegelt (live via MCP)

```
alles übrige im Repo
```

## Historisch gespiegelt, jetzt nur via MCP

Vor INT-002 (2026-05-28) waren zusätzlich gespiegelt: `_Betrieb/CLAUDE.md`, `_Betrieb/CLAUDE-global.md`, `_Betrieb/sync-whitelist.md`, `_Betrieb/Templates/**`, `_Betrieb/Logbuch/**`, `_Betrieb/Backlog/**`, `CLAUDE.md` (Root), `.claude/skills/**`, alle Bereichs-`CLAUDE.md` + `Schritt-Log.md` + `Systemzustand/00_Uebersicht/*.md`. Mit MCP-Live-Zugriff redundant.

## Hart ausgeschlossen (unabhängig vom Whitelist-Eintrag)

```
10_Kunden/**                     # personenbezogen/sensibel — NIE
**/*.env, **/secrets/**, Tokens, Keys   # Secrets — NIE
```

## Aktualitäts-Sicherung

Die Verfassungs-Module tragen `stand:` im Front Matter. Der Chat nennt zu Sitzungsbeginn ungefragt die Stände, damit der Mensch erkennt, ob ein Zyklus den Stand seither überholt hat.
