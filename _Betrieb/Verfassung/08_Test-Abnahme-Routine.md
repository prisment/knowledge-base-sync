---
typ: verfassung
titel: "Test-Abnahme-Routine — das Tor vor dem Kunden"
stand: 2026-06-21
aenderung: "nur nach oben, nur durch bewusste Freigabe"
quelle: "[[PLAT-090]]"
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

## Das Prüf-Instrument passt sich dem Feature-Typ an

- **UI, Chrome-sichtbar** → Chrome-Lauf (DOM, Screenshot, Network, Konsole, Session).
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
