---
typ: backlog_seed
titel: "pg_hba.conf härten: trust 127.0.0.1/32 raus + host-Regeln subnetz-spezifisch"
geltungsbereich: plattform
risikoklasse: sicherheitskritisch-akut
status: offen
klasse: security
zugkraft: jetzt
stufe: sprung
beruehrt: ["customer_postgres pg_hba.conf", "internal_postgres pg_hba.conf"]
stand: 2026-05-26
erzeugt_durch: "Phase-7-Atomisierung HAERTUNGS_SPEC 7.3 + ROADMAP_cf_migration Phase-7-Backlog"
---

## Anlass

Beide Postgres-Cluster haben `trust 127.0.0.1/32` in `pg_hba.conf` —
Auth-Bypass auf Localhost. Bedeutet: jede Code-Execution im PG-
Container (z.B. via SQL-Injection in einem Extension-Plugin, oder
über einen kompromittierten Sidecar) erlaubt sofortigen Superuser-
Zugriff ohne Passwort. In der CVE-2025-55182-Incident-Auswertung als
aktiv ausgenutzter Bypass-Pfad eingestuft (Architekt-Markierung
„aktiv gefährlich", nicht „theoretisch").

Zusätzlich: die `host all all all`-Regel ist zu breit. Die
tatsächlichen Connect-Quellen sind die Docker-Subnetze
`environment_a_*`. Subnetz-spezifische Regeln senken Angriffsfläche
ohne Funktionsbruch.

## Ziel (Soll-Zustand, grob)

- `trust 127.0.0.1/32` aus beiden Clustern entfernen, ersetzt durch
  `host all all 127.0.0.1/32 scram-sha-256` (echtes Passwort).
- `host all all all scram-sha-256` durch subnetz-spezifische Einträge
  pro Docker-Network ersetzen (`db_*`-Networks aus compose.yml).
- Verifikation nach Reload: `psql -h localhost` ohne Passwort scheitert,
  alle Container-Verbindungen funktionieren weiter.

## Stufe / Risiko

**Sprung** — `pg_hba.conf`-Edit + Container-Reload pro Cluster, mit
Live-Daten. Wenn Subnetz-Regeln eine Connect-Quelle nicht abdecken,
brechen Tools. Pflicht: Backup, Test-Reload, Verifikations-Liste pro
Container der zu connecten erwartet wird. Architekt-Freigabe vor
Reload zwingend (DB-Auth, kritischer Pfad).

## Trigger

Jetzt — Architekt hat den Bypass explizit als „eigenständig dringend"
markiert. Sollte parallel zu Phase 5 (Tenant-Isolation) oder direkt
danach laufen — beide Themen berühren DB-Auth-Layer.
