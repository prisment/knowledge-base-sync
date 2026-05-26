---
typ: backlog_seed
titel: "assets.prisment.de: Signed-URLs gegen ungewollte Verbreitung Mandanten-Bilder"
geltungsbereich: prisment
risikoklasse: sicher
status: offen
klasse: security
zugkraft: irgendwann
stufe: sprung
beruehrt: ["assets-Service (Traefik-Route)", "pwa_api Asset-Generator", "Mandanten-Bild-Pfade"]
stand: 2026-05-26
erzeugt_durch: "Phase-7-Atomisierung HAERTUNGS_SPEC 7.7 + ROADMAP_cf_migration Phase-7-Backlog"
---

## Anlass

`assets.prisment.de/{tenant_id}/images/...` ist öffentlich (hinter
CF-Proxy, aber ohne Access). Wer die URL kennt, kann sie weitergeben.
Aktuell akzeptiertes Restrisiko, weil keine sensitiven Bilder dort
liegen. Mittelfristig (mehr Mandanten, Edge-Cases) kann das
unangenehm werden — z.B. wenn ein Kunde versehentlich ein Bild
hochlädt, das nicht öffentlich werden soll.

## Ziel (Soll-Zustand, grob)

- Signed-URL-Mechanismus: Bilder werden über zeitlich begrenzte,
  HMAC-signierte URLs ausgeliefert.
- pwa_api (oder Asset-Generator) erzeugt URLs mit Expiry + Signature.
- assets-Service verifiziert Signature + Expiry, sonst 403.
- Migrationspfad: bestehende öffentliche Bild-URLs müssen weiter
  funktionieren, neue werden signiert. Sanfter Umbau pro Tenant
  möglich.

## Stufe / Risiko

**Sprung** — Service-Edit + pwa_api-Edit + Trafik-Route, mit echten
Mandanten-Daten. Migrationspfad ist die schwierigere Hälfte (keine
toten Links).

## Trigger

Irgendwann — kein konkreter Anlass aktuell, aber mit Onboarding
Kunde #2 (anderes Branding, anderes Volumen) sinnvoll neu zu
bewerten.
