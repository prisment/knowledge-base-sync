---
typ: verfassung
titel: "Projekt-Ebene & Orchestrierung"
stand: 2026-05-30
aenderung: "nur nach oben, nur durch bewusste Freigabe"
---

# 07 — Projekt-Ebene & Orchestrierung

## Was ein Projekt ist (und was nicht)

Ein **Projekt** ist ein Behälter über mehreren Arbeitseinheiten mit **genau einem
fixen, maschinell prüfbaren Wohin**, dessen Wie-Zerlegung emergent ist. Es wird
autonom über mehrere Spuren/Sprünge/Schritte durch den **Orchestrator**
abgewickelt, innerhalb eines vom Menschen ratifizierten **Abweichungs-Kontrakts**.
**Hierarchie:** Mission (loser Filter-Slug) > **Projekt** (fixes Wohin) > Seeds > Specs.
**Keine vierte Stufe:** die Stufen-Achse (Spur/Sprung/Schritt) misst Zeremonie-Tiefe
*einer* Einheit, das Projekt ist eine **Ebene darüber** — Arbeit *im* Projekt
behält ihre Stufe (Abgrenzung zur Roadmap E36, `00_Iterationszyklus.md`: Roadmap
= mehrere unabhängige Wohins/verboten, Projekt = ein Ziel für alle Seeds).

## Der geschachtelte Zyklus

Der Iterationszyklus (00) wird rekursiv angewandt: Akt 1 (Projekt-Sondierung,
CC) → Akt 2 (Kontrakt-Freigabe, **MENSCH**, einziger synchroner Frontstopp) →
Akt 3 (**Orchestrator-Loop**: pro Iteration ein innerer Drei-Akt-Zyklus je Seed
— Sondierung CC-kalt → Freigabe durch den **Orchestrator statt Mensch** (nur
Wie, im Kontrakt) → Abarbeitung durch den **Worker** im eigenen Worktree), bis
Ziel erreicht/Eskalation/Token-Limit (Sleep+Resume im Journal). Wohin/Wie bleibt
exakt erhalten: Mensch besitzt den äußeren Akt 2 (Wohin), Orchestrator nur den
inneren (Wie) — E3 intakt.

## Rollen-Identität wird pro Aufruf injiziert

Orchestrator und Worker sind beide `claude -p`-Kaltstarts und lesen `CLAUDE.md`
identisch. Die Rolle wird daher **nicht** aus `CLAUDE.md` erkannt, sondern vom
**Einstiegsprompt** gesetzt (gleiches Muster wie die nächtliche Security-Routine
in 04): Orchestrator-Rolle IST dieses Modul (07) + der Kontrakt, Worker IST das
Arbeitstier (02) + die eine Spec. `CLAUDE.md` trägt nur einen Zeiger + den
Fail-safe „ohne expliziten Orchestrator-Prompt bist du ein normaler Worker".
**Modell-Routing ist Teil des Aufruf-Vertrags, nie Selbstwahl des laufenden
Agenten:** Worker = Sonnet/medium; Orchestrator = Opus/high; Evaluator = Opus/high;
Telemetrie läuft ins Token-Log. Worker können `advisor()` aufrufen (harness-seitig verfügbar).

## Evaluator-Punkte im Projekt

**Evaluator 1 — Beweis-Design (nach Akt 1, vor Ratifikation).** Kalter Aufruf (Opus/high) liest Sondierung + Kontrakt-Entwurf. Kern-Auftrag: konstruiere mindestens drei Szenarien, in denen alle vorgeschlagenen Beweise grün werden, obwohl das Wohin verletzt bleibt. Außerdem: Checkliste pro AK mit Urteil gedeckt / ungedeckt / nur-mit-Mensch-Rest-gedeckt + Option-0-Gegenrede. Die Sondierung erhält „Einwände & Behandlung" (per `01_Spec-Format.md`), bevor der Mensch ratifiziert. Evaluator 1 hat Wand-Charakter: unbehandelte Einwände blockieren die Ratifikation.

**Evaluator 2 — Implikations-Prüfung (nach jedem grünen `beweis_befehl`, vor Seed-Abschluss).** Kalter Aufruf liest Worker-Report + Beweis-Skript + Diff (read-only; Stichproben am Artefakt erlaubt). Frage: Impliziert dieses Grün den AK — oder nur das, was der Autor geprüft hat? Drei Urteile:

- **ROT**: Seed gilt nicht als abgeschlossen; Re-Dispatch mit den `ungedeckt:`-Einwänden im Auftrag. Worker-Branch persistiert über Retries (kein Reset auf origin/main) — Worker setzt auf seinem letzten Commit auf. Zählung gegen `max_eval2_redispatch` (Default 2): Substanz-Mangel zählt, Beweis-Design-Mangel nicht.
- **GRUEN**: Beweis impliziert AK vollständig → Merge freigegeben.
- **SPEC_AMBIGUITAET**: Die Lücke ist kein Code-Problem — der Worker kann sie ohne Spec-Interpretation nicht schließen. Abgrenzungskriterium: „Kann der Worker die Lücke durch Code/Tests fixen ohne die Spec neu zu deuten? Nein → SPEC_AMBIGUITAET." Pflichtfeld `KLAERUNGSFRAGE:` im Evaluator-Output. Zählt **nicht** gegen `max_versuche` — Eskalation ist sofort und direkt mit der Klärungsfrage.

Evaluator 2 ist beratend mit Wand-Charakter: ROT/SPEC_AMBIGUITAET stoppen den Seed-Abschluss, nie den Gesamt-Loop.

**Ausnahme: `seed_typ: verifikation` (PLAT-070 B5).** Verifikations-Seeds (reine Test-Schreibung ohne Feature-Code) können im Kontrakt als `seed_typ: verifikation` markiert werden. Für solche Seeds entfällt Evaluator 2; das Gate ist stattdessen: (1) `beweis_befehl` exit 0, (2) Assertions-Count ≥ 1 (keine assertions-losen Tests), (3) vollständiger Worker-Report mit `status: fertig`. Merge-Freigabe ohne EVAL_2-Pass. **Nur-nach-oben-Prinzip:** Ein `seed_typ: verifikation`-Seed darf nachträglich keine Feature-Arbeit aufnehmen — ändert sich der Seed-Inhalt in Richtung Feature-Code, gilt wieder der volle EVAL_2-Pfad. Mensch-Sichtprüfung bei Verankerung.

**Evaluator 3 — spontane Seeds (vor Worker-Dispatch).** Kalter Loop-seitiger Gate (baugleich zu 1/2). Greift **nur für spontane Seeds** — Seeds in `planned_seeds` (durch Evaluator 1 vor Projekt-Start gedeckt) überspringen das Gate. Maximal `MAX_EVAL3=2` Läufe: Runde 1 = voller Pass; Runde 2 = Recheck nur bei ≥1 BLOCKIEREND in Runde 1. FREIGABE/Recheck-BESTANDEN → Dispatch. RICHTUNGSWECHSEL_PFLICHT → sofortige Mensch-Eskalation. REWORK mit STRUKTURELL → Eskalation (Architektur-Eingriff, kein Auto-Dispatch). Fehlt `planned_seeds` im Kontrakt → Gate inaktiv (Opt-in-Design).

## Eskalations-Batching & gebatchte Mensch-Gates

**Eine Mail pro Lauf.** Eskalationen, die den Loop nicht blockieren, wandern in die Journal-Sektion „GESAMMELT (Lauf-Ende)" und werden mit dem Abschluss-Report übergeben. Sofort-Mail nur bei Blocker oder einer out-of-bounds-/Wohin-Frage. **Mensch-Gates werden bei der Kontrakt-Ratifikation enumeriert** und gebatcht terminiert — Default: ein Block am Lauf-Ende (E2E + Promotes + Abschluss-Freigabe); an den Anfang nur, was den ersten Dispatch blockiert.

## Abweichungs-Kontrakt (Herz der Sicherheit)

Vom Menschen in Akt 2 ratifiziert. Drei Pflichtteile:

1. **Fixes Akzeptanzkriterium** — maschinell prüfbarer Endzustand. Daran misst der
   Orchestrator „Ziel erreicht".
2. **In-bounds (Wie — Orchestrator entscheidet autonom).**
3. **Out-of-bounds (Wohin + `akut` — harter Stopp/Eskalation).**

**Pflicht-Frontmatter:** `ziel_repo: knowledge-base` oder `ziel_repo: prisment-platform`
ist Pflichtzeile im Kontrakt. Fehlt sie, eskaliert der Loop beim ersten Seed-Dispatch
(kein stiller Default mehr — PLAT-058 B4 / E-13). Alle laufenden Kontrakts
prüfen und nachpflegen.

## Seed-Granularität in der Projekt-Sondierung (Token-Disziplin)

**Regel:** Seeds/Worker-Aufträge werden an **natürlichen Nahtstellen** geschnitten —
jeder Seed eine in sich abgeschlossene, per `beweis_befehl` beweisbare Einheit,
Ziel kurzer Worker-Läufe (Kontext wächst überproportional mit der Turn-Zahl eines
Calls, PLAT-053/PRIS-045). **Grenze gegen Über-Fragmentierung:** Worker sind
cold-by-design — zu kleine, eng gekoppelte Seeds zwingen Folge-Worker, Vorwissen
neu zu erarbeiten; so klein wie abschließbar, nicht kleiner. **`max_iter`:** die
Sondierung schätzt die Seed-Zahl, der Kontrakt setzt `max_iter = Schätzung + Puffer`.

## Projekt-Fakten-Register (der kalte Worker liest geteilte Befunde)

Der Worker ist `cold-by-design` und liest nur Übergabe-Prompt + `CLAUDE.md` — nie
das Journal, nie fremde Reports; ohne geteiltes Gedächtnis erkundet jeder Worker
das Code-Areal von Null (belegt PRIS-046: 45 Turns für null neue Substanz). Das
Register schließt diese Wand, **nicht** das Journal (verdichtete Orchestrator-
Memory, komplett kalt gelesen).

### Das Artefakt: `<projekt_id>_FAKTEN.md`

- **Ort/Lebensdauer:** `Plattform/Arbeitsgedaechtnis/<projekt_id>_FAKTEN.md`,
  projekt-geschoppt, sterblich — wandert bei der Verankerung über
  `phase9_seed_archive.py` ins Archiv; Dauer-Wahrheiten promotet der **Mensch**.
- **Format (HART):** Fakten only, eine Zeile pro Befund:
  `[RC-NN] <KATEGORIE> <Befund> — <Wirkung> @ <datei:zeile>` (Pflicht). Vier
  Kategorien: `ROOT-CAUSE` / `SHARED-ROOT` / `EXISTS-VERIFIED` / `GOTCHA`.

**Schreibseite:** Der **Worker** entscheidet per Urteil, was ein durabler Befund
ist, emittiert `durable_facts: [{kategorie, befund, wirkung, ort}]`. Ein
**deterministisches Loop-Tool** (kein LLM) liest jeden Lauf — grün wie rot —,
entdoppelt, vergibt die nächste `RC-NN` und hängt Neues **auf `main`** an.

**Single-threaded-`main`-Garantie (PLAT-132):** der Orchestrator-Loop dispatcht
sequenziell, kein paralleler Dispatch auf dieselbe `main`-Linie — race-frei
(Kollisions-Schutz für parallele Hände trägt `01_Spec-Format.md`, SSOT
`Plattform/Systemzustand/parallel-agent-schutz.md`).

**Leseseite:** Übergabe-Prompt nennt Pfad + relevante `RC-NN`; kein Register →
Block entfällt. Always-on-Lese-Pflicht für kalte Worker: `CLAUDE.md`.

## Freigabe-Vollmacht des Orchestrators (Option B)

- **`sicher` und `kritisch` laufen autonom** unter Orchestrator-Freigabe.
  `kritisch` mit der bestehenden Pflicht-Vorsicht (Backup vorher, tiefe
  Verifikation, dokumentierter Restore-Pfad). Begründung: der ratifizierte
  Kontrakt IST der mensch-freigegebene Korridor, in dem die Regel aus 00/01
  („`kritisch` = autonom mit Vorsicht, kein Stopp aus Prinzip") gilt.
- **`sicherheitskritisch-akut` wird ZURÜCKGESTELLT, nicht der Loop gestoppt**
  (Direktive 2026-05-30). Ein akut-Job hält den Lauf nicht an: der Orchestrator
  stellt ihn ans Ende (Journal-Liste „ZURÜCKGESTELLT") und arbeitet alles
  Nicht-akute, nicht von ihm Abhängige weiter ab. Erst wenn nur noch
  Zurückgestelltes oder davon Abhängiges übrig ist, übergibt er an den Menschen.
  So kommt das Projekt maximal weit, bevor der Mensch die akut-Reste übernimmt.
- **out-of-bounds und echte Wohin-Gabelungen eskalieren weiterhin sofort** —
  das sind *Richtungs*-Fragen, keine Gefahr; da muss der Mensch entscheiden.
- **Der Orchestrator ist die Risikoklassen-Autorität.** Ein mechanischer Gate
  (classify.py) ist nur **beratend** (Journal-Hinweis) — er stoppt und überstimmt
  das Orchestrator-Urteil nicht. Begründung: das Urteil „akut oder nicht" liegt
  beim denkenden Agenten; ein Schlüsselwort-Matcher erzeugt mehr Fehlalarme als
  Sicherheit. Restrisiko (bewusst getragen): unterschätzt der Orchestrator einen
  akut-Job, fängt ihn kein Mechanismus mehr ab — dafür stehen die ratifizierten
  Out-of-bounds-Linien des Kontrakts + der Mensch am Journal.

## Emergente Seeds im Lauf

- Stößt ein Worker auf ein neues Problem, legt er einen Seed im Seed-Set des
  Projekts an (`projekt:`-getaggt, Risikoklasse-Vorschlag) — der nächste kalte
  Orchestrator findet und priorisiert; freigegebene emergente Seeds durchlaufen
  Evaluator 3 (sofern `planned_seeds` im Kontrakt definiert), vor dem Dispatch.
- **Nur-nach-oben:** ein emergenter Seed kann nicht heruntergestuft werden;
  `kritisch`/`akut`-Vorschlag eskaliert automatisch (feste Liste 01, Code-erkannt).
  Nur `sicher`/`kritisch` läuft autonom weiter. **Restrisiko:** ungelistete
  gefährliche Kategorien entkommen — daher die Liste vollständig halten.

## Eskalations-Trigger (→ Mensch)

Der Orchestrator stoppt den Loop und meldet, wenn:
1. eine Entscheidung out-of-bounds liegt (akut allein stoppt NICHT mehr — es wird
   zurückgestellt, siehe Freigabe-Vollmacht),
2. die richtige Wahl vom Wohin abhängt statt von Technik (echte Gabelung),
3. das Akzeptanzkriterium nur erreichbar wäre, indem das Wohin geändert wird,
4. ein Seed nach `max_versuche` (Kontrakt) nicht lösbar ist,
5. der Maschinen-Beweis eines Workers rot bleibt und kein in-bounds-Pfad greift,
6. nur noch Zurückgestelltes (akut) oder davon Abhängiges übrig ist — dann ist
   alles Mögliche getan, der Mensch übernimmt die akut-Reste.

## Erkennung: Ab wann ist es ein Projekt?

Beratend vorgeschlagen (CC/Architekt), vom Menschen ratifiziert — wie `stufe`.
Zwei Einstiege: Seed als `typ: projekt` geboren, oder Eskalation Spur→Projekt im
Lauf (nur-nach-oben). Kette: Schritt→Sprung→Spur→**Projekt**. **Kriterien
(UND-Gate, #1 hartes Tor):** (1) maschinell prüfbares Akzeptanzkriterium
existiert? Nein → kein Projekt. (2) Jetzt vollständig in Bündel zerlegbar? Ja →
normale Spur. (3) Absehbar mehrere Spuren? Nein → Spur/Sprung. **Lackmustest:**
Unbekannte technisch (mehrere Wege nach Rom) oder Wert-Gabelung (welcher Weg IST
die Frage)? Technisch → projektfähig, sonst nicht.

## Projekt-Abschluss

Der Orchestrator **schließt nie selbst ab.** Der Loop produziert den
Abschluss-Report (Missions-Journal) und meldet per Mail. **Verankerung**
(Logbuch, Doku-Synchronität, Archiv, Seed-Status) ist Mensch-ratifiziert (E3).

**Archiv-/Seed-Teil deterministisch (PLAT-051 Schicht 1+2):** Skill
`projekt-abschluss` → `phase9_seed_archive.py <id>` archiviert `<id>_*.md` +
Seeds mit `projekt: <id>` und `status: abgeschlossen` (Frontmatter-Feld, nicht
die `seed_set:`-Liste — erfasst so auch emergente Seeds), regeneriert Übersicht.

**Verankerungs-Freigabe-Tor (PLAT-056), fail-closed:** gesperrt, solange ein Seed
mit `projekt: <id>` und (`status ≠ abgeschlossen` **oder** `risikoklasse:
sicherheitskritisch-akut`) offen ist — `phase9_seed_archive.py` **verweigert die
Freigabe hart** (Exit 1) mit Auflistung der Obligationen; ein ungetaggter Seed
wird vom pre-commit-Hook abgewiesen (PLAT-056 B0, `check_projekt_tag.py`).

## Kontextbindung

Siehe Verfassung 04 (identische Regel, SSOT dort).
