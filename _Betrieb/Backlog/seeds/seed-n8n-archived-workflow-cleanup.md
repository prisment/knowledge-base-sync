---
typ: backlog_seed
titel: "Archivierter n8n-Workflow YRMUaRbrtsC40CWr aus DB löschen (Pre-PRIS-020-Auth)"
geltungsbereich: intern
risikoklasse: sicher
status: offen
klasse: wartung
zugkraft: bald
mission: ""
stufe: schritt
beruehrt: ["internal_n8n DB", "n8n/workflows/t-praesentation-versenden.json (Repo)"]
stand: 2026-05-27
erzeugt_durch: "PRIS-020 Bündel 3 (A5) Restschuld"
abhaengig_von:
  - "[[Prisment/Archiv/PRIS-020/PRIS-020_ABSCHLUSS]]"
---

## Anlass

PRIS-020 A5 hat die n8n-Workflow-Auth auf Per-Caller-Secrets migriert. Zwei
Präsentations-Workflows existieren in der n8n-DB:

- `v8qI0g91lh8TVe12` ("T: Präsentation: Entwurf erstellen"): inaktiv,
  nicht archiviert → via n8n-API erfolgreich gepatcht (X-Agent-Caller +
  neues Per-Caller-Secret).
- `YRMUaRbrtsC40CWr` ("T: Präsentation: Versenden"): inaktiv UND
  archiviert (`isArchived=true`). API liefert auf PUT:
  `"Cannot update an archived workflow."` → DB-State unverändert mit
  altem Legacy-Auth-Header.

Die Repo-Variante (`n8n/workflows/t-praesentation-versenden.json`) ist
gepatcht. ABER: bei Reaktivierung über die n8n-UI lädt n8n die Definition
aus der eigenen DB, NICHT aus dem Repo. Heißt: wenn jemand diesen Workflow
in der UI ent-archiviert + aktiviert, läuft er mit dem alten Auth-Header
gegen pwa-api — der wirft jetzt 403 (Legacy-Pfad strukturell tot seit
A5-Drop).

## Ziel (Soll-Zustand)

**Workflow aus der n8n-DB löschen** (tot, nicht schlafend — explizite
`isArchived=true`-Markierung des Architekten ist die Nicht-mehr-genutzt-
Aussage). Repo-Variante bleibt als historische Referenz erhalten.

## Verfahren

1. n8n-API DELETE auf den Workflow: `DELETE
   http://internal_n8n:5678/api/v1/workflows/YRMUaRbrtsC40CWr` mit
   X-N8N-API-KEY-Header (Token via Postgres-Lookup wie in n8n-zugang-Skill).
2. Verifikation: GET auf denselben Endpoint liefert 404.
3. Backup vor Delete: per API GET den Workflow exportieren in
   `n8n/workflows/archive/YRMUaRbrtsC40CWr-before-delete.json`.

## Stufe / Risiko

**Schritt** — Workflow ist seit Wochen inaktiv + archiviert, kein
Verbraucher in Sicht. Delete ist via API reversibel durch Re-Import des
Backups.

**Architekten-Freigabe** brauche per Vorkommen (globales CLAUDE:
„Datenlöschung in Produktion = immer per Vorkommen"). Trotz `schritt`-
Stufe kein autonomer Cut.

## Trigger

`bald` — nicht akut, aber je länger der archivierte Workflow in der DB
bleibt, desto wahrscheinlicher wird eine versehentliche Reaktivierung mit
Legacy-Auth-Bruch. In nächsten ~14 Tagen mit Architekten-Freigabe abarbeiten.
