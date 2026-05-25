---
typ: uebersicht
geltungsbereich: plattform
thema: infrastruktur
stand: 2026-05-25
zweck: "Hardware, Compose-Stacks, Verzeichnisstruktur, Update-Pipeline-Status. Sub-Übersicht zu 00_Bereich.md."
---

# Infrastruktur — Plattform

> **Sub-Übersicht.** Detaillierter Einstieg siehe `00_Bereich.md`.
> Diese Datei bleibt lokal (nicht im GitHub-Sync).

## Hardware

Ein Hetzner-Rootserver, dediziert. Kein Cluster, kein Failover-Node.

| | |
|---|---|
| Modell | Hetzner AX42U (Dedicated) |
| CPU | AMD Ryzen 7 Pro 8700GE (8C/16T) |
| RAM | 64 GB ECC |
| Storage | 2× 512 GB NVMe (RAID 1) |
| OS | Ubuntu 24.04 LTS |
| Standort | Hetzner nbg1 (Nürnberg) |

Notausgang bei OS-Bricked: Hetzner-Rescue-System (übers Hetzner-Robot-
Webinterface aktivierbar — siehe `Sicherheit/RESTORE.md`).

## Zwei Compose-Stacks (strikt getrennt)

### `foundation/` — read-only durch claude-deploy

```
/opt/infrastructure/foundation/
├── docker-compose.yml        ← Traefik + Socket-Proxy
└── traefik/
    ├── traefik.yml           ← Statische Konfig
    ├── dynamic.yml           ← File-Provider (Middleware-Defs)
    └── acme.json             ← (historisch; heute via Cloudflare-Origin-Cert)
```

Container: `foundation_traefik`, `foundation_socket_proxy`. Änderungen
**ausschließlich manuell durch den Architekten** (globale CLAUDE.md).
Begründung der Trennung: Edge-Routing darf nicht durch fahrlässige
Compose-Edits in `environment_a/` gebrochen werden.

### `environment_a/` — Business-Services

```
/opt/infrastructure/environment_a/
├── docker-compose.yml        ← alle Plattform- und Prisment-Container
├── .env                      ← Domains, non-secret Config
├── secrets/                  ← Docker File-based Secrets (tmpfs)
├── n8n/Dockerfile + workflows/   ← Custom-Build + Workflow-Sync-Skript
├── langgraph/<agent>/        ← Custom-Builds (content, redaktionsplan, …)
├── ollama/ + squid.conf      ← Custom-Build ollama-proxy
├── pwa/ + admin/             ← Prisment-Produkt
├── cf-alarm/                 ← Custom-Build cf-alarm
├── customer/                 ← Prisment Mandantendaten-DB (pgvector)
├── postgres/init-scripts/    ← PG-Hub Initialisierung
└── scripts/                  ← build_image.sh, promote_image.sh, sync_n8n_workflows.py
```

Claude-deploy darf hier alles schreiben **außer** `secrets/`. Compose-
Änderungen brauchen Einzelfreigabe vor jeder Aktion (systemkritisch).

## Update-Pipeline (Stand 2026-05-25)

| Schicht | Mechanik | Abdeckung |
|---|---|---|
| **OS-Patches** | `unattended-upgrades` täglich + Verifikation im Nacht-Report | ✅ vollständig |
| **Container mit Registry-Tag** | Diun überwacht, nightly meldet Patch/Minor/Major im Morgen-Report | ✅ z.B. postgres, redis, gitea, twenty, openwebui, cloudflared, umami |
| **Custom-Build-Container** | Re-Build via `scripts/build_image.sh` + Promote via `scripts/promote_image.sh` (mit `prev-`-Backup-Tag) | ⚠️ **manuell** — Diun meldet sie als „failed", kein Auto-Update |
| **Prisment-Builds** (`langgraph-*`, `pwa-*`, `admin-*`, `cf-alarm`) | dito Custom-Build-Pfad | ⚠️ manuell |

**Bekannte Drift-Lücke:** n8n läuft auf 2.13.3 (Stand 2026-05-25),
9 Versionen hinter aktuell — Custom-Build mit git+openssh, deshalb von
Diun nicht erfasst, im Nacht-Report als „failed" eingeordnet.
Eigener Folge-Zyklus: `_Betrieb/Backlog/seed-diun-rueckbau.md`
(Nachtrag 2026-05-25 verschärft die Vorbedingung auf
Custom-Build-Vollabdeckung).

## Backup-Mechanik (in zwei Sätzen)

**Restic** (AES-256, deduplizierend) sichert täglich um 03:00 nach
Hetzner Object Storage S3 mit WORM Object Lock — Scope:
`/opt/infrastructure/environment_a/` (IaC + Configs + Secrets-Verzeichnis),
`/var/lib/docker/volumes/`, plus konsistenter PostgreSQL-Gesamtdump.
**Restore-Pfad** komplett dokumentiert in
[`Sicherheit/RESTORE.md`](../Sicherheit/RESTORE.md) (Voraussetzung:
Restic-Passphrase aus Keepass).

Detail-SOP: [`Geteilte-Dienste/backup-restore.md`](../Geteilte-Dienste/backup-restore.md).

## Service-Inventar

Die 14 aktiven Plattform-Container plus 2 Foundation-Container sind in
[`00_Bereich.md`](00_Bereich.md) und in der zentralen Detail-Doku
[`../Infrastruktur/00_Systemübersicht.md`](../Infrastruktur/00_Systemübersicht.md)
namentlich aufgeführt. Image-Pinning-Übersicht:
[`../Infrastruktur/00_System_Environment_Versions.md`](../Infrastruktur/00_System_Environment_Versions.md).
