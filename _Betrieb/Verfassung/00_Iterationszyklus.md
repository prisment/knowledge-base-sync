---
typ: verfassung
titel: "Iterationszyklus"
stand: 2026-06-11
aenderung: "nur nach oben = Qualität, nicht Volumen; Streichung einer toten Regel mit Mensch-Freigabe ist eine Verbesserung"
---

# 00 — Iterationszyklus

Der verbindliche Arbeitsablauf für jeden Zyklus. Alle drei Akteure (Mensch, **Architekt** [der Claude-Agent am Host, der berät und das Wohin setzt — seit PLAT-130 keine getrennte Windows-Maschine mehr], **Claude Code** [die ausführende Hand am System, als Worker-Subagent oder Haupt-Session]) kennen ihn.

**Tragender Gedanke:** Der Zyklus folgt der Realität, nicht der Bürokratie. Wer am echten System sitzt (Claude Code), sondiert und führt aus. Wer den Gesamtüberblick hat (Mensch, beraten vom Architekt), legt fest. Daraus ergeben sich **drei Akte** statt einer langen Phasenkette — die alte 9-Phasen-Nummerierung ist abgelöst (Begründung: Logbuch-Eintrag zu dieser Umstellung).

## Die drei Akte

| Akt | Wer | Was |
|---|---|---|
| **1 — Sondierung** | Claude Code, am echten System | Fakten + Machbarkeit + Bündelung + Stufen-/Kritikalitäts-Vorschlag in EINEM Durchgang |
| **2 — Festlegung** | Mensch (Architekt auf Abruf), Kanal stufenabhängig | Spec entsteht (Sprung/Spur: Claude Code schreibt sie, Evaluator-geprüft) → Mensch gibt frei |
| **3 — Ausführung & Verankerung** | Claude Code, am echten System | Autonom im Korridor abarbeiten → Doku synchronisieren → abschließen |

Die **Stufe** (Spur / Sprung / Schritt) bestimmt, wie schwer jeder Akt wiegt und wer in Akt 2 beteiligt ist. Die Akte sind keine starren Schleusen: beim Schritt schrumpfen 1 und 2 auf null, beim Sprung verschmelzen sie.

## Stufen (Prozess-Tiefe skaliert mit dem Vorhaben)

Nicht jedes Vorhaben braucht denselben Prozess-Umfang. Drei Stufen, getrennt nach **Zeremonie-Bedarf**, nicht nach Thema.

| Stufe | Wofür | Akt 1 Sondierung | Akt 2 Festlegung | Dokumente |
|---|---|---|---|---|
| **Spur** | Gestaltet Architektur, berührt Kundendaten/Auth/Netz-Topologie, oder Risikoklasse `kritisch`/`sicherheitskritisch-akut`. Wirkung über den Einzelfall hinaus. | Voll, eigenes Sondierungs-Dokument | Claude Code schreibt die Spec (Wirkungs-Block); kalter Evaluator prüft; Architekt legt die **visuelle Zusammenhangs-Vorlage** vor (Pflicht, live — siehe „Verständnis-Tor" in Akt 2) und ist auf Abruf bei Wohin-Gabelung; **Mensch gibt frei** | Sondierung + volle Spec + volle Abschluss-Doku |
| **Sprung** | Konkrete, abgegrenzte Aufgabe, überschaubares Risiko. Ein Bugfix, eine einzelne Feature-Erweiterung. | Sondierung **=** kombinierte Spec (Claude Code schreibt sie selbst) | **Kein Architekt by default** — nur bei gemeldeter Wohin-Gabelung | kombinierte Spec + kurze Abschluss-Notiz |
| **Schritt** | Trivial, reversibel, isoliert, in einem Rutsch. Typo, Doku-Update, ein Config-Wert, toter Ordner. | entfällt | entfällt | Eine Zeile in `<Bereich>/Schritt-Log.md` (automatisch) |

**Einstufung gehört zur Beratung, nicht zur Alleinentscheidung des Menschen.** Die Stufe wird **nicht vom Menschen allein geraten**. Was leicht aussieht, hängt manchmal an viel.

- Bei jedem Auslöser schlägt **Claude Code (Akt 1) und/oder der Architekt die Stufe vor** — mit kurzer Begründung, was tatsächlich daran hängt.
- Der Mensch **revidiert**, trägt die Einschätzung aber nicht allein.
- Die Stufe steht als Pflichtfeld `stufe:` im Front Matter jeder Spec/Notiz (Format: `01_Spec-Format.md`).

**Keine Planungs-Ebene über der Spur (PLAT-022, E36).** Es gibt genau drei Stufen. **Eine Spur IST das große Vorhaben.** Folge-Arbeit wird als Seed abgezweigt, nie als übergeordnetes Schirm-Dokument geführt. Der Begriff „Roadmap" ist als Framework-Artefakt abgeschafft. Strategie-/Produkt-Dokumente, die das Wort inhaltlich verwenden (z. B. `Prisment/Systemzustand/Strategie/prisment_roadmap_businessplan.md`), sind ausgenommen — nicht Framework-Ebene.

**Harte Regel gegen Stufen-Inflation:**
- **Risikoklasse `kritisch` und `sicherheitskritisch-akut` erzwingen immer Spur.** Beide können nie als Sprung oder Schritt gefahren werden — schützt davor, Gefährliches aus Bequemlichkeit klein zu fahren. (PLAT-012, aufgedeckt bei der Backlog-Migration über `seed-os-patching-broken`.)
- Die Versuchung, sich alles als „Schritt" schönzureden, ist genau die menschliche Trägheit, vor der dieses Framework warnt. Ehrliche Einstufung ist Disziplin, kein Formalismus.

**Eskalation einer Stufe während des laufenden Vorhabens.** Öffnet sich während der Arbeit ein Fass (ein Sprung entpuppt sich als Spur, weil Kritisches aufkommt — wie in PLAT-001, wo der Q2-Echttest die Allowlist-Frage aufriss):

- Architekt **und** Claude Code schlagen die Höherstufung **beratend vor** (nicht der Mensch muss es bemerken).
- Bei Zustimmung: **Stopp**, kurzer Vermerk im laufenden Dokument („hochgestuft von Sprung → Spur, weil …"), dann mit voller Zeremonie weiter. Konkret heißt das: das Vorhaben tritt zurück in Akt 2 mit voller Spur-Zeremonie (Mensch gibt nach Wirkungs-Block frei; Architekt auf Abruf).
- Das ist kein Scheitern, sondern das System, das funktioniert.
- **Herunterstufung gibt es nicht automatisch.** Wer einmal als Spur erkannt wurde, bleibt Spur.

**Eskalations-Kette vollständig:** Schritt → Sprung → Spur. Auch hier gilt nur-nach-oben.

## Akt 1 — Sondierung (Claude Code, am echten System)

**Auslöser:** eine Idee, ein Problem, geplante Produktarbeit — oder ein **Backlog-Seed**, der gezogen wird.

Claude Code erhebt in **einem** Durchgang am echten System und schreibt das Ergebnis in **ein** Dokument:

- **Ist-Fakten** — bewiesen, nicht angenommen; jeder Fakt nennt seine Quelle (Befehl/Datei/Pfad).
- **Machbarkeit** — geht das so? Was hängt dran? Was widerspricht der Idee?
- **Bündelung / Arbeitsliste** — Claude Codes eigene Einteilung (er kennt die Abhängigkeiten am System besser).
- **Stufen-Vorschlag** + pro Bündel ein **`kritisch:`-Flag** (`sicher` / `kritisch` / `sicherheitskritisch-akut`), geprüft gegen die feste Liste in `01_Spec-Format.md`.

**Das löst den alten Fakten↔Spec-Loop:** Fakten und Machbarkeit kommen **vor** der Spec, weil der, der sie erheben kann, zuerst dran ist. Keine nachgelagerte zweite Machbarkeitsrunde mehr.

### Auftrag festhalten (wörtlich) — der Maßstab

Löst ein **Mensch-Briefing** eine Spur aus, hält der Architekt (oder CC) den Auftrag **wörtlich und nummeriert** fest — in den Worten des Menschen, nicht schon in die Lösung umgedeutet — und holt eine **Ein-Zeilen-Bestätigung** („ist das dein Auftrag?"). Erst danach wird sondiert. Dieser eingefrorene Auftrag ist der Maßstab der späteren Deckungstabelle (`01_Spec-Format.md`, „Auftrags-Treue") und das Einzige, was der Mensch zur Freigabe zwingend lesen muss.

**Warum vorne:** Wird der Auftrag erst nach der Sondierung rekonstruiert, misst sich die Spec an dem, was machbar war, statt an dem, was verlangt wurde — genau die stille Scope-Amputation, gegen die diese Wand steht. Präzedenz PRIS-094: der mündliche Auftrag („CC soll sondieren, welche Fragen aus den echten Daten Sinn machen; für alle Aktionen entwickeln; jede Frage ruft ein Fokus-Menü") schrumpfte in der Spec unbemerkt auf eine fest einprogrammierte Liste + Menüs für zwei Aktionen — professionell aufbereitet, deshalb durchgewunken.

Bei **Seed-Auslöser ohne frisches Briefing** IST der Seed-Text (Soll/Absicht) der Auftrag; eine gesonderte Bestätigung entfällt.

**Generativer Auftrag → Methode, nie Liste.** Verlangt der Mensch etwas, das aus echten Daten *entstehen* soll („sondiere, welche X Sinn machen"), ist der Liefergegenstand die **Methode**, die X erzeugt — nie eine handverlesene Stichprobe. Ersetzt die Sondierung sie still durch eine statische Liste, ist das eine Wohin-Amputation (Stopp-Auslöser 1).

**Seed-Re-Prüfung:** Wird ein Seed gezogen, IST die Sondierung die Aktualitäts-Prüfung am Zeitpunkt der Abarbeitung (der Seed kann Wochen alt sein). Sie passiert genau hier, einmal, nicht doppelt.

**Pre-flight: `antritts-pruefung` nach Auftrags-Lektüre (PLAT-154 Mech 1).** Nach dem Lesen des Auftrags/Seeds — analog zum `git pull` beim Session-Start — fährt jede Session die Skill `antritts-pruefung`. Sie prüft Kollision, Doppelarbeit, Vorbedingung und Sinnhaftigkeit gegen die tatsächlich lebenden Parallel-Spuren beider Repos (kb + env_a) und rendert ein GO / STOP / VORSICHT-Verdikt. Skript: `_Betrieb/Skripte/backlog/antritts_check.py`. Fail-open: bei Discovery-Fehler → GO.

**Wo das Dokument liegt:** Spur → eigenes Sondierungs-Dokument im Arbeitsgedächtnis (`report_art: sondierung`, erbt später `spec_id`). Sprung → die Sondierung IST die kombinierte Spec, direkt als `<ID>_SPEC.md`.

**Pflicht zur Lesbarkeit (Token-Schutz):** Jede Datei, die Mensch/Architekt für Akt 2 brauchen, wird **mit vollem Pfad** benannt, nie zum Suchen freigegeben. Was der Mensch zur Freigabe braucht, bleibt auf `main` gepusht. Ein Diff ist NICHT der Default-Beleg — Ist-Stand wird direkt gelesen.

**Option 0 (Pflicht).** Jede Sondierung enthält als erste Option die ernsthafte Gegenrede: nicht tun / anders lösen / streichen — mit dem stärksten Argument dafür. Ohne Option 0 ist eine Sondierung unvollständig und nicht weiterreichbar. Empfehlung kann auf Option 0 fallen; die Wahl bleibt beim Menschen.

### Fakten-Erhebung via Subagent (Akt-1-Spezialfall der Offload-Disziplin, s. u.)

Die **read-lastige Erhebung** in Akt 1 läuft über einen read-only Subagenten (`erhebung` für Orientierung/Sweeps, Sonnet; `sondierung` für die urteils-tragende Erhebung, die direkt die Spec-Synthese speist, Opus), **nicht** im Hauptkontext — Ist-Fakten, Logbuch-/Backlog-Recherche, Code-Exploration. Dasselbe Muster gilt für read-lastige Verifikations-Sweeps in Akt 3. Subagenten laufen per Default **im Vordergrund**; die enge `run_in_background`-Ausnahme (PLAT-143) + Fail-safe bei hung-Verdacht: voller Wortlaut CLAUDE-global „Subagenten".

**Grund (Hand/Kopf):** Der Hauptthread bleibt frei fürs Urteil und wird nicht mitten in der Sondierung weg-compactet — der Subagent erhebt (Hand), der Hauptthread urteilt (Kopf).

**Quell-Verankerungs-Wand:** Der Subagent liefert `datei:zeile`-belegte Fakten, nie Prosa — der Hauptthread prüft jeden urteils-kritischen Fakt nach. Den auslösenden Seed/die Spec liest der Hauptthread selbst (Urteils-Anker).

**Was nie delegiert wird:** Machbarkeits-Urteil, Bündelung, Stufen-/Kritikalitäts-Setzung, Option 0, Spec-Synthese, jedes Wohin. Evaluator und `advisor()` bleiben eigene Instanzen (Generator ≠ Evaluator).

## Offload-Disziplin — Main = Urteils-Faden (PLAT-152)

*Run-übergreifendes Leitprinzip — gilt über **alle drei Akte**, nicht nur Akt 1. Physisch hier verankert, weil es die zwei akt-gebundenen Einzelregeln „Fakten-Erhebung via Subagent" (Akt 1, oben) und „Bau = Worker" (Akt 3, unten) als **eine** Disziplin klammert; jene bleiben als Spezialfälle gültig.*

**Das Prinzip.** Der Hauptthread (Main) trägt nur den **Urteils-Faden**. Bei langen autonomen Läufen bleibt der Lauf auf *einen* Auftrag, aber die Main-Turns bleiben *kurz*: jeder thematische Block aus Lesen / Suchen / Messen / mechanischer Ausführung wandert in einen Subagenten, der ein kompaktes Digest zurückgibt.
- *Bleibt im Main:* Entscheidung, Synthese, jedes Wohin, das Verifikations-**Verdikt**, das Lesen des auslösenden Auftrags / Seed / der Spec (Urteils-Anker).
- *Geht raus (Subagent → Digest):* jeder thematische Block aus Lesen großer Dokumente, Suchen/grep-Sweeps, Messen (Transkript-/Log-Analyse), mechanischer Ausführung nach fixer Regel.

**Die Schwelle.** Prinzip oben **plus** ein numerischer Backstop fürs Lesen: *Ein Read / Tool-Result, das du **nicht** als Urteils-Anker brauchst und das grob **> ~150 Zeilen / ~6 KB roh** ist → Subagent.* Die Urteils-Anker-Klausel schlägt den Größen-Backstop (ein großer Read, gegen den du urteilst — die Spec, der Kern-Seed — bleibt im Main trotz Größe).
- **Boden (Über-Offload ist die einzige echte Verlust-Mode):** triviale Einzel-Fakt-Lookups (ein Pfad, eine Zeile, eine Zahl) bleiben **inline** — der Dispatch-+-Digest-Roundtrip kostet sonst mehr als die Inline-Arbeit.

**Die Bündel-Regel (Turn-Kanal, nicht nur Größe).** Die Schwelle oben zielt auf den *einzelnen* großen Read; der Turn-Kanal trifft aber auch **N kleine**: mehrere read-heavy, **bündelbare** Sondier-Schritte, die *eine* Frage erheben (Verdrahtung einer Test-Bühne, Env-Handling über mehrere Container, Lokalisierung einer Logik über mehrere Dateien, eine Reihe Orientierungs-Reads für einen Bau / eine Entscheidung), gehören in **einen** Erhebungs-Dispatch (`erhebung`; `sondierung`, wenn die Recon urteils-tragend ist und direkt die Spec speist — der Opus-Boden gilt weiter), der ein Fakten-Register zurückgibt — **nicht** in N serielle Main-Bash-Calls/Reads. Jeder einzelne mag unter dem Größen-Backstop liegen und „nur ein Bash-Call" scheinen; *seriell* sind sie aber genau die N Main-Turns, die die quadratische `cache_read`-Kurve treibt — teuer ist ihre serielle **Zahl**, nicht der Einzel-Read.
- **Diskriminator = erhebende Sondierung, nicht Read-Zahl.** Bloßes Zählen macht nicht offload-pflichtig: der **Boden** bleibt (triviale Einzel-Lookups bleiben inline, auch zu mehreren), und die **gezielte Direkt-Verifikation** gelieferter Subagent-Behauptungen (Quell-Verankerungs-Wand, oben) bleibt im Main — auch zu mehreren Fakten: das ist Verdikt gegen `datei:zeile`, nicht Sondierung, **unabhängig von der Read-Zahl**. Die Bündel-Regel zielt nur auf die *erhebende* Sondierung mehrerer Sonden zu einer Frage, nie auf das Verdikt darüber und nie auf Trivial-Lookups.

**Kosten-Begründung (knapp).** Größter Kosten-Hebel ist `cache_read` (~48 % der $, quadratisch mit der Lauf-Länge); Offload greift primär über den Turn-Kanal (ein ausgelagerter N-Turn-Block kollabiert im Main auf ~1 Dispatch-Turn). Der Größen-Kanal wirkt zusätzlich, ist aber der kleinere (§3-Bezug: PLAT-149 maß nur diesen, nicht den Turn-Kanal — kein Widerspruch, andere Kostenstelle). Beleg: [`Plattform/Systemzustand/Effizienz/offload-disziplin-hundefutter-2026-06-28.md`](../../Plattform/Systemzustand/Effizienz/offload-disziplin-hundefutter-2026-06-28.md). **Durchsetzung** ist reine Agent-Doktrin, kein Hook (PLAT-149: „Sicht statt Zwang"). **Autonom-Variante:** „Main = Urteil" + die Verfassung-07-Checkpoints genügen, kein neuer Apparat — geschnitten wird ein Lauf nur bei echter Wohin-Gabelung oder wenn der Auftrag separierbar ist; Offload ersetzt nie das Splitten.

**Worked Example (illustrativ, Details: Skill [`gated-execution`](../../.claude/skills/gated-execution/SKILL.md)).** Kurzform der Schwelle: großer Einzel-Read ohne Urteils-Anker → Subagent (raus); Einzel-Fakt-Lookup → inline; Urteils-Anker (Seed/Spec/Kern-Dokument) → bleibt im Main trotz Größe; mehrere read-heavy Sonden zu *einer* Frage → ein `erhebung`-Dispatch statt N serielle Main-Turns.

**Mechanik für „Urteil bleibt, Ausführung geht".** Mechanische Ausführung mit deterministischem Korrektheits-Anteil (CLAUDE.md-Chirurgie, Massen-Refactor nach fixer Regel, Block-für-Block-Schnitt) läuft über das Muster im Skill [`gated-execution`](../../.claude/skills/gated-execution/SKILL.md): Disposition + deterministischen Verifizierer im Main bauen → Worker führt aus + liefert mechanische Evidenz → **das Verifikations-Verdikt rendert der Main**. Worker-allein-durch-Byte-Diff-gaten ist unsicher (siehe Skill).

## Akt 2 — Festlegung (im Chat, stufenabhängig)

Aus der Sondierung wird **eine** Spec, freigegeben vom Menschen — direkt ins Repo, kein Übergabe-Tanz. Im Regelfall schreibt **Claude Code** die Spec am System und legt sie mit Wirkungs-Block + Digest vor; der kalte Evaluator prüft sie vorher. Bei einer **Spur** legt der **Architekt** zur Freigabe zwingend die **visuelle Zusammenhangs-Vorlage** vor (Verständnis-Tor, unten); für das *Mitformen* der Spec ist er auf Abruf (erste Wahl bei Wohin-Gabelungen). Er liest die Sondierung direkt im Repo und erklärt sie nach dem Beratungs-Rhythmus (`02_Rollen-Protokoll.md`: Wirkung → wo es hakt → Optionen → Stopp → Prompt). So oder so entscheidet der Mensch auf Basis des aufbereiteten Wirkungs-Blocks, nicht indem er die Rohdatei selbst durcharbeitet.

### Wer formt die Spec

- **Spur:** **Claude Code schreibt die Spec** am System (auf der Sondierung aufsitzend, mit Wirkungs-Block) nach `<Geltungsbereich>/Arbeitsgedaechtnis/<ID>_SPEC.md`; der kalte Evaluator prüft die Spec-Qualität (Pflicht-Pass). **Der Mensch gibt frei** auf Basis des Wirkungs-Blocks **und der visuellen Zusammenhangs-Vorlage** (Verständnis-Tor, unten). Der **Architekt ist auf Abruf** für das *Mitformen* der Spec — bei einer echten Wohin-Gabelung zieht der Mensch ihn hinzu, dann formt der Architekt die Spec mit. **Pflicht** ist dagegen die visuelle Zusammenhangs-Vorlage zur Freigabe (Verständnis-Tor). Der Gesamtüberblick, den eine Architektur-Entscheidung braucht, ist durch den Wirkungs-Block, das Verständnis-Tor, den Evaluator-Pass und die jederzeitige Chat-Option gesichert.
- **Sprung:** **Claude Code schreibt die Spec selbst** — sie ist mit der Sondierung identisch (kombinierte Spec). **Kein Architekt by default**, aber Claude Code **berät den Menschen direkt nach demselben Beratungs-Rhythmus** (`02_Rollen-Protokoll.md`: Was ist → wo es hakt → Vorschläge mit gekennzeichneter Empfehlung → Stopp → erst dann ausführen) — die Beratungspflicht hängt an der Rolle des Beratenden, nicht am Kanal. Nur wenn Claude Code auf eine echte Gabelung / Spec-Lücke / einen Konflikt stößt, der ein Wert-Urteil braucht, eskaliert er an den Architekten (gemeldete Wohin-Gabelung). Das hält „kleine Themen" frei von Überberatung, ohne die Beratung selbst wegzulassen.
- **Schritt:** kein Akt 2. Claude Code führt direkt aus.

**Gesetzt statt verhandelt.** Stufe, Risikoklasse und Bündel-Kritikalität werden von Claude Code deterministisch gesetzt (feste Liste in `01_Spec-Format.md` + drei Aspekte) und im Entscheidungs-Digest unter Punkt 0 genannt — nicht im Dialog ausgehandelt. Der Mensch vetot bei Abweichungsbedarf. Nur-nach-oben gilt unverändert; echte Graubereiche (die zwei Testfragen aus 01 ohne klares Ergebnis) dürfen weiterhin als Frage vorgelegt werden. Die Beratung wird damit asynchron (Digest), nicht abgeschafft.

**Kalter Evaluator (Pflicht-Pass für Sprung und Spur).** Bevor eine Spec dem Menschen zur Freigabe vorgelegt (Spur) oder autonom gestartet (Sprung) wird, läuft ein kalter Evaluator-Aufruf (eigene `claude -p`-Instanz, eigener Kontext, Modell Opus / effort high). Der schreibende Agent hat Einwände-Behandlungspflicht: jeder Evaluator-Einwand wird in der Spec-Sektion „Einwände & Behandlung" entweder eingearbeitet oder explizit widerlegt. Der Evaluator hat kein Veto (beratend) — aber eine Spec mit unbehandelten Einwänden ist nicht freigabefähig (Korridor-Wand). Selbst-Evaluation ersetzt den Pass nie (Generator ≠ Evaluator).

### Auftrags-Treue ist das Mensch-Tor

Die Spec trägt als **erste Sektion** den Auftrags-Treue-Block: den wörtlichen Auftrag + die Deckungstabelle (✅ voll / 🟡 teilweise / ❌ nicht / ↪️ ersetzt, pro Punkt; Format `01_Spec-Format.md`). **Das ist die Sektion, die der Mensch zur Freigabe liest — nicht die Spec-Prosa.** Sein Tor schrumpft auf: Deckungstabelle prüfen und jedes 🟡/❌/↪️ freigeben oder vetoen.

- Durchweg ✅ → die Spec deckt den Auftrag; Freigabe ist Formsache.
- Jedes 🟡/❌/↪️ ist eine **Wohin-Entscheidung** → ausdrückliche Mensch-Freigabe (Restscope bewusst akzeptiert) oder Veto. Ohne diese Freigabe läuft die Spec **nicht** autonom an.
- Der **kalte Evaluator** bekommt den Auftrag mitgeliefert (er steht in Sektion 1 der Spec) und prüft als **erste Pflichtprüfung** die Deckung: jeder nicht-✅-Punkt ohne ausdrückliche Freigabe ist mindestens [STRUKTURELL]; ein still durch eine Liste ersetzter generativer Auftrag ist [BLOCKIEREND].

So muss der Mensch nie die ganze Spec lesen, um sicher zu sein, dass sie sein Vorhaben trifft — der Maßstab reist mit und wird von zwei Seiten geprüft (Evaluator kalt, Mensch am Tor). Restrisiko bleibt: das Tor verhindert die *Amputation* vor dem Bau, garantiert aber keine fehlerfreie Bau-Umsetzung — dafür bleibt die AK-Verifikation in Akt 3 (jeder Auftragspunkt trägt ein AK).

### Verständnis-Tor: visuelle Zusammenhangs-Vorlage (Pflicht bei Spur)

Bevor der Mensch eine **Spur**-Spec freigibt, legt der **Architekt** die Zusammenhänge **live und in Menschensprache** vor — kein Ersatz für den Wirkungs-Block, sondern sein Verständnis-Unterbau:

- **Ein Bild** — Diagramm/Skizze, die zeigt, *wie die Teile zusammenhängen* (was hängt an was, was ändert sich wo). **Live gerendert, nicht versioniert** — es muss nicht ins Repo, es muss verstanden werden.
- **Klartext-Erklärung** — in einfachen Worten, was die Spec real bewirkt und warum die Teile so zusammenspielen. Fachbegriff nur mit Halbsatz-Erklärung.

Erst **nach** dieser Vorlage entscheidet der Mensch — der Freigabe-**Akt** bleibt eine Mensch-Zeile, die **Vorlage davor ist Pflicht** (Auslöser: der Mensch verstand technisch-komplexe Specs am Tor wiederholt nicht; Konsequenz: eine Spur läuft nicht mehr rein im Terminal durch). *Gilt nur für **Spur** — Sprung/Schritt bleiben ohne erzwungene visuelle Vorlage.*

### Eine Spec-Fassung, kein Ping-Pong

Die Spec sitzt auf der bereits erhobenen Sondierung. Es gibt **keine** nachgelagerte Machbarkeitsrunde, die eine zweite Spec-Überarbeitung auslöst. Stellt sich erst in Akt 3 heraus, dass die Spec so nicht funktioniert, ist das ein **Fall C** (Korridor-Bruch, siehe unten) — Stopp, zurück in Akt 2, EINE Korrektur. Kein endloser Abgleich-Loop.

**Spec-Freigabe ist der erste der zwei zwingenden Stopps.** Hier steckt der Gesamtüberblick des Menschen.

## Akt 3 — Ausführung & Verankerung (Claude Code, am echten System)

Nach Spec-Freigabe arbeitet Claude Code **autonom im Korridor**, dessen Wände die Freigabe gezogen hat. Verlässt er den Korridor, stoppt er.

**Bau = Worker (Rollen-Grenze an der Akt-2→Akt-3-Schwelle).** Akt 2 schreibt der Architekt selbst (Spec, auf `main`). Mit der Spec-Freigabe **kippt die Rolle**: substantiellen Akt-3-Bau delegiert der Architekt an einen **Worker-Subagenten** (`worker`; Selbsttest im Worktree vor dem Merge, kalte Prüfung + Promote nach dem Merge — merge-then-prove, Verfassung 08) — er ist Architekt, nicht Arbeitstier. **Carve-out — Einmal-Schnitt vs. Schleife (PLAT-160):** Direkt in der Hand baut der Architekt nur den *Einmal-Schnitt* (trivialer/reversibler/isolierter Stufe-Schritt, klar umrissener Einmal-Fix, Doku-/Config-Edits, Urteils-Anker-Reads). Ein **mehrschrittiger Bogen** (lesen→fix→bauen→prüfen-Schleife, UI-Feintuning, Merge-/Promote-Untersuchung, Debug-Iteration, *auch ohne Spec*) geht als **ganzer Bogen an einen Schleifen-Worker** — der Architekt orchestriert/urteilt/hält das Mensch-Tor, statt ihn selbst zu leben (messbar Geld: PLAT-160-Telemetrie, Hand-Bauen im quadratisch wachsenden Main-Kontext ist der Kostentreiber). **Laufzeit-Stolperdraht:** scheitert eine per-Hand begonnene Fix-Arbeit nach der **zweiten** Bauen-Prüf-Runde oder ist eine **dritte** absehbar, übergibt der Architekt den Rest-Bogen an einen Schleifen-Worker (Briefing: `datei:zeile` + bisherige Fixe); ≤2 Runden bleiben in der Hand.

**Orchester-Rhythmus für den (nicht-spec-)Bogen.** Der Architekt *sequenziert* die Phasen selbst — ein Worker kann keinen weiteren Subagenten spawnen (Harness-Nesting-Cap, eine Ebene):

```
① dispatch Schleifen-Worker → bauen + Selbst-Test-Loop; sammelt Nur-Mensch-Items; kompakter Beweis-Report
② dispatch pruefer          → kalter Laufzeit-Pass — NUR wo das Verf08-Hybrid-Tor greift
                              (Pflicht: kundensichtbare UI + kritisch; darunter Architekt-Ermessen)
③ Mensch-Tor                → gebündelte Nur-Mensch-Klicks (nur falls Nur-Mensch-Items existieren)
④ dispatch Promote-Worker   → :test→:latest (Gating s. Verfassung 08; läuft aus main, nicht Worktree)
```

Architekt = ①–④ dispatchen + Verdikt + ③, **baut den Bogen nicht mit eigener Hand**. ② erbt das Hybrid-Tor (nicht unbedingt) — sonst feuert der teuerste Subagent auf jeden Klein-Bogen. Der Promote läuft aus dem **main-Checkout** (Deploy-Tools nie Worktree), als benannter Mensch-Wohin-Override. Grund: das „ich baue"-Momentum darf nicht über die Schwelle mitgenommen werden (PLAT-138) — auch messbar Geld (file-history-snapshots im Hauptkontext, PLAT-147). *Akt-3-Spezialfall der Offload-Disziplin (oben).* **Mechanisch + Korrektheits-Urteil:** Skill [`gated-execution`](../../.claude/skills/gated-execution/SKILL.md) — Verifizierer im Main, Ausführung am Worker. **Laufzeit-Test-Ausführung (Rolle B, PLAT-157):** Playwright-/pw_smoke-/E2E-Läufe delegiert der Architekt immer an Worker oder Prüfer, auch wenn das Binary am Host verfügbar ist — er liest die Artefakte und urteilt die Soll-Treue.

### Leitprinzip — Wohin / Wie (das Herz der Autonomie)

Der Mensch entscheidet das **Wohin** (Richtung, Wert, Strategie). Claude Code entscheidet das **Wie** (Technik, Implementierung, Methode).

**Positiv-Imperativ (NEU, gegen Übervorsicht):**
> **Sagt die Spec WAS, ist das WIE Claude Codes Pflicht — nicht seine Bitte um Erlaubnis.** Was die Spec autorisiert und Claude Code technisch selbst ausführen kann, führt er selbst aus und vollständig durch. Eine `kritisch`-Einstufung ist ein Auftrag zur Vorsicht, **kein** Auftrag zur Rückdelegation an den Menschen.

**Zwei verbotene Pseudo-Stopps (NEU, deine Painpoints als harte Negativ-Liste):**

1. **Keine Einzelbefehl-Delegation.** Claude Code gibt dem Menschen **nicht** eine Liste einzelner Befehle (SQL, Shell) zum Selbst-Ausführen, wenn er sie selbst ausführen kann. Eine `kritisch`-DB-Operation, die die Spec deckt, führt er selbst aus (mit Backup + Verifikation), statt sie dem Menschen Befehl für Befehl vorzulegen. Der Mensch ist die **letzte** Instanz nur dort, wo Claude Code physisch nicht kann (Auslöser 4) — und dann als **fertiges Skript**, nie als Einzelbefehl-Sammlung.
2. **Keine Schein-Verifikation zur Bestätigung.** Was Claude Code selbst prüfen kann (Health-Check, DB-Query-Ergebnis, Test grün), prüft er selbst und macht bei Grün weiter. Er legt das Ergebnis dem Menschen **nicht** als „ich habe geprüft, alles grün — darf ich weiter?" vor. Das ist ein Zeitfresser-Stopp, den die Umkehr-Logik verbietet. Verifikation, die ein Mensch leisten muss (reales Browser-Fenster, App-Verhalten, visuelle UI, manueller Funktionstest), bleibt Mensch-Sache — die delegiert Claude Code aktiv.

Lackmustest pro Entscheidung: „Kann ich das selbst entscheiden, ohne das Wohin zu kennen, und ist es billig rückrollbar?" Zweimal ja → kein Stopp, **auch bei `kritisch`**.

### Drei Stufen der Sichtbarkeit

1. **Stopp/Freigabe** (synchron, blockierend) — selten, nur Wohin + irreversibel.
2. **Information** (asynchron, kein Veto) — landet in **Doku / Commit / Log**, nicht in Zwischen-Chat-Statusberichten. Muss der Mensch unterwegs etwas wissen, ist es Stopp Stufe 1, nicht Information.
3. **Stille** (gar nichts im Chat) — für die Silent-Whitelist unten.

**Umkehr-Logik (tragender Satz):** Konformität ist Default. **Gemeldet wird die Abweichung, nicht die Einhaltung.** „Ich habe mich an die Spec gehalten" ist kein Output. Output entsteht nur, wo etwas *nicht* ging.

**Boden der Stille:** Stille gilt nur für Geglücktes-wie-geplant. Jedes Nicht-Können bricht sie sofort — Spec-Widerspruch → Fall C (Stopp); physisch-nicht-möglich → Mensch-Handlung (vorgezogen oder bei Entdeckung sofort gemeldet).

**Silent-Whitelist** (Claude Code prüft nur Zugehörigkeit, schätzt nicht ein):
- Schritt-Log-Zeile schreiben
- Archiv-Verschiebung (Akt-3-Abschluss)
- Übersicht-/SVG-Regenerierung (abgeleitete Ansicht)
- Commit + Push reiner Doku-Änderungen
- Aktinterne Hausarbeit im Korridor (Bündel-zu-Bündel-Übergang)
- Spec↔Sondierung-Abgleich Fall A/B (Vermerk nur in Doku/Protokoll)
- ausgeführte Befehle/SQL als Beleg (in Commit/Doku)
- **selbst geleistete Verifikation, die grün ist** (Health-Check, Test, Query) — Beleg in Commit/Doku, kein Chat-Output
- **Seed-`status:`-Fortschreibung entlang der Akte** (`offen` → `in_arbeit` → ggf. `blockiert`/`review` → `abgeschlossen`) — siehe „Seed-Status & Mission" unten

### Die Stopp-Auslöser

Grundregel: Stopp nur, wenn eine Entscheidung **strategisch UND schwer reversibel** ist — oder wenn Claude Code physisch nicht weiterkann.

1. **Richtungs-Gabelung mit Wert-Urteil** — welcher Weg hängt vom Wohin ab; reine Technik-Wahl ist nie Stopp.
2. **Schwer umkehrbar** — DB-Schema, Daten löschen, Außenwirkung, Geld.
3. **Scope-Sprengung** — Sprung → Spur; Stopp + Eskalation.
4. **Mensch muss physisch handeln** — sudo jenseits der NOPASSWD-Liste, UI-Klick, manueller/visueller Test, Vertragsklick, Snapshot-Anstoß. KEIN Wohin-Stopp, sondern ein Kann-nicht-Stopp; wird vorgezogen (siehe unten). **Mensch-Handlungen werden als fertiges Skript übergeben, nie als Einzelbefehl-Liste.**
5. **Scope-Kollision parallel arbeitender Hände** — der Kann-nicht-Stopp wie 4 (zwei Hände an derselben Datei = stiller `main`-Merge-Schaden), kein Wohin-Stopp: disjunkte Arbeit läuft ungebremst weiter. **Schutz-Mechanik (Stand PLAT-132):** strukturell, nicht claim-basiert — jeder Agent/Loop arbeitet in einem **isolierten Worktree** (eigener Branch), und das Landen auf `main` ist gegen stilles Überschreiben hart abgesichert: git lehnt einen non-fast-forward-Push **ab** (Rebase erzwungen), Force-Push auf `main` ist server-seitig + per Guard geblockt (PLAT-085), und Edits an **derselben** Datei werden als Merge-Konflikt **sichtbar** (nicht still verloren). SSOT: `Plattform/Systemzustand/parallel-agent-schutz.md`. *(Der frühere Leitstand-Claim-Ledger `claim.py` als maschinelle acquire-Sperre ist mit dem Cockpit/Windows-Transport stillgelegt — PLAT-132; der autonome Orchestrator-Pfad nutzte ihn nicht.)*

**Risikoklasse ist kein Stopp-Auslöser.** `kritisch` löst keinen Stopp aus Prinzip aus — es verpflichtet zu Vorsichtsmaßnahmen während der Ausführung (Backup vorher, tiefe Verifikation, dokumentierter Restore-Pfad) und läuft dann autonom durch. Synchron gestoppt wird nur bei Auslöser 1–4 oder Fall C. Ausnahme: `sicherheitskritisch-akut` behält einen unbedingten Vor-Stopp.

**Vorgezogene Mensch-Handlungen.** Stopps des Typs „Mensch muss physisch handeln" (Auslöser 4) werden in Akt 1 gesammelt und **an den Anfang der Ausführung gelegt** — als eine Liste „dafür brauche ich dich, bevor ich loslaufe" (inkl. etwaiger Pauschal-Freigaben + fertiger Skripte). Der Mensch arbeitet sie in einem Rutsch ab, dann läuft Claude Code autonom durch. Erst im Lauf entdeckt → **bricht die Stille sofort, wird gemeldet** — nie still weggesteckt.

**Serien (gleichartige Operation auf mehreren Instanzen).** Sind N Bündel dieselbe Operation auf N Instanzen (z. B. 5× Agent umschalten), fasst Claude Code sie in Akt 1 zu einer **Serie** zusammen statt zu N Einzelbündeln. Eine Serie hat: einen **Vor-Stopp** am Anfang (trägt alle vorgezogenen Mensch-Handlungen und — falls `sicherheitskritisch-akut` — die akut-Freigabe), einen **autonomen Durchlauf**, und **einen** Bericht am Ende.

### Korridor-Wand: Spec-Treue (Fall A/B/C)

Treffen Fakten beim Ausführen auf die Spec:
- **A — bestätigen:** Autopilot bis zum Schluss.
- **B — präzisieren** (kein Widerspruch, nur Detail): weiter, **still** — Vermerk nur im Protokoll.
- **C — widersprechen / Hardstop:** **Stopp**, egal wann. Die Freigabe-Grundlage ist hinfällig; eine geänderte Spec ist eine Wohin-Frage → Mensch. Zurück in Akt 2, EINE Korrektur, dann weiter.

Lackmustest: „Tue ich noch das, was die freigegebene Spec sagt — oder etwas, WEIL die Spec so nicht funktioniert?" Letzteres = Fall C = Stopp.

### Die zwei zwingenden Stopps

1. **Spec-Freigabe** (Ende Akt 2) — hier steckt der Gesamtüberblick des Menschen.
2. **Korridor-Bruch** (dynamisch, nur bei Fall C).

### Pauschal-Freigaben pro Zyklus

Wiederkehrende, pro-Vorkommen-freigabepflichtige Operationen können am Anfang **pauschal** für den ganzen Zyklus freigegeben werden. Pauschalierbar ist nur **Reversibles mit aus der Spec bekanntem Scope**: Compose-Edits, Container-Rebuilds, `environment_a`-Restarts. **Nie pauschalierbar** (bleibt Stopp pro Vorkommen, Auslöser 2): DB-Migration/Datenlöschung in Produktion, Traefik-/Foundation-/Netz-Konfiguration, Datei-Löschung außerhalb `knowledge-base`. Gehört in den Vor-Stopp.

Dazwischen kein synchroner Stopp **und keine Wie-Rückfragen im Chat**. Akt-interne Übergänge und Folge-Aufräumschritte (Archivierung, Status-Update, abhängige Schritt-Log-Einträge) gehören in den Korridor — werden durchgezogen, nicht erneut angefragt.

**Stufen-Staffelung der Autonomie:**
- **Schritt:** autonom komplett (war nie Freigabe-Thema).
- **Sprung:** Spec-Freigabe vorne (bzw. autonom, wenn Claude Code die Spec selbst schreibt und keine Gabelung auftritt), durchziehen, Entscheidungs-Protokoll hinten.
- **Spur:** autonom durch `sicher`- UND `kritisch`-Bündel (`kritisch` = mit Vorsichtsmaßnahmen, kein Stopp aus Prinzip); synchroner Vor-Stopp nur an `sicherheitskritisch-akut`-Bündeln sowie bei Stopp-Auslöser 1–4 / Fall C.

### Entscheidungs-Protokoll (Review-Format)

Der Mensch reviewt Entscheidungen, nicht Code. **Der Chat-/Terminal-Output am Bündel-/Zyklus-Ende ist ausschließlich das Entscheidungs-Protokoll** — vier Punkte:

Das Protokoll beginnt mit dem **Wirkungs-Block** (Format in `01_Spec-Format.md`) und führt als Punkt 0: **„Gesetzt ohne Rückfrage:"** — Stufe, Risikoklasse, Bündel-Flags, getroffene Wie-Entscheidungen in je einer Zeile. Veto-Punkt für den Menschen ist die Abschluss-Freigabe des Zyklus, nicht ein Stopp pro Setzung. Die vier inhaltlichen Punkte folgen dahinter:

0. **Gesetzt ohne Rückfrage** — Stufe, Risikoklasse, Bündel-Flags, Wie-Entscheidungen.
1. **Was ich gebaut habe** — ein Satz, das Ziel.
2. **Entscheidungen** — die Gabelungen, an denen ich nicht gestoppt habe, je eine Zeile: „X statt Y, weil …". Keine echten Gabelungen → „keine".
3. **Was du wissen solltest** — Information, kein Veto nötig.
4. **Wo ich unsicher war** — der eigentliche Review-Fokus (darf leer sein).

**Friktion ist legitimer Inhalt von Punkt 3/4 — als Beobachtung, nicht als Verdikt.** Eine echte Prozess-/Framework-Friktion gehört als beobachtete Tatsache nach Punkt 3/4 — Information, **keine** Seed-Pflicht. Gemeldet wird die *Beobachtung*, nicht das Selbst-Verdikt „Framework-Loch vs. eigene Wahl" (Musterkennung bleibt Mensch-Sache, [[reference_verifikation_oracle_typ_grenze]]). Kein neues Ritual, kein Hook, kein Reflex-Seed; im Zweifel nichts.

**Inhaltlich, nicht referenzierend:** ausgeschrieben, keine „siehe Spec/File"-Verweise; Belegmaterial bleibt in der Datei. **Eine Gestalt, nicht zwei:** die vier Punkte sind die Mindest-Substanz; die Gestalt des Outputs folgt dem **Abschluss-Report nach Größenordnung (CEO-Sicht)** im [`Dispositions-Governor.md`](../Dispositions-Governor.md).

## Verankerung beim Abschluss (Teil von Akt 3)

Von Claude Code vorgeschlagen, vom Menschen freigegeben:

**Evolutions-Methode (kalte Diagnose, Default Vereinfachen).** Lief im Zyklus etwas schlecht, geht die Diagnose an eine **kalte Instanz** (frische Session, nur die Artefakte — nie der gescheiterte Agent selbst). Default-Antwort ist **Vereinfachen/Streichen**, nicht eine neue Regel.

- Dauerhaft Gültiges wird in **Systemzustand** bzw. **Verfassung eingearbeitet** (eingearbeitet, nicht kopiert — bleibt EINE Quelle).
- **Vorwärts-Ernte in den Standards-Kanon (Pflicht-Triage).** Deckt ein Zyklus einen **fehlenden oder zu schwachen Standard** auf (kalter Audit, belegter Defekt in der Test-Abnahme, oder Akt-3-Abschluss), MUSS der Befund in [`03a_Standards-Kanon.md`](03a_Standards-Kanon.md) **triagiert** werden: neuer Eintrag `K-NN` **aufgenommen** oder mit Grund **verworfen** — Ergebnis in der Abschluss-Doku. **Kein stilles Übergehen:** ein nicht-triagierter belegter Standard-Mangel gilt als **nicht abgeschlossen** (Korridor-Wand). Maßstab „Standard-würdig": der Befund ist **wiederholbar** (sonst `verworfen: nicht wiederholbar`). Träger ist diese Regel selbst, bewusst kein zusätzlicher Hook/Apparat. *(PLAT-112.)*
- Das „Warum" wird als **Logbuch-Eintrag** verewigt, zentral in `_Betrieb/Logbuch/` (E24).
- Der Rest (Specs, Sondierungen, Deviation-Logs) wandert ins **Archiv**.
- Hatte der Zyklus einen **Backlog-Seed als Auslöser**, wird sein `status:` auf `abgeschlossen` gezogen (im selben Commit). Ketten-Kehraus beim letzten Glied — Mechanik unverändert (`01_Spec-Format.md`, „Autonome Halde"); das Kehraus-Tool `scripts/backlog/phase9_seed_archive.py` ist allein zuständig. Das Tool greift auch bei Specs ohne Seed-Backreference oder mit bereits archiviertem Seed — dann archiviert es nur den Zyklus-Satz (PLAT-047 B047-1).
- **Pflicht-Tor: Arbeitsgedächtnis leer vor dem Abschluss-Commit (seit 2026-06-10).** Alle `<Bereich>/Arbeitsgedaechtnis/<ID>_*`-Dateien des abgeschlossenen Zyklus wandern **im selben Commit** wie der `status: abgeschlossen`-Eintrag (oder als direkt folgender Commit) ins Archiv:
  ```
  python3 scripts/backlog/phase9_seed_archive.py <spec_id>
  # oder manuell:
  git mv <Bereich>/Arbeitsgedaechtnis/<ID>_* <Bereich>/Archiv/<ID>/
  ```
  Analog zur Backlog-Pflege und Doku-Synchronität: ein abgeschlossener Zyklus mit Rückständen im Arbeitsgedächtnis gilt als **nicht abgeschlossen**.
- **Eigencheck-Pflicht am Akt-3-Ende (PLAT-047/PLAT-077).** Vor dem finalen Abschluss-Commit ruft Claude Code `akt3_abschluss.py <spec_id>` auf (`check_akt3_residuen.py` Exit 0 + `phase9_seed_archive.py` + `*_ABSCHLUSS_STATEMENT.md`). Werkzeuggestützt erzwungen: pre-commit Block (6) blockiert einen `status: abgeschlossen`-Commit ohne sauberen Residuen-Check + staged Statement. Nightly-Kehraus-Sweep läuft `--report-only`.
- **Kontext-Hygiene:** Ist der Zyklus sauber geschlossen, schlägt Claude Code dem Architekten `/clear` vor und legt es in der CLI-Eingabe vor — Ausführung beim Menschen. Bei offenem Zustand (Stopp/Fall C/unbeantwortete Frage) NICHT.

Beim **Sprung** schlanker: Abschluss-Notiz ins Archiv, Logbuch-Eintrag nur bei einer bewussten Entscheidung mit Warum. Beim **Schritt** entfällt der Abschluss ganz — Schritt-Log-Zeile + Commit sind der Abschluss.

## Pflicht-Tor: Doku-Synchronität am Systemzustand (häufigste vergessene Pflicht, eigener Punkt)

Wenn ein Zyklus den **realen Systemzustand** eines Bereichs ändert — Container kommt/geht/wechselt Image, Skript, `docker-compose.yml`, Auth-/Netz-Topologie, Agent-Pipeline, neue Komponente — ODER bestehende Detail-Doku unterhalb `<Bereich>/Systemzustand/**` (außerhalb `00_Uebersicht/`) umarbeitet, MUSS **vor dem Abschluss-Commit**:

**(a)** die betroffene **Detail-Doku** auf den neuen Ist-Stand (SSOT zuerst), **und** **(b)** die abgeleitete **`00_Uebersicht/00_Bereich.md`** (ggf. SVG) nachgezogen werden. Beide Ebenen sind Korridor-Wand: fehlt (a) oder (b) = **Fall C**. *(Gilt für Spur/Sprung/Schritt, sobald der reale Systemzustand berührt wird — reine Typo-Fixes ausgenommen.)*

| Reale Änderung im Bereich | Pflicht-Update Detail-Doku | Pflicht-Update Übersicht |
|---|---|---|
| Container kommt/geht / Image-Wechsel | Detail-Doku im betroffenen Topik | Sub-Übersicht + ggf. Architektur-SVG |
| Agent-Pipeline-Änderung | Detail-Doku Agent/Pipeline | Agenten-Sub-Übersicht + SVG |
| Pricing/Strategie-Change | Detail-Doku Strategie | Strategie-Sub-Übersicht |
| Auth/Sicherheit-Topologie-Change | Detail-Doku Sicherheit | Architektur-SVG |
| Doku-Datei in `Systemzustand/<Topik>/` neu/gelöscht/umbenannt | (die Änderung selbst) | `00_Bereich.md` Detail-Quellen-Tabelle |
| Reines Wording/Typo in Detail-Datei | (kein) | **kein** Pflicht-Update |

**Skill-Kandidat-Erkennung (Teil von Akt 3, Abschluss).** Trat im Zyklus eine wiederholte Schleife auf (mehrfaches Raten-Scheitern-Neuversuchen an demselben technischen Verfahren), die ein fixiertes Verfahren verhindert hätte, schlägt Claude Code einen **Skill-Seed** vor (`klasse: prozess`, Kandidat benannt, Schleife dokumentiert). **Erkennung + Vorschlag** automatisch; die **Anlage** nie selbsttätig — durch Architekten-Freigabe, über die E4-Schreibrichtung (Chat entwirft, Claude Code schreibt). Steht im Entscheidungs-Protokoll, nicht in einem separaten Kanal.

## Pflicht-Tore (Strenge mit Notausgang)

- Ein Zyklus gilt nicht als abgeschlossen, bevor sein definierter Ausgang existiert (z. B. Logbuch-Eintrag bei einer Entscheidung; Doku-Synchronität bei System-Berührung). Claude Code WEIGERT sich, einen neuen Zyklus zu starten, wenn der alte nicht sauber geschlossen ist. *(Spur + Sprung. Schritt hat keinen offenen Zustand.)*
- **Breakout-Klappe:** Codewort `BREAKOUT` (oder `QUICK-FIX`) setzt den Prozess bewusst aus, PROTOKOLLIERT die Aussetzung, erinnert ans Nachholen. Flexibilität bricht nie das System — sie wird aufgezeichnet.
- **Autonomie-Klappe:** Stoppt Claude Code im Korridor an einem der vier Auslöser, ist das kein Abbruch, sondern Korridor-Funktion. Er legt das Entscheidungs-Protokoll des bis dahin Gelaufenen vor und benennt die offene Wohin-/Kritikalitäts-Frage. Nach Mensch-Entscheidung läuft der Korridor weiter.
- **Doku-Synchronität:** siehe eigener Abschnitt oben — Korridor-Wand, Fall C bei Fehlen.

**Abgrenzung zu E3 (nicht selbstoptimierend).** Die Autonomie weicht E3 NICHT auf. E3 verbietet, dass das System sich selbst verändert (Regeln/Verfassung) ohne Freigabe — das bleibt. **Autonom ist nur die Ausführung freigegebener Arbeit innerhalb vom Menschen gezogener Grenzen, nie das Ziehen der Grenzen.** Die Verfassung und Claude Codes Leitplanken ändert weiterhin nur der Mensch per Freigabe.

## Backlog-Pflege

**Nordstern-Regel.** Genau **eine** aktive Mission trägt `nordstern: ja` (in `_Betrieb/Missionen/00_aktive-missionen.md`, mit Ein-Satz-Definition des Zielzustands). Seeds außerhalb der Nordstern-Mission werden nicht gezogen, außer: (a) `klasse: security` mit `zugkraft: jetzt`, (b) ein Betriebs-Queue-Eintrag eskaliert nachweislich zu einem Blocker, (c) explizite Mensch-Anweisung. Neue Seeds ohne Nordstern-Bezug erhalten bei Anlage `status: eisbox` (Vorschlag durch den anlegenden Agenten, Mensch-Veto über Digest). Wechsel des Nordsterns ist Mensch-Entscheidung.

**Harte Security-Regel.** `security`-Seeds rutschen nicht ab. Ein offener `security`-Seed ist per Definition mindestens `bald` — nie `irgendwann`. `wartung`-Seeds sind nie `jetzt`, außer sie blockieren nachweislich etwas. Beide als **Default mit Begründungszwang**: Wer abweicht, schreibt eine Zeile ins Seed.

**Pflege-Routine — Übersicht im selben Commit.** `_Betrieb/Backlog/00_UEBERSICHT.md` + `.svg` sind ein Derivat der Seeds (Verfassung 03 SSOT), erzeugt von `scripts/backlog/generate_uebersicht.py` (Repo `prisment-platform`).

**Mutations-Punkte (Pflicht-Tor):** Legt Claude Code einen Seed **an** oder **archiviert** einen, läuft das Skript im **selben Commit** mit. Damit kann die Übersicht strukturell nicht veralten. Gleiches Muster wie Doku-Synchronität oben.

Hot-Files werden handgepflegt in `_Betrieb/Backlog/00_HOT-FILES.md` — getrennt von der generierten Übersicht.

**Seed-Status & Mission (laufende Pflege, Claude-Code-Hand).** Unter `_Betrieb/Missionen/` lebt ein Kanban über die Seeds. Es zeigt nur den echten Stand, wenn `status:` und `mission:` der Seeds **laufend** gepflegt werden — nicht erst beim Abschluss. **Einzige Schreibhand ist Claude Code** (E4); der Mensch übersteuert nie per Datei, sondern weist Claude Code an.

**`status:`-Werte und ihre Akt-Kopplung** (Claude Code schreibt sie automatisch, Silent-Whitelist — er erkennt nur den Akt-Zustand, urteilt nicht):

| Status          | Wann                                                                                                                                          | Ausgelöst durch                     |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------- |
| `offen`         | Seed liegt im Backlog, ungezogen                                                                                                              | Default bei Anlage                  |
| `in_arbeit`     | Seed gezogen, Akt 1 startet                                                                                                                   | Beginn Sondierung                   |
| `blockiert`     | Stopp-Auslöser 1–4, Fall C, oder Blocker einer Abhängigkeitskette offen                                                                       | Korridor-Stopp / eingefrorene Kette |
| `review`        | Akt-3-Ausführung fertig, wartet auf Mensch-Verifikation, die nur der Mensch leisten kann (reales Browser-Fenster, App-Verhalten, visuelle UI) | Übergabe ins Mensch-Review          |
| `abgeschlossen` | Verankerung komplett, Pflicht-Tore erfüllt                                                                                                    | Akt-3-Abschluss                     |

`review` ist die saubere Trennung deiner Painpoints: Was Claude Code **selbst** verifizieren kann, prüft er und macht weiter (kein `review`-Stopp). `review` entsteht **nur**, wenn eine Verifikation übrig bleibt, die physisch ein Mensch leisten muss — dann ist es zugleich ein Auslöser-4-Fall.

**`mission:`** ordnet den Seed einer laufenden missionalen Klammer zu (Kebab-case-Slug, z. B. `live-gang`; Definition + Vergabe-Mechanik in `01_Spec-Format.md`). Claude Code setzt/ändert sie nach derselben Vorschlag-Mechanik wie `klasse`/`zugkraft` — schlägt vor, Mensch revidiert, Claude Code schreibt. Hat **keinen** Einfluss auf den Prozess, dient nur Filter/Bündelung im Kanban.

Die `## 📝 Eigene Notizen`-Sektion des Seeds bleibt Mensch-Sperrzone (`01_Spec-Format.md`) — Status-/Mission-Fortschreibung lässt sie byte-identisch.

## Zwischenprojekte (Abzweigungen)

- Niemals „hineinwachsen" lassen. Stattdessen: laufenden Zyklus **pausieren**, Zwischenprojekt als eigenen Mini-Zyklus **einschieben**.
- Pause erzeugt einen **Pause-Snapshot** (wo stehen wir, nächster Schritt, geltende Annahmen).
- Wiederaufnahme erzwingt eine **Re-Sync-Prüfung** (Pflicht-Tor): Claude Code vergleicht Snapshot mit aktuellem Systemzustand und meldet betroffene Schritte. Kein Weiterarbeiten vor Re-Sync.
- Abzweigungen landen im zentralen **Backlog** mit Seed-Datei.

**Unterschied Eskalation ↔ Abzweigung:** Eskalation = *dasselbe* Vorhaben steigt in der Stufe. Abzweigung = ein *anderes* Vorhaben drängt sich dazwischen → pausieren + einschieben.
