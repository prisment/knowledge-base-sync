---
typ: verfassung
titel: "Projekt-Ebene & Orchestrierung"
stand: 2026-05-30
aenderung: "nur nach oben, nur durch bewusste Freigabe"
---

# 07 — Projekt-Ebene & Orchestrierung

## Was ein Projekt ist (und was nicht)

Ein **Projekt** ist ein Behälter über mehreren Arbeitseinheiten mit **genau einem
fixen, maschinell prüfbaren Wohin**, dessen Wie-Zerlegung emergent ist — die
Folge-Arbeiten zeigen sich erst beim Anfassen. Ein Projekt wird autonom über
mehrere Spuren/Sprünge/Schritte hinweg abgewickelt durch den **Orchestrator**,
innerhalb eines vom Menschen ratifizierten **Abweichungs-Kontrakts**.

**Projekt ist KEINE vierte Stufe.** Die Stufen-Achse (Spur/Sprung/Schritt) misst
Zeremonie-Tiefe *einer* Einheit. Das Projekt ist eine **Ebene darüber**,
orthogonal. Arbeit *im* Projekt behält ihre Stufe.

**Abgrenzung zur abgeschafften Roadmap (E36):** Eine Roadmap bündelte *mehrere
unabhängige Wohins* zum Scheduling — verboten. Ein Projekt hat **genau ein
Akzeptanzkriterium**; alle seine Seeds dienen diesem einen Ziel. Das ist der
Unterscheider. Ein-Ziel-Behälter ist erlaubt, Mehr-Ziel-Schirm nicht.

**Hierarchie:** Mission (loser Filter-Slug, keine Prozess-Wirkung) > **Projekt**
(ein fixes Wohin, prozess-tragend) > Seeds > Specs.

## Der geschachtelte Zyklus

Der Iterationszyklus (00) wird nicht ersetzt — er wird rekursiv angewandt.

```
PROJEKT (ein fixes Wohin; Kontrakt vom Menschen ratifiziert)
│
├─ Akt 1  Projekt-Sondierung (CC) ──── Frame + Abweichungs-Kontrakt
├─ Akt 2  Kontrakt-Freigabe (MENSCH) ◄─ EINZIGER synchroner Frontstopp
└─ Akt 3  Orchestrator-Loop:
      │
      └─ pro Iteration ein innerer Drei-Akt-Zyklus je Seed:
            inner-Akt 1  Seed-Sondierung   (CC, kalt)
            inner-Akt 2  Freigabe          (ORCHESTRATOR statt Mensch — nur Wie, im Kontrakt)
            inner-Akt 3  Abarbeitung       (WORKER, eigener Worktree, bestehender Korridor)
      ▲
      └─ Schleife bis: Ziel erreicht → Report+Mail
                       Eskalation     → Stopp+Mail
                       Token-Limit    → Sleep+Resume (Zustand liegt im Journal)
```

Der Orchestrator besetzt die **Akt-2-Freigabe-Rolle der inneren Zyklen** — aber
nur für Wie, nur innerhalb des Kontrakts. Die menschliche Akt-2-Rolle wandert auf
die Projekt-Ebene: der Mensch ratifiziert den Kontrakt einmal. Wohin/Wie bleibt
exakt erhalten: Mensch besitzt den äußeren Akt 2 (Wohin als Kontrakt),
Orchestrator den inneren Akt 2 (Wie im Kontrakt). E3 intakt — alle Grenzen zieht
weiter der Mensch; der Orchestrator führt nur darin aus.

## Rollen-Identität wird pro Aufruf injiziert

Orchestrator und Worker sind beide `claude -p`-Kaltstarts und lesen `CLAUDE.md`
identisch. Die Rolle wird daher **nicht** aus `CLAUDE.md` erkannt, sondern vom
**Einstiegsprompt** gesetzt, den der Loop übergibt (gleiches Muster wie die
nächtliche Security-Routine in 04). Die Orchestrator-Rolle IST dieses Modul (07) +
der Kontrakt; der Worker IST das bestehende Arbeitstier (02) + die eine Spec.
`CLAUDE.md` trägt nur einen Zeiger + den Fail-safe „ohne expliziten
Orchestrator-Prompt bist du ein normaler Worker" (siehe CLAUDE-global.md-Ergänzung).

**Modell-Routing ist Teil des Aufruf-Vertrags, nie Selbstwahl des laufenden Agenten:**
Worker = Sonnet / effort medium; Orchestrator = Opus / effort high; Evaluator = Opus / effort high.
Telemetrie pro Aufruf (Modell, Effort, Turns, Tokens, Beweis-Status first-pass) wird im Token-Log mitgeschrieben.

Worker können `advisor()` aufrufen für substanzielle Entscheidungen — analog zur interaktiven Arbeitsweise. Das Tool ist harness-seitig in allen CC-Sessions verfügbar.

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

**Eine Mail pro Lauf** (plus Start-Bestätigung, falls konfiguriert). Eskalationen, die den Loop nicht blockieren (z. B. ausstehende Promotes, auf die kein offener Seed wartet), wandern in die Journal-Sektion „GESAMMELT (Lauf-Ende)" und werden mit der End-Eskalation / dem Abschluss-Report übergeben. Sofort-Mail nur, wenn der Loop nichts mehr dispatchen kann (Blocker) oder eine out-of-bounds-/Wohin-Frage ansteht.

**Mensch-Gates werden bei der Kontrakt-Ratifikation enumeriert** (Liste „dafür brauche ich dich") und gebatcht terminiert — Default: ein Block am Lauf-Ende (E2E + Promotes + Abschluss-Freigabe); an den Anfang nur, was den ersten Dispatch blockiert. Das operationalisiert „Vorgezogene Mensch-Handlungen" (00) für den Projekt-Modus.

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

Wie die Projekt-Sondierung (Akt 1) die Seeds schneidet, bestimmt maßgeblich den
Token-Verbrauch des Laufs. Belegt am realen Lauf (PLAT-053, Beleg PRIS-045): der
**Worker-Call ist der teure Posten**, und sein Kontext-Verbrauch wächst
**überproportional mit der Turn-Zahl eines Calls** — mechanisch O(n²), weil jeder
Turn den gewachsenen Verlauf desselben Calls mitschleppt (empirisch super-linear,
durch Cache-Rabatt gedämpft). Einen langen Worker-Lauf in zwei kurze, je
eigenständig beweisbare Läufe geschnitten senkt den teuersten Posten
überproportional. Iterationen sind davon unberührt — jeder Call ist Kaltstart
(cold-by-design).

**Regel:** Seeds / Worker-Aufträge werden an **natürlichen Nahtstellen**
geschnitten — jeder Seed eine in sich abgeschlossene, per `beweis_befehl`
eigenständig beweisbare Einheit, mit dem Ziel **kurzer Worker-Läufe** (wenige Turns
je Call).

**Grenze gegen Über-Fragmentierung:** Worker sind cold-by-design und erinnern sich
NICHT zwischen Seeds. Künstlich zu kleine, eng gekoppelte Seeds zwingen
Folge-Worker, Vorwissen neu zu erarbeiten — das kostet Qualität UND Token. Also:
**so klein wie in sich abschließbar + beweisbar, nicht kleiner.** Schnitt an
logischen Nähten, nie willkürlich.

**`max_iter` (Kontrakt-Parameter):** Die Projekt-Sondierung schätzt die Seed-Zahl;
der Kontrakt setzt `max_iter = Schätzung + Puffer` statt pauschal 20.

## Projekt-Fakten-Register (der kalte Worker liest geteilte Befunde)

Komplement zur Seed-Granularität: dort sinkt die Turn-Zahl *eines* Worker-Calls,
hier die **Neu-Erkundung über Seeds hinweg**. Der Worker ist `cold-by-design` und
liest nur seinen Übergabe-Prompt + `CLAUDE.md` — nie das Journal, nie fremde Reports.
Ohne geteiltes Gedächtnis erkundet jeder Worker das Code-Areal von Null, auch wenn
ein Vorgänger die Wurzel längst fand (belegt PRIS-046: ein reiner Merge-Sync-Retry
brauchte 45 Turns für null neue Substanz).

Das Register schließt diese Wand — **nicht** über das Journal: das Journal ist die
verdichtete Orchestrator-Memory und wird jede Iteration komplett kalt gelesen;
Root-Cause-Detail mit Index dort hinein verlagert den O(n²)-Schmerz vom Worker auf
den Orchestrator. Distinkter Zweck → distinkte Datei (03, SSOT).

### Das Artefakt: `<projekt_id>_FAKTEN.md`

- **Ort:** neben dem Missions-Journal, `Plattform/Arbeitsgedaechtnis/<projekt_id>_FAKTEN.md`.
- **Lebensdauer:** projekt-geschoppt, sterblich. Wandert bei der Verankerung über das
  bestehende Kehraus (`phase9_seed_archive.py`, Glob `<id>_*.md`) automatisch ins
  Archiv — kein Zusatzbau. Dauerhaft gültige Codebasis-Wahrheiten promotet der
  **Mensch** bei der Verankerung in den Systemzustand (E3/Akt 3); das Register ist
  Arbeits-Memory des Laufs, nicht die Dauer-Wahrheit.
- **Format (HART — keine Empfehlung; sonst wird es ein zweites Logbuch und frisst die
  gesparten Token zurück):**
  - **Fakten only, keine Narrative.** Eine Zeile pro Befund.
  - **Indexiert:** `[RC-NN] <KATEGORIE> <Befund> — <Wirkung/Konsequenz> @ <datei:zeile>`.
  - **`datei:zeile` Pflicht** (oder Tabelle/Objekt-Pfad bei DB) — sonst kein gültiger Eintrag.
  - **Vier Kategorien:** `ROOT-CAUSE` / `SHARED-ROOT` / `EXISTS-VERIFIED` / `GOTCHA`.
  - Header kennzeichnet: kuratiert vom Loop, gelesen von jedem Worker, fakten-only,
    stirbt mit dem Projekt-Archiv.

### Schreibseite — Worker emittiert (Urteil), Loop hängt an (Mechanik)

Der **Worker** entscheidet per Urteil, was ein durabler, seed-übergreifender Befund
ist (nicht jeder Implementierungs-Schritt — nur, was einem Folge-Worker Erkundung
spart), und emittiert ihn in seinem Report als `durable_facts: [{kategorie, befund,
wirkung, ort}]`. Ein **deterministisches Loop-Tool** (kein LLM) liest nach jedem
Worker-Lauf — **grün wie rot** (auch ein gescheiterter Versuch kann eine geteilte
Wurzel/`GOTCHA` gefunden haben, die dem nächsten Worker Erkundung spart) — die
`durable_facts`, entdoppelt gegen die bestehenden Einträge (Match über `befund`+`ort`),
vergibt die nächste `RC-NN` und hängt Neues an das Register **auf `main`** an — vor dem
nächsten Dispatch.

> Nicht der Worker schreibt das Register, nicht der LLM-Orchestrator entdoppelt:
> Schriebe der Worker im Worktree, erreichte der Eintrag den nächsten Worker erst
> nach dem Phase-9-Merge — dieselbe Worktree↔`main`-Race, die in PRIS-046 die
> Sync-Rots erzeugte. Der Loop operiert zwischen den Iterationen auf `main`
> (single-threaded) → race-frei. Urteil beim Worker, Mechanik (Append/Dedupe/Index)
> deterministisch.

### Leseseite — Dispatch nennt das Register, CLAUDE.md erzwingt das Lesen

Der Übergabe-Prompt nennt (a) den vollständigen Pfad zu `<projekt_id>_FAKTEN.md` und
(b) die für diesen Seed relevanten `RC-NN` (oder „lies das ganze Register" — es ist
klein). Existiert kein Register (erstes Projekt / erster Seed), entfällt der Block,
nichts bricht. Die Always-on-Lese-Pflicht für kalte Worker steht in `CLAUDE.md` (die
einzige Datei, die jeder kalte Worker garantiert liest).

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

- Stößt ein Worker auf ein neues, separat zu lösendes technisches Problem, legt er
  am Ende seines Laufs einen Seed im Seed-Set des Projekts an (`projekt:`-getaggt),
  mit Risikoklasse-Vorschlag. Der nächste kalte Orchestrator findet ihn und
  priorisiert.
- Emergente Seeds, die der Orchestrator zur Abarbeitung freigibt, durchlaufen Evaluator 3
  (sofern `planned_seeds` im Kontrakt definiert ist), bevor der Worker dispatcht wird.
- **Sicherheit ohne Mensch-Ratifikation der Klasse:** Es gilt **nur-nach-oben** —
  ein emergenter Seed kann nicht heruntergestuft werden. `kritisch`/`akut`-Vorschlag
  → automatische Eskalation (der Orchestrator gibt `akut` ohnehin nicht frei).
  Die **feste Liste** (01) greift mechanisch (RLS/Auth/Kundendaten etc. werden per
  Code erkannt, nicht per Urteil). Nur `sicher`/`kritisch`-Seeds laufen autonom
  weiter; alles Gelistete self-eskaliert. **Restrisiko:** eine *ungelistete*
  gefährliche Kategorie — dieselbe Lücke wie heute, nur ohne Mensch im Lauf. Daher:
  feste Liste vor jedem Projekt-Lauf vollständig halten.

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

Beratend vorgeschlagen (CC/Chat-Architekt), vom Menschen ratifiziert — wie `stufe`.
Zwei Einstiege: (a) Seed wird als `typ: projekt` geboren; (b) **Eskalation
Spur→Projekt** im Lauf (analog Sprung→Spur, nur-nach-oben). Kette:
Schritt→Sprung→Spur→**Projekt**.

**Kriterien (UND-Gate, #1 ist hartes Tor):**
1. **Ein maschinell prüfbares Akzeptanzkriterium existiert?** Nein → kein Projekt
   (offene Exploration ist Wohin-Arbeit, läuft nie autonom).
2. **Jetzt vollständig in Bündel zerlegbar?** Ja → normale Spur.
3. **Absehbar mehrere Spuren?** Nein → Spur/Sprung.

**Lackmustest:** Sind die Unbekannten technisch (mehrere Wege nach Rom, egal
welcher) oder Wert-Gabelungen (welcher Weg IST die Frage)? Technisch →
projektfähig. Wert-Gabelungen → nicht.

## Projekt-Abschluss

Der Orchestrator **schließt nie selbst ab.** Der Loop produziert den
Abschluss-Report (Missions-Journal) und meldet per Mail. **Verankerung**
(Logbuch, Doku-Synchronität, Archiv, Seed-Status) ist Mensch-ratifiziert (E3) —
der Mensch startet eine frische Session und gibt frei oder lässt einen Fall
nach-sondieren.

**Archiv-/Seed-Teil der Verankerung — deterministisch (PLAT-051 Schicht 1+2).**
Der Mensch-ratifizierte Charakter bleibt; der *mechanische* Teil (Zyklus-Satz +
projekt-gebundene Done-Seeds ins Archiv) läuft über ein deterministisches
Werkzeug, damit er nicht still ausbleibt (wie bei PRIS-045, dem ersten
Live-Lauf, geschehen — beide Archiv-Tools kannten den Projekt-Modus nicht):
- **Skill `projekt-abschluss`** in der frischen Session — Ein-Befehlspfad:
  `phase9_seed_archive.py <id>` (archiviert `<id>_*.md` + alle Seeds mit
  Frontmatter `projekt: <id>` und `status: abgeschlossen`) + Übersicht-Regen
  + Residuen-Re-Check.
- **Bindung** Projekt ↔ Seed ist deterministisch das Seed-Frontmatter-Feld
  `projekt: <id>`, **nicht** die `seed_set:`-Liste der PROJEKT-Datei — so
  werden emergente, während des Laufs angelegte Seeds miterfasst, sofern sie
  korrekt getaggt sind.
- **Reminder:** Die `ziel_erreicht`-Mail des Orchestrators trägt eine
  Archiv-Vorschau (projekt-fähiger `check_akt3_residuen.py`), sodass ein
  unarchiviertes Projekt nie unsichtbar bleibt.

**Verankerungs-Freigabe-Tor (PLAT-056).** Die Verankerungs-Freigabe eines
Projekts ist gesperrt, solange ein Seed mit `projekt: <id>` und
(`status ≠ abgeschlossen` **oder** `risikoklasse: sicherheitskritisch-akut`)
offen ist. `phase9_seed_archive.py` prüft dies deterministisch über den
geteilten Rechner `projekt_obligationen.py` und **verweigert die Freigabe hart**
(Exit 1, fail-closed) mit Auflistung der offenen Obligationen — analog zur
`check_akt3_residuen.py`-Wand für Archiv-Residuen, nur für die Obligationen-
Bedingung und orthogonal dazu. „Ziel erreicht" und „abgeschlossen" sind damit
auf Mechanik-Ebene unverwechselbar getrennt (der zweimalige Fehlschluss bei
PRIS-045/046). Mensch-Ratifikation bleibt (E3); das Tor verhindert nur, dass
eine Freigabe an offenen Obligationen vorbei erteilt wird — der Mensch löst die
Obligation (Status setzen / bewusst reklassifizieren) und verankert dann.
**Voraussetzung:** `projekt: <id>` ist die einzige deterministische Projekt-
Bindung; ein loop-emergenter Seed ohne `projekt:` wird vom pre-commit-Hook
abgewiesen (PLAT-056 B0, `check_projekt_tag.py`).

## Kontextbindung

Gilt für den aktuellen Kontext (Solo-Gründer, Zeit als Engpass, Abo-Modell mit
Token-Pausen). Sobald zahlende Kunden mit SLA bestehen, sind Freigabe-Vollmacht
(B) und Eskalationsschwellen neu zu bewerten.
