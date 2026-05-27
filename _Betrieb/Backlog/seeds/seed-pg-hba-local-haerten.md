---
typ: backlog_seed
titel: "pg_hba.conf: local all all trust ersetzen (In-Prozess-Bypass schließen)"
geltungsbereich: plattform
risikoklasse: sicherheitskritisch-akut
status: offen
klasse: security
zugkraft: bald
mission: security-haertung-plattform
stufe: spur
beruehrt: ["internal_postgres pg_hba.conf", "customer_postgres pg_hba.conf"]
stand: 2026-05-26
erzeugt_durch: "PLAT-029 Folge-Seed (Spec-Abschnitt 'Nicht Teil dieser Spec')"
abhaengig_von:
  - "[[Plattform/Archiv/PLAT-029/PLAT-029_SPEC_pg_hba_haerten]]"
---

## Anlass

PLAT-029 hat den `host`-Layer-Bypass (`trust 127.0.0.1/32` + breite
`host all all all`-Regel) in beiden produktiven Postgres-Clustern
geschlossen. **`local all all trust`** (Unix-Socket im Container)
bleibt vorerst aktiv — bewusst aufgehoben für eigenständige
Betrachtung, weil Reduktion Verfahrens-Klärung verlangt.

**Warum das wirklich gefährlich ist, nicht nur kosmetisch:** gegen den
`docker exec`-Angreifer (root im Container) ist die Härtung tatsächlich
kosmetisch — wer schon root im Container ist, hat Datenzugriff sowieso.
Aber gegen den **In-Prozess-Code-Execution-Pfad aus dem
CVE-2025-55182-Anlass** (Extension-Bug, SQL-Injection in einer
geladenen Extension, kompromittierter Sidecar mit Zugriff auf den
Container-Socket) wirkt `local trust` weiter als passwortloser
Superuser-Pfad — der Exploit läuft als `postgres`-User im Container und
erreicht den Unix-Socket direkt. Genau der Eskalationsschritt, den
PLAT-029 für 127.0.0.1 geschlossen hat, steht via Unix-Socket noch
offen.

## Ziel (Soll-Zustand, grob)

- `local all all trust` ersetzt durch ein Verfahren, das passwortlosen
  Superuser-Zugriff vom Unix-Socket aus verhindert — Kandidat:
  `local all all scram-sha-256` mit `.pgpass` für die PG-internen
  Wartungs-Tasks, oder `peer` mit OS-User-Mapping.
- `local replication all trust` analog behandelt (gleicher Pfad).
- Verifikation: passwortloser Connect via `docker exec` ohne
  PG-Credentials scheitert; PG-interne Wartungs-Tasks
  (`pg_dump`/`pg_dumpall` aus Container, `setup-db.sh`,
  Backup-Pipeline) funktionieren weiter.

## Stufe / Risiko

**Spur** — verlangt vorgelagerte Klärung welche internen Tasks heute
`local`-Connects nutzen (Backup-Pipeline, Init-Scripts, manuelle
`docker exec psql`-Sessions), bevor man auf scram/peer umstellt.
Sonst brechen Backup oder Init still. Spec-Phase muss diese Inventur
mitliefern.

## Trigger

`bald` — kein akuter Live-Bypass-Hinweis (im Gegensatz zum
PLAT-029-Anlass), aber der Rest-Bypass aus E45/E46-Logik gehört
geschlossen, bevor das nächste Extension-/Sidecar-CVE kommt. Realistisch
nach Stabilisierung der Bündel-Reihe um Logging-/Renovate-Härtung
(Phase-7-Backlog).

## Abgrenzung

- Nicht Teil dieses Seeds: PG-Passwort-Rotation, Role-Inventar
  (separater Strang).
- Nicht Teil dieses Seeds: pgaudit / Audit-Logging.
