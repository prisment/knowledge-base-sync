---
typ: backlog_seed
titel: "Data-Integrity-Architektur für LangGraph-Agents (zentrale Datenpunkt-Definition + Verifikation + definierte Übergaben)"
geltungsbereich: prisment
risikoklasse: kritisch
status: offen
prioritaet: HOCH
klasse: qualitaet
zugkraft: jetzt
stufe: spur
beruehrt: ["langgraph/", "n8n/"]
stand: 2026-05-25
erzeugt_durch: Chat-Architekt (Diskussion nach Security/Integrity-Woche)
---

## Anlass

Prisment fiel wiederholt aus, weil die vielen Datenpunkte, die durch die
LangGraph-Agents laufen, an einer Stelle erzeugt/verarbeitet, an einer anderen
relevanten aber nicht konsumiert wurden. Der Fehler ist still: ein Agent
schreibt ein Feld, der nächste erwartet es unter anderem Namen / Format / gar
nicht — kein Crash, sondern ein funktional kaputtes Feature, das erst spät
auffällt. Das ist die Datenpunkt-Variante der „Health-Check-Lüge" (Logbuch
E19): „läuft" ist kein Beweis für „Daten kommen an".

Bei Interview- und Content-Agent wurde bereits begonnen, die Übergabe als JSON
zu definieren (liegt im Code-Repo, brauchbarer Start, noch nicht getestet —
durch den Next.js-Hack unterbrochen). Das ist der Keim, auf dem dieser Zyklus
aufsetzt: vereinheitlichen, an eine Integrity-Mechanik anpassen, auf alle
5 Agents durchziehen.

## Ziel (Soll-Zustand, grob)

Vier zusammenhängende Bausteine:

1. **Verifikation pro Agent — möglichst früh fehlschlagend.** Ein Check, der
   beweist, dass die erwarteten Datenpunkte tatsächlich genutzt/weitergereicht
   werden. Wunsch: schlägt schon bei der Code-Erstellung fehl (Build-/CI-Zeit),
   nicht erst im Betrieb. Ob zusätzlich ein Runtime-Gate sinnvoll ist (Agent
   verweigert Lauf bei fehlendem Pflicht-Feld), ist offene Frage für die
   Faktensammlung — beide Schichten denkbar.

2. **Zentrale Datenpunkt-Definition (SSOT).** Eine Quelle, die jeden relevanten
   Datenpunkt definiert (Name, Typ, Bedeutung, Pflicht/optional, welcher Agent
   produziert, welcher konsumiert). Tendenz **generierend** statt nur prüfend
   (siehe „Architektur-Weiche" unten) — die eine Quelle, aus der Agent-Typen/
   -Verträge abgeleitet werden, statt eine zweite Wahrheit, die man manuell
   synchron halten muss (Verfassung 03 SSOT).

3. **Pflege-Prozess für die Definition** („Code-Handbuch des Produkts"). Wie
   wird die zentrale Definition laufend aktuell gehalten, ohne zu driften.
   Setzt bewusst auf das bestehende Pflege-Muster auf (siehe „Bezug" unten) —
   kein dritter, neu erfundener Mechanismus.

4. **Definierte Übergaben zwischen allen Agents.** Wer gibt was in welchem
   Format an wen weiter. Die bestehende Interview→Content-JSON-Definition wird
   zur einheitlichen Form gehoben und auf alle 5 Agents durchgezogen.

## Architektur-Weiche (die teure Entscheidung — in der Spec zu fällen)

**Generierend vs. prüfend** ist die Kern-Weiche:

- **Generierend:** Eine zentrale Definition ist die Quelle; Agent-Verträge/
  Typen/Validierung werden daraus abgeleitet/generiert. Ein Datenpunkt
  existiert genau einmal definiert, alle Agents erben ihn. Driftet strukturell
  nicht. Höherer Initialaufwand, aber danach schnell + iterativ änderbar (Feld
  an einer Stelle ändern → überall konsistent).
- **Prüfend:** Agents bleiben wie sie sind, ein Schema validiert nur dagegen.
  Billiger initial, aber die Definition kann von der Realität abweichen (zwei
  Wahrheiten), und genau das ist die Fehlerklasse, die dieser Zyklus abstellen
  soll.

**Mensch-Präferenz (Stand dieser Diskussion):** Tendenz generierend, weil der
Mensch keinen Code-Überblick hält und sich darauf verlassen will, dass ein
Feature funktioniert, ohne den Code nachzulesen — schnelles iteratives Arbeiten
statt starrer manueller Synchronisation. Endgültige Festlegung nach
Faktensammlung am echten Code (was ist mit der bestehenden JSON-Mechanik
realistisch erreichbar, was kostet die generierende Variante wirklich).

## Offene Fragen für die spätere Spec (Faktensammlung-Material)

- **Verifikations-Ebene:** Build/CI-Zeit (Schema-Validierung, Type-Checks,
  pre-commit/pre-build) vs. zusätzlich Runtime-Gate? Alles prüfen, was Sinn
  macht — Entscheidung am echten Code.
- **Bestehende JSON-Definition:** Format (JSON Schema vs. selbstgebaut)? Taugt
  sie als Keim der zentralen Definition oder neu aufsetzen? Erst testen
  (war vor dem Hack nicht mehr dazu gekommen).
- **Topologie der 5 Agents:** lineare Kette vs. verzweigt/zyklisch? Claude Code
  klärt am echten System — bestimmt, wie aufwendig „definierte Übergaben für
  alle" wird.
- **Generator-Mechanik (falls generierend):** Aus welcher Quelle (JSON Schema,
  Pydantic-Modelle, anderes) wird was generiert? Python-seitig (LangGraph) und
  ggf. TypeScript-seitig (PWA-Konsum) aus EINER Quelle?
- **Welche Agents konsumieren welche Datenpunkte:** vollständige Ist-Inventur
  als Faktensammlung — wo genau bricht die Kette heute (konkrete Ausfälle als
  Beweis-Anker rekonstruieren, solange erinnerbar).
- **Verhältnis zur Telegram-EOL-Doku-Bereinigung:** Die Agent-Dokus werden
  ohnehin nach Telegram-EOL re-geschrieben (`seed-sma-doku-rewrite-nach-telegram-eol.md`).
  Reihenfolge klären — Integrity-Umbau verändert dieselben Agent-Dateien.

## Bezug zu bestehendem Pflege-Muster (kein neuer Mechanismus)

Baustein 3 (laufende Pflege) setzt bewusst auf das bestehende Muster auf statt
einen dritten zu erfinden:
- **Phase-9-Pflicht-Tor + Drift-Check** aus `ROADMAP_lebende-bereichs-doku`
  Schritt 8 (Zyklus, der das echte System ändert, muss Doku/Definition im
  selben Commit aktualisieren; nightly Drift-Check außerhalb von Zyklen).
- **`seed-skills-evaluieren`:** Falls die Datenpunkt-Definition + ihr
  Verifikations-Verfahren ein wiederkehrendes, fehleranfälliges Verfahren wird,
  ist es Skill-Kandidat (erst beim dritten Auftreten bauen).

## Verhältnis zur Verfassung

- `03_Code-Standards.md` ist aktuell Platzhalter und soll „aus den realen Zyklen
  heraus" wachsen, explizit beginnend mit der Data-Integrity-Pipeline. Dieser
  Zyklus ist genau dieser Wachstums-Anlass — sein Abschluss (Phase 9) speist
  die ersten echten Data-Integrity-Standards in `03_Code-Standards.md` ein.

## Stufe / Risiko

**Spur, zwingend.** Architektur-gestaltend (Kern-Datenfluss des Produkts),
berührt alle Agent-Codepfade, prägt einen Verfassungs-Standard. Risikoklasse
`kritisch` (greift in produktiven Produkt-Code ein, der Kundendaten verarbeitet)
→ erzwingt ohnehin Spur (Verfassung 00). Voller Zyklus mit Faktensammlung,
Spec, Machbarkeit, gebündelter Ausführung mit Einzel-Freigaben, Abschluss-Doku.

## Trigger

HOCH priorisiert — wiederholte echte Produktausfälle sind der bewiesene Schmerz
(kein „erst beim dritten Mal"). Einordnung gegen die laufende Plattform-Roadmap
(PLAT-008/-010) und die SMA-Doku-Re-Write beim Spec-Start.
