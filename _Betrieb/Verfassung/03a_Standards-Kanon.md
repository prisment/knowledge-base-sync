---
typ: verfassung
titel: "Standards-Kanon (lebendes Register zu 03 Code-Standards)"
stand: 2026-06-23
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

Das Register wächst aus zwei Quellen — „nur nach oben, über Phase 9":

- **Rückwärts-Ernte:** ein kalter Audit/eine Sondierung erntet bestehende belegte Mängel als
  Kandidaten (so entstanden K-01…K-08 aus PLAT-109). Pflicht-Ausgabe-Sektion `## Kanon-Saat`
  im Sondierungs-Template.
- **Vorwärts-Ernte (laufende Zyklen):** Deckt **irgendein** Zyklus künftig einen fehlenden
  oder zu schwachen Standard auf — per Audit/Sondierung, per belegtem Test-Abnahme-Defekt
  (Verfassung 08) oder beim Akt-3-Abschluss einer normalen Spec — ist die **Triage in dieses
  Register Pflicht**: aufgenommen als `K-NN` **oder** mit Grund verworfen, **kein stilles
  Übergehen**. Maßstab „Standard-würdig": der Befund ist *wiederholbar*.

Die verbindliche Regel dazu lebt in **Verfassung 00**, Abschnitt „Vorwärts-Ernte in den
Standards-Kanon" (SSOT — hier nur der Verweis). *(Schleife verankert mit PLAT-112.)*

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
| K-09 | Auslieferung | Worker-gebaute Windows-Ziel-`.ps1` werden **UTF-8-mit-BOM** kodiert und setzen **`$ErrorActionPreference='Continue'`** (PS 5.1 liest BOM-loses UTF-8 als ANSI → Parse-Fehler; `EAP='Stop'` macht git-stderr + `Write-Error` terminierend → `Write-Error→exit N`-Muster kollabiert auf Exit 1) | PLAT-111 Fix-Loop, 2 Windows-only-Defekte in der Architekt-Windows-Abnahme; nur durch Windows-Review-Tor fangbar (Worker hat kein pwsh) | Tor (Architekt-Windows-Review-Tor; git-land.ps1 regression-guarded via `_Betrieb/Skripte/architekt/tests/test_git_land_encoding.sh`) |

> **K-04** ist der einzige Eintrag mit einem heute real laufenden Check — im **Warn-Modus**,
> nicht scharf (PLAT-110 B4, `check_container_haertung.py`, `kanon_ref: K-04`). Der genaue
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
