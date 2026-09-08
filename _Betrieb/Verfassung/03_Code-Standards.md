---
typ: verfassung
titel: "Code-Standards"
stand: 2026-07-02
aenderung: "nur nach oben, nur durch bewusste Freigabe"
---

# 03 — Code-Standards

## Code gehört NICHT in die Knowledge-Base

Die Knowledge-Base dokumentiert ÜBER Code, sie enthält ihn nicht. Echter Code (Skripte, App-Code, Workflow-Definitionen) lebt im separaten Code-Repo. Grund: Code an zwei Orten = zwei Versionen = zweite Wahrheit = Bruch des Single-Source-of-Truth-Prinzips.

Konsequenz: `.py`-Dateien, n8n-Workflow-JSONs, Shell-Skripte und Ähnliches werden bei der Migration NICHT in die neue Struktur einsortiert, sondern als „gehört ins Code-Repo" markiert und ausgesondert.

## Single Source of Truth

Jede Information existiert genau einmal. Ansichten (SVG-Übersichten, Zusammenfassungen) werden aus der einen Quelle ABGELEITET, nie als zweite Quelle gepflegt. Eine veraltete Doku ist schlimmer als keine, weil man ihr vertraut.

## Standards-Platzhalter (wachsen mit der Arbeit)

Konkrete Code-Vorgaben (Test-Strategie, CI/CD, Datentrennung, Datenintegrität) werden aus den realen Zyklen heraus eingearbeitet. Bis dahin gilt diese Datei als Platzhalter, der nach jedem relevanten Zyklus über Phase 9 wächst (nie nach unten).

### → Lebendes Register: [`03a_Standards-Kanon.md`](03a_Standards-Kanon.md) (PLAT-110)

Der konkrete Träger dieses Platzhalters ist die Schwester-Datei **[`03a_Standards-Kanon.md`](03a_Standards-Kanon.md)** — ein lebendes Register belegter Standards (Format je Eintrag: `ID · Schicht · Regel · Herkunft · Enforcement-Modus`). Sie wächst „über Phase 9, nur nach oben" durch Rückwärts-Ernte aus den Zyklen; sie ist seit der Produkt-Ausgliederung (H2, TOOL-SCHNITT) ein reines Wissens-Register ohne maschinelles Enforcement (der frühere Check-Runner lag im Produkt-Repo und ist mit ihm gegangen). Die selten geänderte Regel-Kerndatei (diese hier) bleibt dabei stabil. Etabliert mit PLAT-110 (Fundament-Härtung 1/6).

## Destruktive Operationen: kein Error-Suppress (PLAT-085, 2026-06-19)

Destruktive, schwer rückrollbare Operationen (Repo-/Branch-Löschung, `DELETE`
gegen die Gitea-API, `rm -rf`, DB-DROP, Force-Push) werden **nie** mit
`2>/dev/null`, `|| true` oder `|| :` maskiert. Der unterdrückte Fehler verbarg
am 2026-06-12 eine versehentliche Repo-Löschung ganze 33 Minuten lang (die Session
hielt ihren `curl -X DELETE …/repos/…` für einen Token-Cleanup und sah keinen
Fehler) — der Schaden blieb danach ~1 Woche unbemerkt. Regel: bei destruktiven
Befehlen Exit-Code **prüfen und sichtbar machen**, nicht verschlucken. Lesende/
idempotente Befehle dürfen weiter `2>/dev/null` nutzen.


## Test-/Dev-/Staging-Daten — Pseudonymisierungs-Werkzeug-Pflicht (PLAT-046, 2026-05-28)

Test-/Dev-/Staging-Bühnen tragen nie un-pseudonymisierte Echtdaten. Wenn eine Bühne aus Live-Daten gefüttert wird, ist **Skill `test-data-pseudonymize`** (`.claude/skills/test-data-pseudonymize/SKILL.md`) Pflicht-Werkzeug — er trägt das Verfahren, die zweistufigen PII-Audit-Pattern und die Self-Test-Akzeptanz.

**Mapping-Tabelle ist Architekten-Hand-Schlüssel** (Pseudonym-Wahl + Speicherort). Sie lebt **außerhalb des Repos** unter `/home/claude-deploy/<bühnen-name>-pseudonymization-mapping.json` mit perms `0600`. Auch außerhalb des knowledge-base-Vaults, sonst Klon-Risiko über Vault-Sync. Eine Beispiel-Datei mit Dummy-Werten (`*-pseudonymization-mapping.example.json`) darf im Repo liegen.

**Pattern-Vorlagen** liegen als Vorbild im Skill: Pseudonymisierungs-Skript (Mapping-basiert, längste-zuerst, idempotent + Selbsttest, neutral benannt nach Header-Trap-Lehre), zweistufiges PII-Audit (bekannte Tokens + breite Pattern-Klassen), Hochfahr-Pfad mit Fail-closed-Marker-Check (`-- PSEUDONYMIZED <Datum>`).

Verfassungs-Anker für die Risikoklasse-Wirkung: `04_Sicherheits-Prinzipien.md`, Abschnitt „Echtdaten-Risikoklasse-Kopplung".

## Dependency-Pinning & Update-Pfad

- **npm:** `package.json` mit `==`/exakten Versionen für direkte Deps; Lockfile zwingend committed. `overrides`-Feld ist legitimes Werkzeug bei transitiv-eingebrannten CVEs (postcss-via-next), aber mit Kommentar warum.
- **pip:** `requirements.txt` mit `==`-Pinning; Floor-Constraints (`>=`) nur für CVE-Schutz (`langsmith>=0.8.0`), nicht für API-Komfort. **Seit PLAT-169 zusätzlich pro pip-Service ein kompiliertes `requirements.lock`** (uv, exakt gepinnt, Header mit Regenerier-Befehl + `source-sha256`): die `requirements.txt` bleibt SSOT der DIREKTEN Deps (Intent), das Lock pinnt die vollständige transitive Menge und ist die einzige Install-Quelle der Dockerfiles (`pip install --no-deps -r requirements.lock` — fail-closed: eine unvollständige Lock-Closure ist ein Build-Fehler, kein stilles Nach-Resolven). **Dep-Bump-Verfahren:** `requirements.txt` ändern → Lock-Datei mit dem im Service-Repo hinterlegten Regenerier-Befehl (steht im Lock-Header) neu erzeugen → beides im selben Commit. Ein Lock, das nicht zur `requirements.txt` passt, ist ein Build-Fehler — das trägt der fail-closed `--no-deps`-Install oben, unabhängig davon, ob ein CI-Gate danebensteht.
- **Wer bumped:** Diun watcht Container-Images, nicht Lockfiles. Bumps erfolgen reaktiv (roter Build/Audit), proaktiv im jeweiligen Zyklus, und **maschinell via Renovate-CLI** (PLAT-021, täglicher Cron: `run-renovate.sh` 01:30 + `renovate_liveness_watchdog.sh` 02:30 + `render-renovate-status.sh` 04:30 — PR-los, passend zum No-PR-Workflow). *(Der frühere Satz „Renovate o.Ä. bewusst verworfen" ist seit PLAT-021 überholt — korrigiert 2026-07-04, PLAT-174 B3.)*
- **Versionswahl (Neuwahl) & EOL — Kanon K-17 (PLAT-171):** Beim **Neu-Schreiben** einer Versionsangabe (FROM-Basis-Image, neue Dependency, Scaffold) wird die aktuelle sichere Version **live geprüft** (`npm view <pkg> version` / PyPI / endoflife.date / Registry) — nie aus Modell-Trainingsdaten übernommen; Beleg als eine Zeile im Commit/Report. Für den **Bestand** wacht `scripts/canon/check_eol.py` (Canon-Runner, Warn-Modus) über EOL von Basis-Images + Frameworks — die CVE-Gates oben (npm audit/pip-audit) sehen EOL nicht: eine abgekündigte Version ohne gelisteten CVE bleibt dort grün (Befund 2026-07-03: `node:20.20.2` in beiden Next-Dockerfiles, Node 20 EOL seit 2026-04-30, CI grün).
