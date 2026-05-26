---
typ: backlog_seed
titel: "Secret-Inventar codifizieren: pg_authid-Rollen-Audit pro Postgres-Cluster"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: prozess
zugkraft: bald
stufe: schritt
beruehrt: ["internal_postgres", "customer_postgres", "Plattform/Systemzustand/Sicherheit/ (neue Doku)"]
stand: 2026-05-26
erzeugt_durch: "Phase-7-Atomisierung HAERTUNGS_SPEC 7.8"
---

## Anlass

Im CVE-Incident-Postmortem fiel auf, dass der DB-User `tenant_app_user`
vergessen worden war (Phase-5-Vorarbeit) — er existierte zwar in der
DB, aber niemand wusste das. Lehre: ohne ein dokumentiertes Inventar
aller Login-Rollen pro PG-Cluster gibt es kein verlässliches Bild
über die Auth-Fläche.

## Ziel (Soll-Zustand, grob)

- Pro PG-Cluster (`internal_postgres`, `customer_postgres`):
  `SELECT rolname, rolsuper, rolcanlogin, rolbypassrls, rolcreatedb
  FROM pg_authid WHERE rolcanlogin;` ausführen und in einer
  versionierten Doku-Datei festhalten.
- Pro Rolle: Zweck, Owner-Service, Berechtigungs-Profil (BYPASSRLS
  ja/nein, SUPERUSER ja/nein, welche DBs/Tabellen).
- Aufnahme als Pflicht-Tor in den nightly-Report: Soll vs. Ist-
  Vergleich; neue oder verschwundene Login-Rolle → Alarm.
- Ablage: `Plattform/Systemzustand/Sicherheit/pg_rollen_inventar.md`
  (neu).

## Stufe / Risiko

**Schritt** — reines Lesen + Doku. Reversibel. Null Risiko für Live.

## Trigger

Bald — gehört vor oder direkt nach Phase 5 (Tenant-Isolation), weil
dort der `tenant_app_user` zentral wird. Sinnvoll als Vorarbeit:
Bestandsaufnahme deckt Lücken auf, bevor sie in der DB-Härtung weh
tun.
