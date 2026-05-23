---
typ: backlog_seed
titel: "SMA-Operative-Doku in strukturierte Systemzustand-Dateien aufteilen"
geltungsbereich: prisment
risikoklasse: sicher
status: offen
stand: 2026-05-23
erzeugt_durch: BOOT-001
---

## Anlass

Die alte `08_Projekte/Social_Media_Automation/CLAUDE.md` enthält viel Sachinhalt
(Stack, Agent-Übersicht, DB-Topologie seit A3, Voice-DB-Architektur,
Test/Promote-Strategie, Debugging-Patterns). Sie wurde im Bootstrap BOOT-001
1:1 als `Prisment/Systemzustand/Architektur/SMA_Operative_Doku.md` übernommen,
um keinen Inhalt zu verlieren — Aufteilung ist ein eigener Mini-Zyklus.

## Soll-Zustand (grob)

`SMA_Operative_Doku.md` wird in mehrere thematische Systemzustand-Dateien
aufgetrennt. Vorschlag der Schnitte:

- `Prisment/Systemzustand/Architektur/Stack.md` — Stack-Versionen, Standard-Dateistruktur pro Agent, Interface Contract.
- `Prisment/Systemzustand/Architektur/agents/Agent-Uebersicht.md` — Tabelle der 4 Agents (Container, Port, LLM, Status).
- `Prisment/Systemzustand/Architektur/DB-Topologie.md` — agent_data + auth/public, search_path, db_sync.py Divergenz.
- `Prisment/Systemzustand/Architektur/Voice-DB.md` — Voll-Injektion-Architektur, V6-Konsum, entfernte V5-Pfade.
- `Prisment/Systemzustand/Architektur/Test-und-Promote.md` — PWA- und Agent-Test-Umgebung, Build/Promote-Strategie, Rollback.
- `Prisment/Systemzustand/Architektur/Debugging-Patterns.md` — httpx.delete, docker restart, n8n-bot Collaborator etc.

`SMA_Operative_Doku.md` wird nach erfolgreicher Aufteilung entfernt.

## Offene Fragen für die spätere Spec

- Aktualitätsabgleich pro Block gegen das echte System (Versionen, Container-Stati).
- Sind alle Debugging-Patterns noch relevant oder durch Code-Änderungen obsolet?
- n8n-Credentials-Liste prüfen (aktueller Stand).
