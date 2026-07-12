---
typ: verfassung
titel: "Standards-Kanon (lebendes Register zu 03 Code-Standards)"
stand: 2026-07-03
aenderung: "nur nach oben, nur durch bewusste Freigabe"
---

# 03a — Standards-Kanon

> **Schwester-Datei zu [`03_Code-Standards.md`](03_Code-Standards.md).** Während 03 die
> selten geänderte Regel-Kerndatei bleibt, ist dies das **wachsende Register**: jeder
> Folge-Zyklus erntet neue belegte Mängel als zusätzliche Kanon-Einträge. Etabliert mit
> **PLAT-110** (Fundament-Härtung 1/6).

Der Standards-Kanon ist die SSOT der **belegten, verbindlichen Code-/Betriebs-Standards**.
Jeder Eintrag ist aus einem **belegten** Mangel geerntet (Rückwärts-Ernte) — kein Eintrag
ohne Fundstelle. Der Kanon ist *forward-erzwingend*: `maschinell`-markierte Einträge werden
über den CI-Check-Runner (`prisment-platform/scripts/canon/run_canon_checks.py`) als
Vorwärts-Wächter scharf gestellt — ein Check pro Folge-Fix, der den Rückfall nach dem Fix
verhindert.

## Woher neue Einträge kommen (Rückwärts- UND Vorwärts-Ernte)

Das Register wächst aus zwei Quellen — „nur nach oben, über Phase 9": **Rückwärts-Ernte**
(ein kalter Audit/eine Sondierung erntet bestehende belegte Mängel, Pflicht-Ausgabe-Sektion
`## Kanon-Saat` im Sondierungs-Template — so entstanden K-01…K-08 aus PLAT-109) und
**Vorwärts-Ernte** (deckt irgendein Zyklus künftig einen fehlenden/zu schwachen Standard auf
— Audit, belegter Test-Abnahme-Defekt Verf. 08, Akt-3-Abschluss —, ist Triage Pflicht: `K-NN`
aufgenommen oder mit Grund verworfen, kein stilles Übergehen; Maßstab „Standard-würdig" =
*wiederholbar*). Verbindliche Regel: **Verfassung 00**, Abschnitt „Vorwärts-Ernte in den
Standards-Kanon" (SSOT, hier nur Verweis; Schleife verankert mit PLAT-112).

## Eintrags-Format (verbindlich)

Jeder Standard trägt **genau diese fünf Felder** — keine Format-Erfindung beim Anhängen:

| Feld | Bedeutung |
|---|---|
| **ID** | `K-NN`, fortlaufend, nie wiederverwendet. |
| **Schicht** | Architektur-Schicht (Daten · Sicherheit · Auslieferung · Beobachtbarkeit · API …). |
| **Regel** | Die Vorgabe in **einem Satz** — was gilt, prüfbar formuliert. |
| **Herkunft** | Die **belegte Fundstelle** des Mangels (Sondierungs-Sektion / `datei:zeile` / verifizierter Befund), nie Prosa. |
| **Enforcement-Modus** | `maschinell` (CI-Check) · `Tor` (bewusster Freigabe-Schritt) · `Urteil` (Review). |

> **Kein Schärfe-/Status-Feld in diesem Register.** Ob ein `maschinell`-Eintrag im CI-Runner
> *scharf* (blockierend) oder im *Warn-Modus* läuft, ist eine **abgeleitete Ansicht** des
> realen Runner-Zustands — die SSOT dafür ist `run_canon_checks.py --report`, **nicht** ein
> handgepflegtes Markdown-Feld. (Begründung: `03_Code-Standards.md` § „Single Source of
> Truth" verbietet eine zweite, still veraltende Quelle. PLAT-110 Einwand 1.)

### Vorlage zum Anhängen (kopieren, ausfüllen, nichts erfinden)

```
| K-NN | <Schicht> | <Regel in einem Satz> | <belegte Fundstelle> | <maschinell | Tor | Urteil> |
```

## Register

| ID | Schicht | Regel | Herkunft | Enforcement-Modus |
|---|---|---|---|---|
| K-01 | Daten | RLS **fail-closed**: jede `tenant_id`-Tabelle trägt Policy **default-deny + `FORCE ROW LEVEL SECURITY`**, oder ist explizit shared-reference; `tenant_app_user` nie `SELECT` auf policy-lose `tenant_id`-Tabelle | PLAT-109 §(b), 4 RLS-offene Tabellen verifiziert | maschinell (Ziel-Check Spec 2) |
| K-02 | Daten | `agent_data` führt ein Applied-Migrations-Ledger; CI prüft DB-Stand == Repo-Migrationen | PLAT-109 §(b), 0 Tracking-Tabelle, 52+1 SQL, 007-Kollision | Tor (Ziel Spec 4) |
| K-03 | Sicherheit | Secrets ruhend verschlüsselt oder mind. `0600`; keine Auth-Tokens als Klartext-Spalte | PLAT-109 §(d), `auth.accounts.*_token` Klartext + 644-Secrets | maschinell (Ziel Spec 3) |
| K-04 | Sicherheit | Dienste laufen non-root + `no-new-privileges:true` + `cap_drop: ALL` | PLAT-109 §(d), `pwa_api`/`langgraph_content` root, 3/22 nnp | maschinell (**Beispiel-Check B4, Warn-Modus**) |
| K-05 | Auslieferung→Daten | Kein `:latest`-Flip ohne verifizierten Migrations-Gleichstand der Ziel-DB | PLAT-109 §(e), Promote-Tore prüfen Image-Smoke, nicht Schema-Drift | Tor (Ziel Spec 4) |
| K-06 | Beobachtbarkeit | Klasse-A/B-Dienst hat Healthcheck + JSON-Logs; ein Fehler-Aggregat existiert | PLAT-109 §(f), ~50% Healthcheck, keine Aggregation | Tor + Urteil (Ziel Spec 6) |
| K-07 | Beobachtbarkeit | Kein Service-Drift: compose == running == registry | PLAT-109 §(g), 22/26/29-Spreizung | maschinell (Ziel Spec 6) |
| K-08 | API | Mutierende Endpunkte idempotent oder explizit als nicht-idempotent markiert | PLAT-109 §(c), Idempotenz punktuell (`mark_onboarded`) | Urteil/Review |
| K-09 ⊘ | Auslieferung | **[stillgelegt PLAT-132]** Worker-gebaute Windows-Ziel-`.ps1` werden **UTF-8-mit-BOM** kodiert und setzen **`$ErrorActionPreference='Continue'`** (PS 5.1 liest BOM-loses UTF-8 als ANSI → Parse-Fehler) | PLAT-111 Fix-Loop, 2 Windows-only-Defekte. **Gegenstandslos seit PLAT-132:** Desktop-Architekt tot, kein Windows-`.ps1`-Bau-Pfad mehr; `git-land.ps1` + sein Encoding-Regressions-Guard archiviert. Reaktiviert sich nur, falls je wieder ein Worker Windows-`.ps1` baut | — (stillgelegt; historischer Anker) |
| K-10 | Auslieferung | Skripte, die in **mehr als einem Wurzel-Kontext** laufen können (on-host *und* CI-Checkout), leiten die Repo-Wurzel **aus der Umgebung ab** (`GITHUB_WORKSPACE` → `git rev-parse --show-toplevel` → skript-relativ) statt einen absoluten Host-Pfad zu hardcoden | PLAT-123 Sondierung (`## Kanon-Saat`); `scripts/sync_shared_contracts.sh:15` `REPO_ROOT=/opt/infrastructure/environment_a` hart → brach im act-runner-Checkout (`/workspace/...`). Die konkrete Fundstelle wurde durch PLAT-123 (Wand D) gegenstandslos, das *Muster* ist wiederholbar (jedes geteilte Sync-/Build-/Helfer-Skript) | Urteil (Code-Review); optional maschinell per grep-Lint auf `REPO_ROOT=…/opt/…`-Hardcodes in `scripts/**` |
| K-11 | Auslieferung/Frontend | Vollbild-Overlays (Onboarding-Spotlight, Tour, Modal) tragen `pointer-events: none` an jedem durchlässigen Root-Layer, sonst schlucken sie Klick/Scroll unsichtbar über dem eigentlichen UI | PRIS-078 CoachMark: „Editor-Scroll-Bug" war eine unsichtbare Vollbild-CoachMark ohne `pointer-events:none` am Root (memory `project_coachmark_touch_trap`); erst messen, dann fixen | Urteil (Review) |
| K-12 | API/Auslieferung | **[Workaround-Standard]** Binär-Uploads (Audio/Foto) gehen als **base64-JSON**, nicht `multipart/form-data` — Cloudflare blockt Binär-multipart mit „Just-a-moment"-403. Gilt nur, **solange** die CF-WAF Binär-multipart blockt; der korrekte Dauer-Fix ist eine CF-WAF-Ausnahme für die Upload-Endpunkte (base64 ≈ +33 % Payload) — fällt die CF-Blockade, entfällt der Standard | PRIS-047/CF-Befund: multipart→403, Fix Audio/Foto als base64-JSON (memory `project_cloudflare_blocks_binary_multipart`); Workaround-Label PLAT-166 | Urteil (Review) |
| K-13 | Daten | DB-Migrationen/Objekt-Referenzen **pinnen das Schema explizit** (`schema.table`, `GRANT USAGE ON SCHEMA`) statt sich auf `search_path` zu verlassen — CI-Bootstrap-`search_path` (auth,public) weicht vom Live-Default (public) ab → „relation does not exist" | PLAT-127 (search_path CI-ephemer vs. Live) + PLAT-133 (`relation users does not exist` = fehlendes `USAGE ON SCHEMA auth`; memory `project_dev_login_schema_usage`, `project_plat127_searchpath_ci_ephemer`) | Urteil (Review) |
| K-14 | API/Daten | One-shot-Zustandsmarker (Onboarding-Schritt, „vorgezogen", Versand-Flag) werden **idempotent** gesetzt — nie bei jedem Start/Aufruf re-gefeuert | PRIS-099 Interview-Timing: Marker `interview_vorgezogen` feuerte bei jedem Start statt einmalig (memory `project_pris099_interview_timing`) | Urteil (Review) |
| K-15 | Beobachtbarkeit/API | Operationen, die >10 s dauern können, bekommen einen **Fortschritts-/Poll-Pfad** (Status-GET) statt eines einzelnen blockierenden Requests — Browser/Proxy brechen die Verbindung bei ~13–20 s ab, obwohl das Backend noch 200 liefert | PWA „KI-Verbindungsfehler": pwa_api 200, Browser bricht bei 13–20 s; Fix paralleles GET /posts-Pollen (memory `project_pwa_ki_verbindungsfehler`) | Urteil (Review) |
| K-16 | Auslieferung | Direkte Dependencies sind **exakt gepinnt** (pip `==`, npm exakte Version — kein `^`/`~`/Range) in allen getrackten `requirements.txt` und `package.json`; Floor-Constraints (`>=`) nur mit `# canon-floor: <CVE/Grund>`-Kommentar. **Seit PLAT-169 zusätzlich:** jeder pip-Dockerfile-Service trägt ein kompiliertes `requirements.lock` (uv, transitive Menge exakt, `source-sha256`-Header), synchron zur `requirements.txt` | Canon-Review PLAT-166 (2026-07-02): `langgraph/content/requirements.txt` 7×`>=` und `admin/admin_web/package.json` `^`-Ranges trotz 03-Pinning-Standard (seit 2026-05-28) — Review-Modus nachweislich unzureichend, Regel grep-bar; Lock-Erweiterung PLAT-169 (transitive Deps waren ungepinnt, Audit≠Image) | maschinell (`check_deps_pinned.py` Warn-Modus; Lock-Sync: `check_lock_sync.py` — CI-blockierend in `audit-python`, Canon-Runner Warn-Modus) |
| K-17 | Auslieferung/Sicherheit | Versionswahl nur gegen **Live-Quelle**: jede neu geschriebene Versionsangabe (Basis-Image `FROM`, direkte Dependency, Scaffold) wird vor dem Schreiben gegen Registry/PyPI/npm/endoflife.date geprüft — nie aus Modell-Trainingsdaten übernommen (Beleg: eine Zeile im Commit/Report) — und kein Basis-Image/Framework im Bestand läuft über sein EOL-Datum hinaus | PLAT-171 (2026-07-03): `pwa/pwa-web/Dockerfile` + `admin/admin_web/Dockerfile` mit `FROM node:20.20.2` — Node 20 EOL seit 2026-04-30 (endoflife.date), Nightly-CI trotzdem grün (audit-Gates prüfen nur CVEs, kein EOL); Vorläufer-Vorfälle (Betreiber-Bericht 2026-07-03): Next.js-Erstinstallation mit Trainingsdaten-Altversion (später kompromittiert) + manuelles EOL-Update 2026-07-02 | maschinell (`check_eol.py`, Warn-Modus) + Urteil (Neuwahl-Beleg im Commit/Report) |
| K-18 | Auslieferung/Prozess | **Ablösung ⇒ Abbau-Pflicht:** Jede Spec, die einen bestehenden erreichbaren Pfad funktional ersetzt, trägt die Pflicht-Sektion `## Ablösung` (Reste-Inventar + Abbau-Entscheid pro Rest + Pflicht-AK „alter Pfad tot ODER abgebaut", Beleg statt Behauptung) — kein neuer Pfad ohne Entscheid über den alten | PLAT-177 (2026-07-12): Chat-First→Fokus-First ließ ≥7 tote/halbtote Stellen liegen (Reste-Inventar im Seed `chat-first-reste-bereinigung`, Rev 3d17abb — u. a. `ChatStream.tsx` tot via FokusShell-isRoot-Ternary); Schadensfall Monatszettel PRIS-148: `pwa/pwa-api/app/scheduling/worker.py:383-386` sendete zettel_cards in nie gerenderten Chat — live, für den Kunden unsichtbar, teure Fehldiagnose. Muster wiederholbar bei jedem UX-/Architektur-Wechsel | Tor (Korridor-Wand `01_Spec-Format.md` „Ablösung") + Urteil (Evaluator [STRUKTURELL]) |

> **Real laufende Checks (Stand PLAT-171, 2026-07-03):** der Runner führt **sechs** Checks —
> K-01 `rls-failclosed`, K-04 `container-haertung` (PLAT-110 B4, `check_container_haertung.py`),
> PRIS-118 `posts-tenant-filter`, K-16 `deps-pinned`, K-16 `lock-sync`
> (`check_lock_sync.py`, PLAT-169) und K-17 EOL (`check_eol.py`, PLAT-171 — Basis-Images +
> Frameworks gegen die endoflife.date-API, fail-open bei Netzfehlern). **K-01 und
> `posts-tenant-filter` laufen scharf** im CI-Job `canon-k01-rls-scharf`
> (`CANON_SCHARF: K-01,posts-tenant-filter`)
> gegen das committete Schema; K-01 zusätzlich nightly im Warn-Modus gegen die Live-DB
> (`rls_failclosed_nightly.sh`, PLAT-135). K-04, K-16 `deps-pinned`, K-16 `lock-sync` und
> K-17 laufen im Canon-Runner im Warn-Modus — `lock-sync` blockt zusätzlich hart als eigener
> Step im CI-Gate-Job `audit-python` (vor pip-audit). Der genaue
> Schärfe-Zustand kommt aus `run_canon_checks.py --report`, nicht aus dieser Tabelle.
>
> **K-03/K-04 sind dokumentiert, nicht gefixt** — die harte Grenze von PLAT-110 (keine
> Live-Security-Änderung) ist gewahrt; das Fixen ist je Folge-Spec.

## Schärfe-Zustand abfragen

Welcher `maschinell`-Eintrag aktuell scharf bzw. im Warn-Modus läuft, druckt der Runner aus
dem echten Check-Register:

```bash
python3 /opt/infrastructure/environment_a/scripts/canon/run_canon_checks.py --report
```

Dies ist die **einzige Wahrheit** über den Schärfe-Zustand. Scharfschalten eines Checks ist
je ein **bewusster Schritt** der zugehörigen Folge-Spec (Programm „Fundament-Härtung",
PLAT-110 Programm-Fahrplan), nie eine stille Markdown-Änderung hier.
