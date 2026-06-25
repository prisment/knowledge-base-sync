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
n8n, Config. (Herkunft: drei misslungene Live-Tests vor dem ersten Kunden; [[PLAT-090]].)

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
- **Nicht-UI** (Backend / Pipeline / Agent / n8n / Config) → Backend-Belege.
  *Durchgearbeitetes Beispiel — ein n8n-Workflow-Feature:* das AK ist nicht „Workflow aktiv",
  sondern **n8n-Execution-Status `success`** + die **erwartete Ziel-DB-Row existiert**
  (psql-Query) + **Trace/Log ohne Error**. „Deploy ok" ist hier so wenig hinreichend wie
  „UI grün" bei einem Klick-Feature.

Das Instrument wechselt, die AK-Disziplin und das Logbuch bleiben.

## Zwei Bühnen

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
Duplikat).

## Kadenz

- **Pro Feature** vor Promote — Pflicht.
- **Voller Vor-Kunden-Pass** (Echt-Handy, Mensch) vor jedem Termin.
- Regressions-Suite (AK-Katalog) wächst mit jedem Bug. Zeitgesteuerter Dauer-Smoke erst bei
  belegtem Drift.

## Maschinelles Tor (nicht disziplinabhängig)

Das Tor wird **erzwungen**, nicht der Disziplin überlassen: ein **Promote-Hook** verweigert
`:latest`-Promote eines `:test`-Images, solange das Run-Log keinen grünen Lauf mit **genau
dem `image.revision`-Commit-SHA** des Builds trägt. Umgehung nur **laut + auditierbar**
(Pflicht-Begründung + Spur). So lässt kein alter Grün-Eintrag ein ungetestetes Feature durch.

## Verfahren & Artefakte

- **Ausführung:** Skill [`live-abnahme`](../../.claude/skills/live-abnahme/SKILL.md).
- **Test-Logbuch:** `Prisment/Systemzustand/Test/` — AK-Katalog (Master) + `run-log.jsonl`.
- **Herkunft/Begründung:** [[PLAT-090]], Pilot [[PRIS-097]].
