---
typ: backlog_seed
titel: "Base-Image-Versionen aktualisieren (Node-LTS, Python-Base, etc.)"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: wartung
zugkraft: irgendwann
stufe: sprung
beruehrt: ["pwa-web/Dockerfile", "pwa-api/Dockerfile", "admin-web/Dockerfile", "langgraph/*/Dockerfile"]
stand: 2026-05-26
erzeugt_durch: "Phase-7-Atomisierung HAERTUNGS_SPEC 7.5"
---

## Anlass

Eigene gebaute Images (pwa-web, pwa-api, admin-web, fünf LangGraph)
basieren auf Base-Images mit potenziell veralteten Major-Versionen
(Node 20 → aktueller LTS prüfen, Python-Slim-Tag, etc.). Patch-Pflege
findet bisher nicht systematisch statt — Base-Images werden mit
Container-Rebuilds versehentlich aktualisiert, sonst nicht.

## Ziel (Soll-Zustand, grob)

- Pro eigenes Dockerfile: aktuelle LTS-/Stable-Base-Image-Version
  ermitteln.
- Wenn Aktualisierung möglich ohne Breaking-Changes:
  Dockerfile-Update + Build via `scripts/build_image.sh` +
  promote via `scripts/promote_image.sh` (siehe Image-Build-
  Konvention CLAUDE.md).
- Pro Service: Acceptance-Test (Healthcheck + ein End-to-End-Klick)
  bevor `:latest` umgehängt wird.

## Stufe / Risiko

**Sprung** — Pro Service ein eigenständiger Rebuild + Test-Zyklus.
Aufwand ~1h pro Service. Risiko niedrig wegen Test-vs-Live-Trennung
(`:test`-Tag-Mechanik). Reversibel via `:prev-<timestamp>`-Tag, der
beim `promote_image.sh` automatisch gesetzt wird.

## Trigger

Irgendwann — kein konkreter CVE-Anlass aktuell sichtbar.
Auslöser könnten sein: Renovate-Alert (siehe Seed
`dependency-monitoring-renovate`), Major-Node-Release, Security-
Advisory eines Base-Images.

## Hinweis

Profitiert stark von Renovate (Seed dependency-monitoring-renovate)
— sobald der Bot läuft, kommt das Thema von alleine als PR rein und
muss nicht als eigenständiges Backlog-Item gepflegt werden. Heißt:
dieser Seed könnte sich nach Renovate-Setup auflösen.
