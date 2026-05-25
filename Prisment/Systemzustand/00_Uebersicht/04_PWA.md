---
typ: uebersicht
geltungsbereich: prisment
thema: pwa
stand: 2026-05-25
zweck: "PWA-Stack, DB-Topologie, Notification-Architektur, APScheduler, Test-vs-Live-Trennung. Sub-Übersicht zu 00_Bereich.md."
---

# PWA — Prisment

> **Sub-Übersicht.** Detaillierter Einstieg siehe `00_Bereich.md`.

## In einem Absatz

Der **PWA-Stack** besteht aus drei Containern: `pwa_web` (Next.js
Mandanten-Frontend, **derzeit offline** via Compose-Profile-Lock seit
HARDENING Phase 1), `pwa_api` (FastAPI Backend, aktiv, einziger
Schreibzugriff auf `customer_postgres`) und `admin_web` (React
Korbinian-UI für Tenant-Verwaltung, aktiv). Alle drei liegen im
`edge_internal`-Netz; `pwa_api` zusätzlich in `db_customer` (DB-Zugriff)
und `net_ai_pipeline` (Calls an langgraph-Agents). Auth: NextAuth in
der PWA mit eigener Session-Verwaltung, `X-Agent-Secret`-Header für
HTTP-Calls zwischen pwa-api und langgraph-Agents. **DB-Topologie:** Die
zentrale Datenbank `agent_data` in `customer_postgres` hat zwei
Schemas — `auth.*` (next-auth + Push-Subscriptions + Notifications +
User-Settings + Approval-Audit + tenant_memberships) und `public.*`
(Tenant-Daten: posts, topics, sessions, voicedb_entries,
editorial_plans, scheduled_jobs). **Notification-Architektur:** Agents
rufen `pwa-api /internal/notify` mit typisiertem Payload → Eintrag in
`notifications`-Tabelle → sofortige PWA-Push via `web-push`-Library
(wenn `push_enabled` im User-Setting), Email-Fallback nach
`BUNDLE_WINDOW_SECONDS=60` falls keine Push-Bestätigung. Quiet-Hours
22:00-07:00 Europe/Berlin unterdrücken Email-Versand. `BUNDLED_TYPES`
verhindert Mehrfach-Notifications eines Typs im Window.
**APScheduler in pwa-api** ist der zentrale Cron-Layer (seit
n8n-Cron-Migration 2026-05-18, Feature-Log) — zwei Job-Klassen:
A für Interview-Trigger + Posting-Reminder, B+ für die generische
`scheduled_jobs`-Queue (aktuell Redaktionsplan; Analytics kommt mit
eigener Spec). **Test-Stack** läuft parallel über
`docker-compose.test.yml` (eigener Postgres `customer_postgres_test`,
Test-API auf Port 8006), Promote-Workflow via
`scripts/build_image.sh` + `scripts/promote_image.sh`.

## Container-Stack

| Container | Image | Status | Funktion |
|---|---|---|---|
| `pwa_web` | `pwa-web:latest` | ⚠️ **OFFLINE** (Profile-Lock `live-pwa`) | Next.js Mandanten-Frontend |
| `pwa_api` | `pwa-api:latest` | ✅ aktiv | FastAPI Backend, einziger Schreibzugriff auf customer_postgres |
| `admin_web` | `admin-web:latest` | ✅ aktiv | React Korbinian-UI für Tenant-Verwaltung |

### Wiederanlauf-Status `pwa_web`

`pwa_web` ist seit **HARDENING Phase 1** offline via Compose-Profile-Lock:

```yaml
pwa-web:
  image: pwa-web:latest
  # HARDENING Phase 1: pwa_web bleibt offline bis Phase 6 (PWA-Wiederanlauf).
  # Profile-Lock verhindert versehentliches Hochfahren durch `docker compose up -d`.
  profiles: [live-pwa]
```

**Wiederanlauf:**
```bash
docker compose --profile live-pwa up -d pwa-web
```

Konsequenz: `app.prisment.de` ist derzeit kein lebender Mandanten-
Touchpoint. PWA ist gebaut + getestet (im Test-Stack erreichbar),
produktiver Mandanten-Betrieb wartet auf Phase-6-Freigabe.

## DB-Topologie

Zentrale DB: `customer_postgres` (Image `pgvector/pgvector:pg16`, Netz
`db_customer`).

```
customer_postgres
├── agent_data (Haupt-DB)
│   ├── auth.*    ← next-auth + PWA-Tabellen
│   │   · users, sessions, accounts, verification_token
│   │   · push_subscriptions, tenant_memberships
│   │   · notifications, user_settings, approval_audit
│   └── public.*  ← Tenant-Daten + globale Tabellen
│       · posts, topics, sessions (Interview-Sessions!)
│       · voicedb_entries, editorial_plans, scheduled_jobs
│       · post_revisions, boost_flags, analytics_reports
└── pwa_app (Legacy-Fallback, soll später droppen)
```

**Namens-Konflikt:** `sessions` existiert in beiden Schemas (auth: PWA-
Web-Sessions; public: Voice-Interview-Sessions). Im Code IMMER
`public.sessions` qualifizieren wenn Voice-Session gemeint ist. Default
`FROM sessions` → `auth.sessions`. Detail siehe
[`../Architektur/SMA_Operative_Doku.md`](../Architektur/SMA_Operative_Doku.md)
„DB-Topologie A3".

**`search_path`:** datenbankweit auf `auth, public` gesetzt — non-
qualifizierte Tabellennamen landen automatisch im richtigen Schema.

## Notification-Architektur

Endpoint: `pwa-api /internal/notify` (`X-Agent-Secret` pflicht).

### Notification-Types

Aktuell verwendete Types (aus den Agents):

| Type | Trigger | Bundled? | Email-Fallback? |
|---|---|---|---|
| `post_pending` | Content-Agent nach `save_post_draft` | ✅ ja | ✅ ja |
| `post_revised` | Content-Agent nach `/revise` | nein | nein |
| `interview_due` | APScheduler (Interview-Cron) | nein | ✅ ja |
| `interview_question` | Interview-Agent nach `/run` | nein | nein |
| `interview_follow_up` | Interview-Agent (Follow-up) | ✅ ja | nein |
| `plan_review` | Redaktionsplan-Agent nach `/run` | nein | ✅ ja |
| `boost_recommended` | Analytics-Agent nach `boost_check` | nein | nein |
| `monthly_report` / `monthly_report_ready` | Analytics-Agent nach `monthly_report` | nein | ✅ ja |
| `legal_blocked` | Content-Agent nach Compliance-BLOCK | nein | nein |
| `admin_alert` | Error-Sammler (geplant, siehe ROADMAP_SMA) | nein | ✅ ja (geplant) |

### Bundling + Email-Fallback (exakte Config)

```python
# pwa-api/app/routes/internal.py
BUNDLED_TYPES = {"post_pending", "interview_follow_up"}
BUNDLE_WINDOW_SECONDS = 60

# pwa-api/app/notifications/fallback_cron.py
EMAIL_FALLBACK_TYPES = {"post_pending", "interview_due", "plan_review", "monthly_report"}
QUIET_HOURS_TZ   = ZoneInfo("Europe/Berlin")
QUIET_HOURS_START = 22  # 22:00
QUIET_HOURS_END   = 7   # 07:00

# pwa-api/app/config.py
EMAIL_NOTIFICATIONS_ENABLED = (os.environ.get("EMAIL_NOTIFICATIONS_ENABLED", "false").lower() == "true")
```

**Mechanik:**
- **Bundling:** Wenn `notification_type in BUNDLED_TYPES`, prüft pwa-api
  ob in den letzten `BUNDLE_WINDOW_SECONDS` schon eine Notification des
  gleichen Typs für den User raus ging → wenn ja, kein neuer Push
  (verhindert Spam).
- **Email-Fallback:** Wenn `notification_type in EMAIL_FALLBACK_TYPES`,
  läuft ein Cron-Job (`fallback_cron.py`) und schickt nach
  `BUNDLE_WINDOW_SECONDS` eine Email, falls keine Push-Bestätigung kam.
  Aber: nur wenn `EMAIL_NOTIFICATIONS_ENABLED=True` (ENV-Flag, default
  False) UND außerhalb Quiet-Hours.
- **Quiet-Hours:** 22:00-07:00 Europe/Berlin unterdrücken Email
  komplett. Push geht weiter.

### User-Settings

Pro User in `auth.user_settings`:
- `push_enabled` (boolean)
- `email_fallback_enabled` (boolean)
- `notification_types` (jsonb-Liste: welche Types darf der User
  empfangen)
- `locale` (für Email-Sprache)

## APScheduler

Zentraler Cron-Layer in pwa-api (`pwa/pwa-api/app/scheduling/`,
seit n8n-Cron-Migration 2026-05-18). Zwei Job-Klassen:

### Klasse A — feste Trigger
- `interview_cron_tick` — fällige Interview-Trigger anhand
  `tenants.next_interview_at`
- Posting-Zeitpunkt-Reminder (S3-5, Spec-Pfad 3b)

### Klasse B+ — generische scheduled_jobs-Queue
- `scheduled_jobs_tick` — arbeitet fällige Jobs aus
  `public.scheduled_jobs` ab (atomares `UPDATE … RETURNING` für
  Idempotenz)
- Aktuell: `job_type='redaktionsplan'`
- Analytics-Trigger geplant mit eigener Spec (siehe
  [`../../../_Betrieb/Backlog/ROADMAP_SMA.md`](../../../_Betrieb/Backlog/ROADMAP_SMA.md))

## Test-vs-Live-Stack-Trennung

Test-Stack läuft parallel über `docker-compose.test.yml` (alle Container
mit `_test`-Suffix):

| Komponente | Live | Test |
|---|---|---|
| Web | `test.app.prisment.de` (web offline → derzeit nicht aktiv) | `localhost:3001` |
| API | `pwa_api` (intern 8005) | `pwa_api_test` (Port 8006) |
| DB | `customer_postgres` (Port 5432) | `customer_postgres_test` (Port 5435) |
| Agents | 5× `langgraph_*` (Live-Images) | 5× `langgraph_*_test` (Test-Images) |

Promote-Workflow:
```bash
scripts/build_image.sh pwa-api pwa/pwa-api/    # baut :test + :<sha>
scripts/promote_image.sh pwa-api               # taggt :test → :latest, recreate
```

Operator-Script: `/home/claude-deploy/pwa_test_env.sh`. Detail siehe
[`../Architektur/SMA_Operative_Doku.md`](../Architektur/SMA_Operative_Doku.md)
„Testumgebung – Pflichtprozess".

## Detail-Quellen

| Datei | Inhalt |
|---|---|
| [`../Produkt/PWA_KUNDEN_APP.md`](../Produkt/PWA_KUNDEN_APP.md) | PWA-Spec (vollständig — Tech-Stack, Auth, Endpoints, UX-Flow) |
| [`../Produkt/PWA_DESIGN.md`](../Produkt/PWA_DESIGN.md) | UX-Spec für PWA-Frictionless-Überarbeitung (Living Document) |
| [`../Architektur/SMA_Operative_Doku.md`](../Architektur/SMA_Operative_Doku.md) | Dev-Workflow + Test-Stack + Promote-Strategie + DB-Topologie A3 |
| [`../Architektur/Social Media Automation – Technische Dokumentation.md`](../Architektur/Social%20Media%20Automation%20%E2%80%93%20Technische%20Dokumentation.md) | Haupt-Tech-Doku |
| `/opt/infrastructure/environment_a/pwa/` | Source-Code |
| `/opt/infrastructure/environment_a/pwa/migrations/` | DB-Schema-Migrationen |
