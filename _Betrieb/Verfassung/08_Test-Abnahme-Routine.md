---
typ: verfassung
titel: "Test-Abnahme-Routine — das Tor vor dem Kunden"
stand: 2026-07-13
aenderung: "nur nach oben, nur durch bewusste Freigabe"
quelle: "[[PLAT-090]], [[PRIS-114]], [[PLAT-178]]"
---

# 08 — Test-Abnahme-Routine

## Grundsatz

**Nichts erreicht den Kunden, das nicht vorher auf dev/test mit belegtem Nachweis
abgenommen wurde.** Der Kundentermin ist eine Vorführung funktionierender Software, nie
ein Test. Gilt für **jedes** Feature und **jede** Änderung — UI, Backend, Pipeline, Agent,
Config. (Herkunft: drei misslungene Live-Tests vor dem ersten Kunden; [[PLAT-090]].)

## Definition of Done (erweitert)

Ein Feature ist nicht fertig, wenn der Code kompiliert oder der Deploy durchläuft. Es ist
fertig, wenn der **vollständige nutzersichtbare Fluss einmal grün abgenommen** wurde — mit
Beleg, im Run-Log geloggt (`Prisment/Systemzustand/Test/run-log.jsonl`).

## Kernprinzip — zwei Belege pro Kriterium

Jedes Akzeptanzkriterium braucht **UI-Sicht (bzw. „Deploy ok") UND einen
Hintergrund-Wahrheits-Check.** UI-grün allein zählt nie — dort verstecken sich die stillen
Fehler (Pilot [[PRIS-097]]: „Post nicht gefunden" war ein maskierter HTTP-500; ein Post ging
live, aber ohne Hashtags — beides UI-grün).

## Drittes Prinzip — Vollständigkeit & Frische (PRIS-114)

Zwei Belege je AK genügen **nicht**, wenn die *erwartete Menge* nicht gegengezählt ist und die
UI nicht *nach Reload* gegen das Backend abgeglichen wurde. Abwesenheits- und Timing-Fehler
verstecken sich genau dort. Herkunft: Agent-Lauf 2026-06-24 übersah die nie generierte
FB-Cross-Post-Hälfte (prüfte nur Vorhandenes) + ein Zweit-Thema, das erst nach Reload erschien.

- **Vollständigkeit:** Grün erfordert einen **Existenz-/Count-Assert gegen ein Soll-Inventar**,
  nicht eine Stichprobe. „Ein Exemplar gesehen" ≠ „alle da". (Werkzeug: `XP-ASSERT`,
  `scripts/test/xp_assert.sh`.)
- **Frische:** Bei Listen-/Sichtbarkeits-Aussagen ist die UI **nach Reload** gegen das Backend
  abzugleichen (`RR-STEP`). „X fehlt" gilt nie aus der UI allein.
- **Soll-getrieben:** Eingang jeder Agent-Abnahme ist das Feature-Soll-Inventar
  (`Prisment/Systemzustand/Test/Agent-Test_A-Feature-Inventar.md`); ein Flow ohne Soll-Eintrag
  ist „nicht abgedeckt", nie grün. Strategie-Satz: `Agent-Test_A..D` ebd.
- **Helpdesk-Wissen reist mit (PRIS-143):** Applikabilitäts-Heuristik — neue/geänderte
  kundensichtbare Begriffe, Preise, Abläufe oder Screens ⇒ `helpdesk_knowledge/*.md` prüfen und
  ggf. im selben Bündel nachziehen (der Nightly-Index-Wächter zieht den Index automatisch nach,
  nicht den Korpus-Inhalt selbst).

## Das Prüf-Instrument passt sich dem Feature-Typ an

- **PWA-E2E (Klicks/Flows/DOM/Network)** → seit PRIS-114 **Playwright im autonomen
  Ubuntu-Lauf** (headless, dev-CF-Bypass). Der **Worker** (PLAT-130) fährt die Flows; der
  **Architekt** prüft **Soll-Treue** und sichtet die Beweise; **Real-Gerät/Mensch-Augen am
  Handy** ist Mensch-Tor. Selbsttest verdrahtet (PLAT-131/B6): Worker fährt `pw_smoke.mjs`,
  hartes Worker-Tor (Baseline-Delta = 0 Console-Errors, 0 App-4xx/5xx, DOM-Asserts grün vor
  `status: review`). Nicht deterministisch Belegbares bleibt **Mensch-Augen-AK** (Skill
  `live-abnahme` + `scripts/test/PW-SMOKE.md`). **Anti-false-green:** „Playwright grün" ≠
  „visuell abgenommen".
- **Nicht-UI** (Backend/Pipeline/Agent/Config) → Backend-Belege, z. B. bei einer
  Agent-Pipeline **DB-Row** (psql) + **Trace/Log ohne Error** + **API-Antwort mit erwartetem
  Inhalt**, statt „Pipeline läuft durch".

Das Instrument wechselt, die AK-Disziplin und das Logbuch bleiben.

## Test-Stufen — LLM-Budget-Disziplin auf der dev-Bühne (PLAT-178)

Echte LLM-Calls sind der teuerste Betriebsstoff der dev-Bühne (Kosten-Forensik
11.–13.07.2026: ~90 % von 92 € Testkosten waren wiederholte Voll-Plan-Generierungen als
bloße Kulisse für reine Funktions-Checks — kein einziger Lauf hatte Textqualität als
Prüfziel). Darum benennt jeder Testlauf **vor dem Start sein Prüfziel** — das Prüfziel
bestimmt die Stufe:

| Stufe | Prüfziel | Instrument | echte LLM-Calls |
|---|---|---|---|
| **0 — Mock** | Logik, Zustand, Format, Routing, Rendering | gebackene pytest-Suite (gemockt), `cd-docker test-exec` | keine |
| **1 — eine echte Runde** | Verhalten gegen reale LLM-Ausgabe, Pipeline-Integration end-to-end | Revisions-Schleife gekappt (`/tmp/smoke_mode`-Sentinel → `_vc_max=1`); **Suite-Default seit PLAT-178** | reduziert (1 Revision/Post statt bis zu 5) |
| **2 — volle Schleife** | **Textqualität ist selbst das Prüfziel** (Markenstimme, Abnahme) | `stufe2`-Fixture pro Test (Grund im Docstring Pflicht) bzw. bewusster Voll-Plan-Lauf via **`/run` mit `stufe: 2`** (PLAT-181; nur auf der Test-Bühne wirksam, lauter Log-Beleg `[stufen] STUFE 2 EXPLIZIT ANGEFORDERT`). Ein liegender Sentinel `/tmp/smoke_mode` schlägt auch eine Stufe-2-Anforderung (Smoke-Gate gewinnt immer — die `[stufen]`-Log-Zeile zeigt die Ursache) | voll |

Drei Regeln:

1. **Fix-Iterationen steigen nie über Stufe 1** — und fahren das minimale betroffene
   Einzel-Szenario, nie den Voll-Plan. Stochastisches Verhalten → N Wiederholungen des
   Einzel-Szenarios, nicht N Voll-Läufe.
2. **Stufe 2 braucht einen ausgesprochenen Grund** („Prüfziel ist Textqualität, weil …").
   Ohne Grund gilt sie als versehentliche Brechstange. Ein Voll-Plan-Lauf ist ein bewusster
   Schluss-Schritt am Bogen-Ende (Kandidat-Abnahme), maximal einer pro Fix-Bogen.
3. **Der kalte Prüfer unterliegt denselben Stufen** — adversariale Szenarien brauchen selten
   Stufe 2; sein Report benennt die gefahrene Stufe.

**Abgrenzung (kein stiller cv-Downgrade):** Die Stufen entlasten den **Iterations-Weg**,
nicht die Tore. Ein kundensichtbarer Promote braucht weiterhin mindestens **eine echte
Runde (Stufe 1)** mit Zwei-Belegen + Vollständigkeit/Frische (PRIS-114) und das
Vor-Promote-Stop-Tor unverändert — Stufe 0 allein promotet nie.

**Mechanik vs. Disziplin (ehrlich):** Mechanisiert sind die **Revisions-Kappe als
Suite-Default** (SSOT: `langgraph/content/tests/conftest.py`, env_a — autouse-Fixture
`stufe1_default` + `stufe2`-Fixture — und `nodes.py`, `SMOKE_SENTINEL`) und seit
PLAT-179 die **Einzel-Szenario-Selektion**: `cd-docker test-exec <svc>
--expect-revision <sha> --select <token>` (validiertes `-k`-Token, alle 6
Test-Services; lauter TEIL-LAUF-Banner — ein Selektions-Ergebnis ist nie ein
Voll-Suite-/Abnahme-Beleg; Sicherheitsrationale:
`Plattform/Systemzustand/Sicherheit/cd-docker-test-exec-verb.md`). Seit **PLAT-181**
zusätzlich die **non-pytest-Default-Kappung**: der content-Test-Container läuft mit
`VC_STUFE_DEFAULT=1` (docker-compose.test.yml) — jeder Lauf über `/run` (Playwright,
`test-content-run`, curl) ist default auf `_vc_max=1` gekappt; die volle Schleife gibt
es nur per explizitem `stufe: 2` im `/run`-Request (Live bleibt unberührt: kein Env
gesetzt, Verhalten unverändert). Disziplin bleiben: die Stufen-Wahl selbst und die
Grund-Nennung (Docstring-Konvention bzw. Grund im Beweis-Bericht) — beide Regeln stehen
seit PLAT-181 auch in `.claude/agents/worker.md`/`pruefer.md`.

**Geteilter Sentinel (PLAT-063-Erbe):** `/tmp/smoke_mode` ist zugleich das
Live-Smoke-Gate aus PLAT-063 (`deploy_smoke.sh` setzt/räumt ihn via docker exec auf den
**Live**-Containern). Ein dort liegen gebliebener Rest kappt echte Kunden-Posts still auf
1 Revision — geerbtes PLAT-063-Risiko, kein PLAT-178-Delta, aber hier offengelegt. Auf dem
**Test**-Container failt ein Crash-Rest für Funktions-Prüfziele in die billige Richtung
(Stufe 1); für ein **Qualitäts**-Prüfziel wäre er eine stille Kappung — seit PLAT-181
braucht Stufe 2 keinen Räum-Vorschritt mehr (Schalter ist `stufe: 2` im Request), aber
ein Sentinel-Rest gewinnt weiterhin gegen jede Anforderung; die `[stufen]`-Log-Zeile
macht das laut sichtbar.

## Zwei Bühnen

> **Bühnen-Topologie:** siehe `Plattform/Systemzustand/Infrastruktur/buehnen-bild.md` (SSOT)

- **dev (isoliert, test_db):** der schnelle, **autonome** Lauf — alles ohne echtes Außen-System.
- **Live-Wegwerf:** nur für **irreversible Außen-Schritte** (echter Publish/Versand, braucht echte
  Tokens + Wegwerf-Konto), die dev nicht kann.

## Mensch ↔ autonom

- **Autonom:** Fluss-Validierung, Defekt-Erfassung, Hintergrund-Belege, Regressions-Läufe,
  dev komplett.
- **Mensch:** **irreversible Außen-Aktionen nie autonom** (echter Publish/Versand =
  Mensch-Klick), Output-Qualität, der finale Echt-Handy-Pass, Go/No-Go.

## Der Takt (Architekt ↔ Worker)

Architekt findet → **präziser Defekt-Bericht** (Trace, Konsole, Network, exakte Repro) →
Worker fixt **+ legt einen Regressions-Check an** → `:test` neu → Architekt re-testet nur die
gefallenen AKs + Smoke. Minuten/Runde. **Jeder gefundene Bug wird ein dauerhaftes AK.**

**Deckt ein Defekt einen *allgemeinen* Standard-Mangel auf** (nicht nur einen lokalen Bug,
sondern etwas Wiederholbares — es könnte in einem anderen Dienst/Zyklus erneut auftreten), wird
er zusätzlich in den **Standards-Kanon** vorwärts-geerntet: Pflicht-Triage `aufgenommen K-NN /
verworfen-mit-Grund` in [`03a_Standards-Kanon.md`](03a_Standards-Kanon.md). Die Regel lebt in
Verfassung 00, Abschnitt „Vorwärts-Ernte in den Standards-Kanon" (hier nur der Pointer — kein
Duplikat). **Auch ein bestätigter Befund des kalten Prüfers** (s.u.) ist eine **zusätzliche
Quelle** dieser Schleife — er läuft durch dieselbe Triage `aufgenommen K-NN / verworfen-mit-Grund`,
**kein** zweites Register, **kein** neues 03a-Feld.

## Kalter Prüfer-Pass (adversariale Laufzeit-Verifikation außerhalb der AKs)

Nach dem Worker-Selbstbeweis (dev-Deploy) ruft der Architekt risiko-gestaffelt den **kalten
Prüfer** (`pruefer`, Opus/high) auf — er teilt den Denkrahmen des Bauenden **nicht** und
**beschießt das laufende System**, statt es zu bestätigen, und findet so blinde Flecken.

- **Kein Doppeltest:** der Worker fährt AK-Flows bestätigend (Happy-Path, hartes Tor); der Prüfer
  re-fährt sie **nie**, sondern erfindet **neue** Szenarien außerhalb der AKs (Grenzwert,
  Fehler-State, Doppelklick, Idempotenz, Rechte-Grenze). **Multi-instrument:** für Nicht-UI prüft
  er Hintergrund-Wahrheit (psql-Row, API/HTTP, Trace).
- **Konfidenz-klassifiziert:** jeder Fund `sicherer-bug`/`fragwürdig`/`scope-frage`; gegen ein
  sauberes Feature null `sicherer-bug`. **Prisment-Linse:** liest `Urteil`-Einträge des
  Standards-Kanons (03a), leitet Angriffs-Fragen ab — schreibt selbst nicht in 03a.
- **Tenant-Zustands-Vorbedingung ist Pflicht-Dispatch-Angabe (PLAT-186):** jeder Prüfer-Dispatch
  benennt den Tenant-Zustand der dev-Bühne (Onboarding-Stand — für Faden-/Steady-State-Prüfungen
  **past-onboarding** Pflicht: `onboarded_at NOT NULL`, `current_phase` ≠ `onboarding_*`; Meta
  verbunden; ≥1 FB-Post + ≥1 Karussell-Post). Fehlt sie, maskiert ein frischer Onboarding-Tenant die
  Faden-Flächen und der Prüfer meldet „sauber", ohne das Feature je erreicht zu haben (Herkunft:
  22.07.2026, zwei kalte Pässe gegen frischen Onboarding-Tenant). SSOT der Regel: `.claude/agents/pruefer.md`.
- **Write-confined:** nur **Report, keine Commits** (`.claude/hooks/pruefer-confinement.py`).
  **Salvage-Report (PLAT-161), fail-closed:** append-only auf stabilem Pfad, endet exakt auf
  `STATUS: ABGESCHLOSSEN` — `pruefer_stamp.py gruen` verlangt genau diese Zeile, ein Teil-Report
  disarmt das Tor nie; stirbt der Prüfer mitten im Lauf, hängt Re-Dispatch verlustfrei an, oder
  lauter Skip (`... skip --grund`).
- **Rolle B:** der Prüfer führt Tests aus, liefert den Report; der Architekt **löst** aus,
  **liest**, **urteilt**, **löst** den `gruen`-Stamp — klickt nie selbst; Worker-Fix-Loop nur bei
  bestätigten Defekten. **Hybrid-Tor:** Pflicht für kundensichtbare UI-Flows + `kritisch`
  (maschinell erzwungen); darunter Architekt-Ermessen — Opus-Kosten gedeckelt.

### Oracle-Typ-Routing — welches Instrument je AK (PLAT-160)

Jedes AK trägt einen **Oracle-Typ**; er bestimmt, *wer* es prüfen kann:

- **Maschinen-Oracle** (DB-Row, n8n-Execution `success`, Network ohne 5xx, Trace, DOM-Assertion,
  Contract-Grep): läuft durch den **vollen Prüfer-/Abnahme-Rhythmus oben — unverändert, vital.**
  Hier wird nichts gespart.
- **Nur-Mensch-Oracle** (visuelle Treue gg. Design-Frames, echter-Finger-Touch, ästhetisches
  Urteil): die Maschinerie kann es **strukturell nicht** prüfen (kein Visual-Diff existiert; Touch
  ist Mensch-Augen-AK). Solche AK werden **gebündelt ans Bogen-Ende** gezogen und in **einem**
  Mensch-Tor (wenige Klicks, schneller Vorschau-Link) abgenommen — **kein** Prüfer-/CI-Rigor wird
  darauf verschwendet (mehr Maschinen-Rigor dort = null Ertrag, nur Loop-Latenz; PRIS-121-Lehre).
  Der Worker sammelt sie als `status: review`-Liste, statt Eigen-Runden zu verbrennen.

**Abgrenzung (kein stiller cv-Promote):** „kein Maschinen-Rigor auf Nur-Mensch-Oracle" lockert
**nicht** das Vor-Promote-Stop-Tor. Ein kundensichtbarer Build braucht weiterhin Prüfer-grün auf
seinen Maschinen-Oracle-AK **oder** einen **lauten Skip** (`pruefer_stamp.py skip --grund`) **plus**
den Promote-Gate-SHA-Match (s. „Maschinelles Tor" / PLAT-144). Das Routing entlastet nur das
einzelne Nur-Mensch-AK, nicht das Tor.

## Kadenz

- **Pro Feature** vor Promote — Pflicht.
- **Voller Vor-Kunden-Pass** (Echt-Handy, Mensch) vor jedem Termin.
- Regressions-Suite (AK-Katalog) wächst mit jedem Bug. Zeitgesteuerter Dauer-Smoke erst bei
  belegtem Drift.

## Promote-Topologie — merge-then-prove (PLAT-165)

**Leitbild:** Der Worker-Selbsttest (Zwei-Belege auf dev) bleibt VOR dem Merge — nur die kalte
institutionelle Prüfung (Prüfer-Pass) und der Promote wandern dahinter. Merge nach `main` **ist**
die Kandidatur: der Abnahme-Kandidat wird per `build_image.sh --kandidat` aus dem main-Checkout auf
`origin/main`-Stand gebaut (main-HEAD), nicht aus einem Worktree-/Sibling-SHA. **Akzeptierter
Trade-off („Kollateral-Promote", gewollt):** der Kandidat trägt alle gemergten Sibling-Deltas mit
— der kalte Prüfer prüft den integrierten Stand, der live geht (`promote_image.sh` zeigt das
Delta laut an). Fällt er NACH dem Merge rot aus, ist der Rückweg der **Revert-/Fix-forward-Commit
auf main** — billig, denn `main` ≠ `live`; `promote_image.sh` verlangt einen main-SHA als
Descendant der Live-Revision, nicht zwingend main-HEAD (Ancestor-/Revert-Guards im Skript-Header).

**WIP (max. 3 cv-Spuren — PLAT-165 → PLAT-189, Korbinian-GO 2026-07-28):** höchstens drei
kundensichtbare Bau-Spuren parallel (ab Bühnen-Pool live; davor zwei) — nur bei service-/
dateidisjunkten Scopes; gekoppelte Arbeit bleibt seriell, auch innerhalb einer Mission;
End-Abnahmen am Sync-Punkt bündeln, ein main-Kandidat trägt alle Spuren. Bündeln heißt nie
blockierend auf Korbinian warten — offene Mensch-Abnahmen parken die Spur, nie den CoS.
**Tor-Gesundheits-Regel:** die Umgehungsquote jedes Tors wird quartalsweise gemessen
(`tor_gesundheit.py`); überschreitet sie **>20 %**, wird das Tor redesignt/abgeschafft.

**Bühnen-Belegung (PLAT-164 → PLAT-176 → PLAT-189 Pool):** Es gibt **drei** Test-Bühnen (Slots).
Ein Zyklus **least eine ganze Bühne** statt einzelner Images:
`buehne_lock.sh slot-acquire <zweck>` druckt `SLOT=<N>` + einen **Token**; `slot-refresh`/
`slot-release` verlangen genau diesen Token. Ist kein Slot frei, gibt es **laute Ablehnung mit
Halter-Anzeige** (exit 1) — kein stilles Warten. Der Token als Halter-Identität ersetzt die alte
Session-ID-Identität, die zwischen Geschwister-Subagenten derselben Session wirkungslos war.

- **Slot 1** = die bisherige Bühne, unverändert: `dev.${DOMAIN}`, `test_db`, `*_test`,
  Image-Tag `:test`. **Kundensichtbare Kandidat-Builds laufen auf Slot 1** — `promote_image.sh`
  liest `:test`, damit ist nur Slot 1 promote-fähig. Das ist gewollt.
- **Slots 2/3** sind additiv und **nicht extern erreichbar** (kein DNS-Record, kein
  `certresolver`, host-lokal): `test_db_<N>`, `*_test_<N>`, Image-Tag `:test-s<N>`,
  Compose-Projekt `environment_a_t<N>`, `restart: "no"` (ein nicht geleaster Slot bleibt
  gestoppt — das hält die Kosten mechanisch, nicht per Doku-Bitte).
- **Per-Slot-Image-Tags** sind der Kern: ohne sie serialisiert der geteilte `:test`-Tag den Pool
  weiter, und der Pool wäre eine Fassade. `build_image.sh --slot N` taggt `:test-s<N>`; ohne
  `--slot` ist das Verhalten byte-gleich zum Bestand.
- Der **per-Image-Lock** (`acquire <image>`) bleibt für Slot 1 bestehen und wird nicht
  abgeräumt, solange er dort der einzige Schutz ist (Ablöse-Anker: der erste Zyklus, der
  nachweislich auf Slot 2 läuft).
- `sudo cd-docker recreate-test --slot N` prüft die Lease des Slots (Bühnen-Guard, Token-Weg).
- **Merge mit `scripts/`-Delta braucht einen Deploy-Schritt (End-Prüfer-Blocker 2026-07-29):**
  anders als bei Image-Deltas (`build_image.sh` → `:test` → `promote_image.sh`) gibt es für
  Änderungen an `scripts/*` am geteilten Checkout `/opt/infrastructure/environment_a` keinen
  eigenen benannten Schritt — nach jedem env_a-Merge mit `scripts/`-Delta gehört
  `git -C /opt/infrastructure/environment_a pull --ff-only` **zum Merge-Schritt selbst**, sonst
  bleibt der Checkout still zurück und Fix-Orakel messen den alten Skript-Stand.

Mechanik-SSOT: `Plattform/Systemzustand/Geteilte-Dienste/test-buehne.md`, Abschnitt
„Bühnen-Pool"; Topologie-SSOT: `Plattform/Systemzustand/Infrastruktur/buehnen-bild.md`.

## Vor-Promote-Stop-Tor (PLAT-144, in-session Erzwingung)

**Schichten-Modell:** (1) **Stop-Tor (in-session)** — eine Session mit kundensichtbarem (`cv`)
Build kann nicht als erledigt enden ohne kalten Pruefer-Pass (`pruefer_pass:gruen`) ODER
expliziten lauten Skip im Run-Log; greift VOR dem Promote. (2) **Promote-Tor (Backstop)** —
`promote_image.sh` verweigert `:latest`-Flip ohne beides. (3) **SessionStart-Erinnerung** — armed
cv-`:test`-Zustand sichtbar beim nächsten Start. **Cv-Ausloeser (PLAT-165: nur Kandidat-Build
armt):** der Stop-Hook erkennt `build_image.sh --kandidat` **und** ein cv-Image (SSOT
`_Betrieb/Skripte/pruefer-gate/cv_images.json`); ein gewöhnlicher Iterations-Build im Worktree
armt **nicht**. **Disarm:** `pruefer_stamp.py <img> <rev> gruen --report <pfad>` ODER `... skip
--grund '<text>'` ODER `... gelb --grund '<text>'`. **Fail-open:** interne Gate-Fehler geben
`allow`, schreiben aber eine Zeile in `Prisment/Systemzustand/Test/pruefer-gate.log`.

## Maschinelles Tor (nicht disziplinabhaengig)

Das Tor wird **erzwungen**, nicht der Disziplin ueberlassen: ein **Promote-Hook** verweigert
`:latest`-Promote eines `:test`-Images, solange das Run-Log keinen gruenen Lauf mit **genau dem
`image.revision`-Commit-SHA** des Builds traegt. Umgehung nur **laut + auditierbar**.
**Pruefer-Tor (parallel, PLAT-142 + PLAT-144):** Pflicht, wenn Feature-/Spec-Frontmatter
`risikoklasse: kritisch` **oder** `kundensichtbar: true` traegt — **oder** das Image in der
cv-Allowlist steht (`_Betrieb/Skripte/pruefer-gate/cv_images.json`). Run-Log-Feld
**`pruefer_pass`** (`gruen` / `skip:<grund>` / `gelb:<grund>`); Umgehung nur laut+auditierbar via
`PROMOTE_SKIP_PRUEFER=1 + PROMOTE_SKIP_PRUEFER_GRUND`. Unterhalb der Flags/cv-Allowlist bleibt
der Pruefer-Pass ehrlich **Architekt-Ermessen** (Disziplin, kein Tor).

**Drei Verdikte, zwei Toren (PRIS-190-Plattform-Befund, 29.07.):** `gruen` und `skip:<grund>`
("Pass nicht anwendbar", bewusster Verzicht) disarmen **beide** Tore (Stop-Tor + dieses
Promote-Prüfer-Tor). `gelb:<grund>` ("Pass **lief**, war nicht grün") disarmt **nur** das
in-session Stop-Tor — das Promote-Prüfer-Tor bleibt für `gelb:` bewusst bewaffnet, damit ein
laut übersprungener, nicht-grüner Befund nie unbemerkt Kunden-fähig wird. Details + Tabelle:
`Plattform/Systemzustand/Test/pruefer-gate.md`.

## Verfahren & Artefakte

- **Ausführung:** Skill [`live-abnahme`](../../.claude/skills/live-abnahme/SKILL.md).
- **Laufzeit-Test-Ausführung → Worker/Prüfer (Rolle B, PLAT-157):** läuft im Worker-/Prüfer-Subagenten,
  **nicht** im Architekten-Kontext; Architekt löst aus, liest Artefakte, urteilt Soll-Treue (Gate B).
- **Test-Logbuch:** `Prisment/Systemzustand/Test/` — AK-Katalog + `run-log.jsonl`.
- **Herkunft:** [[PLAT-090]], Pilot [[PRIS-097]].
