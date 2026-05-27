---
typ: backlog_seed
titel: "PgBouncer vor customer_postgres — bei Connection-Druck einbauen"
geltungsbereich: plattform
risikoklasse: kritisch
status: offen
klasse: wartung
zugkraft: spaeter
stufe: spur
beruehrt: ["customer_postgres", "pwa-api Pool-Layer", "langgraph_* Pool-Layer"]
stand: 2026-05-27
erzeugt_durch: "PRIS-020 Bündel 4 (A8), Folge aus PgBouncer-Verzicht-Entscheidung"
abhaengig_von:
  - "[[Prisment/Archiv/PRIS-020/PRIS-020_ABSCHLUSS]]"
---

## Anlass

PRIS-020 A8 hat die `db_sync.py`-Pool-Variante konsolidiert (Common-Core,
Sync-Script). PgBouncer als zweite Skalierungs-Stufe wurde nach
G3-Faktencheck **bewusst nicht eingebaut**: aktuelle Auslastung bei einem
Live-Mandanten lag bei 5/100 Backends customer + 6/100 internal — kein
Druck, PgBouncer wäre Vorsorglich-Bloat ohne realen Nutzen.

Beim nächsten Mandanten-Onboarding (oder bei Lasttest gegen Mandant #2)
ist eine Re-Messung Pflicht. Wenn die Auslastung Richtung 60+/100 Backends
geht, ist PgBouncer einzubauen, bevor max_connections-Saturation eintritt.

## Trigger (operativ)

- `pg_stat_activity`-Count auf customer_postgres > 60 Backends im Mittel
  über 24h, ODER
- `pg_stat_activity`-Count auf internal_postgres > 60, ODER
- Ein Service liefert real `FATAL: too many connections for role`-Fehler.

## Ziel (Soll-Zustand, grob)

- PgBouncer im Transaction-Mode vor customer_postgres (Standard-Pattern
  für asyncpg/psycopg2-Clients).
- pwa-api + 4 LangGraph-Agents connecten gegen PgBouncer-Port statt direkt.
- Connection-Limits pro Pool-Typ in `pgbouncer.ini`:
  - admin_user-Pool: höhere Connection-Reserve (Cross-Tenant-Reads)
  - tenant_app_user-Pool: niedrigere, RLS-geschützt
- PgBouncer in Docker-Compose unter eigenem Service `pgbouncer-customer`,
  auf eigenem Netz `environment_a_db_customer`.
- Healthcheck + Diun-Updates.

## Stufe / Risiko

**Spur** — DB-Layer-Eingriff, Connection-Pfad live umgebaut. Verlangt
Architekten-Stopp pro Cluster-Cut (analog A8-Beschluss). Restic-Snapshot
Pflicht.

## Trigger

`spaeter` — kein akuter Druck, eigenständiger Bedarf wenn Mandanten-Anzahl
oder Load wächst. Eskalation auf `bald`/`jetzt` bei realem Druck-Beweis.
