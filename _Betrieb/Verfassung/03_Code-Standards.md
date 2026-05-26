---
typ: verfassung
titel: "Code-Standards"
stand: 2026-05-27
aenderung: "nur nach oben, nur durch bewusste Freigabe"
---

# 03 — Code-Standards

## Code gehört NICHT in die Knowledge-Base

Die Knowledge-Base dokumentiert ÜBER Code, sie enthält ihn nicht. Echter Code (Skripte, App-Code, Workflow-Definitionen) lebt im separaten Code-Repo. Grund: Code an zwei Orten = zwei Versionen = zweite Wahrheit = Bruch des Single-Source-of-Truth-Prinzips.

Konsequenz: `.py`-Dateien, n8n-Workflow-JSONs, Shell-Skripte und Ähnliches werden bei der Migration NICHT in die neue Struktur einsortiert, sondern als „gehört ins Code-Repo" markiert und ausgesondert.

## Single Source of Truth

Jede Information existiert genau einmal. Ansichten (SVG-Übersichten, Zusammenfassungen) werden aus der einen Quelle ABGELEITET, nie als zweite Quelle gepflegt. Eine veraltete Doku ist schlimmer als keine, weil man ihr vertraut.

## Standards-Platzhalter (wachsen mit der Arbeit)

Konkrete Code-Vorgaben (Test-Strategie, CI/CD, Data-Integrity, Tenant-Isolation) werden aus den realen Zyklen heraus eingearbeitet — beginnend mit dem Security-Refactor (RLS + Tenant-Isolation) und der Data-Integrity-Pipeline. Bis dahin gilt diese Datei als Platzhalter, der nach jedem relevanten Zyklus über Phase 9 wächst (nie nach unten).

## Security-Gates im CI (PLAT-031, 2026-05-27)

Jeder Commit/PR/nightly auf `prisment-platform` durchläuft folgende blockierende Gates (`.gitea/workflows/ci.yml`, Gitea-Actions, ein internal_gitea_runner):

- **`npm audit --audit-level=moderate`** für `pwa/pwa-web` + `admin/admin_web`. Bei moderate-CVE: Fail. Fix-Pfad: gezielte Paket-Anhebung (nie `npm audit fix --force` — Major-Downgrades brechen alles), ggf. `overrides`-Feld in package.json gegen transitive Pins.
- **`pip-audit -r requirements.txt --strict --vulnerability-service osv`** für die 5 LangGraph-Agents + pwa-api. Bei beliebigem CVE: Fail. Bewusste Ausnahmen via `--ignore-vuln <ID>` direkt in `ci.yml` mit einzeiliger Begründung (verhindert „magisches" Wegklicken).
- **ESLint (`next lint --max-warnings=0`)** für beide Next-Projekte. `.eslintrc.json` erbt `next/core-web-vitals`. Kosmetische Regeln, die ganzes Bestands-JSX brechen würden, dürfen per Rule-Override deaktiviert werden — Security-relevante Regeln bleiben hart.
- **Ruff (`ruff check`)** für alle Python-Pakete. Starter-Set in `pyproject.toml` ist nur `F` (Pyflakes/echte Bugs). Style-Regeln (`E`/`W`) wachsen iterativ ein, wenn die Diskussion dazu reif ist. Pre-existing Verstöße werden per-Zeile `# noqa: <CODE> — TODO …` markiert, NIE global disabled.

**Nightly:** Derselbe Workflow läuft per `cron: '0 4 * * *'` gegen `main` — fängt neu bekannt gewordene CVEs in bereits-gemergten Deps. Fail-Notification per Gitea-Mailer an `info@prisment.de`.

## Tenant-Isolation: RLS-Pattern (PLAT-031 Bündel 5, 2026-05-27)

Tenant-Daten leben in `agent_data.public.*`, Auth/Membership in `agent_data.auth.*` (siehe `pwa-api/app/db.py:1-24`). RLS ist verbindlich für **jede** `public.X`-Tabelle, die Tenant-Daten trägt:

1. Pflicht-Spalte `tenant_id TEXT NOT NULL`.
2. `ENABLE` + `FORCE ROW LEVEL SECURITY` (FORCE: auch Eigentümer/Migrator unterliegen der Policy, sonst stille Cross-Tenant-Writes).
3. Anwendung ausschließlich über `SELECT public._apply_tenant_rls('public.X')` aus `customer/postgres/init-scripts/06_rls.sql` — das Pattern lebt an EINER Stelle (SSOT). Migrationen rufen die Funktion auf, definieren keine eigene Policy.
4. Konsum durch `tenant_app_user` mit `SET LOCAL app.current_tenant = '<id>'` pro Transaktion (siehe `pwa-api/app/db.py:21-23`).

**`auth.tenant_memberships`-Sonderregel (Logbuch E39):** Kein RLS — Schutz per GRANT-Engerung in `05_grants.sql`. `tenant_app_user` darf nur SELECT (Login-Pfad braucht es), schreibend nur `admin_user` + `pwa_migrator`. Begründung: Brücken-Tabelle definiert Zugehörigkeit, RLS würde Login brechen.

**Verifikation:** `customer/postgres/scripts/rls_smoke.sh` spinnt ephemere DB hoch, lädt Init-Scripts, prüft 7 Aspekte (Tenant-Isolation in beide Richtungen, admin-Cross-Tenant, E39-GRANTs, Cross-Tenant-Write-Block via WITH CHECK). Smoke ist Reproduzier-Punkt für Drift-Verdacht.

## Dependency-Pinning & Update-Pfad

- **npm:** `package.json` mit `==`/exakten Versionen für direkte Deps; Lockfile zwingend committed. `overrides`-Feld ist legitimes Werkzeug bei transitiv-eingebrannten CVEs (postcss-via-next), aber mit Kommentar warum.
- **pip:** `requirements.txt` mit `==`-Pinning; Floor-Constraints (`>=`) nur für CVE-Schutz (`langsmith>=0.8.0`), nicht für API-Komfort.
- **Wer bumped:** Update-Initiative liegt nicht beim Daemon (Diun watcht Container-Images, nicht Lockfiles). Bumps erfolgen reaktiv (CI-Fail) oder proaktiv im jeweiligen Zyklus. Renovate o.Ä. wurde bewusst verworfen — passt nicht zum No-PR-Workflow.
