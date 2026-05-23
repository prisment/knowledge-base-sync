---
typ: backlog_seed
titel: "Migration der ausgesonderten Code-Dateien ins prisment-platform-Repo"
geltungsbereich: plattform
risikoklasse: kritisch
status: offen
stand: 2026-05-23
erzeugt_durch: BOOT-001
---

## Anlass

BOOT-001 sondert Code-Dateien (Python, n8n-JSONs, Shell-Skripte, docker-compose,
nginx.conf etc.) aus der Knowledge-Base aus und schiebt sie nach
`_AUSGESONDERT_CODE/` mit `Inventar.md`. Diese Dateien gehören laut Verfassung
03 (Code-Standards) ins separate Code-Repo `prisment-platform`
(`/opt/infrastructure/environment_a/`) — nicht in die KB. Die Aussonderung
bewahrt sie reversibel; die tatsächliche Überführung ins Code-Repo ist eine
eigene Mini-Spec mit eigener Risiko-Bewertung.

## Soll-Zustand (grob, wird in der späteren Spec präzisiert)

- Jede Code-Datei aus `_AUSGESONDERT_CODE/` ist im `prisment-platform`-Repo
  am passenden Pfad eingecheckt oder als nicht mehr benötigt protokolliert
  (mit Begründung).
- `_AUSGESONDERT_CODE/` ist leer und wird entfernt.
- Etwaige Drift zwischen ausgesondertem Stand und aktuellem `prisment-platform`-
  Stand ist je Datei explizit entschieden (Code-Repo gewinnt / KB-Stand übernehmen
  / verwerfen).

## Offene Fragen für die spätere Spec

- Ist der ausgesonderte Stand älter/jünger als der jeweilige Code im laufenden
  Code-Repo? (Pro Datei mit `diff` prüfen.)
- Welche n8n-Workflow-JSONs sind aktuell live in n8n und welche nur Archiv?
- Sind die `docker-compose.yml`-Schnipsel unter `05_Entwicklung/test-env/` und
  `05_Entwicklung/Marketing/Landingpage/server/` noch in Verwendung oder durch
  das Mono-Repo unter `environment_a/` obsolet?

## Risiko-Hinweis

Risikoklasse kritisch, weil Code-Bewegung Live-Workflows und Container betrifft.
Pro Datei explizite Entscheidung, nicht pauschal.
