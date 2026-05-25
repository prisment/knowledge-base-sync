---
typ: backlog_seed
titel: "Diun zurückbauen, sobald nightly Stabilität bewiesen hat"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: wartung
zugkraft: irgendwann
stufe: sprung
beruehrt: ["diun/", "n8n/workflows/"]
stand: 2026-05-25
erzeugt_durch: BOOT-001
nachtrag: 2026-05-25 (n8n-Update-Drift aufgedeckt — Vorbedingung verschärft)
---

## Anlass

Diun (Docker Image Update Notifier, läuft als Container in `environment_a` und
schickt Mails über die SMTP-Secrets) wurde als passive Image-Update-Notification
eingerichtet. Seit BOOT-001 Bündel E übernimmt der nächtliche Lauf
(`run_nightly.sh` + Steuerdatei + Tool-Set) diese Aufgabe deutlich reicher:

- Unterscheidet Patch / Minor / Major (Diun: nicht).
- Korreliert mit CVE-Quellen (Diun: nicht).
- Spielt Updates ein mit Rollback (Diun: rein Notification).
- Macht Funktions-Health-Check (Diun: nichts).

Diun ist damit semantisch redundant. Aber: solange der nightly noch nicht
bewiesen hat, dass er stabil läuft (keine Fehlalarme, keine vergessenen
Updates, kein Tool-Permission-Loch), bleibt Diun als zweite Linie.

## Soll-Zustand

- Nightly-Lauf läuft 2–3 Wochen sauber durch: Morgen-Reports liegen vor, keine
  Tool-Block-Fehler, keine offenen Rollbacks.
- Diun-Container wird aus `docker-compose.yml` entfernt.
- Zugehörige SMTP-Notif-Config in Diun bereinigt.
- Etwaige Diun-spezifische Doku in `Plattform/Systemzustand/Sicherheit/diun-setup.md`
  archiviert (`Plattform/Archiv/`) mit Verweis auf den nightly als Nachfolger.

## Auslöser für die Mini-Spec

Wenn der nightly nach ca. 3 Wochen ohne menschliches Eingreifen läuft, eröffnen
wir den Rückbau-Zyklus. Falls vorher Probleme auftreten: nightly debuggen,
Diun bleibt parallel.

## Offene Fragen

- Welche Diun-Notifications waren in den letzten Monaten nützlich, die der
  nightly NICHT abdeckt? (Wenn ja, in den nightly einbauen, bevor Diun raus.)
- Soll der Diun-Mail-Kanal als „audit notification" trotzdem bleiben (einmalige
  Image-Up-to-Date-Bestätigung), oder komplett raus?

## Nachtrag 2026-05-25 — Vorbedingung verschärft: ALLE Container müssen erfasst sein

**Anlass:** n8n wurde am 2026-05-25 im Browser als „9 Versionen hinter aktuell"
gemeldet (UI-Banner: 2.13.3 statt 2.21.7). Verifikation am System:

- `n8n --version` im Container: **2.13.3**
- Image-LABEL `org.opencontainers.image.version`: **2.13.3**, „2 months ago"
- `docker-update-log.md` letzter Eintrag: **2026-05-09** (redis-twenty). n8n
  erscheint **nirgends** im Log.
- Nacht-Report 2026-05-24 §1: Diun-Lauf „23 Images analysiert, 0 updates,
  13 unchanged, **10 failed**: lokale Eigen-Builds ohne Registry-Push: erwartet."

**Befund:** Custom-Build-Images (`environment_a-n8n-internal`, ggf. weitere wie
`environment_a-ollama-proxy`, `langgraph-*`, `pwa-api`, `admin-web`,
`landingpage-web`, `cf-alarm`) fallen aus der gesamten Update-Pipeline — Diun
kann nicht vergleichen, also auch der nightly tut nichts. Das wurde im
Nacht-Report als „erwartet" eingeordnet — was es **NICHT** ist. Ein Container
ohne Update-Pfad ist ein Sicherheits-Loch mit Zeitfaktor.

**Verschärfte Vorbedingung für Diun-Rückbau** (zwingend, ohne diese kein Rückbau):

1. Inventur aller Container in `environment_a` mit Update-Klasse:
   - Klasse A: offizielles Registry-Image mit Tag → Diun/nightly funktioniert
   - Klasse B: Custom-Build aus eigenem Dockerfile → fällt aktuell durch
2. Für Klasse B Update-Pfad bauen, der **nicht** auf Diun angewiesen ist.
   Mögliche Mechaniken (Spec-Entscheidung):
   - Base-Image-Pin im Dockerfile + nightly prüft Base-Image auf neue Tags,
     triggert Re-Build via `scripts/build_image.sh` + `promote_image.sh`
   - Upstream-Version-Check via API (n8n: GitHub-Releases, npm-Tag) →
     Vergleich gegen LABEL → bei Diff Re-Build
   - Push der Custom-Builds ins lokale Gitea-Registry, dann sieht Diun sie
3. Nightly-Report muss explizit auflisten, **welche Container er gar nicht
   prüfen konnte**, mit Begründung. „failed" als Sammelbecken ist die
   eigentliche Ursache, warum n8n unbemerkt drei Monate alt wurde.
4. Akzeptanzkriterium für Rückbau: **jeder Container** in `environment_a` hat
   einen Eintrag in der letzten Wochen-Verlaufs-Tabelle „letzter Update-Check"
   (Datum) + „letzter Update-Apply" (Datum oder „n/a — pinned"). Keine
   stillen Lücken.

**Konsequenz für die Stufung:** Bei Umsetzung wird das KEIN reiner
Diun-Rückbau-Zyklus mehr, sondern „Nightly-Update-Pipeline-Vollabdeckung"
mit Diun-Rückbau als letzter Schritt. Seed-Titel passt nicht mehr ganz —
wird in der zugehörigen Spec umbenannt.
