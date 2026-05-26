---
typ: derivat
titel: "Backlog-Übersicht (generiert)"
erzeugt_durch: "scripts/backlog/generate_uebersicht.py (prisment-platform)"
hinweis: "Generiert. Nicht händisch editieren. Handgepflegt: 00_HOT-FILES.md."
---

# Backlog-Übersicht

Sortierung: gruppiert nach `geltungsbereich`. Primär `zugkraft` (jetzt → bald → irgendwann), sekundär `klasse` (security → qualitaet → prozess → feature → wartung), tertiär `stand` (aufsteigend).

## Geltungsbereich: alle

| Datei | Titel | klasse | zugkraft | risikoklasse | stufe | status | abhängig von |
|-------|-------|--------|----------|--------------|-------|--------|--------------|
| `seeds/seed-error-sammler-security-checks.md` | Zentraler Error-Sammler + wiederkehrende Security-Checks-Schedule | qualitaet | bald | sicher | spur | offen | - |
| `seeds/seed-feature-flag-zustand.md` | Feature-Flag als anerkannter Zustand: gebaut, aber nicht scharf | prozess | irgendwann | sicher | sprung | offen | - |

## Geltungsbereich: plattform

| Datei | Titel | klasse | zugkraft | risikoklasse | stufe | status | abhängig von |
|-------|-------|--------|----------|--------------|-------|--------|--------------|
| `seeds/seed-code-security-checks.md` | Code-Security-Standards + automatisierte Checks für Prisment-Produkt-Code (Next.js + LangGraph) | security | jetzt | kritisch | spur | offen | - |
| `seeds/seed-pg-hba-haerten.md` | pg_hba.conf härten: trust 127.0.0.1/32 raus + host-Regeln subnetz-spezifisch | security | jetzt | sicherheitskritisch-akut | sprung | offen | - |
| `seeds/seed-brand-protection-saved-queries.md` | Cloudflare Brand Protection — Saved Queries + Logos einrichten | security | bald | sicher | sprung | offen | - |
| `seeds/seed-dependency-monitoring-renovate.md` | Dependency-Monitoring (Dependabot/Renovate) für alle Gitea-Repos | security | bald | kritisch | sprung | offen | - |
| `seeds/seed-firewall-drop-port22-public.md` | Firewall-Drop Port 22 auf Public-Interface (defense-in-depth über SSH-Socket-Bind) | security | bald | sicher | sprung | offen | - |
| `seeds/seed-image-pinning-latest-services.md` | Image-Pinning: Gitea, Twenty, Open WebUI und übrige :latest/:main → exakte Versionen | security | bald | sicher | schritt | in_arbeit | - |
| `seeds/seed-logging-forensik-grundlage.md` | Logging/Forensik-Grundlage: CF-Access-Logs Logpush + Postgres log_connections + Traefik accessLog | security | bald | kritisch | spur | offen | - |
| `seeds/seed-n8n-migration-2-21.md` | n8n-Migration 2.13.3 → aktuelle Upstream-Version (erster echter Pipeline-Lauf) | security | bald | kritisch | sprung | offen | - |
| `seeds/seed-test-umgebung-neu-secrets.md` | Test-Familie neu aufbauen: eigene Secrets, intern-only, eigene Test-DB | security | bald | sicher | spur | offen | - |
| `seeds/seed-pwa-user-superuser-entzug.md` | pwa_user-Restschuld: SUPERUSER entziehen (impliziert noch BYPASSRLS) | qualitaet | bald | kritisch | schritt | offen | - |
| `seeds/seed-apply-autonomie-pipeline.md` | Apply-Autonomie-Politik: Custom-Build-Patch + Renovate-Merge-Rebuild nutzen die PLAT-002-Wand | prozess | bald | kritisch | spur | offen | PLAT-002_SPEC |
| `seeds/seed-dsgvo-dpa-cloudflare.md` | DSGVO-Auftragsverarbeitungsvertrag mit Cloudflare unterzeichnen | prozess | bald | sicher | schritt | offen | - |
| `seeds/seed-notausgang-doku.md` | Notausgang-Doku: schriftliche Rescue-System- und Telefon-Tailscale-Prozedur | prozess | bald | sicher | schritt | offen | - |
| `seeds/seed-n8n-execution-data-pruning.md` | n8n: EXECUTIONS_DATA_PRUNE aktivieren (3.8 GB DB durch ungebremstes execution_data) | wartung | bald | sicher | schritt | offen | - |
| `seeds/seed-next-spec-id-working-tree.md` | next-spec-id: untracked Working-Tree-Specs einbeziehen, damit IDs nicht kollidieren | wartung | bald | sicher | schritt | offen | - |
| `seeds/seed-cve-watchdog-regel-b.md` | Cloudflare WAF: CVE-2025-55182 Watchdog-Regel B mit gehärteter Expression | security | irgendwann | sicher | schritt | offen | - |
| `seeds/seed-waf-owasp-block-modus.md` | Cloudflare WAF OWASP Core Ruleset von 'log' auf 'block' umstellen (nach Live-Tuning) | security | irgendwann | sicher | schritt | offen | - |
| `seeds/seed-foundation-repo.md` | Foundation-Stack als zweites Git-Repo (Versionierung der Traefik/Foundation-Configs) | prozess | irgendwann | sicher | schritt | offen | - |
| `seeds/seed-gitea-container-registry.md` | Gitea-Container-Registry-Pfad für Custom-Builds (End-to-End-Pull-Pfad) | prozess | irgendwann | sicher | sprung | offen | - |
| `seeds/seed-pending-kernel-debconf-noise.md` | Pending-Kernel-Whiptail-Dialog bei -updates-Apply beobachten/unterdrücken | wartung | irgendwann | sicher | schritt | offen | - |
| `seeds/seed-diun-rueckbau.md` | Diun zurückbauen, sobald nightly Stabilität bewiesen hat | wartung | irgendwann | sicher | sprung | offen | - |
| `seeds/seed-base-image-majors.md` | Base-Image-Major-Sprünge: Node 20→22, Python 3.12→3.13, Alpine-Pin | wartung | irgendwann | sicher | sprung | offen | - |
| `seeds/seed-base-image-versionen.md` | Base-Image-Versionen aktualisieren (Node-LTS, Python-Base, etc.) | wartung | irgendwann | sicher | sprung | offen | - |
| `seeds/seed-dns-cache-resolved-hairpin.md` | systemd-resolved Cache: Origin-IP statt CF-Edge bei Hairpin-Aufrufen vom Server selbst | wartung | irgendwann | sicher | schritt | offen | - |

## Geltungsbereich: prisment

| Datei | Titel | klasse | zugkraft | risikoklasse | stufe | status | abhängig von |
|-------|-------|--------|----------|--------------|-------|--------|--------------|
| `seeds/seed-sma-dsgvo-sammel.md` | DSGVO-Konformität SMA — Sammel-Spec vor erstem zahlenden Mandanten | security | jetzt | kritisch | spur | offen | - |
| `seeds/seed-data-integrity-agents.md` | Data-Integrity-Architektur für LangGraph-Agents (zentrale Datenpunkt-Definition + Verifikation + definierte Übergaben) | qualitaet | jetzt | sicher | spur | offen | - |
| `seeds/seed-sma-skalierungs-haertung-mandant-2.md` | Skalierungs-Härtung vor Mandant #2 (Cron-Idempotency, Per-Caller-Secrets, API-Versionierung, Indizes, Pool-Konsolidierung) | qualitaet | jetzt | kritisch | spur | offen | - |
| `seeds/seed-sma-llm-kosten-pii.md` | LLM-Kosten-Tracking + PII-Anonymisierungs-Layer in Agent-Calls | qualitaet | bald | sicher | sprung | offen | - |
| `seeds/seed-sma-push-subscription-hardening.md` | Push-Subscription-Hardening: Silent Re-Subscribe + Zero-Friction-Onboarding E2E | qualitaet | bald | sicher | sprung | offen | - |
| `seeds/seed-sma-voice-check-audit.md` | Voice-Check-Audit + Admin-Insights (Issues sichtbar machen statt verstecken) | qualitaet | bald | sicher | sprung | offen | - |
| `seeds/seed-sma-admin-configs-editor.md` | Admin-UI: Configs-Editor + Archetyp-Detail + Versions-History | feature | bald | sicher | sprung | offen | - |
| `seeds/seed-sma-geposted-section.md` | Geposted-Section in der PWA — echte Engagement-Visualisierung als Motivations-Loop | feature | bald | sicher | sprung | offen | seed-sma-meta-api-integration |
| `seeds/seed-sma-meta-api-integration.md` | Meta Graph API Integration (echte Analytics, BoostCard, Voice-Learning-Trigger, Hashtag-Performance) | feature | bald | sicher | spur | offen | - |
| `seeds/seed-sma-multi-persona-onboarding.md` | Multi-Persona-Onboarding sauber durchziehen (Audio-Strategie + Admin-UI + E2E) | feature | bald | sicher | sprung | offen | - |
| `seeds/seed-sma-multi-tenant-ux.md` | Multi-Tenant-UX: Tenant-Auswahl-Screen nach Login + sanfter Tenant-Switch | feature | bald | sicher | sprung | offen | - |
| `seeds/seed-assets-signed-urls.md` | assets.prisment.de: Signed-URLs gegen ungewollte Verbreitung Mandanten-Bilder | security | irgendwann | sicher | sprung | offen | - |
| `seeds/seed-sma-rag-pgvector-hybrid.md` | RAG-Endzustand: pgvector-Hybrid (Variante C) — wenn Voice-Material zu groß für direkten Prompt | qualitaet | irgendwann | sicher | spur | offen | - |
| `seeds/seed-sma-prestashop-meta-audiences.md` | Webshop-Anbindung Prestashop → Meta Custom Audiences (Lookalike + Cold-Audience) | feature | irgendwann | kritisch | spur | offen | seed-sma-meta-api-integration |
| `seeds/seed-sma-webauthn-passkeys.md` | WebAuthn / Passkeys als einheitlicher Login (Hybrid-Flow ablösen) | feature | irgendwann | sicher | spur | offen | - |
| `seeds/seed-pwa-api-db-pfad-konsolidierung.md` | pwa-api: zwei parallele DB-Pfade konsolidieren (asyncpg + psycopg2 → einer) | wartung | irgendwann | sicher | spur | offen | - |
| `seeds/seed-test-mandant-cleanup-voicedb.md` | test-mandant Aufräumung in voicedb_entries (4 Testdaten-Zeilen) | wartung | irgendwann | sicher | schritt | offen | - |

## Geltungsbereich: intern

| Datei | Titel | klasse | zugkraft | risikoklasse | stufe | status | abhängig von |
|-------|-------|--------|----------|--------------|-------|--------|--------------|
| `seeds/seed-assistant-agent.md` | Assistant-Agent: handelnder Agent an Geschäftsprozessen (eigenes Sicherheitsmodell) | feature | irgendwann | kritisch | spur | offen | - |

---

**Hot-Files** (strang-übergreifend, nur seriell anfassbar): siehe handgepflegte Datei [`00_HOT-FILES.md`](00_HOT-FILES.md).

**Parallelisierungs-Karte:** kommt nach Pflege von `beruehrt:` auf allen Seeds + Phase 2 des Autonomie-Zyklus.
