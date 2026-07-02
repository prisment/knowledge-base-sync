---
typ: verfassung
titel: "Test-Abnahme-Routine — das Tor vor dem Kunden"
stand: 2026-06-24
aenderung: "nur nach oben, nur durch bewusste Freigabe"
quelle: "[[PLAT-090]], [[PRIS-114]]"
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

**WIP (max. 2 cv-Spuren):** höchstens zwei kundensichtbare Bau-Spuren laufen parallel, eine dritte
wartet. **Tor-Gesundheits-Regel:** die Umgehungsquote jedes Tors wird quartalsweise gemessen
(`tor_gesundheit.py`); überschreitet sie **>20 %**, wird das Tor redesignt/abgeschafft.

## Vor-Promote-Stop-Tor (PLAT-144, in-session Erzwingung)

**Schichten-Modell:** (1) **Stop-Tor (in-session)** — eine Session mit kundensichtbarem (`cv`)
Build kann nicht als erledigt enden ohne kalten Pruefer-Pass (`pruefer_pass:gruen`) ODER
expliziten lauten Skip im Run-Log; greift VOR dem Promote. (2) **Promote-Tor (Backstop)** —
`promote_image.sh` verweigert `:latest`-Flip ohne beides. (3) **SessionStart-Erinnerung** — armed
cv-`:test`-Zustand sichtbar beim nächsten Start. **Cv-Ausloeser (PLAT-165: nur Kandidat-Build
armt):** der Stop-Hook erkennt `build_image.sh --kandidat` **und** ein cv-Image (SSOT
`_Betrieb/Skripte/pruefer-gate/cv_images.json`); ein gewöhnlicher Iterations-Build im Worktree
armt **nicht**. **Disarm:** `pruefer_stamp.py <img> <rev> gruen --report <pfad>` ODER `... skip
--grund '<text>'`. **Fail-open:** interne Gate-Fehler geben `allow`, schreiben aber eine Zeile in
`Prisment/Systemzustand/Test/pruefer-gate.log`.

## Maschinelles Tor (nicht disziplinabhaengig)

Das Tor wird **erzwungen**, nicht der Disziplin ueberlassen: ein **Promote-Hook** verweigert
`:latest`-Promote eines `:test`-Images, solange das Run-Log keinen gruenen Lauf mit **genau dem
`image.revision`-Commit-SHA** des Builds traegt. Umgehung nur **laut + auditierbar**.
**Pruefer-Tor (parallel, PLAT-142 + PLAT-144):** Pflicht, wenn Feature-/Spec-Frontmatter
`risikoklasse: kritisch` **oder** `kundensichtbar: true` traegt — **oder** das Image in der
cv-Allowlist steht (`_Betrieb/Skripte/pruefer-gate/cv_images.json`). Run-Log-Feld
**`pruefer_pass`** (`gruen` / `skip:<grund>`); Umgehung nur laut+auditierbar via
`PROMOTE_SKIP_PRUEFER=1 + PROMOTE_SKIP_PRUEFER_GRUND`. Unterhalb der Flags/cv-Allowlist bleibt
der Pruefer-Pass ehrlich **Architekt-Ermessen** (Disziplin, kein Tor).

## Verfahren & Artefakte

- **Ausführung:** Skill [`live-abnahme`](../../.claude/skills/live-abnahme/SKILL.md).
- **Laufzeit-Test-Ausführung → Worker/Prüfer (Rolle B, PLAT-157):** läuft im Worker-/Prüfer-Subagenten,
  **nicht** im Architekten-Kontext; Architekt löst aus, liest Artefakte, urteilt Soll-Treue (Gate B).
- **Test-Logbuch:** `Prisment/Systemzustand/Test/` — AK-Katalog + `run-log.jsonl`.
- **Herkunft:** [[PLAT-090]], Pilot [[PRIS-097]].
