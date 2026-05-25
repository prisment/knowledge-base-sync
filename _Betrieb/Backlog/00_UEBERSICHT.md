---
typ: derivat
titel: "Backlog-Übersicht (generiert)"
erzeugt_durch: "scripts/backlog/generate_uebersicht.py (prisment-platform)"
hinweis: "Generiert. Nicht händisch editieren. Handgepflegt: 00_HOT-FILES.md."
---

# Backlog-Übersicht

Sortierung: gruppiert nach `geltungsbereich`. Primär `zugkraft` (jetzt → bald → irgendwann), sekundär `klasse` (security → qualitaet → prozess → feature → wartung), tertiär `stand` (aufsteigend).

## Aktive Roadmaps

| Datei | Titel | Geltungsbereich | Stufe | Stand |
|-------|-------|-----------------|-------|-------|
| `roadmaps/ROADMAP_backlog-priorisierung.md` | Backlog-Priorisierung (Klasse × Zugkraft + harte Security-Regel) + generierte Übersicht | alle | spur | 2026-05-25 |
| `roadmaps/ROADMAP_lebende-bereichs-doku.md` | Lebende Bereichs-Dokumentation (Bereinigung + Übersichten + Automatisierung) | alle | spur | 2026-05-24 |
| `roadmaps/ROADMAP_SMA.md` | SMA — Social Media Automation Qualitätsarbeit | prisment | spur | 2026-05-25 |

## Geltungsbereich: alle

| Datei | Titel | klasse | zugkraft | risikoklasse | stufe | status | abhängig von |
|-------|-------|--------|----------|--------------|-------|--------|--------------|
| `seeds/seed-autonomie-korridor-parallele-sessions.md` | Autonome Ausführung im freigegebenen Korridor (Freigabe-Modell v2) + parallele Sessions via Worktrees | prozess | jetzt | kritisch | spur | offen | - |
| `seeds/seed-backlog-priorisierung-uebersicht.md` | Backlog-Priorisierung (Klasse × Zugkraft + harte Security-Regel) + generierte Übersicht mit Abhängigkeits-Graph | prozess | jetzt | sicher | spur | offen | - |
| `seeds/seed-bereichs-uebersichten.md` | Bereichs-Übersichten je Geltungsbereich erstellen | prozess | bald | sicher | spur | offen | - |
| `seeds/seed-feature-flag-zustand.md` | Feature-Flag als anerkannter Zustand: gebaut, aber nicht scharf | prozess | irgendwann | sicher | sprung | offen | - |
| `seeds/seed-skills-evaluieren.md` | Skills evaluieren: wiederkehrende operative Verfahren als SKILL.md fixieren | prozess | irgendwann | sicher | sprung | offen | - |
| `seeds/seed-dateisystem-bereinigung.md` | Dateisystem-Bereinigung nach Konsolidierung | wartung | irgendwann | sicher | sprung | offen | seed-code-repo-migration, seed-bereichs-uebersichten |

## Geltungsbereich: plattform

| Datei | Titel | klasse | zugkraft | risikoklasse | stufe | status | abhängig von |
|-------|-------|--------|----------|--------------|-------|--------|--------------|
| `seeds/seed-os-patching-broken.md` | OS-Patching greift nicht — Diagnose + Fix (nächster Zyklus nach BOOT-001) | security | jetzt | sicherheitskritisch-akut | spur | offen | - |
| `seeds/seed-code-security-checks.md` | Code-Security-Standards + automatisierte Checks für Prisment-Produkt-Code (Next.js + LangGraph) | security | jetzt | kritisch | spur | offen | - |
| `seeds/seed-brand-protection-saved-queries.md` | Cloudflare Brand Protection — Saved Queries + Logos einrichten | security | bald | sicher | sprung | offen | - |
| `seeds/seed-pretooluse-hook-allowlist.md` | PreToolUse-Hook als echte Bash-Tool-Allowlist (E13 strukturell realisieren) | security | bald | kritisch | spur | offen | PLAT-001_SPEC_v2 |
| `seeds/seed-compose-recreate-after-edit.md` | Pflicht-Recreate nach Compose-Topologie-Edit | wartung | bald | mittel | sprung | offen | - |
| `seeds/seed-apt-get-download-pin.md` | apt-get download muss immer mit exakter Versions-Pinnung erfolgen | wartung | irgendwann | niedrig | sprung | offen | - |
| `seeds/seed-pending-kernel-debconf-noise.md` | Pending-Kernel-Whiptail-Dialog bei -updates-Apply beobachten/unterdrücken | wartung | irgendwann | niedrig | schritt | offen | - |
| `seeds/seed-diun-rueckbau.md` | Diun zurückbauen, sobald nightly Stabilität bewiesen hat | wartung | irgendwann | sicher | sprung | offen | - |
| `seeds/seed-paperless-db-aufloesen.md` | Verwaiste paperless-DB im PostgreSQL-Hub auflösen | wartung | irgendwann | sicher | sprung | offen | - |

## Geltungsbereich: prisment

| Datei | Titel | klasse | zugkraft | risikoklasse | stufe | status | abhängig von |
|-------|-------|--------|----------|--------------|-------|--------|--------------|
| `seeds/seed-data-integrity-agents.md` | Data-Integrity-Architektur für LangGraph-Agents (zentrale Datenpunkt-Definition + Verifikation + definierte Übergaben) | qualitaet | jetzt | kritisch | spur | offen | - |
| `seeds/seed-sma-operative-doku-aufteilen.md` | SMA-Operative-Doku in strukturierte Systemzustand-Dateien aufteilen | wartung | bald | sicher | sprung | offen | - |
| `seeds/seed-sma-doku-rewrite-nach-telegram-eol.md` | SMA-Doku Re-Write nach Telegram-EOL (8 Dateien mit VERALTET-Markierung) | wartung | bald | sicher | sprung | offen | - |

## Geltungsbereich: intern

| Datei | Titel | klasse | zugkraft | risikoklasse | stufe | status | abhängig von |
|-------|-------|--------|----------|--------------|-------|--------|--------------|
| `seeds/seed-assistant-agent.md` | Assistant-Agent: handelnder Agent an Geschäftsprozessen (eigenes Sicherheitsmodell) | feature | irgendwann | kritisch | spur | offen | - |

---

**Hot-Files** (strang-übergreifend, nur seriell anfassbar): siehe handgepflegte Datei [`00_HOT-FILES.md`](00_HOT-FILES.md).

**Parallelisierungs-Karte:** kommt nach Pflege von `beruehrt:` auf allen Seeds + Phase 2 des Autonomie-Zyklus.
