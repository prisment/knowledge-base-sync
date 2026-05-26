---
name: postgres-connect
description: Standard-Verbindung zu den internen Postgres-Containern (internal_postgres als zentraler Hub) mit der richtigen User/DB-Kombination. Trigger wenn der Nutzer in eine interne Datenbank reinwill (Stichworte "psql", "postgres connect", "in die n8n DB", "in die gitea DB", "in die pwa_app DB", "in die twenty DB", "datenbanken listen", "tabelle in postgres", "internal_postgres", "postgres-hub", "DB-Größe", "pg-hub query"). Wrapper über die User/DB-Tabelle aus PostgreSQL-Hub.md.
---

# Skill: Postgres-Connect (internal_postgres Hub)

Zentraler Hub-Container: **`internal_postgres`** (Postgres 16).
Passwörter laufen via Docker Secrets, kein Klartext-Login nötig —
`docker exec` umgeht das.

## DB ↔ Owner-Tabelle

| Datenbank   | App-User          | Zweck                       |
|---|---|---|
| `gitea`     | `gitea_user`      | Gitea-Backend               |
| `n8n`       | `n8n_user`        | n8n-Backend                 |
| `twenty`    | `twenty_user`     | Twenty CRM                  |
| `pwa_app`   | `pwa_user`        | Prisment PWA                |
| `postgres`  | `postgres_root`   | Wartungs-/System-DB         |

## Standard-Aufrufe

```bash
# Als App-User in seine DB (lesen/schreiben gemäß Owner-Rechten)
docker exec -it internal_postgres psql -U <app_user> -d <db>

# Als Root in die Wartungs-DB
docker exec -it internal_postgres psql -U postgres_root -d postgres

# Konkret:
docker exec -it internal_postgres psql -U n8n_user    -d n8n
docker exec -it internal_postgres psql -U gitea_user  -d gitea
docker exec -it internal_postgres psql -U pwa_user    -d pwa_app
```

## Häufige Sofort-Queries

```bash
# Alle Datenbanken mit Größe
docker exec -it internal_postgres psql -U postgres_root -d postgres -c \
  "SELECT datname, pg_size_pretty(pg_database_size(datname)) FROM pg_database;"

# Alle Schemas der aktuellen DB
docker exec -it internal_postgres psql -U <user> -d <db> -c "\dn"

# Alle Tabellen eines Schemas
docker exec -it internal_postgres psql -U <user> -d <db> -c "\dt <schema>.*"

# Aktive Connections
docker exec -it internal_postgres psql -U postgres_root -d postgres -c \
  "SELECT datname, usename, state, count(*) FROM pg_stat_activity GROUP BY 1,2,3;"
```

## Skript-Variante (non-interactive)

```bash
# Ohne -t für Pipe-Verarbeitung
docker exec internal_postgres psql -U <user> -d <db> -tA -c "<query>"
```

## Wann dieser Skill greift

- Sofortige DB-Inspektion (Schemas, Tabellen, Datensätze).
- Diagnose: „warum sieht der n8n-Workflow keine Werte?" → erst Daten
  in der DB prüfen, dann Workflow-Logik.
- Größenmonitoring vor Backup-Größen-Sprüngen.

## Anti-Pattern

- **Keine** Migrationen oder Datenlöschung in Produktion ohne Architekten-
  Freigabe (Verfassungs-Pflicht). Dieser Skill ist **read/connect**, nicht
  „mach mal SQL".
- **Keine** Passwörter aus dem Skill heraus extrahieren — Docker Secrets
  reichen für `docker exec`.

## Nicht hierher

- **Postgres-Architektur, Backup, Rollen-Modell** →
  `Plattform/Systemzustand/Geteilte-Dienste/PostgreSQL-Hub.md`.
- **Rollen-Inventar** →
  `Plattform/Systemzustand/Sicherheit/pg_rollen_inventar.md`.
