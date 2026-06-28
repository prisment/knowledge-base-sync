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

- **PWA-E2E (Klicks/Flows/DOM/Network)** → seit PRIS-114, weil deterministisch herstellbar,
  **Playwright im autonomen Ubuntu-Lauf** (headless, dev-CF-Bypass), nicht mehr Architekt-Chrome.
  Rollen: der **Worker** (Subagent am Host, PLAT-130 — Chrome-in-Windows ist entfallen) fährt die Flows
  und produziert die Artefakte (Screenshots/Traces/Asserts);
  der **Architekt** prüft **Soll-Treue** (Spec+AKs, Review vor dem Test) und **sichtet die Beweise**;
  **Real-Gerät / Mensch-Augen am Handy** ist Mensch-Tor (echtes Rendering, finale Abnahme).
  Die Selbsttest-Rolle ist **verdrahtet** (PLAT-131/B6): der Worker fährt `pw_smoke.mjs`
  (`scripts/test/`, host-direkt) und das **hartes Worker-Tor** gilt — die deterministischen Checks
  (Baseline-Delta = 0 Console-Errors, 0 App-4xx/5xx, DOM-Asserts) müssen grün sein vor `status: review`.
  Was er nicht deterministisch belegen kann, bleibt expliziter **Mensch-Augen-AK** (`status: review`) —
  nie still als „grün" geführt. Bedienung: Skill `live-abnahme` + `scripts/test/PW-SMOKE.md`.
  **Anti-false-green:** „Playwright grün" ≠ „visuell abgenommen" — der grüne Lauf deckt Verhalten/DOM/
  Flows, nie das ästhetische Urteil, und ersetzt das visuelle Mensch-Tor nicht.
- **Nicht-UI** (Backend / Pipeline / Agent / Config) → Backend-Belege.
  *Durchgearbeitetes Beispiel — eine Agent-Pipeline:* das AK ist nicht „Pipeline läuft durch“,
  sondern **die erwartete DB-Row existiert** (psql-Query) + **Trace/Log ohne Error** + **API-Antwort mit
  erwartetem Inhalt**. „Deploy ok“ ist hier so wenig hinreichend wie
  „UI grün“ bei einem Klick-Feature.

Das Instrument wechselt, die AK-Disziplin und das Logbuch bleiben.

## Zwei Bühnen

> **Bühnen-Topologie + dev/test-Namensregel:** siehe `Plattform/Systemzustand/Infrastruktur/buehnen-bild.md` (SSOT)

- **dev (isoliert, test_db):** der schnelle, **autonome** Lauf — alles, was kein echtes
  Außen-System braucht. Frisch pro Lauf.
- **Live-Wegwerf:** nur für **irreversible Außen-Schritte** (echter Publish/Versand), die
  dev nicht kann (braucht echte Tokens + Wegwerf-Konto).

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

Nach dem Worker-Selbstbeweis — und **nachdem der Worker nach dev deployt hat** — ruft der
Architekt risiko-gestaffelt den **kalten Prüfer** (`pruefer`, Opus/high,
[`.claude/agents/pruefer.md`](../../.claude/agents/pruefer.md)) auf. Er teilt den Denkrahmen
des Bauenden **nicht** (Kälte) und **beschießt das laufende System**, statt es zu bestätigen —
er findet die blinden Flecken, die die AKs grün und das Feature trotzdem kaputt ließen.

- **Verhältnis zu `pw_smoke` (kein Doppeltest):** Der Worker fährt `pw_smoke.mjs` / die
  Flow-Skripte für **seine** AKs — bestätigend, Happy-Path, hartes Tor. Der Prüfer re-fährt
  diese AK-Flows **nie**; er erfindet **neue** Szenarien **außerhalb der AKs** (Grenzwert,
  Fehler-State, Doppelklick, abgebrochener Fluss, Idempotenz, Rechte-Grenze, Latenz) auf
  denselben `pw_lib.mjs`-Primitiven. Schnitt **nach Absicht, nicht nach Werkzeug**.
- **Multi-instrument, nicht nur UI:** für Nicht-UI prüft er Hintergrund-Wahrheit (psql-Row,
  API/HTTP, Trace) — wie jede Abnahme hier.
- **Konfidenz-klassifiziert:** jeder Fund `sicherer-bug` / `fragwürdig` / `scope-frage`; gegen
  ein sauberes Feature **null `sicherer-bug`**, Rauschrate beziffert (Oracle-/Rausch-Disziplin).
- **Zusätzliche Prisment-Linse:** er liest die `Urteil`-Einträge des Standards-Kanons (03a) und
  leitet je eine Angriffs-Frage ab — er **schreibt** nicht in 03a.
- **Write-confined:** der Prüfer liefert einen **Report, keine Commits** — ein
  PreToolUse-Confinement (`.claude/hooks/pruefer-confinement.py`) erzwingt, dass er nie an
  Source schreibt (Good-Faith-Guardrail auf `isolation: worktree`, kein adversarieller Sandbox).
- **Architekt triagiert; Worker-Fix-Loop nur bei bestätigten Defekten.**
- **Hybrid-Tor (Kosten-Deckel):** **Pflicht-Tor** für kundensichtbare UI-Flows + alles
  `kritisch` (maschinell erzwungen, s. „Maschinelles Tor"); **darunter** (Schritt/Bugfix/Doku)
  ehrlich **Architekt-Ermessen** (Disziplin, kein Tor). So ist er nicht genau dann skippbar,
  wenn er am nötigsten ist; die Opus-Kosten bleiben gedeckelt.

## Kadenz

- **Pro Feature** vor Promote — Pflicht.
- **Voller Vor-Kunden-Pass** (Echt-Handy, Mensch) vor jedem Termin.
- Regressions-Suite (AK-Katalog) wächst mit jedem Bug. Zeitgesteuerter Dauer-Smoke erst bei
  belegtem Drift.

## Vor-Promote-Stop-Tor (PLAT-144, in-session Erzwingung)

**Schichten-Modell (Defense-in-depth):**

1. **Stop-Tor (in-session)** — eine Session, die einen kundensichtbaren (`cv`) Build
   verantwortet hat, kann nicht als erledigt enden, solange kein kalter Pruefer-Pass
   (`pruefer_pass:gruen`) ODER ein expliziter lauter Skip in das Run-Log geschrieben wurde.
   Greift VOR dem Promote, ohne dass der Mensch eine neue Session starten muss.
2. **Promote-Tor (terminal Backstop)** — `promote_image.sh` verweigert `:latest`-Flip
   ohne gruenen Run-Log-Eintrag (Schicht A) und ohne `pruefer_pass:gruen` (Schicht B).
3. **SessionStart-Erinnerung** — beim naechsten Session-Start wird ein armed cv-`:test`-Zustand
   sichtbar (faengt verpasste Stops und Fortsetzungs-Sessions).

**Maschineller cv-Ausloeser:** Der Stop-Hook (`kb/.claude/hooks/pruefer-gate-stop.py`) erkennt
einen cv-Build aus dem Session-Transcript — Signal: Bash-Aufruf mit `build_image.sh` **und**
einem cv-Image-Namen oder cv-Context-Dir. Die cv-Image-Allowlist ist SSOT in
`_Betrieb/Skripte/pruefer-gate/cv_images.json` (pwa-web, pwa-api, langgraph-*, admin-web).

**Disarm-Wege:** `pruefer_stamp.py <img> <rev> gruen --report <pfad>` (nach echtem Pruefer-Pass)
ODER `pruefer_stamp.py <img> <rev> skip --grund '<text>'` (lauter Skip, auditierbar).

**Fail-open (kein Total-Lockout):** Interne Gate-Fehler geben `allow`, schreiben aber eine
persistente Zeile in `Prisment/Systemzustand/Test/pruefer-gate.log`. Der SessionStart-Selfcheck
meldet einen kaputten Gate beim naechsten Start.

## Maschinelles Tor (nicht disziplinabhaengig)

Das Tor wird **erzwungen**, nicht der Disziplin ueberlassen: ein **Promote-Hook** verweigert
`:latest`-Promote eines `:test`-Images, solange das Run-Log keinen gruenen Lauf mit **genau
dem `image.revision`-Commit-SHA** des Builds traegt. Umgehung nur **laut + auditierbar**
(Pflicht-Begruendung + Spur). So laesst kein alter Gruen-Eintrag ein ungetestetes Feature durch.

**Pruefer-Tor (parallel, PLAT-142 + PLAT-144).** Der Ausloeser ist **maschinell ablesbar**:
Pflicht, wenn die Feature-/Spec-Frontmatter `risikoklasse: kritisch` **oder** `kundensichtbar: true`
traegt — **oder** das Image in der cv-Allowlist steht (`_Betrieb/Skripte/pruefer-gate/cv_images.json`,
PLAT-144). Die cv-Allowlist-Ableitung gilt unabhaengig von run-log-Feldern: ein spec-loser,
aber abgenommener Build wird trotzdem geblockt, wenn das Image kundensichtbar ist.
Das Run-Log-Schema traegt ein Feld **`pruefer_pass`** (`gruen` / `skip:<grund>`).
Umgehung nur laut+auditierbar via `PROMOTE_SKIP_PRUEFER=1 + PROMOTE_SKIP_PRUEFER_GRUND`.
**Unterhalb** der Flags und der cv-Allowlist bleibt der Pruefer-Pass ehrlich
**Architekt-Ermessen** (Disziplin, kein Tor).

## Verfahren & Artefakte

- **Ausführung:** Skill [`live-abnahme`](../../.claude/skills/live-abnahme/SKILL.md).
- **Test-Logbuch:** `Prisment/Systemzustand/Test/` — AK-Katalog (Master) + `run-log.jsonl`.
- **Herkunft/Begründung:** [[PLAT-090]], Pilot [[PRIS-097]].
