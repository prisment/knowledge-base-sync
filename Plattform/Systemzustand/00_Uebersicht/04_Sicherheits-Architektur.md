---
typ: uebersicht
geltungsbereich: plattform
thema: sicherheits-architektur
stand: 2026-05-25
zweck: "Schichten-Modell der Verteidigungslinien, HARDENING-Status, nightly-Mechanik. Sub-Übersicht zu 00_Bereich.md."
---

# Sicherheits-Architektur — Plattform

> **Sub-Übersicht.** Detaillierter Einstieg siehe `00_Bereich.md`.
> Diese Datei bleibt lokal (nicht im GitHub-Sync).

## In einem Absatz

Sicherheit kommt nicht aus einer einzelnen Schicht, sondern aus
**vier hintereinander gestaffelten Verteidigungslinien** für Web-Traffic
plus einem **parallelen Admin-Pfad** über Tailscale, der gar nicht
öffentlich existiert. **Schicht 1 (Cloudflare Edge)** terminiert TLS,
filtert WAF/Bot/DDoS für öffentliche Hosts und blockt CVE-Scanner —
Origin-IP des Servers ist nicht öffentlich. **Schicht 2 (Cloudflare
Access)** ist die Identity-Schicht: für die acht Access-Apps (`n8n`,
`git`, `ai`, `crm`, `admin`, `konzept`, `agent-content`,
`analytics-admin`) erzwingt sie Login per E-Mail
(`korbinian.schnall@prisment.de`) und reicht den `Cf-Access-...`-Header
an den Origin durch. **Schicht 3 (Cloudflare Tunnel, `cloudflared`)** ist
der einzige HTTPS-Eingang — kein offener Inbound-Port am Server.
**Schicht 4 (App-Session)** ist die container-eigene Auth (Twenty-JWT,
n8n-Session, Open-WebUI-Session), die den CF-Access-Headern vertraut.
**Schicht 5 (geplant — Tenant-Isolation)** wird fail-closed RLS in
`customer_postgres` etablieren, bevor Kunde #2 onboardet wird — das ist
HARDENING Phase 5 und noch offen. Der **parallele Admin-Pfad** (Tailscale
Mesh-VPN) bindet SSH-22 und Gitea-SSH-2222 ausschließlich an die
Tailnet-IP — niemand außer Korbinian kommt überhaupt in die Nähe.
**Nightly-Mechanik** sichert das System operativ: unattended-upgrades
für OS-Patches, Diun für Container-Image-Updates, integritätsgeprüfte
Steuerdatei `nacht-aufgaben.md` (SHA-256), Morgen-Report als
Nachweis-Spur. Bekannte Lücke: Custom-Builds (n8n, ollama-proxy,
langgraph-*) fallen aus der Update-Pipeline durch (eigener Seed).
**Backup-Schicht** als letztes Sicherheits-Netz: Restic mit
AES-256-Verschlüsselung + WORM Object Lock auf Hetzner S3, täglich,
Restore-Pfad komplett dokumentiert.

## Architektur als Bild (lokal)

![Sicherheits-Architektur](04_Sicherheits-Architektur.svg)

> SVG nur lokal — Substanz steht oben im Absatz.

## HARDENING-Status (HAERTUNGS_SPEC_PRISMENT.md)

| Phase | Stand | Beleg |
|---|---|---|
| CF-MIG (Authentik → CF Access) | ✅ abgeschlossen | Logbuch E26, Mai 2026 |
| Telegram-EOL | ✅ abgeschlossen | Logbuch E25 (PLAT-009 2026-05-25) |
| Public-Exposure-Reduktion (Mattermost weg, Test-Familie deprecated) | ✅ abgeschlossen | HARDENING Phase 1.1 / 1.4 |
| **Tenant-Isolation (RLS in customer_postgres)** | ⬜ **noch offen** | Phase 5 · vor Onboarding Kunde #2 |
| WAF-Skip für Access-Apps | ✅ abgeschlossen | Logbuch E26 (BREAKOUT in PLAT-009) |

Detail: [`HAERTUNGS_SPEC_PRISMENT.md`](../Sicherheit/HAERTUNGS_SPEC_PRISMENT.md).

## Nightly-Mechanik

- [`nightly-runtime.md`](../Sicherheit/nightly-runtime.md) — Lauf-Pfad, Tool-Set, Sicherheits-Tor
- [`nacht-aufgaben.md`](../Sicherheit/nacht-aufgaben.md) — Steuerdatei (gehasht, Integritäts-gecheckt)
- [`os-updates-runbook.md`](../Sicherheit/os-updates-runbook.md) + [`os-update-log.md`](../Sicherheit/os-update-log.md) — unattended-upgrades
- [`diun-setup.md`](../Sicherheit/diun-setup.md) — Image-Update-Notifier (mit Custom-Build-Lücke)
- [`docker-update-log.md`](../Sicherheit/docker-update-log.md) — Container-Update-Verlauf
- Morgen-Reports in `Plattform/Arbeitsgedaechtnis/nacht-report-YYYY-MM-DD.md`
- **Liveness-Watchdog (PLAT-020):** eigener Cron 06:30 prüft Heartbeat
  des Nightly-Laufs; bei Stille → `NIGHTLY-AUSFALL-<datum>.md` ins
  Arbeitsgedächtnis (mail-unabhängige Sichtbarkeits-Garantie). Pre-
  commit-Hook erzwingt `nacht-aufgaben.md ↔ .sha256`-Konsistenz —
  Vergess-Aussetzen wie am 25./26.05. nicht mehr möglich.

## Backup & Disaster Recovery

- **Tool:** Restic (AES-256, deduplizierend)
- **Ziel:** Hetzner Object Storage S3 (nbg1), **WORM Object Lock aktiv**
- **Zeitplan:** täglich 03:00 via Root-Cronjob
- **Monitoring:** msmtp → `info@prisment.de` bei jedem Lauf
- **Restore-Runbook:** [`RESTORE.md`](../Sicherheit/RESTORE.md) — vollständiger Wiederherstellungs-Pfad auf frischer Ubuntu-VM, Voraussetzung: Restic-Passphrase aus Keepass
- **Backup-Mechanik-SOP:** [`backup-restore.md`](../Geteilte-Dienste/backup-restore.md)

## Cloudflare-Alarm

`cf_alarm` (Container) empfängt HMAC-signierte Cloudflare-Notifications
(TLS-Cert-Events, Tunnel-Status, Account-Audit etc.) und routet sie weiter
— siehe [`cf-alarm.md`](../Sicherheit/cf-alarm.md). Externer Webhook ist
`cf-alarm.prisment.de` (public, aber HMAC-validiert · WAF + Bot Fight
skipped via cf-alarm-spezifischer Custom-Rule seit BOOT-001 Bündel E).

## Eingang Tailscale (paralleler Pfad)

- Tailnet-IP `100.100.69.78` · Server ist „unbemannter Node"
- **Key-Expiry deaktiviert** in Tailscale-Admin (sonst 180-Tage-Abbruch)
- SSH-22 + Gitea-SSH-2222 binden ausschließlich an Tailnet-IP (nicht `0.0.0.0`)
- Bind-Konvention im Compose: `100.100.69.78:port:port` (verhindert UFW-Bypass durch Docker)
- Detail: [`tailscale.md`](../Geteilte-Dienste/tailscale.md), [`gitea.md`](../Geteilte-Dienste/gitea.md)
- Hintergrund-Entscheidung: Tailscale bleibt manuell verwaltet (Logbuch E21)
