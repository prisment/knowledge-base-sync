---
typ: derivat
titel: "Backlog-Übersicht (generiert)"
erzeugt_durch: "scripts/backlog/generate_uebersicht.py (prisment-platform)"
hinweis: "Generiert. Nicht händisch editieren. Handgepflegt: 00_HOT-FILES.md."
---

# Backlog-Übersicht

Sortierung: gruppiert nach `geltungsbereich`. Primär `zugkraft` (jetzt → bald → irgendwann), sekundär `klasse` (security → qualitaet → prozess → feature → wartung), tertiär `stand` (aufsteigend).

## Nach Mission

Aktive Missionen aus `_Betrieb/Missionen/00_aktive-missionen.md`. Seeds ohne Mission stehen in den Geltungsbereich-Tabellen unten.

### Mission: live-gang (8)

| Datei | Titel | geltungsbereich | klasse | zugkraft | status |
|-------|-------|-----------------|--------|----------|--------|
| `seeds/seed-data-integrity-agents.md` | Data-Integrity-Architektur für LangGraph-Agents (zentrale Datenpunkt-Definition + Verifikation + definierte Übergaben) | prisment | qualitaet | jetzt | erledigt |
| `seeds/seed-sma-llm-kosten-pii.md` | LLM-Kosten-Tracking + PII-Anonymisierungs-Layer in Agent-Calls | prisment | qualitaet | bald | offen |
| `seeds/seed-sma-push-subscription-hardening.md` | Push-Subscription-Hardening: Silent Re-Subscribe + Zero-Friction-Onboarding E2E | prisment | qualitaet | bald | offen |
| `seeds/seed-sma-voice-check-audit.md` | Voice-Check-Audit + Admin-Insights (Issues sichtbar machen statt verstecken) | prisment | qualitaet | bald | offen |
| `seeds/seed-sma-admin-configs-editor.md` | Admin-UI: Configs-Editor + Archetyp-Detail + Versions-History | prisment | feature | bald | offen |
| `seeds/seed-sma-geposted-section.md` | Geposted-Section in der PWA — echte Engagement-Visualisierung als Motivations-Loop | prisment | feature | bald | offen |
| `seeds/seed-sma-meta-api-integration.md` | Meta Graph API Integration (echte Analytics, BoostCard, Voice-Learning-Trigger, Hashtag-Performance) | prisment | feature | bald | offen |
| `seeds/seed-sma-multi-persona-onboarding.md` | Multi-Persona-Onboarding sauber durchziehen (Audio-Strategie + Admin-UI + E2E) | prisment | feature | bald | offen |

### Mission: kunde-2 (4)

| Datei | Titel | geltungsbereich | klasse | zugkraft | status |
|-------|-------|-----------------|--------|----------|--------|
| `seeds/seed-dsgvo-dpa-cloudflare.md` | DSGVO-Auftragsverarbeitungsvertrag mit Cloudflare unterzeichnen | plattform | prozess | bald | offen |
| `seeds/seed-sma-dsgvo-sammel.md` | DSGVO-Konformität SMA — Sammel-Spec vor erstem zahlenden Mandanten | prisment | security | jetzt | offen |
| `seeds/seed-sma-multi-tenant-ux.md` | Multi-Tenant-UX: Tenant-Auswahl-Screen nach Login + sanfter Tenant-Switch | prisment | feature | bald | offen |
| `seeds/seed-assets-signed-urls.md` | assets.prisment.de: Signed-URLs gegen ungewollte Verbreitung Mandanten-Bilder | prisment | security | irgendwann | offen |

### Mission: framework (3)

| Datei | Titel | geltungsbereich | klasse | zugkraft | status |
|-------|-------|-----------------|--------|----------|--------|
| `seeds/seed-machbarkeit-anti-stop-pflicht.md` | Machbarkeits-Format schärfen: Anti-Stop-Pflicht für Mensch-Schritte (kein versteckter Stop mitten in der Sequenz) | alle | prozess | bald | offen |
| `seeds/seed-feature-flag-zustand.md` | Feature-Flag als anerkannter Zustand: gebaut, aber nicht scharf | alle | prozess | irgendwann | offen |
| `seeds/seed-apply-autonomie-pipeline.md` | Apply-Autonomie-Politik: Custom-Build-Patch + Renovate-Merge-Rebuild nutzen die PLAT-002-Wand | plattform | prozess | bald | in_arbeit |

### Mission: security-haertung-plattform (13)

| Datei | Titel | geltungsbereich | klasse | zugkraft | status |
|-------|-------|-----------------|--------|----------|--------|
| `seeds/seed-error-sammler-security-checks.md` | Zentraler Error-Sammler + wiederkehrende Security-Checks-Schedule | alle | qualitaet | bald | offen |
| `seeds/seed-brand-protection-saved-queries.md` | Cloudflare Brand Protection — Saved Queries + Logos einrichten | plattform | security | bald | offen |
| `seeds/seed-dependency-monitoring-renovate.md` | Dependency-Monitoring (Dependabot/Renovate) für alle Gitea-Repos | plattform | security | bald | offen |
| `seeds/seed-firewall-drop-port22-public.md` | Firewall-Drop Port 22 auf Public-Interface (defense-in-depth über SSH-Socket-Bind) | plattform | security | bald | offen |
| `seeds/seed-logging-forensik-grundlage.md` | Logging/Forensik-Grundlage: CF-Access-Logs Logpush + Postgres log_connections + Traefik accessLog | plattform | security | bald | offen |
| `seeds/seed-n8n-migration-2-21.md` | n8n-Migration 2.13.3 → aktuelle Upstream-Version (erster echter Pipeline-Lauf) | plattform | security | bald | offen |
| `seeds/seed-pg-hba-local-haerten.md` | pg_hba.conf: local all all trust ersetzen (In-Prozess-Bypass schließen) | plattform | security | bald | offen |
| `seeds/seed-test-umgebung-neu-secrets.md` | Test-Umgebung wieder aufbauen: test.app.prisment.de neu — intern-only, eigene Secrets, eigene Test-DB | plattform | security | bald | offen |
| `seeds/seed-pwa-user-superuser-entzug.md` | pwa_user-Restschuld: SUPERUSER entziehen (impliziert noch BYPASSRLS) | plattform | qualitaet | bald | offen |
| `seeds/seed-diun-rueckbau.md` | Diun zurückbauen, sobald nightly Stabilität bewiesen hat | plattform | wartung | irgendwann | offen |
| `seeds/seed-base-image-majors.md` | Base-Image-Major-Sprünge: Node 20→22, Python 3.12→3.13, Alpine-Pin | plattform | wartung | irgendwann | offen |
| `seeds/seed-fastapi-starlette-cve-bump.md` | fastapi 0.115 → 0.136 Cross-Minor-Bump (schließt 3 starlette-CVEs) | plattform | security | spaeter | offen |
| `seeds/seed-langchain-core-1x-bump.md` | langchain-core 0.3.x → 1.x Bump in langgraph-helpdesk | plattform | security | spaeter | offen |

## Geltungsbereich: alle

| Datei | Titel | mission | klasse | zugkraft | risikoklasse | stufe | status | abhängig von |
|-------|-------|---------|--------|----------|--------------|-------|--------|--------------|
| `seeds/seed-error-sammler-security-checks.md` | Zentraler Error-Sammler + wiederkehrende Security-Checks-Schedule | security-haertung-plattform | qualitaet | bald | sicher | spur | offen | - |
| `seeds/seed-machbarkeit-anti-stop-pflicht.md` | Machbarkeits-Format schärfen: Anti-Stop-Pflicht für Mensch-Schritte (kein versteckter Stop mitten in der Sequenz) | framework | prozess | bald | sicher | schritt | offen | - |
| `seeds/seed-feature-flag-zustand.md` | Feature-Flag als anerkannter Zustand: gebaut, aber nicht scharf | framework | prozess | irgendwann | sicher | sprung | offen | - |

## Geltungsbereich: plattform

| Datei | Titel | mission | klasse | zugkraft | risikoklasse | stufe | status | abhängig von |
|-------|-------|---------|--------|----------|--------------|-------|--------|--------------|
| `seeds/seed-brand-protection-saved-queries.md` | Cloudflare Brand Protection — Saved Queries + Logos einrichten | security-haertung-plattform | security | bald | sicher | sprung | offen | - |
| `seeds/seed-dependency-monitoring-renovate.md` | Dependency-Monitoring (Dependabot/Renovate) für alle Gitea-Repos | security-haertung-plattform | security | bald | kritisch | sprung | offen | - |
| `seeds/seed-firewall-drop-port22-public.md` | Firewall-Drop Port 22 auf Public-Interface (defense-in-depth über SSH-Socket-Bind) | security-haertung-plattform | security | bald | sicher | sprung | offen | - |
| `seeds/seed-logging-forensik-grundlage.md` | Logging/Forensik-Grundlage: CF-Access-Logs Logpush + Postgres log_connections + Traefik accessLog | security-haertung-plattform | security | bald | kritisch | spur | offen | - |
| `seeds/seed-n8n-migration-2-21.md` | n8n-Migration 2.13.3 → aktuelle Upstream-Version (erster echter Pipeline-Lauf) | security-haertung-plattform | security | bald | kritisch | sprung | offen | - |
| `seeds/seed-pg-hba-local-haerten.md` | pg_hba.conf: local all all trust ersetzen (In-Prozess-Bypass schließen) | security-haertung-plattform | security | bald | sicherheitskritisch-akut | spur | offen | PLAT-029_SPEC_pg_hba_haerten |
| `seeds/seed-test-umgebung-neu-secrets.md` | Test-Umgebung wieder aufbauen: test.app.prisment.de neu — intern-only, eigene Secrets, eigene Test-DB | security-haertung-plattform | security | bald | sicher | spur | offen | seed-code-security-checks, seed-data-integrity-agents |
| `seeds/seed-pwa-user-superuser-entzug.md` | pwa_user-Restschuld: SUPERUSER entziehen (impliziert noch BYPASSRLS) | security-haertung-plattform | qualitaet | bald | kritisch | schritt | offen | - |
| `seeds/seed-apply-autonomie-pipeline.md` | Apply-Autonomie-Politik: Custom-Build-Patch + Renovate-Merge-Rebuild nutzen die PLAT-002-Wand | framework | prozess | bald | kritisch | spur | in_arbeit | PLAT-002_SPEC |
| `seeds/seed-dsgvo-dpa-cloudflare.md` | DSGVO-Auftragsverarbeitungsvertrag mit Cloudflare unterzeichnen | kunde-2 | prozess | bald | sicher | schritt | offen | - |
| `seeds/seed-n8n-execution-data-pruning.md` | n8n: EXECUTIONS_DATA_PRUNE aktivieren (3.8 GB DB durch ungebremstes execution_data) | - | wartung | bald | sicher | schritt | offen | - |
| `seeds/seed-foundation-repo.md` | Foundation-Stack als zweites Git-Repo (Versionierung der Traefik/Foundation-Configs) | - | prozess | irgendwann | sicher | schritt | offen | - |
| `seeds/seed-pending-kernel-debconf-noise.md` | Pending-Kernel-Whiptail-Dialog bei -updates-Apply beobachten/unterdrücken | - | wartung | irgendwann | sicher | schritt | offen | - |
| `seeds/seed-diun-rueckbau.md` | Diun zurückbauen, sobald nightly Stabilität bewiesen hat | security-haertung-plattform | wartung | irgendwann | sicher | sprung | offen | - |
| `seeds/seed-base-image-majors.md` | Base-Image-Major-Sprünge: Node 20→22, Python 3.12→3.13, Alpine-Pin | security-haertung-plattform | wartung | irgendwann | sicher | sprung | offen | - |
| `seeds/seed-fastapi-starlette-cve-bump.md` | fastapi 0.115 → 0.136 Cross-Minor-Bump (schließt 3 starlette-CVEs) | security-haertung-plattform | security | spaeter | sicher | sprung | offen | - |
| `seeds/seed-langchain-core-1x-bump.md` | langchain-core 0.3.x → 1.x Bump in langgraph-helpdesk | security-haertung-plattform | security | spaeter | sicher | sprung | offen | - |
| `seeds/seed-pgbouncer-customer-postgres-bei-druck.md` | PgBouncer vor customer_postgres — bei Connection-Druck einbauen | - | wartung | spaeter | kritisch | spur | offen | PRIS-020_ABSCHLUSS |

## Geltungsbereich: prisment

| Datei | Titel | mission | klasse | zugkraft | risikoklasse | stufe | status | abhängig von |
|-------|-------|---------|--------|----------|--------------|-------|--------|--------------|
| `seeds/seed-sma-dsgvo-sammel.md` | DSGVO-Konformität SMA — Sammel-Spec vor erstem zahlenden Mandanten | kunde-2 | security | jetzt | kritisch | spur | offen | - |
| `seeds/seed-data-integrity-agents.md` | Data-Integrity-Architektur für LangGraph-Agents (zentrale Datenpunkt-Definition + Verifikation + definierte Übergaben) | live-gang | qualitaet | jetzt | sicher | spur | erledigt | - |
| `seeds/seed-sma-llm-kosten-pii.md` | LLM-Kosten-Tracking + PII-Anonymisierungs-Layer in Agent-Calls | live-gang | qualitaet | bald | sicher | sprung | offen | - |
| `seeds/seed-sma-push-subscription-hardening.md` | Push-Subscription-Hardening: Silent Re-Subscribe + Zero-Friction-Onboarding E2E | live-gang | qualitaet | bald | sicher | sprung | offen | - |
| `seeds/seed-sma-voice-check-audit.md` | Voice-Check-Audit + Admin-Insights (Issues sichtbar machen statt verstecken) | live-gang | qualitaet | bald | sicher | sprung | offen | - |
| `seeds/seed-sma-admin-configs-editor.md` | Admin-UI: Configs-Editor + Archetyp-Detail + Versions-History | live-gang | feature | bald | sicher | sprung | offen | - |
| `seeds/seed-sma-geposted-section.md` | Geposted-Section in der PWA — echte Engagement-Visualisierung als Motivations-Loop | live-gang | feature | bald | sicher | sprung | offen | seed-sma-meta-api-integration |
| `seeds/seed-sma-meta-api-integration.md` | Meta Graph API Integration (echte Analytics, BoostCard, Voice-Learning-Trigger, Hashtag-Performance) | live-gang | feature | bald | sicher | spur | offen | - |
| `seeds/seed-sma-multi-persona-onboarding.md` | Multi-Persona-Onboarding sauber durchziehen (Audio-Strategie + Admin-UI + E2E) | live-gang | feature | bald | sicher | sprung | offen | - |
| `seeds/seed-sma-multi-tenant-ux.md` | Multi-Tenant-UX: Tenant-Auswahl-Screen nach Login + sanfter Tenant-Switch | kunde-2 | feature | bald | sicher | sprung | offen | - |
| `seeds/seed-assets-signed-urls.md` | assets.prisment.de: Signed-URLs gegen ungewollte Verbreitung Mandanten-Bilder | kunde-2 | security | irgendwann | sicher | sprung | offen | - |
| `seeds/seed-sma-rag-pgvector-hybrid.md` | RAG-Endzustand: pgvector-Hybrid (Variante C) — wenn Voice-Material zu groß für direkten Prompt | - | qualitaet | irgendwann | sicher | spur | offen | - |
| `seeds/seed-zero-downtime-pwa-agents.md` | PWA + Agents: Update ohne Memory-Verlust für offene Kundensitzungen | - | prozess | irgendwann | kritisch | spur | offen | PLAT-026_SPEC |
| `seeds/seed-sma-prestashop-meta-audiences.md` | Webshop-Anbindung Prestashop → Meta Custom Audiences (Lookalike + Cold-Audience) | - | feature | irgendwann | kritisch | spur | offen | seed-sma-meta-api-integration |
| `seeds/seed-sma-webauthn-passkeys.md` | WebAuthn / Passkeys als einheitlicher Login (Hybrid-Flow ablösen) | - | feature | irgendwann | sicher | spur | offen | - |
| `seeds/seed-pwa-api-db-pfad-konsolidierung.md` | pwa-api: zwei parallele DB-Pfade konsolidieren (asyncpg + psycopg2 → einer) | - | wartung | irgendwann | sicher | spur | offen | - |

## Geltungsbereich: intern

| Datei | Titel | mission | klasse | zugkraft | risikoklasse | stufe | status | abhängig von |
|-------|-------|---------|--------|----------|--------------|-------|--------|--------------|
| `seeds/seed-n8n-archived-workflow-cleanup.md` | Archivierter n8n-Workflow YRMUaRbrtsC40CWr aus DB löschen (Pre-PRIS-020-Auth) | - | wartung | bald | sicher | schritt | offen | PRIS-020_ABSCHLUSS |
| `seeds/seed-assistant-agent.md` | Assistant-Agent: handelnder Agent an Geschäftsprozessen (eigenes Sicherheitsmodell) | - | feature | irgendwann | kritisch | spur | offen | - |

---

**Hot-Files** (strang-übergreifend, nur seriell anfassbar): siehe handgepflegte Datei [`00_HOT-FILES.md`](00_HOT-FILES.md).

**Parallelisierungs-Karte:** kommt nach Pflege von `beruehrt:` auf allen Seeds + Phase 2 des Autonomie-Zyklus.
