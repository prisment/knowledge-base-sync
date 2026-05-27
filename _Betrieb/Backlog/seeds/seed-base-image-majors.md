---
typ: backlog_seed
titel: "Base-Image-Major-Sprünge: Node 20→22, Python 3.12→3.13, Alpine-Pin"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: wartung
zugkraft: irgendwann
mission:         security-haertung-plattform
stufe: sprung
beruehrt: ["pwa/pwa-web/Dockerfile", "admin/admin_web/Dockerfile", "langgraph/*/Dockerfile", "n8n/Dockerfile", "ollama/Dockerfile", "pwa/pwa-api/Dockerfile", "cf-alarm/Dockerfile"]
stand: 2026-05-26
erzeugt_durch: "PLAT-021 Phase 3 — Majors bewusst aus der Routine ausgeklammert (Routine macht nur Patch in derselben Major.Minor)"
---

## Anlass

PLAT-021 pinnt alle 10 Custom-Build-Dockerfiles auf konkrete
**Patch-Tags derselben Major.Minor-Linie** wie heute
(`python:3.12-slim` → `python:3.12.7-slim`, `node:20-alpine` →
`node:20.18-alpine`). Major-Sprünge bleiben **ausdrücklich raus**,
weil sie Breaking-Change-Risiko tragen und nicht autonom apply-bar
sein dürfen.

Stand 2026-05-26:
- **Node:** 20 (Maintenance-LTS bis April 2026) → 22 (aktueller LTS).
  `pwa-web`, `admin_web` betroffen.
- **Python:** 3.12 → 3.13 (seit Oktober 2025). `langgraph/*`,
  `pwa-api`, `cf-alarm` betroffen — sieben Dockerfiles.
- **Alpine:** `n8n/Dockerfile` (Builder-Stage) + `ollama/Dockerfile`
  nutzen `alpine:latest` ohne Pin — pinnen auf konkrete Major
  (`alpine:3.20`) ist eigentlich kein Major-Sprung, aber strukturell
  hier zu lösen (Build-Reproduzierbarkeit).

## Soll-Zustand pro Major

- **Node 20 → 22** in `pwa-web` + `admin_web`:
  - Per Dockerfile FROM-Update + Re-Build + Acceptance-Test
    (`npm run build` + sample-Routen).
  - Next.js-15-Kompatibilität mit Node 22 prüfen (laut Next.js-Docs
    unterstützt).
  - Sequentiell, nicht in einem Bündel — pro App ein eigener Test.
- **Python 3.12 → 3.13** in 7 Dockerfiles:
  - Sequentiell pro App (langgraph-helpdesk zuerst als am
    isoliertesten, dann der Rest).
  - Acceptance-Test: Health-Endpoint + ein End-to-End-Workflow.
- **Alpine-Pin** in `n8n/Dockerfile` + `ollama/Dockerfile`:
  - Aktuelle Stable-Major (z.B. `alpine:3.20`) einsetzen, pinnen.
  - Hardening-Layer von n8n (apk-Install) gegen die neue Alpine-
    Version testen.

## Stufe / Risiko

**Sprung pro Major-Linie** (Node, Python, Alpine sind drei
unabhängige Sub-Zyklen). Risikoklasse `sicher`, weil:
- Reversibel via `:prev-<timestamp>`-Tag (PLAT-021-Konvention).
- Sequentiell + betreut, kein autonomer Apply.
- Health-Check + Rollback-Pfad pro Apply.

## Trigger

**Irgendwann.** Konkrete Auslöser:
- Node-20-Maintenance-LTS-End am 2026-04-30 → wird **bald**
  überfällig, sobald die Routine aus PLAT-021 läuft.
- Python-3.12-Sicherheits-EOL (noch fern, 2028).
- PLAT-021 nightly-Aufgabe „Base-Image-Drift" reportet einen Major
  als verfügbar — das ist der direkte Antrieb.

## Doppelrolle als Beweis-Lauf der Patch-Routine

Sobald die PLAT-021-Routine läuft, wird sie reportieren:
„Major-Sprung verfügbar, nicht-autonom-Apply". Das ist der direkte
Eingangs-Trigger für einen der drei Sub-Zyklen dieses Seeds. Die
Routine schiebt also nicht nur Patches autonom, sondern reicht den
Major-Bedarf ehrlich rüber — dieser Seed ist die Antwort darauf.

## Hinweis

Nicht alle drei Majors gleichzeitig starten. Eine Sub-Linie pro
Zyklus, sonst werden Health-Checks unklar zugeordnet.
