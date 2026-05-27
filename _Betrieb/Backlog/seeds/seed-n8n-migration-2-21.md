---
typ: backlog_seed
titel: "n8n-Migration 2.13.3 → aktuelle Upstream-Version (erster echter Pipeline-Lauf)"
geltungsbereich: plattform
risikoklasse: kritisch
status: offen
klasse: security
zugkraft: bald
mission:         security-haertung-plattform
stufe: sprung
beruehrt: ["n8n/Dockerfile", "scripts/build_image.sh", "internal_n8n Container", "n8n-Workflows"]
stand: 2026-05-26
erzeugt_durch: "PLAT-021 Phase 3 — konkreter Anwendungsfall der neuen Routine + Trigger-Vorbedingung für seed-diun-rueckbau"
---

## Anlass

`internal_n8n` läuft auf 2.13.3, Upstream-stable ist 2.21.x (Stand
2026-05-26, exakte Latest-Version aus dem ersten Lauf der Routine).
Drift ist ~3 Monate, im April/Mai 2026 zufällig im UI-Banner
bemerkt — strukturell verfehlt. PLAT-020 hat die Sichtbarkeit
hergestellt (1c im nightly-Report), PLAT-021 baut den Upstream-
Check-Pfad für n8n. **Dieser Seed ist der erste echte Lauf** der
Achse 2 (Fremdcode) aus PLAT-021.

## Soll-Zustand

- `internal_n8n` läuft auf der aktuellen stable Minor-Linie (Stand
  beim Apply, vermutlich 2.21.x), Workflows funktionsgleich.
- Migrationspfad: gestaffelt (2.13 → 2.17 → 2.21), nicht im
  Einzelsprung, falls Upstream-Changelog Workflow-Breaking-Changes
  pro Zwischen-Minor markiert.
- Health-Check pro Stufe: Workflows starten, Trigger-DB ok,
  Aktivierungen unverändert, sample-Webhook antwortet.
- Rollback-Pfad: `:prev-<timestamp>`-Tag aus `build_image.sh`-Konvention.

## Stufe / Risiko

**Sprung.** Eine zusammenhängende Migration mit klarer Abgrenzung.
Risikoklasse **kritisch** wegen Workflow-Daten-Berührung
(Trigger-DB-Schema kann zwischen Minor-Versionen migrieren) +
Auth-Stelle (n8n-User-Sessions).

## Trigger

**Bald.** Auslöser-Vorbedingung: PLAT-021 Bündel 4 (n8n-Upstream-
Check) ist scharf und reportet die aktuelle Latest-Version
verlässlich.

## Doppelrolle als Trigger für seed-diun-rueckbau

Der erfolgreiche Apply dieser Migration ist gleichzeitig
Trigger-Kriterium #2 für `seed-diun-rueckbau`:

> n8n wurde über den neuen Achse-2-Pfad von 2.13.3 auf aktuell
> gezogen — der konkrete Beweis, dass der neue Pfad genau die
> Lücke schließt, die Diun strukturell nie sah.

Also: dieser Seed schließt eine konkrete Drift UND beweist die
Pipeline-Wirkung gleichzeitig.

## Offene Fragen

- Welche Zwischen-Versionen passieren wir? (Upstream-Changelog
  lesen, Breaking-Changes-Marker.)
- Datenbank-Migration: in-place (n8n bringt eigene Migration mit)
  oder Backup + Restore-Test vorher?
- Custom-Nodes in `n8n/data/nodes/` — Kompatibilität pro Sprung
  prüfen.

## Hinweis

Nicht starten, bevor PLAT-021 Bündel 4 grün ist (sonst gibt's
keinen Upstream-Check, der die Ziel-Version aus dem System
ablesen kann — bleibt Hand-Arbeit).
