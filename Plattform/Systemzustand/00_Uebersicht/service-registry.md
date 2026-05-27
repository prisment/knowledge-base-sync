---
typ: schema-doku
titel: "Service-Registry — Schema und Pflege"
geltungsbereich: plattform
stand: 2026-05-27
related:
  - "[[Plattform/Systemzustand/00_Uebersicht/service-registry.json]]"
  - "[[Plattform/Systemzustand/00_Uebersicht/05_Update_Routine.md]]"
  - "[[Plattform/Arbeitsgedaechtnis/PLAT-026_SPEC]]"
---

# Service-Registry — SSOT der Update-Routine

`service-registry.json` ist die **einzige Wahrheit**, aus der die Update-Routine ihre Klassifikation, Build-Befehle, Health-Endpoints und Klient-Verbindungen kennt. Alle Drift-Skripte, Apply-Skripte, Allowlist-Fragmente und Renovate-Manager werden daraus generiert (`make registry-apply` — PLAT-026 Bündel 2).

## Warum JSON statt YAML

Ursprünglich war YAML angedacht. Python-Stdlib hat keinen YAML-Parser, und CLAUDE.md verbietet `pip install` auf dem Host. JSON ist Stdlib, lesbar genug für ~25 Services, und Generator-Skripte sind eine Zeile (`json.load`).

## Feld-Schema pro Service

| Feld | Typ | Pflicht | Bedeutung |
|---|---|---|---|
| `name` | string | ja | Service-Bezeichnung, Slug-Form (`langgraph-content`, nicht `langgraph_content`) |
| `container` | string | ja | Docker-Container-Name wie in `docker ps` (`langgraph_content`) |
| `klasse` | `"A"\|"B"\|"C"` | ja | A = vollautonom, B = autonom + Seed bei Minor, C = immer betreut |
| `build` | object \| null | ja | null bei offiziellen Images. Bei Custom-Builds: `{script, image, context, promote, compose_service}` |
| `health` | object | ja | `{type: "docker"\|"docker_running"}`. `docker` = `State.Health.Status==healthy`; `docker_running` = `State.Running==true` (Container ohne Healthcheck) |
| `db_client` | bool | ja | true wenn Service Postgres-/Redis-Verbindung hält. Verriegelt Rebuild während pg_dump läuft |
| `base_image_drift` | bool | ja | true wenn `check-base-image-drift.sh` den Service scannen soll (nur Custom-Builds mit Dockerfile) |
| `renovate` | bool | ja | true wenn Renovate-Manager das Custom-Build-Repo scannen soll |
| `notes` | string | optional | freier Text |

## Klassen-Definition (Kurzfassung)

- **A — vollautonom:** Patch + Minor (Base-Image) + Renovate-Patch/Minor laufen autonom in der Nacht. Rollback bei Health-rot, Auto-Seed bei Rollback.
- **B — autonom + Seed bei Minor:** Patch (Base-Image) autonom, Minor wirft Auto-Seed (keine Auto-Apply). Renovate-Patch/Minor autonom.
- **C — immer betreut:** kein Auto-Apply. Drift-Befund (Patch/Minor/Major) wirft Auto-Seed. Architekt entscheidet.

## Pflege

**Neuer Container in env_a:** **PFLICHT**, einen Eintrag in `service-registry.json` zu setzen, bevor er deployed wird. Verfahren: Skill `service-onboarding` (kommt in Bündel 9).

**Was passiert, wenn vergessen:** der Coverage-Audit (täglich 04:40, kommt in Bündel 5) legt am nächsten Morgen einen Auto-Seed `coverage-gap-container-<name>` im Backlog an. Du siehst die Lücke aktiv, ohne Reports zu lesen.

## Heutige Verteilung (Stand 2026-05-27)

| Klasse | Anzahl | Services |
|---|---|---|
| A | 2 | cf-alarm, ollama-proxy |
| B | 8 | langgraph-{analytics,content,helpdesk,interview,redaktionsplan}, admin-web, pwa-api, pwa-web |
| C | 14 | n8n + alle offiziellen Images (gitea, postgres, customer-postgres, redis-twenty, twenty, open-webui, ollama, whisper, cloudflared, diun, assets-server, presentation-server, gitea-runner) |

## Bewusste Auslassungen

- `foundation_traefik`, `foundation_socket_proxy`: nicht env_a-Compose-Projekt, eigener Pflegezyklus.
- `landingpage_web`, `umami_app`, `umami_postgres`: andere Compose-Projekte (Marketing-Site, Analytics).
- `paperless-db`: 2026-05-26 entfernt (PLAT-016).

Diese Container werden vom Coverage-Audit ignoriert (Filter `label=com.docker.compose.project=environment_a`).
