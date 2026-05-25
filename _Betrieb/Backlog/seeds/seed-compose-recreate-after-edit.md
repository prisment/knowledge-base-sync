---
typ: backlog_seed
titel: "Pflicht-Recreate nach Compose-Topologie-Edit"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: wartung
zugkraft: bald
stufe: sprung
beruehrt: ["_Betrieb/Verfassung/", "scripts/"]
stand: 2026-05-24
erzeugt_durch: PLAT-001 Bündel 3.1 (Postgres-Recreate-Vorfall)
---

## Anlass

In PLAT-001 Bündel 3.1 (Docker-CE-Update) kollabierte beim Daemon-Restart ein
latenter Netzwerk-Anker (`db_authentik`) im laufenden `internal_postgres`-
Container. Der Anker stammte aus einem früheren Compose-Edit, der **NICHT** mit
`docker compose up -d --force-recreate` aktiviert worden war — der alte
Lifecycle-Zustand lebte im Container weiter, bis er beim Daemon-Restart
auseinanderfiel.

## Soll-Zustand

- **Pflicht-Disziplin:** Nach jedem Compose-Edit, der Container-Topologie ändert
  (Netzwerke, Volumes, Dependencies, Labels), sofort
  `docker compose -p environment_a up -d --force-recreate <svc>` ausführen.
  NICHT erst beim nächsten Daemon-Restart, NICHT erst beim nächsten Update.
- **Mechanische Absicherung (Optionen, zur Spec-Entscheidung):**
  - Git-pre-commit-Hook im prisment-platform-Repo, der bei Diff in
    `docker-compose.yml` einen Reminder ausgibt oder das Recreate forciert.
  - Doku-Eintrag in `Plattform/Systemzustand/Geteilte-Dienste/<svc>.md` als
    Pflichtschritt der „Compose-ändern"-Checkliste.
  - Optional: ein Verifikations-Script, das laufende Container-Configs gegen
    die aktuelle Compose-Datei vergleicht und Drift meldet.

## Trigger / Abhängigkeit

- Niedrig-mittel priorisiert. Kein akuter Defekt, aber latente
  Topologie-Bomben sind genau die Klasse von Befunden, die spät und unklar
  zurückkommen (siehe E19).

## Offene Fragen für die spätere Spec

- Welche Edits am Compose triggern den Pflicht-Recreate genau (alles?
  nur Topologie-Felder)?
- Greift der Mechanismus auch bei Edits via API (n8n / langgraph hot-reload),
  oder nur bei manuellen YAML-Edits?
- Verhältnis zu BUILD-Mechanik (`scripts/build_image.sh` + `promote_image.sh`):
  Recreate ist Teil des `promote`-Schritts, dort schon abgedeckt.
