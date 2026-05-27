---
typ: backlog_seed
titel: "n8n: EXECUTIONS_DATA_PRUNE aktivieren (3.8 GB DB durch ungebremstes execution_data)"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: wartung
zugkraft: bald
mission:         ""
stufe: schritt
beruehrt: ["/opt/infrastructure/environment_a/docker-compose.yml (n8n-Service-ENV)", "internal_postgres/n8n.execution_data"]
stand: 2026-05-26
erzeugt_durch: "PRIS-016b Bündel 0.1.b Backup-Lauf (n8n pg_dump dauerte ~5 Min bei 3.8 GB DB; aufgefallen als Seitenbefund)"
---

## Anlass

Beim Restic-Backup-Lauf für PRIS-016b 0.1.b zeigte sich: `internal_postgres/n8n`
ist **3.8 GB groß**. `pg_dump -Fc n8n` braucht ~5 Minuten — der Großteil
hängt an `public.execution_data` mit aktuell **751 Workflow-Execution-
Zeilen** und großen JSONB-Payloads (`workflowData`, `data`,
`workflowVersion`). Es ist keine Retention konfiguriert (`docker exec
internal_n8n env | grep EXEC` liefert leer) — n8n speichert per Default
alle Executions ewig.

Konsequenzen, die heute noch klein sind, mit jedem Workflow-Lauf wachsen:
- Backup-Laufzeit pro Nacht steigt mit der DB.
- Restic-Storage-Kosten steigen mit der Backup-Größe.
- Beim Disaster-Recovery wird `pg_restore` proportional langsamer.
- Performance-Overhead intern (Index-Bloat auf execution_data).

## Ziel (Soll-Zustand, grob)

- n8n-Service in `docker-compose.yml` bekommt ENV-Variablen:
  - `EXECUTIONS_DATA_PRUNE=true`
  - `EXECUTIONS_DATA_MAX_AGE=336` (= 14 Tage in Stunden; konservativ)
  - `EXECUTIONS_DATA_PRUNE_MAX_COUNT=10000` (Hard-Limit als Absicherung)
- n8n-Container neu starten.
- Nach Stunden/Tagen: `execution_data`-Zeilenzahl + DB-Größe gesunken,
  Backup-Laufzeit zurück auf Minutenbruchteile.

**Vorsicht bei der Wahl der MAX_AGE:** 14 Tage ist defensiv. Falls
Webhooks/Workflows längere Debug-Sichtbarkeit brauchen (Architekten-
Frage), höher setzen. n8n-Doku empfiehlt 30 Tage als Default.

## Stufe / Risiko

**Schritt.** Compose-Edit + Container-Recreate. Reversibel (ENV
zurücknehmen, Container neu erstellen). Risikoklasse `sicher` — keine
Daten-Migration, keine Schema-Änderung. Beim ersten Aktivieren räumt
n8n den Backlog automatisch auf — dieser erste Lauf ist die einzige
größere Schreiboperation.

## Trigger

Bald — kein akuter Schmerz, aber: jedes weitere PRIS-Backup, jedes
Disaster-Recovery, jeder n8n-Performance-Test wird mit jedem Tag
unnötig teurer. Spätestens vor Onboarding Kunde #2 sinnvoll.

## Hinweis

`docker-compose.yml`-Edit braucht Architekten-Freigabe (Standard-Pfad
für Compose-Änderungen). Compose-Recreate mit `up -d --force-recreate
internal_n8n` ist reversibel; `n8n_data`-Volume bleibt unangetastet
(Storage liegt in der Postgres-DB, nicht im Volume).
