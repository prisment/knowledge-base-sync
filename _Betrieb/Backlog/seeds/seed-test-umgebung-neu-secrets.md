---
typ: backlog_seed
titel: "Test-Familie neu aufbauen: eigene Secrets, intern-only, eigene Test-DB"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: security
zugkraft: bald
stufe: spur
beruehrt: ["docker-compose.test.yml", "neue secrets-Familie", "neue Test-DB im customer_postgres", "Tailscale-Reichweite"]
stand: 2026-05-26
erzeugt_durch: "Phase-7-Atomisierung HAERTUNGS_SPEC 7.6 + ROADMAP_cf_migration Phase-7-Backlog"
---

## Anlass

In Phase 1 (HAERTUNGS-Spec, 1.4) wurde die alte Test-Familie samt
Volumes vernichtet, weil ihre Secrets im CVE-Incident verbrannt
waren UND sie öffentlich erreichbar lief. Aktuell existiert keine
Test-Umgebung — alle Live-Tests laufen am Prod. Das ist auf Dauer
nicht haltbar (kein Spielfeld für Image-Promotion-Tests, RLS-Tests,
Migration-Tests).

## Ziel (Soll-Zustand, grob)

- Neue Test-Familie als eigenständiger Compose-Block, intern-only
  (nur über Tailscale erreichbar — Public-Routes existieren nie).
- Eigene Secrets-Familie: `secrets/*_test.txt`, nie identisch mit
  Live. Eigene OAuth-Apps, eigene API-Keys, eigene Crypto-Keys.
- Eigene Test-DB im `customer_postgres` (DB `test_db` mit eigenem
  Test-Tenant), keine Berührungspunkte zu Live-Mandanten.
- Promote-Pfad bleibt sauber: `build_image.sh` baut `:test`-Tag,
  Test-Compose nutzt `:test`, nach Test-Klick promote auf Live.

## Stufe / Risiko

**Spur** — viele Bauteile (neue Secrets, neue DB, neuer Compose-
Block, Traefik-Routes). Reversibel (alles zusammen weg). Risiko
niedrig solange intern-only. Pflicht: Secret-Generierung mit Mensch-
Hand (kein Skript darf Live-Secrets versehentlich kopieren).

## Trigger

Bald — vor allem nach Phase-5-Abschluss (Tenant-Isolation getestet
sich gegen die neue Test-Tenant-Schiene angenehmer als gegen Live-
Mandanten).
