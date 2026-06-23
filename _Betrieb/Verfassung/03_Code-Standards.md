---
typ: verfassung
titel: "Code-Standards"
stand: 2026-05-28
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

### → Lebendes Register: [`03a_Standards-Kanon.md`](03a_Standards-Kanon.md) (PLAT-110)

Der konkrete Träger dieses Platzhalters ist die Schwester-Datei **[`03a_Standards-Kanon.md`](03a_Standards-Kanon.md)** — ein lebendes Register belegter Standards (Format je Eintrag: `ID · Schicht · Regel · Herkunft · Enforcement-Modus`). Sie wächst „über Phase 9, nur nach oben" durch Rückwärts-Ernte aus den Zyklen; `maschinell`-Einträge werden über den CI-Check-Runner (`prisment-platform/scripts/canon/run_canon_checks.py`) als Vorwärts-Wächter scharf gestellt. Die selten geänderte Regel-Kerndatei (diese hier) bleibt dabei stabil. Etabliert mit PLAT-110 (Fundament-Härtung 1/6).

## Destruktive Operationen: kein Error-Suppress (PLAT-085, 2026-06-19)

Destruktive, schwer rückrollbare Operationen (Repo-/Branch-Löschung, `DELETE`
gegen die Gitea-API, `rm -rf`, DB-DROP, Force-Push) werden **nie** mit
`2>/dev/null`, `|| true` oder `|| :` maskiert. Der unterdrückte Fehler verbarg
am 2026-06-12 eine versehentliche Repo-Löschung ganze 33 Minuten lang (die Session
hielt ihren `curl -X DELETE …/repos/…` für einen Token-Cleanup und sah keinen
Fehler) — der Schaden blieb danach ~1 Woche unbemerkt. Regel: bei destruktiven
Befehlen Exit-Code **prüfen und sichtbar machen**, nicht verschlucken. Lesende/
idempotente Befehle dürfen weiter `2>/dev/null` nutzen.

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

### Tenant-Tabellen-Klassifikation (PLAT-044, 2026-05-28)

Das ursprüngliche E39-Pattern hat zwei Tabellen-Klassen unterschieden (Tenant-Daten mit RLS / `auth.tenant_memberships` als Brücke ohne RLS). PLAT-044 hat aufgedeckt, dass `public.tenants` strukturell eine **dritte** Klasse ist und in PLAT-031 fälschlich als Brücke behandelt wurde. Damit künftig keine Spec dasselbe Versehen wiederholt, gilt die folgende Klassifikation als verbindlich:

| Kategorie | Beispiele | RLS | Filter-Spalte | GRANT-Engerung | Begründung |
|---|---|---|---|---|---|
| **Tenant-Daten** | `voicedb_entries`, `posts`, `sessions`, `topics`, `editorial_plans` | Pflicht (ENABLE + FORCE) | `tenant_id` | optional (RLS reicht) | Mandanten-Inhalte. RLS-Helper-Funktion `_apply_tenant_rls('public.X')` direkt anwendbar. |
| **Tenant-Stammdaten** | `tenants` | Pflicht (ENABLE + FORCE), Filter via `id` | `id` (= Stammschlüssel = Tenant-Identifier) | optional, ABER Cross-Tenant-Hub-Rolle (`admin_user`) braucht **explizite GRANTs**, sonst stille permission-denied-Bugs | Tabelle, deren Primärschlüssel selbst die Tenant-ID ist. Helper-Funktion taugt NICHT direkt (sie hardcodet `tenant_id`-Spalte). Eigene `CREATE POLICY` mit `id`-Filter pflicht. |
| **Brücken-Tabellen** | `auth.tenant_memberships` | **KEIN RLS** (Login-Pfad braucht ungefilterten Read über User-Tenant-Mappings, bevor User einen Tenant aktiviert) | n/a | **Pflicht** (E39): `nextauth_user` nur SELECT, `tenant_app_user` gar keine GRANTs (auch nicht SELECT — engster Stand) | Definiert Zugehörigkeits-Beziehung. RLS würde Login brechen, weil User vor Tenant-Aktivierung seine Membership lesen muss. |

**Vor einer neuen RLS-Spec: klassifizieren.** Tenant-Daten / Tenant-Stammdaten / Brücken — die drei sind nicht austauschbar, jeder Patzer ist eine eigene Sicherheits-Lücke (Stammdaten ohne RLS = PLAT-044-Lücke, Brücke mit RLS = Login-Bruch, Tenant-Daten ohne RLS = Cross-Tenant-Leak).

**Live-`admin_user`-GRANTs auf `public.tenants` müssen vorhanden sein** (PLAT-044-Befund 2026-05-28): in PLAT-031 wurden die GRANTs für `admin_user.public.tenants` versehentlich nie gesetzt, weil die Tabelle nicht als RLS-Ziel behandelt wurde. Folge: 4-6 Live-Code-Pfade (`pwa-api/scheduling/worker.py`, `pwa-api/routes/tenant_settings.py`, `pwa-api/routes/admin.py`, `langgraph/helpdesk/app/tools.py`), die `UPDATE tenants` als `admin_user` ausführen wollten, scheiterten still mit `permission denied`. PLAT-044 setzt die GRANTs in `05_grants.sql` und führt die Live-Aktivierung durch.

## Test-/Dev-/Staging-Daten — Pseudonymisierungs-Werkzeug-Pflicht (PLAT-046, 2026-05-28)

Test-/Dev-/Staging-Bühnen tragen nie un-pseudonymisierte Echtdaten. Wenn eine Bühne aus Live-Daten gefüttert wird, ist **Skill `test-data-pseudonymize`** (`.claude/skills/test-data-pseudonymize/SKILL.md`) Pflicht-Werkzeug — er trägt das Verfahren, die zweistufigen PII-Audit-Pattern und die Self-Test-Akzeptanz.

**Mapping-Tabelle ist Architekten-Hand-Schlüssel** (Pseudonym-Wahl + Speicherort). Sie lebt **außerhalb des Repos** unter `/home/claude-deploy/<bühnen-name>-pseudonymization-mapping.json` mit perms `0600`. Auch außerhalb des knowledge-base-Vaults, sonst Klon-Risiko über Vault-Sync. Eine Beispiel-Datei mit Dummy-Werten (`*-pseudonymization-mapping.example.json`) darf im Repo liegen.

**Pattern-Vorlagen** liegen in `prisment-platform/scripts/fixtures/`:
- `pseudonymize_voicedb_snapshot.py` — Pseudonymisierungs-Skript (Mapping-basiert, längste-zuerst, idempotent + Selbsttest, neutral benannt nach Header-Trap-Lehre).
- `pii_audit.py` — zweistufiges PII-Audit (bekannte Tokens + breite Pattern-Klassen).
- `build_voicedb_snapshot.py` — Hochfahr-Pfad mit Fail-closed-Marker-Check (`-- PSEUDONYMIZED <Datum>`).

Verfassungs-Anker für die Risikoklasse-Wirkung: `04_Sicherheits-Prinzipien.md`, Abschnitt „Echtdaten-Risikoklasse-Kopplung".

## Data-Integrity: Agent-Übergabe-SSOT (PRIS-019, 2026-05-27)

Datenpunkte, die zwischen LangGraph-Agents fließen, leben in **einer**
Pydantic-Definition — `langgraph/shared/contracts.py`. Die Klasse `SessionData`
ist die Quelle für die Interview→Content-Brücke (Felder gemäß PRIS-019-Spec
A.1-Inventur); `AnalyticsPlanHints` für Analytics→Redaktionsplan;
`AnalyticsModeLiteral` für den Analytics-`mode`-Parameter.

**Verteilung der SSOT in die Agents:** Build-Contexts sind heute pro Agent
(kein Mono-Image). Verteilung per `scripts/sync_shared_contracts.sh` —
kopiert die kanonische Datei vor jedem Build in alle vier
`langgraph/<agent>/app/contracts.py`. Diese Kopien sind **nicht** direkt zu
editieren (Header trägt entsprechende Warnung). Der CI-Job
`contracts-ssot` (`.gitea/workflows/ci.yml`) blockiert jeden Drift.

**Eingangskonvertierung am SSOT, nicht im Caller:** JSONB/Array-Felder, die in
der DB als `NULL` landen können (Migration ohne `DEFAULT`), werden über
Pydantic-`field_validator(mode='before')` auf leere Defaults gemappt
(`None → {}` / `None → []`). Konvertierungslogik liegt am Modell-Eingang,
nicht in jedem `read_session`/`load_session_data`-Caller.

**Runtime-Gate:** Konsumenten validieren Sessions hart gegen `SessionData`
beim Laden (`content/app/tools.py:load_session_data` raised `ValueError` bei
Pflichtfeld-/Typ-Verstoß). Stille Mist-Generierung aus unvollständigen Daten
ist abgeschafft — sichtbarer Abbruch zwingt Re-Run.

**Mode-Felder als Literal:** Analytics-`mode` (und künftige Enum-artige Felder)
werden als `typing.Literal[...]` typisiert. Ein Tippfehler schlägt als
`ValidationError` fehl, nicht als falscher Branch.

**CI-Schema-Check (`scripts/check_contracts.py`):** drei Schichten,
blockierend:
1. Drift zwischen `shared/contracts.py` und Agent-Kopien.
2. Fixture-Validation einer Beispiel-Session gegen `SessionData`.
3. AST-Walk: jeder `session_data.get('KEY')` und
   `state.session_data.get('KEY')` in den Agent-app/-Dirs muss zu einem Feld
   im Modell passen. Dokumentierte Sonderfälle (DB-Legacy-Aliase wie `_wip`,
   `created_at`, `topics`, `metadata`) leben in
   `scripts/check_contracts.whitelist.txt` mit Begründungs-Kommentar.

**Lesepfad-Eindeutigkeit:** Eine Quelle pro Datenpunkt. VoiceDB ist heute
Postgres-only (PRIS-019 B5 schloss die Dual-Write-Asymmetrie). Routes in
`gitea_client.read_md_file`/`append_md_file` mit `voicedb_md`-Erkennung leiten
direkt nach Postgres, ohne Gitea-Fallback — bei DB-Fehler bricht der Lauf ab
statt aus möglicherweise veralteter Zweitquelle zu lesen.

**Telemetrie-Pflicht für strukturelle Pfad-Gabelungen:** Wenn Code zwischen
zwei Pfaden unterscheidet (V6 vs. Legacy, neuer vs. alter Generator), wird
das Auswahl-Ereignis geloggt (`[…/telemetry] PRIS-XXX has_X=… session=…
tenant=…`). Damit ist in Prod sichtbar, welcher Pfad wie oft greift —
unsichtbare Pfade sind unwartbar.

**Legacy-Erkennung-mit-Abbruch, nicht Migration:** Wenn der neue Pfad nicht
beliefert ist (V6-Daten fehlen), wird hart abgebrochen statt aus Alt-Daten zu
generieren. Re-Run via neuem Pfad ist sauberer Fix; Migration alter Werte ist
Raten und damit selbst eine Bruchstelle.

## Dependency-Pinning & Update-Pfad

- **npm:** `package.json` mit `==`/exakten Versionen für direkte Deps; Lockfile zwingend committed. `overrides`-Feld ist legitimes Werkzeug bei transitiv-eingebrannten CVEs (postcss-via-next), aber mit Kommentar warum.
- **pip:** `requirements.txt` mit `==`-Pinning; Floor-Constraints (`>=`) nur für CVE-Schutz (`langsmith>=0.8.0`), nicht für API-Komfort.
- **Wer bumped:** Update-Initiative liegt nicht beim Daemon (Diun watcht Container-Images, nicht Lockfiles). Bumps erfolgen reaktiv (CI-Fail) oder proaktiv im jeweiligen Zyklus. Renovate o.Ä. wurde bewusst verworfen — passt nicht zum No-PR-Workflow.
