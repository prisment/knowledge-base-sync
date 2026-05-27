---
typ: backlog_seed
titel: "Firewall-Drop Port 22 auf Public-Interface (defense-in-depth über SSH-Socket-Bind)"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: security
zugkraft: bald
mission:         security-haertung-plattform
stufe: sprung
beruehrt: ["UFW/iptables-Regeln Host-Level", "Public-Interface eno1 (Hetzner)"]
stand: 2026-05-26
erzeugt_durch: "Phase-7-Atomisierung HAERTUNGS_SPEC 3.4 (Phase 3 Aufräum-Backlog)"
---

## Anlass

Phase 3 SSH-Lockdown bindet `ssh.socket` ausschließlich auf
`100.100.69.78:22` (Tailscale-IP). Defense-in-depth fehlt: wenn der
Socket-Override aus irgendeinem Grund verloren ginge (Systemd-
Generator-Bug, Drop-In-Datei versehentlich gelöscht, Reload greift
falsch), wäre Port 22 schlagartig wieder öffentlich erreichbar. Eine
Firewall-Regel auf Host-Level würde diese Lücke verhindern.

## Ziel (Soll-Zustand, grob)

- UFW (oder direkt nftables) installieren / konfigurieren.
- Regel: Port 22 auf Public-Interface `eno1` droppen, auf
  Tailscale-Interface `tailscale0` erlauben.
- Verifikation: `iptables -L INPUT` oder `nft list ruleset` zeigt
  die erwartete Regel; SSH über Public-IP weiterhin refused, SSH
  über Tailscale-IP klappt.
- Mit Dead-Man-Switch analog SSH-Lockdown (UFW-Regel-Rollback
  binnen 10 Min via `at`, falls Verbindung bricht).

## Stufe / Risiko

**Sprung** — Aussperrungs-Risiko niedrig (zweiter unabhängiger
Pfad existiert via Tailscale), aber Firewall-Konfig-Fehler kann
trotzdem Tailscale-Interface mit erwischen wenn Regel zu breit
gestrickt ist. Pflicht: parallel SSH-Sessions + Dead-Man-Switch
analog Block 7.1.

## Trigger

Bald — kein Eilfall, aber überfällig. Spätestens vor Phase-6
(PWA-Wiederanlauf), weil dann Lockdown-Drift ein größeres
operatives Risiko wird.
