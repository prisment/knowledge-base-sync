---
typ: uebersicht
geltungsbereich: plattform
thema: geteilte-dienste
stand: 2026-05-25
zweck: "Topologie + Datenflüsse zwischen den Plattform-Diensten. Sub-Übersicht zu 00_Bereich.md."
---

# Geteilte Dienste — Topologie + Datenflüsse

> **Sub-Übersicht.** Detaillierter Einstieg siehe `00_Bereich.md`.
> Diese Datei bleibt lokal (nicht im GitHub-Sync).

## In einem Absatz

Die geteilten Plattform-Dienste gruppieren sich in vier Cluster.
**Cluster A** ist die zentrale Daten-Schicht: `internal_postgres`
(PostgreSQL-Hub) bedient gitea, n8n, twenty, pwa_app (Prisment) und
open_webui — jeder Konsument in seinem eigenen `db_*`-Netz-Tunnel
(Lateral-Movement-Schutz). Twenty bekommt zusätzlich `internal_redis_twenty`
als dedizierten Cache im selben `db_twenty`-Netz, ebenfalls isoliert.
**Cluster B** ist die AI-Pipeline: open_webui, langgraph-Agents (Prisment)
und n8n als LLM-Konsumenten greifen über `net_ai_pipeline` auf
`internal_ollama` zu; `internal_whisper` macht Audio-Transkription für
den Onboarding-Workflow. **Kritisch:** Ollama hat keinen direkten
Internet-Zugang — der gesamte Egress läuft durch `internal_ollama_proxy`
(Squid mit Whitelist auf `*.ollama.ai`, `*.ollama.com`,
`*.r2.cloudflarestorage.com`). **Cluster C** ist Umami: `umami_app` plus
**eigener** `umami_postgres` (Postgres 15-alpine, bewusst nicht im
PG-Hub — getrennte Datenströme von App-Daten und Tracking-Daten).
**Cluster D** sind Edge-Helpers + Update-Notifier: `cloudflared` als
Tunnel-Endpoint hinter dem `foundation_traefik` weiterreicht, `cf_alarm`
als HMAC-Webhook-Empfänger für Cloudflare-Notifications, `internal_diun`
als Image-Update-Notifier (mit bekannter Lücke bei Custom-Builds —
`seed-diun-rueckbau.md`).

## Topologie als Bild (lokal)

![Geteilte Dienste](03_Geteilte-Dienste.svg)

> Das SVG bleibt nur lokal — Substanz steht oben im Absatz.

## Cluster-Übersicht

### A — PostgreSQL-Hub + Konsumenten

| Konsument | Netz | DB | Detail |
|---|---|---|---|
| `internal_gitea` | `db_gitea` | `gitea` | [gitea.md](../Geteilte-Dienste/gitea.md) |
| `internal_n8n` | `db_n8n` | `n8n` | [n8n.md](../Geteilte-Dienste/n8n.md) |
| `internal_twenty` (+ `internal_redis_twenty`) | `db_twenty` | `twenty` | [twenty-crm.md](../Geteilte-Dienste/twenty-crm.md), [redis-twenty.md](../Geteilte-Dienste/redis-twenty.md) |
| `pwa_api` (Prisment) | `db_pwa` | `pwa_app` | (Prisment-Bereich) |
| `internal_open_webui` | im Hub | `open_webui` | [openwebui.md](../Geteilte-Dienste/openwebui.md) |

Verwaiste DB: `paperless` (Owner `paperless_user`, kein Container) —
Backlog-Seed `seed-paperless-db-aufloesen.md`.

Hub-Detail: [PostgreSQL-Hub.md](../Geteilte-Dienste/PostgreSQL-Hub.md).

### B — AI-Pipeline

| Komponente | Rolle | Detail |
|---|---|---|
| `internal_ollama` | lokale LLM-Inferenz + Embedding (`mxbai-embed-large`) | [ollama.md](../Geteilte-Dienste/ollama.md) |
| `internal_ollama_proxy` | Squid-Egress-Whitelist (Anti-Exfiltration) | [ollama-proxy.md](../Geteilte-Dienste/ollama-proxy.md) |
| `internal_whisper` | Audio-Transkription (Onboarding-Audio-Upload) | [Whisper.md](../Geteilte-Dienste/Whisper.md) |

Konsumenten: `internal_open_webui` (RAG + Chat-UI), Prisment-langgraph-
Agents, `internal_n8n` (Embedding-Calls aus Workflows).

**Egress-Pfad:** `internal_ollama` → `internal_ollama_proxy` → Internet
(nur für Modell-Downloads, sonst blockiert).

### C — Umami isoliert

| Container | Image | Netz |
|---|---|---|
| `umami_app` | `ghcr.io/umami-software/umami:postgresql-v2.13.1` | `edge_internal` + `landingpage_umami_internal` |
| `umami_postgres` | `postgres:15-alpine` | nur `landingpage_umami_internal` |

Detail: [umami.md](../Geteilte-Dienste/umami.md). Bewusste Trennung vom
PG-Hub, damit Tracking-Daten und App-Daten getrennte Datenflüsse haben.

### D — Edge-Helpers + Update-Notifier

| Container | Rolle | Detail |
|---|---|---|
| `cloudflared` | Cloudflare-Tunnel-Endpoint | [cloudflared.md](../Geteilte-Dienste/cloudflared.md) |
| `cf_alarm` | HMAC-signiertes Cloudflare-Notification-Webhook | [Sicherheit/cf-alarm.md](../Sicherheit/cf-alarm.md) |
| `internal_diun` | Image-Update-Notifier (Registry-Tags) | [Sicherheit/diun-setup.md](../Sicherheit/diun-setup.md) |
| `foundation_traefik` | interner Reverse-Proxy (Foundation, read-only) | [Traefik.md](../Geteilte-Dienste/Traefik.md) |

## Historische Erwähnung

`KI-Wissenspipeline.md` trägt VERALTET-Header — beschreibt eine
RAG-Pipeline-Konzeption aus 2026-03 (Mattermost als Eingang, AnythingLLM
als Vektordatenbank). **Beide sind heute tot.** Aktuelle RAG läuft über
Cluster B (Open-WebUI) plus den n8n-Workflow `I: Git RAG Sync`, der
Markdown aus Gitea in die Vektor-Sicht synct. Re-Write-Seed:
`seed-sma-doku-rewrite-nach-telegram-eol.md` (für die Prisment-Dokus —
KI-Wissenspipeline.md selbst braucht eigenen kleineren Re-Write,
informeller Backlog-Punkt).

## Netz-Detail

Vollständige Netz-Topologie (alle 11 Docker-Netze + Externe Wege rein/raus):
[`../Geteilte-Dienste/network-topology.md`](../Geteilte-Dienste/network-topology.md).
