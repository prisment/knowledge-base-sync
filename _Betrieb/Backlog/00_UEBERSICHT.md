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
| `seeds/seed-bereichs-uebersichten.md` | Bereichs-Übersichten je Geltungsbereich erstellen | prozess | bald | sicher | spur | offen | - |
| `seeds/seed-korridor-beobachtung.md` | Korridor-Beobachtung: 4 Datenpunkte aus PLAT-013 nach 1–3 Spuren auswerten | prozess | bald | sicher | sprung | offen | - |
| `seeds/seed-feature-flag-zustand.md` | Feature-Flag als anerkannter Zustand: gebaut, aber nicht scharf | prozess | irgendwann | sicher | sprung | offen | - |
| `seeds/seed-skills-evaluieren.md` | Skills evaluieren: wiederkehrende operative Verfahren als SKILL.md fixieren | prozess | irgendwann | sicher | sprung | offen | - |

## Geltungsbereich: plattform

| Datei | Titel | klasse | zugkraft | risikoklasse | stufe | status | abhängig von |
|-------|-------|--------|----------|--------------|-------|--------|--------------|
| `seeds/seed-code-security-checks.md` | Code-Security-Standards + automatisierte Checks für Prisment-Produkt-Code (Next.js + LangGraph) | security | jetzt | kritisch | spur | offen | - |
| `seeds/seed-pg-hba-haerten.md` | pg_hba.conf härten: trust 127.0.0.1/32 raus + host-Regeln subnetz-spezifisch | security | jetzt | sicherheitskritisch-akut | sprung | offen | - |
| `seeds/seed-brand-protection-saved-queries.md` | Cloudflare Brand Protection — Saved Queries + Logos einrichten | security | bald | sicher | sprung | offen | - |
| `seeds/seed-pretooluse-hook-allowlist.md` | PreToolUse-Hook als echte Bash-Tool-Allowlist (E13 strukturell realisieren) | security | bald | kritisch | spur | offen | - |
| `seeds/seed-aktualitaets-vollabdeckung.md` | Aktualitäts-Vollabdeckung: jede Komponente aktuell + jederzeit beweisbar (Sammel-Seed) | security | bald | kritisch | spur | offen | - |
| `seeds/seed-dependency-monitoring-renovate.md` | Dependency-Monitoring (Dependabot/Renovate) für alle Gitea-Repos | security | bald | kritisch | sprung | offen | - |
| `seeds/seed-firewall-drop-port22-public.md` | Firewall-Drop Port 22 auf Public-Interface (defense-in-depth über SSH-Socket-Bind) | security | bald | sicher | sprung | offen | - |
| `seeds/seed-image-pinning-latest-services.md` | Image-Pinning: Gitea, Twenty, Open WebUI und übrige :latest/:main → exakte Versionen | security | bald | sicher | schritt | offen | - |
| `seeds/seed-logging-forensik-grundlage.md` | Logging/Forensik-Grundlage: CF-Access-Logs Logpush + Postgres log_connections + Traefik accessLog | security | bald | kritisch | spur | offen | - |
| `seeds/seed-test-umgebung-neu-secrets.md` | Test-Familie neu aufbauen: eigene Secrets, intern-only, eigene Test-DB | security | bald | sicher | spur | offen | - |
| `seeds/seed-architekt-lesezugriff-worktree.md` | Architekten-Lesezugriff auf Worktree-Branche (Sync-Werkzeug, nicht git-Topologie biegen) | prozess | bald | sicher | sprung | offen | - |
| `seeds/seed-dsgvo-dpa-cloudflare.md` | DSGVO-Auftragsverarbeitungsvertrag mit Cloudflare unterzeichnen | prozess | bald | sicher | schritt | offen | - |
| `seeds/seed-notausgang-doku.md` | Notausgang-Doku: schriftliche Rescue-System- und Telefon-Tailscale-Prozedur | prozess | bald | sicher | schritt | offen | - |
| `seeds/seed-secret-inventar-pg-rollen.md` | Secret-Inventar codifizieren: pg_authid-Rollen-Audit pro Postgres-Cluster | prozess | bald | sicher | schritt | offen | - |
| `seeds/seed-spec-id-vergabe-kollisionsfrei.md` | Spec-ID-Vergabe kollisionsfrei machen (Race-Schutz für parallele Worktree-Spuren) | prozess | bald | sicher | sprung | offen | - |
| `seeds/seed-compose-recreate-after-edit.md` | Pflicht-Recreate nach Compose-Topologie-Edit | wartung | bald | sicher | sprung | offen | - |
| `seeds/seed-cve-watchdog-regel-b.md` | Cloudflare WAF: CVE-2025-55182 Watchdog-Regel B mit gehärteter Expression | security | irgendwann | sicher | schritt | offen | - |
| `seeds/seed-waf-owasp-block-modus.md` | Cloudflare WAF OWASP Core Ruleset von 'log' auf 'block' umstellen (nach Live-Tuning) | security | irgendwann | sicher | schritt | offen | - |
| `seeds/seed-foundation-repo.md` | Foundation-Stack als zweites Git-Repo (Versionierung der Traefik/Foundation-Configs) | prozess | irgendwann | sicher | schritt | offen | - |
| `seeds/seed-pending-kernel-debconf-noise.md` | Pending-Kernel-Whiptail-Dialog bei -updates-Apply beobachten/unterdrücken | wartung | irgendwann | sicher | schritt | offen | - |
| `seeds/seed-diun-rueckbau.md` | Diun zurückbauen, sobald nightly Stabilität bewiesen hat | wartung | irgendwann | sicher | sprung | offen | - |
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

## Geltungsbereich: intern

| Datei | Titel | klasse | zugkraft | risikoklasse | stufe | status | abhängig von |
|-------|-------|--------|----------|--------------|-------|--------|--------------|
| `seeds/seed-assistant-agent.md` | Assistant-Agent: handelnder Agent an Geschäftsprozessen (eigenes Sicherheitsmodell) | feature | irgendwann | kritisch | spur | offen | - |

---

**Hot-Files** (strang-übergreifend, nur seriell anfassbar): siehe handgepflegte Datei [`00_HOT-FILES.md`](00_HOT-FILES.md).

**Parallelisierungs-Karte:** kommt nach Pflege von `beruehrt:` auf allen Seeds + Phase 2 des Autonomie-Zyklus.
