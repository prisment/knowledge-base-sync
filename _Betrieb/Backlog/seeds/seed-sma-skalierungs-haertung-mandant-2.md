---
typ: backlog_seed
titel: "Skalierungs-Härtung vor Mandant #2 (Cron-Idempotency, Per-Caller-Secrets, API-Versionierung, Indizes, Pool-Konsolidierung)"
geltungsbereich: prisment
risikoklasse: kritisch
status: offen
klasse: qualitaet
zugkraft: jetzt
stufe: spur
beruehrt: ["pwa/pwa-api/", "langgraph/", "n8n/"]
stand: 2026-05-25
erzeugt_durch: ROADMAP_SMA-Zerlegung („Architektur-Härtung VOR MANDANT #2", A4–A8)
---

## Anlass

Aus dem Architektur-Sweep 2026-05-13 nach der Tenant-ID-Migration. Stack
ist solide, A1 (Test-Suite/CI) ist in `seed-code-security-checks.md`
integriert, A2 (RLS) + A3 (DB-Konsolidierung) sind live. Bleiben **fünf
Skalierungs-Schulden**, die jeder neue Mandant linear teurer macht.

Wichtig zur Abgrenzung: das ist **keine Sicherheits-Härtung** (die läuft
über die laufende HAERTUNGS_SPEC + ROADMAP_cloudflare_migration), sondern
Prisment-DB/API-Skalierung. Eigener Zyklus, eigene Reihenfolge.

## Befunde (gebündelt)

### A4 — Cron-Idempotency
`sessions` hat kein UNIQUE-Constraint. n8n-Restart oder Doppel-Trigger
erzeugt Duplicate-Sessions. **Plus:** nach n8n-Cron-Migration laufen
diese Trigger heute über APScheduler in pwa-api — die alten Patterns
müssen auf die neue Mechanik übertragen werden.
- `SELECT ... FROM sessions WHERE tenant_id=$1 AND status IN
  ('open','in_progress') AND created_at > NOW() - interval '24h'` als
  Pre-Check
- UNIQUE-Constraint auf `sessions(tenant_id, mode, created_date)` als
  Sicherheitsnetz

### A5 — Per-Caller Service-to-Service-Auth
Alle 4 Agents + n8n + pwa-api benutzen denselben `AGENT_SECRET`. Bei
Leak: 6 Container synchron rotieren, kein Audit-Trail.
- Pro Caller eigenes Token: `agent_secret_interview`,
  `agent_secret_content`, `agent_secret_redaktionsplan`,
  `agent_secret_analytics`, `agent_secret_helpdesk`, `agent_secret_n8n`,
  `agent_secret_pwa_api`
- `validate_agent_secret`-Middleware prüft Set + loggt Caller
- Rotation-Plan dokumentiert (90d-Intervall)

### A6 — API-Versionierung auf `/api/v1/`
Endpoints unter `/api/tenants/...`, kein `/v1/`. `/admin` außerhalb des
`/api/`-Präfix. Erste Breaking-Change → keine Coexistenz-Strategie.
- pwa-api auf `/api/v1/...` umgezogen
- Alte Pfade als 301-Alias mindestens 1 Quartal
- `/admin` zu `/api/v1/admin/...` konsolidiert

### A7 — Fehlende Indizes
- `posts.scheduled_for` und `approval_audit.created_at` haben keine
  Indizes
- Bei 100 Tenants × 30 Posts/Monat × 12 Monate = 36k Rows in `posts`.
  Publisher-Cron wird Sequential-Scan.
- 10 Min Aufwand, gewaltiger Skalierungs-Hebel
```sql
CREATE INDEX idx_posts_scheduled ON posts(scheduled_for) WHERE status='approved';
CREATE INDEX idx_approval_audit_created ON approval_audit(tenant_id, created_at DESC);
```

### A8 — PgBouncer + Pool-Konsolidierung
pwa-api hält parallel asyncpg-Pool + psycopg2 ThreadedPool auf
agent_data (doppelter Footprint). Bei 50+ User × 4 Agents kommt man an
`max_connections=100`.
- PgBouncer im Transaction-Mode vor customer_postgres
- pwa-api auf 1 Pool-Typ konsolidiert
- Connection-Limits pro Service in pgbouncer.ini

**Hinweis:** A8 kann entfallen, wenn nach A3 (DB-Konsolidierung,
bereits live) der Sync-Pool eh weg ist. Faktencheck in der
Faktensammlung.

## Reihenfolge / Aufwand

| # | Punkt | Aufwand |
|---|---|---|
| 1 | A7 Indizes | 10 Min |
| 2 | A4 Cron-Idempotency | 1–2 h |
| 3 | A6 API-Versionierung | 1–2 h |
| 4 | A5 Per-Caller-Secrets | 1 Tag |
| 5 | A8 PgBouncer (falls noch nötig) | 1–2 Tage |

**Total realistisch:** 2–4 Tage konzentrierter Arbeit.

## Offene Fragen für die spätere Spec

- **A4-Idempotency auch in APScheduler nötig?** Heute läuft das im
  selben Process — Doppel-Trigger weniger wahrscheinlich, aber bei
  Restart-Race möglich.
- **A6-Migration vs. Mobile-Konsumenten:** PWA ist heute einziger
  Konsument, leicht zu migrieren. Brauchen wir die 301-Alias-Periode
  überhaupt?
- **A8-Notwendigkeit:** Connection-Druck heute messbar oder
  vorsorglich? (Bei Tenant-Anzahl 1 vermutlich kein Druck — könnte
  warten.)

## Stufe / Risiko

**Spur.** Architektur-/Auth-/DB-naher Refactor (A5 Auth, A6 Public-API,
A8 Connection-Layer). Risikoklasse `kritisch` (Auth + Datenpfad in
einem Bündel) → erzwingt Spur ohnehin.

## Trigger

JETZT — explizit „vor Mandant #2", da jeder neue Tenant die Punkte
linear teurer macht. Reihenfolge gegen die laufende Security-Härtung
und die Daten-Integritäts-Spur klären beim Spec-Start.
