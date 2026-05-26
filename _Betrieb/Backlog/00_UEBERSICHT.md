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
| `seeds/seed-os-patching-broken.md` | OS-Patching greift nicht — Diagnose + Fix (nächster Zyklus nach BOOT-001) | security | jetzt | sicherheitskritisch-akut | spur | offen | - |
| `seeds/seed-code-security-checks.md` | Code-Security-Standards + automatisierte Checks für Prisment-Produkt-Code (Next.js + LangGraph) | security | jetzt | kritisch | spur | offen | - |
| `seeds/seed-brand-protection-saved-queries.md` | Cloudflare Brand Protection — Saved Queries + Logos einrichten | security | bald | sicher | sprung | offen | - |
| `seeds/seed-pretooluse-hook-allowlist.md` | PreToolUse-Hook als echte Bash-Tool-Allowlist (E13 strukturell realisieren) | security | bald | kritisch | spur | offen | - |
| `seeds/seed-architekt-lesezugriff-worktree.md` | Architekten-Lesezugriff auf Worktree-Branche (Sync-Werkzeug, nicht git-Topologie biegen) | prozess | bald | sicher | sprung | offen | - |
| `seeds/seed-spec-id-vergabe-kollisionsfrei.md` | Spec-ID-Vergabe kollisionsfrei machen (Race-Schutz für parallele Worktree-Spuren) | prozess | bald | sicher | sprung | offen | - |
| `seeds/seed-compose-recreate-after-edit.md` | Pflicht-Recreate nach Compose-Topologie-Edit | wartung | bald | sicher | sprung | offen | - |
| `seeds/seed-foundation-repo.md` | Foundation-Stack als zweites Git-Repo (Versionierung der Traefik/Foundation-Configs) | prozess | irgendwann | sicher | schritt | offen | - |
| `seeds/seed-pending-kernel-debconf-noise.md` | Pending-Kernel-Whiptail-Dialog bei -updates-Apply beobachten/unterdrücken | wartung | irgendwann | sicher | schritt | offen | - |
| `seeds/seed-diun-rueckbau.md` | Diun zurückbauen, sobald nightly Stabilität bewiesen hat | wartung | irgendwann | sicher | sprung | offen | - |

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
