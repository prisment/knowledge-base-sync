---
typ: backlog_seed
titel: "Image-Pinning: Gitea, Twenty, Open WebUI und übrige :latest/:main → exakte Versionen"
geltungsbereich: plattform
risikoklasse: sicher
status: in_arbeit
spec_id: PLAT-030
klasse: security
zugkraft: bald
stufe: schritt
beruehrt: ["/opt/infrastructure/environment_a/docker-compose.yml"]
stand: 2026-05-26
erzeugt_durch: "Phase-7-Atomisierung HAERTUNGS_SPEC 7.4 + ROADMAP_cf_migration Phase-7-Backlog"
---

## Anlass

Vierzehn Services in `docker-compose.yml` laufen aktuell auf `:latest`
(`gitea`, `ollama`, `twenty`, `whisper`, fünf LangGraph-Container,
`diun`, `pwa-api`, `pwa-web`, `admin-web`, `cf-alarm`) plus Open WebUI
auf `:main`. Heißt: jeder `docker compose pull` kann ohne unsere
Kontrolle eine Major-Version reinziehen — beim Update-Lauf der nightly-
Routine ein potenzielles Brick-Risiko. Diun (läuft) erkennt Updates
nur, weil es Tags vergleicht; ohne Pinning ist die „Erkennung" gegen
einen sich-bewegenden Sollwert.

## Ziel (Soll-Zustand, grob)

- Aktuell laufende Image-Digests aus `docker inspect <container>
  --format '{{.Image}}'` lesen, in den Compose-Eintrag als
  `image: ...:<tag>@sha256:<digest>` einsetzen (oder mindestens
  konkreter Versions-Tag, abhängig von Service).
- Pro Service eine bewusste Entscheidung: stabiler Versions-Tag
  (z.B. `gitea:1.22.3`) oder Digest-Pin (für Services ohne
  semantische Versions-Tags wie Open WebUI `:main`).
- Diun behält Update-Erkennung gegen die gepinnten Tags. Updates
  laufen als bewusste Compose-Edits + Commits, nicht durch stillen
  Pull.

## Stufe / Risiko

**Schritt** — Compose-Edit, kein Verhalten ändert sich (laufende
Container bleiben unangetastet, kein Restart nötig). Reversibel.
Pflicht: Architekt-Freigabe vor Compose-Edit (per CLAUDE.md).

## Trigger

Bald — vor Onboarding Kunde #2 sinnvoll, aber nicht blockierend.
Kann jederzeit eingeschoben werden.
