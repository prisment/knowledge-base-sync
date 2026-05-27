---
name: service-onboarding
description: Verfahren zum Aufnehmen eines neuen Containers/Dienstes in environment_a in die Service-Registry. Trigger wenn ein neuer Service deployed werden soll oder ein bestehender Container in der Registry fehlt (Coverage-Audit-Befund "coverage-gap-container-*"). Liefert Entscheidungsbaum für Klasse A/B/C, Health-Endpoint-Definition, db_client-Flag, Registry-Edit + registry-apply.
---

# Skill: service-onboarding

Vor dem Deploy eines neuen Containers in `environment_a` ist ein
Registry-Eintrag PFLICHT (Plattform/CLAUDE.md). Ohne fehlen Drift-
Erkennung, Auto-Apply und Health-Coverage — der Service wird unsichtbar.

## Wann anwenden

- Neuer Container/Dienst soll in `environment_a` deployed werden.
- Coverage-Audit hat einen Auto-Seed `coverage-gap-container-<name>` gelegt
  (Container läuft, kein Registry-Eintrag).
- Bestehender Service ändert grundlegend (Klassen-Wechsel, neuer Health-
  Endpoint, db_client-Status ändert).

## Schritt 1 — Klasse wählen

Entscheidungsbaum:

```
Ist der Service "kritisch" (DB-Schema, Auth, Edge, Workflow-Engine)
oder hat er Migrations-Risiko zwischen Versionen?
  ↓ ja                                ↓ nein
  Klasse C                            ↓
  (immer betreut, Auto-Seed)          Ist der Service eine Eigen-Build
                                      mit Health-Endpoint + isoliert?
                                        ↓ ja                ↓ nein
                                        ↓                   Klasse C
                                      Ist er sehr klein und ohne DB-Bindung
                                      (Webhook-Empfänger, statischer Proxy)?
                                        ↓ ja                ↓ nein
                                      Klasse A              Klasse B
                                      (Patch+Minor auto)    (Patch auto,
                                                             Minor wirft Seed)
```

Beispiele heute:
- A: cf-alarm, ollama-proxy
- B: langgraph-*, admin-web, pwa-*
- C: n8n, gitea, postgres, twenty, alles Offizielle ohne semver-Patch-Sicherheit

## Schritt 2 — Health-Endpoint definieren

Pflicht-Feld `health: {type: "..."}`. Zwei Typen:

- `"docker"` — Container hat `HEALTHCHECK` im Dockerfile/Compose.
  `docker inspect --format '{{.State.Health.Status}}'` muss `healthy` liefern.
  Bevorzugt: jeder neue Service sollte einen Healthcheck haben.
- `"docker_running"` — kein Healthcheck, nur `State.Running == true` reicht.
  Nur für Services ohne echten Health-Endpoint (z. B. statischer Nginx-Proxy).

Wenn neu zu bauen: Health-Endpoint im Service implementieren
(`/health` mit 200, kein DB-Call wenn DB nicht zwingend), Compose-Healthcheck
ergänzen.

## Schritt 3 — db_client-Flag

`db_client: true` wenn der Service eine Verbindung zu internal_postgres,
customer_postgres, umami_postgres oder Redis hält. Wirkt in der pg_dump-
Verriegelung (apply-lib.sh): Apply darf db_client-Container nicht restarten
während pg_dump läuft.

Prüfen: `docker network inspect environment_a_default` + Compose-Connection-
String. Im Zweifel `true` (konservativ — kostet nur Apply-Zeitfenster).

## Schritt 4 — Registry-Edit

Datei: `Plattform/Systemzustand/00_Uebersicht/service-registry.json`

Eintrag im `services[]`-Array, alphabetisch nach Klasse + Name sortieren.
Pflicht-Felder: `name`, `container`, `klasse`, `build` (null bei offiziellem
Image, sonst `{script, image, context, promote, compose_service}`), `health`,
`db_client`, `base_image_drift`, `renovate`, `notes` (optional).

Schema-Doku: `service-registry.md` daneben.

## Schritt 5 — registry-apply

```bash
/home/claude-deploy/git/knowledge-base/_Betrieb/Skripte/registry/registry-apply.sh
```

Schritte:
1. Schema-Check (alle Pflicht-Felder, gültige Klasse, Build-Sub-Felder)
2. Generator-Skripte (allowlist-fragment)
3. Diff anzeigen

Wenn grün: Generator-Output ins Allowlist-Fragment unter
`_Betrieb/Skripte/registry/generated/allowlist-fragment.conf`.

## Schritt 6 — Allowlist scharf schalten (nur Klasse A/B)

Wenn der neue Service Klasse A/B ist, müssen die generierten Allowlist-
Stellen scharf geschaltet werden. Architekt führt aus:

```bash
sudo bash /home/claude-deploy/git/knowledge-base/_Betrieb/Skripte/pretooluse-hook/install-allowlist-fragment.sh
```

(Install-Skript merget allowlist-fragment.conf in /etc/claude/nightly-allowlist.conf.)

## Schritt 7 — Architekt-Review + Commit

- Diff von Registry + Allowlist-Fragment anschauen
- Commit-Message: `service-registry: <name> aufgenommen (Klasse <A|B|C>)`
- Push

Beim nächsten Nachtlauf (04:40) ist Coverage-Audit grün, Drift-Skripte
sehen den Service, Apply-Cron arbeitet ihn ab.

## Verweise

- Registry: `Plattform/Systemzustand/00_Uebersicht/service-registry.json`
- Schema-Doku: `Plattform/Systemzustand/00_Uebersicht/service-registry.md`
- Update-Routine-Übersicht: `Plattform/Systemzustand/00_Uebersicht/05_Update_Routine.md`
- PLAT-026-Spec: `Plattform/Arbeitsgedaechtnis/PLAT-026_SPEC.md`
