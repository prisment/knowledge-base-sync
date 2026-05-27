---
typ: backlog_seed
titel: "langchain-core 0.3.x → 1.x Bump in langgraph-helpdesk"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: security
zugkraft: spaeter
mission:         security-haertung-plattform
stufe: sprung
beruehrt: ["langgraph/helpdesk/"]
stand: 2026-05-27
erzeugt_durch: PLAT-031 Phase 9 (Restschuld Logbuch E48)
baut_auf: ["[[PLAT-031_SPEC_code-security]]"]
---

## Anlass

PLAT-031 hat langchain-core in langgraph/helpdesk auf 0.3.85 angehoben
(schließt CVE-2025-68664). Drei weitere CVEs bleiben offen, alle brauchen
langchain-core 1.x:

| CVE              | Fix in langchain-core |
|------------------|-----------------------|
| CVE-2026-26013   | 1.2.11                |
| CVE-2026-44843   | 0.3.85 → versionsspezifisch (in --ignore) |
| CVE-2026-34070   | 1.2.22                |

Bump auf 1.x scheitert heute am Pin `langchain-anthropic==0.3.0` (Konflikt-
Resolver). In `ci.yml` per `--ignore-vuln` namentlich dokumentiert.

## Ziel

Synchroner Bump: `langchain-core` auf 1.x **plus** `langchain-anthropic` auf
kompatibles Major. Helpdesk-Container nach Rebuild healthy + Intent-/RAG-
Klassifikation funktional unverändert.

## Architektur-Weiche

langchain-Major-Sprünge brechen häufig die Tool-Dispatch-API. Helpdesk nutzt
Custom-Tools via Pydantic-Schemas — diese sind das Hauptangriffsfläche des
Bumps. Risiko: Re-Verdrahtung der Tool-Definitionen.

Andere 4 Agents (content/interview/redaktionsplan/analytics) nutzen
`langchain-core` nur transitiv über `langgraph`. Bei diesen ist der Bump
sekundär — wenn man's macht, dann alle 5 gleichzeitig (sonst zwei
langchain-Welten im selben Repo).

## Stufe / Risikoklasse

`sprung` — < 1 Tag, helpdesk ist isoliert (kein Pipeline-Kopplung mit
content/interview).
`sicher` — Image-Rollback via `:prev` deckt die Bruch-Klasse ab. Kein
Live-Daten-Risiko.

## Trigger

`spaeter` — Architekt-Urteil analog zum starlette-Bump: theoretisches Risiko
ohne aktiven Schmerz. Anlass: helpdesk-Bug, der langchain-core-Bump aus
funktionalem Grund nötig macht, oder vierter CVE in derselben Komponente.
