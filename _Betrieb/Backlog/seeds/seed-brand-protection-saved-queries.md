---
typ: backlog_seed
titel: "Cloudflare Brand Protection — Saved Queries + Logos einrichten"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: security
zugkraft: bald
stufe: sprung
beruehrt: ["(extern: Cloudflare)"]
stand: 2026-05-24
erzeugt_durch: BOOT-001
---

## Anlass

Drei Notification-Policies konnten in Bündel E nicht angelegt werden, weil sie
vorab konfigurierte Brand-Protection-Ressourcen brauchen:

- `brand_protection_alert` — Hit auf Saved Query
- `brand_protection_digest` — Wochenüberblick aller Saved Queries
- `logo_match_alert` — Saved-Logo-Match auf gescannten URLs

Diese Ressourcen werden in der Cloudflare Brand Protection Console (Dashboard:
Account → Security → Brand Protection) angelegt — nicht via Standard-API.

## Soll-Zustand

- Mindestens eine Saved Query für `prisment` (Domain-Variationen, typische
  Typo-Squats z. B. `prisment-de`, `prisment.com`, `prismment`, `prisrnent`,
  homoglyph-Varianten).
- Logo Match: das Prisment-Logo als Asset hochladen (PNG/SVG).
- Drei oben genannten Policies nachträglich anlegen — gehen dann mit
  `query_tag` / `logo_tag` Filter auf die jeweilige Saved-Ressource.

## Hinweis

Brand Protection ist auf Pro vorhanden, aber begrenzt. Falls ein Match auftritt
und der Workflow Takedown sinnvoll macht, ist das ein eigener Mini-Zyklus
(Takedown-Antrag via Cloudflare-Form an Registrar/Hoster).

## Offene Fragen für die spätere Spec

- Welche Typo-Squat-Varianten sind real wahrscheinlich? Liste pflegen.
- Wie wird ein Match-Triage-Workflow im Alltag aussehen (wahrscheinlich
  hoch False-Positive)?
