---
typ: uebersicht
thema: aktualitaet
geltungsbereich: plattform
stand: 2026-05-27
zweck: "Abgeleitete Aktualitäts-Sicht. SSOT aus state-Files + nightly-Sektionen. KEINE eigene Drift-Messung."
---

# Aktualität — Plattform-Bestand vs. Upstream

> **Generiert:** 2026-05-27T07:44:00.453650+02:00
> **Vom nightly:** Cron 04:35 nach allen Drift-Schreibern (04:15 base-image, 04:20 n8n-upstream, 04:30 renovate-render).
> **SSOT:** zieht aus `~/.cache/{base-image-drift,n8n-upstream}/state.json` + Renovate-PR-API + statische Container-Inventur. Macht keine eigene Drift-Messung.

## Klassen-Ampel

| Komponenten-Klasse | Status | Beleg |
|---|---|---|
| OS `-security` | autonom gepatcht | unattended-upgrades-Log |
| OS `-updates` 1a/1b | nur reporten (PLAT-001 v2) | nacht-report Sektion 3 |
| Container Klasse A (Registry) | täglich Diun + nightly Sektion 1 | siehe Container-Inventur unten |
| Container Klasse B (Custom-Build) Base-Image-Patch | **1 Drift(s)** | `~/.cache/base-image-drift/state.json` (2026-05-27T04:15:01) |
| n8n Upstream | **MINOR: → 2.21.7** | `~/.cache/n8n-upstream/state.json` |
| Renovate `admin/prisment-platform` | **4 offene PR(s)** | Gitea, Dashboard-Issue #1 |
| Renovate `internal/knowledge-base` | **0 offene PR(s)** | Gitea, Dashboard-Issue #1 |

## Container-Inventur

| Container | Image | LABEL image.version | Klasse | Apply-Pfad |
|---|---|---|---|---|
| admin_web | `admin-web:latest` | `—` | B | build_image.sh + promote_image.sh (Patch: nightly-Vorschlag, Major: seed) |
| assets_server | `nginx:alpine` | `—` | A | Diun + nightly (autonom Patch/Minor, Major reporten) |
| cf_alarm | `cf-alarm:latest` | `2a18767` | B | build_image.sh + promote_image.sh (Patch: nightly-Vorschlag, Major: seed) |
| cloudflared | `cloudflare/cloudflared:2026.5.0` | `—` | A | Diun + nightly (autonom Patch/Minor, Major reporten) |
| customer_postgres | `pgvector/pgvector:pg16` | `—` | A | Diun + nightly (autonom Patch/Minor, Major reporten) |
| internal_diun | `crazymax/diun:latest` | `4.31.0` | A | Diun + nightly (autonom Patch/Minor, Major reporten) |
| internal_gitea | `gitea/gitea:1.25.5` | `1.25.5` | A | Diun + nightly (autonom Patch/Minor, Major reporten) |
| internal_gitea_runner | `gitea/act_runner:0.6.1` | `—` | A | Diun + nightly (autonom Patch/Minor, Major reporten) |
| internal_n8n | `environment_a-n8n-internal` | `2.13.3` | B | build_image.sh + promote_image.sh (Patch: nightly-Vorschlag, Major: seed) |
| internal_ollama | `ollama/ollama:latest` | `24.04` | A | Diun + nightly (autonom Patch/Minor, Major reporten) |
| internal_ollama_proxy | `environment_a-ollama-proxy` | `2a18767` | B | build_image.sh + promote_image.sh (Patch: nightly-Vorschlag, Major: seed) |
| internal_open_webui | `ghcr.io/open-webui/open-webui:main` | `main` | A | Diun + nightly (autonom Patch/Minor, Major reporten) |
| internal_postgres | `postgres:16-alpine` | `—` | A | Diun + nightly (autonom Patch/Minor, Major reporten) |
| internal_redis_twenty | `redis:7-alpine` | `—` | A | Diun + nightly (autonom Patch/Minor, Major reporten) |
| internal_twenty | `twentycrm/twenty:latest` | `—` | A | Diun + nightly (autonom Patch/Minor, Major reporten) |
| internal_whisper | `onerahmet/openai-whisper-asr-webservice:latest` | `—` | A | Diun + nightly (autonom Patch/Minor, Major reporten) |
| langgraph_analytics | `langgraph-analytics:latest` | `—` | B | build_image.sh + promote_image.sh (Patch: nightly-Vorschlag, Major: seed) |
| langgraph_content | `langgraph-content:latest` | `—` | B | build_image.sh + promote_image.sh (Patch: nightly-Vorschlag, Major: seed) |
| langgraph_helpdesk | `langgraph-helpdesk:latest` | `—` | B | build_image.sh + promote_image.sh (Patch: nightly-Vorschlag, Major: seed) |
| langgraph_interview | `langgraph-interview:latest` | `—` | B | build_image.sh + promote_image.sh (Patch: nightly-Vorschlag, Major: seed) |
| langgraph_redaktionsplan | `langgraph-redaktionsplan:latest` | `—` | B | build_image.sh + promote_image.sh (Patch: nightly-Vorschlag, Major: seed) |
| presentation_server | `nginx:alpine` | `—` | A | Diun + nightly (autonom Patch/Minor, Major reporten) |
| pwa_api | `pwa-api:latest` | `—` | B | build_image.sh + promote_image.sh (Patch: nightly-Vorschlag, Major: seed) |
| pwa_web | `pwa-web:latest` | `—` | B | build_image.sh + promote_image.sh (Patch: nightly-Vorschlag, Major: seed) |

## Major-Bumps NICHT autonom

Verfügbar laut Renovate-Dashboard-Issue / Folge-Seeds:

- **n8n** 2.13.3 → 2.21.7 (MINOR) — Migration: `seed-n8n-migration-2-21`
- **Node** 20 → 22, **Python** 3.12 → 3.13, **Tailwind** 3 → 4, **Next.js** 15 → 16, **lucide-react** 0 → 1 — Sammel-Seed: `seed-base-image-majors` / Renovate-Dashboard

## Vollabdeckungs-Akzeptanz (seed-aktualitaets-vollabdeckung)

Ziel erreicht, wenn diese sechs Klassen grüne Sicht haben:

| Klasse | Sicht heute |
|---|---|
| 1. Registry-Container | ✓ (Diun + nightly Sektion 1) |
| 2. Custom-Build-Container Base-Image | ✓ (nightly Sektion 7 + dieses Dokument) |
| 3. OS `-security` | ✓ (unattended-upgrades) |
| 4. OS `-updates` 1a/1b | ✓ (nightly Sektion 3) |
| 5. npm-Deps | ✓ (Renovate, Sektion 6) |
| 6. pip-Deps | ✓ (Renovate, Sektion 6) |

Plus n8n-Upstream als separate Achse 2 (nightly Sektion 8 + dieses Dokument).
