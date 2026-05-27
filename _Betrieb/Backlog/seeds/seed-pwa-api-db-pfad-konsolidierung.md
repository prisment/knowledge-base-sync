---
typ: backlog_seed
titel: "pwa-api: zwei parallele DB-Pfade konsolidieren (asyncpg + psycopg2 → einer)"
geltungsbereich: prisment
risikoklasse: sicher
status: offen
klasse: wartung
zugkraft: irgendwann
mission: ""
stufe: spur
beruehrt: ["pwa/pwa-api/app/db.py", "pwa/pwa-api/app/db_sync.py", "alle 6 db_sync-konsumierenden Routen (dashboard, plan, analytics, interview, posts, internal)"]
stand: 2026-05-26
erzeugt_durch: "PRIS-017 Phase-5-Machbarkeit (E2-Klärung): Konsolidierung aus dieser Spur explizit ausgenommen — als A8-Skalierungs-Schuld separat"
---

## Anlass

pwa-api hat zwei parallele DB-Pfade nebeneinander:
- **asyncpg** über `app/db.py` (`pool()` / `acquire_tenant()`) — async, in den
  meisten Routen.
- **psycopg2** über `app/db_sync.py` (`_conn()` / `_tenant_conn()`) — sync,
  genutzt von `dashboard.py`, `plan.py`, `analytics.py`, `interview.py`,
  `posts.py`, `internal.py`, plus `main.py`.

Das ist seit der A3-Konsolidierung (14.05.) historisch gewachsen — manche
Code-Pfade brauchten sync, der Großteil läuft async. Doppel-Pflege: jede
Connection-/Rollen-/Pool-Änderung muss in beiden Pfaden gespiegelt werden
(siehe PRIS-017 Bündel 5a + 5b: beide Pfade müssen analog umgestellt
werden).

## Ziel (Soll-Zustand, grob)

- Einheitlicher DB-Pfad in pwa-api: nur asyncpg (oder nur psycopg2, je
  nach Aufwand-Vorteil).
- Alle psycopg2-Aufrufer (6 Module + main.py) auf den async-Pfad
  umgestellt; `db_sync.py` entfällt.
- Keine Doppel-Pflege bei späteren Rollen-/Pool-Änderungen mehr.

Sub-Ziel: Wenn die sync-Variante in einigen Pfaden konkrete Vorteile hat
(z.B. blockierende Skripte, Cron-Jobs), wird das vorher zuverlässig
identifiziert und ggf. behalten — keine prinzipielle Async-Religion.

## Stufe / Risiko

**Spur** — viele kleine Code-Eingriffe in mehreren Routen, jeder ein
Aufwand. Reversibel per Route. Risikoarm wenn Test-Coverage vorhanden;
ohne Tests ist Live-Verifikation pro Route nötig.

## Trigger

Irgendwann — kein konkreter Anlass aktuell. Auslöser kommen bald aus:
- Skalierungs-Schulden-Aufräumung (Seed-Kontext A4–A8)
- Nächste Rollen-/Pool-Änderung mit Doppel-Pflege-Schmerz

## Bezug zu PRIS-017

PRIS-017 (Tenant-Isolation) klärt in seinem E2-Beschluss explizit, dass
diese Konsolidierung NICHT im selben Zyklus passiert (Scope Creep im
kritischsten Bündel). Bündel 5a + 5b in PRIS-017 leben mit der Doppel-
Pflege als kalkulierter Aufwand; dieser Seed erlöst die Doppel-Pflege
in einem späteren eigenständigen Zyklus.
