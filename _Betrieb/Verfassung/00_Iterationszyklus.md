---
typ: verfassung
titel: "Iterationszyklus"
stand: 2026-06-11
aenderung: "nur nach oben (nie schlechter), nur durch bewusste Freigabe des Menschen"
---

# 00 — Iterationszyklus

Der verbindliche Arbeitsablauf für jeden Zyklus. Alle drei Akteure (Mensch, Chat-Architekt, Claude Code) kennen ihn.

**Tragender Gedanke:** Der Zyklus folgt der Realität, nicht der Bürokratie. Wer am echten System sitzt (Claude Code), sondiert und führt aus. Wer den Gesamtüberblick hat (Mensch, beraten vom Chat-Architekt), legt fest. Daraus ergeben sich **drei Akte** statt einer langen Phasenkette — die alte 9-Phasen-Nummerierung ist abgelöst (Begründung: Logbuch-Eintrag zu dieser Umstellung).

---

## Die drei Akte

| Akt | Wer | Was |
|---|---|---|
| **1 — Sondierung** | Claude Code, am echten System | Fakten + Machbarkeit + Bündelung + Stufen-/Kritikalitäts-Vorschlag in EINEM Durchgang |
| **2 — Festlegung** | Mensch (Chat-Architekt auf Abruf), Kanal stufenabhängig | Spec entsteht (Sprung/Spur: Claude Code schreibt sie, Evaluator-geprüft) → Mensch gibt frei |
| **3 — Ausführung & Verankerung** | Claude Code, am echten System | Autonom im Korridor abarbeiten → Doku synchronisieren → abschließen |

Die **Stufe** (Spur / Sprung / Schritt) bestimmt, wie schwer jeder Akt wiegt und wer in Akt 2 beteiligt ist. Die Akte sind keine starren Schleusen: beim Schritt schrumpfen 1 und 2 auf null, beim Sprung verschmelzen sie.

---

## Stufen (Prozess-Tiefe skaliert mit dem Vorhaben)

Nicht jedes Vorhaben braucht denselben Prozess-Umfang. Drei Stufen, getrennt nach **Zeremonie-Bedarf**, nicht nach Thema.

| Stufe | Wofür | Akt 1 Sondierung | Akt 2 Festlegung | Dokumente |
|---|---|---|---|---|
| **Spur** | Gestaltet Architektur, berührt Kundendaten/Auth/Netz-Topologie, oder Risikoklasse `kritisch`/`sicherheitskritisch-akut`. Wirkung über den Einzelfall hinaus. | Voll, eigenes Sondierungs-Dokument | Claude Code schreibt die Spec (Wirkungs-Block); kalter Evaluator prüft; **Mensch gibt frei** (Terminal genügt). Chat-Architekt auf Abruf bei Wohin-Gabelung / visueller Aufbereitung | Sondierung + volle Spec + volle Abschluss-Doku |
| **Sprung** | Konkrete, abgegrenzte Aufgabe, überschaubares Risiko. Ein Bugfix, eine einzelne Feature-Erweiterung. | Sondierung **=** kombinierte Spec (Claude Code schreibt sie selbst) | **Kein Architekt by default** — nur bei gemeldeter Wohin-Gabelung | kombinierte Spec + kurze Abschluss-Notiz |
| **Schritt** | Trivial, reversibel, isoliert, in einem Rutsch. Typo, Doku-Update, ein Config-Wert, toter Ordner. | entfällt | entfällt | Eine Zeile in `<Bereich>/Schritt-Log.md` (automatisch) |

### Einstufung gehört zur Beratung, nicht zur Alleinentscheidung des Menschen

Die Stufe wird **nicht vom Menschen allein geraten**. Was leicht aussieht, hängt manchmal an viel.

- Bei jedem Auslöser schlägt **Claude Code (Akt 1) und/oder der Chat-Architekt die Stufe vor** — mit kurzer Begründung, was tatsächlich daran hängt.
- Der Mensch **revidiert**, trägt die Einschätzung aber nicht allein.
- Die Stufe steht als Pflichtfeld `stufe:` im Front Matter jeder Spec/Notiz (Format: `01_Spec-Format.md`).

### Keine Planungs-Ebene über der Spur (PLAT-022, E36)

Es gibt genau drei Stufen. **Eine Spur IST das große Vorhaben.** Folge-Arbeit wird als Seed abgezweigt, nie als übergeordnetes Schirm-Dokument geführt. Der Begriff „Roadmap" ist als Framework-Artefakt abgeschafft. Strategie-/Produkt-Dokumente, die das Wort inhaltlich verwenden (z. B. `Prisment/Systemzustand/Strategie/prisment_roadmap_businessplan.md`), sind ausgenommen — nicht Framework-Ebene.

**Die einzige erlaubte Ebene über der Spur ist das Projekt** (`07_Projekt-Orchestrierung.md`) — und das bleibt mit der Roadmap-Abschaffung vereinbar: Eine Roadmap bündelte *mehrere unabhängige Wohins* (verboten); ein Projekt hat **genau ein** maschinell prüfbares Akzeptanzkriterium, dem alle seine Seeds dienen. Ein-Ziel-Behälter erlaubt, Mehr-Ziel-Schirm nicht. Das Projekt ist zudem keine Stufe, sondern eine orthogonale Ebene — die „genau drei Stufen" gelten unverändert.

### Harte Regel gegen Stufen-Inflation

- **Risikoklasse `kritisch` und `sicherheitskritisch-akut` erzwingen immer Spur.** Beide können nie als Sprung oder Schritt gefahren werden — schützt davor, Gefährliches aus Bequemlichkeit klein zu fahren. (PLAT-012, aufgedeckt bei der Backlog-Migration über `seed-os-patching-broken`.)
- Die Versuchung, sich alles als „Schritt" schönzureden, ist genau die menschliche Trägheit, vor der dieses Framework warnt. Ehrliche Einstufung ist Disziplin, kein Formalismus.

### Eskalation einer Stufe während des laufenden Vorhabens

Öffnet sich während der Arbeit ein Fass (ein Sprung entpuppt sich als Spur, weil Kritisches aufkommt — wie in PLAT-001, wo der Q2-Echttest die Allowlist-Frage aufriss):

- Chat-Architekt **und** Claude Code schlagen die Höherstufung **beratend vor** (nicht der Mensch muss es bemerken).
- Bei Zustimmung: **Stopp**, kurzer Vermerk im laufenden Dokument („hochgestuft von Sprung → Spur, weil …"), dann mit voller Zeremonie weiter. Konkret heißt das: das Vorhaben tritt zurück in Akt 2 mit voller Spur-Zeremonie (Mensch gibt nach Wirkungs-Block frei; Chat-Architekt auf Abruf).
- Das ist kein Scheitern, sondern das System, das funktioniert.
- **Herunterstufung gibt es nicht automatisch.** Wer einmal als Spur erkannt wurde, bleibt Spur.

**Eskalations-Kette vollständig:** Schritt → Sprung → Spur → **Projekt**. Die letzte Stufe der Kette — Spur → Projekt — ist **keine vierte Stufe**, sondern der Übertritt auf die Projekt-**Ebene** (orthogonal zur Stufen-Achse): ein Vorhaben entpuppt sich als emergent-mehrspurig mit *einem fixen, maschinell prüfbaren Wohin*, das ein menschlich ratifizierter Abweichungs-Kontrakt + ein autonomer Orchestrator-Loop abwickelt. Mechanik, Kontrakt und Freigabe-Vollmacht: **`07_Projekt-Orchestrierung.md`**. Auch hier gilt nur-nach-oben.

---

## Akt 1 — Sondierung (Claude Code, am echten System)

**Auslöser:** eine Idee, ein Problem, geplante Produktarbeit — oder ein **Backlog-Seed**, der gezogen wird.

Claude Code erhebt in **einem** Durchgang am echten System und schreibt das Ergebnis in **ein** Dokument:

- **Ist-Fakten** — bewiesen, nicht angenommen; jeder Fakt nennt seine Quelle (Befehl/Datei/Pfad).
- **Machbarkeit** — geht das so? Was hängt dran? Was widerspricht der Idee?
- **Bündelung / Arbeitsliste** — Claude Codes eigene Einteilung (er kennt die Abhängigkeiten am System besser).
- **Stufen-Vorschlag** + pro Bündel ein **`kritisch:`-Flag** (`sicher` / `kritisch` / `sicherheitskritisch-akut`), geprüft gegen die feste Liste in `01_Spec-Format.md`.

**Das löst den alten Fakten↔Spec-Loop:** Fakten und Machbarkeit kommen **vor** der Spec, weil der, der sie erheben kann, zuerst dran ist. Keine nachgelagerte zweite Machbarkeitsrunde mehr.

**Seed-Re-Prüfung:** Wird ein Seed gezogen, IST die Sondierung die Aktualitäts-Prüfung am Zeitpunkt der Abarbeitung (der Seed kann Wochen alt sein). Sie passiert genau hier, einmal, nicht doppelt.

**Wo das Dokument liegt:**
- **Spur:** eigenes Sondierungs-Dokument im Arbeitsgedächtnis (`report_art: sondierung`, erbt später die `spec_id`).
- **Sprung:** die Sondierung IST bereits die kombinierte Spec (Fakten/Soll inline) — Claude Code schreibt sie direkt als `<ID>_SPEC.md`.

**Pflicht zur Lesbarkeit (Token-Schutz):** Jede Datei, die der Mensch oder der Chat-Architekt für Akt 2 braucht, wird **mit vollem Pfad** in der Übergabe benannt und liegt **auf `main` gepusht** (auch aus einem Worktree heraus — der relevante Stand muss auf `main`, sonst kann der Chat-Architekt ihn via MCP nicht lesen). **Verboten** ist, den Chat-Architekten suchen zu lassen: existiert-und-Pfad-genannt ist Pflicht. Ein Diff ist NICHT der Default-Beleg — der Chat-Architekt liest den Ist-Stand der Datei direkt via MCP. Diff nur, wenn der Mensch explizit „Änderung gegen vorher" sehen will.

**Option 0 (Pflicht).** Jede Sondierung enthält als erste Option die ernsthafte Gegenrede: nicht tun / anders lösen / streichen — mit dem stärksten Argument dafür, nicht dem schwächsten. Eine Sondierung ohne Option 0 ist unvollständig und darf nicht weitergereicht werden. Option 0 wird wie jede andere Option behandelt: Empfehlung kann auf sie fallen, die Wahl bleibt beim Menschen (bzw. beim Orchestrator im Projektkontrakt, wenn das Wohin unberührt bleibt).

---

## Akt 2 — Festlegung (im Chat, stufenabhängig)

Aus der Sondierung wird **eine** Spec, freigegeben vom Menschen — direkt ins Repo, kein Übergabe-Tanz. Im Regelfall schreibt **Claude Code** die Spec am System und legt sie mit Wirkungs-Block + Digest vor (Terminal genügt); der kalte Evaluator prüft sie vorher. Zieht der Mensch den **Chat-Architekten** hinzu — erste Wahl bei Wohin-Gabelungen und für visuelle Aufbereitung —, liest dieser die Sondierung live via MCP und erklärt sie nach dem Beratungs-Rhythmus (`02_Rollen-Protokoll.md`: Wirkung → wo es hakt → Optionen → Stopp → Prompt). So oder so entscheidet der Mensch auf Basis des aufbereiteten Wirkungs-Blocks, nicht indem er die Rohdatei selbst durcharbeitet.

### Wer formt die Spec

- **Spur:** **Claude Code schreibt die Spec** am System (auf der Sondierung aufsitzend, mit Wirkungs-Block) nach `<Geltungsbereich>/Arbeitsgedaechtnis/<ID>_SPEC.md`; der kalte Evaluator prüft die Spec-Qualität (Pflicht-Pass). **Der Mensch gibt frei** auf Basis des Wirkungs-Blocks — Terminal genügt. Der **Chat-Architekt ist auf Abruf, kein Pflicht-Bahnhof** (`02`/`06`): Der Mensch zieht ihn bei einer echten Wohin-Gabelung oder für visuelle Aufbereitung hinzu — dann formt der Architekt die Spec mit (UC1). Der Gesamtüberblick, den eine Architektur-Entscheidung braucht, ist durch den Wirkungs-Block, den Evaluator-Pass und die jederzeitige Chat-Option gesichert, nicht durch einen Pflicht-Kanal.
- **Sprung:** **Claude Code schreibt die Spec selbst** — sie ist mit der Sondierung identisch (kombinierte Spec). **Kein Chat-Architekt by default**, aber Claude Code **berät den Menschen direkt nach demselben Beratungs-Rhythmus** (`02_Rollen-Protokoll.md`: Was ist → wo es hakt → Vorschläge mit gekennzeichneter Empfehlung → Stopp → erst dann ausführen) — die Beratungspflicht hängt an der Rolle des Beratenden, nicht am Kanal. Nur wenn Claude Code auf eine echte Gabelung / Spec-Lücke / einen Konflikt stößt, der ein Wert-Urteil braucht, eskaliert er per UC2 an den Chat-Architekten. Das hält „kleine Themen" frei von Überberatung, ohne die Beratung selbst wegzulassen.
- **Schritt:** kein Akt 2. Claude Code führt direkt aus.

**Gesetzt statt verhandelt.** Stufe, Risikoklasse und Bündel-Kritikalität werden von Claude Code deterministisch gesetzt (feste Liste in `01_Spec-Format.md` + drei Aspekte) und im Entscheidungs-Digest unter Punkt 0 genannt — nicht im Dialog ausgehandelt. Der Mensch vetot bei Abweichungsbedarf. Nur-nach-oben gilt unverändert; echte Graubereiche (die zwei Testfragen aus 01 ohne klares Ergebnis) dürfen weiterhin als Frage vorgelegt werden. Die Beratung wird damit asynchron (Digest), nicht abgeschafft.

**Kalter Evaluator (Pflicht-Pass für Sprung und Spur).** Bevor eine Spec dem Menschen zur Freigabe vorgelegt (Spur) oder autonom gestartet (Sprung) wird, läuft ein kalter Evaluator-Aufruf (eigene `claude -p`-Instanz, eigener Kontext, Modell Opus / effort high). Der schreibende Agent hat Einwände-Behandlungspflicht: jeder Evaluator-Einwand wird in der Spec-Sektion „Einwände & Behandlung" entweder eingearbeitet oder explizit widerlegt. Der Evaluator hat kein Veto (beratend) — aber eine Spec mit unbehandelten Einwänden ist nicht freigabefähig (Korridor-Wand). Selbst-Evaluation ersetzt den Pass nie (Generator ≠ Evaluator).

### Eine Spec-Fassung, kein Ping-Pong

Die Spec sitzt auf der bereits erhobenen Sondierung. Es gibt **keine** nachgelagerte Machbarkeitsrunde, die eine zweite Spec-Überarbeitung auslöst. Stellt sich erst in Akt 3 heraus, dass die Spec so nicht funktioniert, ist das ein **Fall C** (Korridor-Bruch, siehe unten) — Stopp, zurück in Akt 2, EINE Korrektur. Kein endloser Abgleich-Loop.

**Spec-Freigabe ist der erste der zwei zwingenden Stopps.** Hier steckt der Gesamtüberblick des Menschen.

---

## Akt 3 — Ausführung & Verankerung (Claude Code, am echten System)

Nach Spec-Freigabe arbeitet Claude Code **autonom im Korridor**, dessen Wände die Freigabe gezogen hat. Verlässt er den Korridor, stoppt er.

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

**Risikoklasse ist kein Stopp-Auslöser.** `kritisch` löst keinen Stopp aus Prinzip aus — es verpflichtet zu Vorsichtsmaßnahmen während der Ausführung (Backup vorher, tiefe Verifikation, dokumentierter Restore-Pfad) und läuft dann autonom durch. Synchron gestoppt wird nur bei Auslöser 1–4 oder Fall C. Ausnahme: `sicherheitskritisch-akut` behält einen unbedingten Vor-Stopp.

### Vorgezogene Mensch-Handlungen

Stopps des Typs „Mensch muss physisch handeln" (Auslöser 4) werden in Akt 1 gesammelt und **an den Anfang der Ausführung gelegt** — als eine Liste „dafür brauche ich dich, bevor ich loslaufe" (inkl. etwaiger Pauschal-Freigaben + fertiger Skripte). Der Mensch arbeitet sie in einem Rutsch ab, dann läuft Claude Code autonom durch. Erst im Lauf entdeckt → **bricht die Stille sofort, wird gemeldet** — nie still weggesteckt.

### Serien (gleichartige Operation auf mehreren Instanzen)

Sind N Bündel dieselbe Operation auf N Instanzen (z. B. 5× Agent umschalten), fasst Claude Code sie in Akt 1 zu einer **Serie** zusammen statt zu N Einzelbündeln. Eine Serie hat: einen **Vor-Stopp** am Anfang (trägt alle vorgezogenen Mensch-Handlungen und — falls `sicherheitskritisch-akut` — die akut-Freigabe), einen **autonomen Durchlauf**, und **einen** Bericht am Ende.

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

### Stufen-Staffelung der Autonomie

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

**Inhaltlich, nicht referenzierend.** Direkt im Output ausgeschrieben — **keine „siehe Spec / siehe File"-Verweise.** Belegmaterial (Akzeptanzkriterien-Häkchen, Diffs, Hash-Listen, „was ich geprüft habe") bleibt in der Datei, NICHT im Review-Anker. Ausnahme nur für rein deskriptive Verweise („Details siehe `<Datei>` Z. 42–60") — auch dort nicht ohne kurze Vorab-Substanz.

---

## Verankerung beim Abschluss (Teil von Akt 3)

Von Claude Code vorgeschlagen, vom Menschen freigegeben:

- Dauerhaft Gültiges wird in **Systemzustand** bzw. **Verfassung eingearbeitet** (eingearbeitet, nicht kopiert — bleibt EINE Quelle).
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
- **Eigencheck-Pflicht am Akt-3-Ende (PLAT-047/PLAT-077).** Vor dem finalen Abschluss-Commit ruft Claude Code `akt3_abschluss.py <spec_id>` auf — das ruft `check_akt3_residuen.py` (Exit 0 = sauber) und `phase9_seed_archive.py` auf und generiert ein `*_ABSCHLUSS_STATEMENT.md`-Artefakt. **Exit 0 + Statement sind Voraussetzung für den Abschluss-Commit** — das Tor ist werkzeuggestützt erzwungen: Block (6) im pre-commit-Hook (aktiv seit PLAT-077 A3, 2026-06-15) blockiert jeden Commit, der eine SPEC auf `status: abgeschlossen` setzt, ohne (a) sauberen Residuen-Check und (b) staged Statement. Nightly-Kehraus-Sweep (`nightly_kehraus_sweep.py`, Crontab 04:55): läuft im Modus `--report-only` (Mensch-Tor läuft, ≥3 saubere Nächte post-PLAT-077-A1-Fix nötig für --sharp-Freigabe).
- **Kontext-Hygiene:** Ist der Zyklus sauber geschlossen, schlägt Claude Code dem Architekten `/clear` vor und legt es in der CLI-Eingabe vor — Ausführung beim Menschen. Bei offenem Zustand (Stopp/Fall C/unbeantwortete Frage) NICHT.

Beim **Sprung** schlanker: Abschluss-Notiz ins Archiv, Logbuch-Eintrag nur bei einer bewussten Entscheidung mit Warum. Beim **Schritt** entfällt der Abschluss ganz — Schritt-Log-Zeile + Commit sind der Abschluss.

---

## Pflicht-Tor: Doku-Synchronität am Systemzustand (eigener, sichtbarer Punkt)

**Das ist die häufigste vergessene Pflicht — deshalb steht sie jetzt als eigener Akt-3-Abschnitt, nicht als Unterpunkt.**

Wenn ein Zyklus den **realen Systemzustand** eines Bereichs ändert — Container kommt/geht/wechselt Image, n8n-Workflow, Skript, `docker-compose.yml`, Auth-/Netz-Topologie, Agent-Pipeline, neue Komponente — ODER bestehende Detail-Doku unterhalb `<Bereich>/Systemzustand/**` (außerhalb `00_Uebersicht/`) umarbeitet, MUSS **vor dem Abschluss-Commit**:

**(a)** die betroffene **Detail-Doku** im `Systemzustand/` auf den neuen Ist-Stand gebracht werden (SSOT zuerst), **und**
**(b)** die abgeleitete **`00_Uebersicht/00_Bereich.md`** (ggf. thematische Sub-Übersicht inkl. SVG) nachgezogen werden.

Beide Ebenen sind Korridor-Wand: fehlt (a) oder (b) beim Abschluss = **Fall C = synchroner Stopp**. Begründete Ausnahme (Bereich bewusst undokumentiert) gehört in die Abschluss-Doku.

*(Gilt für Spur, Sprung UND Schritt — sobald der reale Systemzustand berührt wird. Nur rein doku-interne Trivialitäten ohne System-Bezug, z. B. Typo-Fix, sind ausgenommen.)*

| Reale Änderung im Bereich | Pflicht-Update Detail-Doku | Pflicht-Update Übersicht |
|---|---|---|
| Container kommt/geht / Image-Wechsel | Detail-Doku im betroffenen Topik | Sub-Übersicht + ggf. Architektur-SVG |
| Neuer/entfallener n8n-Workflow | Workflow-Doku im Topik | Onboarding-/Operative-Doku-Übersicht |
| Agent-Pipeline-Änderung | Detail-Doku Agent/Pipeline | Agenten-Sub-Übersicht + SVG |
| Pricing/Strategie-Change | Detail-Doku Strategie | Strategie-Sub-Übersicht |
| Auth/Sicherheit-Topologie-Change | Detail-Doku Sicherheit | Architektur-SVG |
| Doku-Datei in `Systemzustand/<Topik>/` neu/gelöscht/umbenannt | (die Änderung selbst) | `00_Bereich.md` Detail-Quellen-Tabelle |
| Reines Wording/Typo in Detail-Datei | (kein) | **kein** Pflicht-Update |

---

## Skill-Kandidat-Erkennung (Teil von Akt 3, Abschluss)

Trat im Zyklus eine wiederholte Schleife auf (mehrfaches Raten-Scheitern-Neuversuchen an demselben technischen Verfahren), die ein fixiertes Verfahren verhindert hätte, schlägt Claude Code einen **Skill-Seed** vor (`klasse: prozess`, Kandidat benannt, Schleife dokumentiert). **Erkennung + Vorschlag** automatisch; die **Anlage** nie selbsttätig — durch Architekten-Freigabe, über die E4-Schreibrichtung (Chat entwirft, Claude Code schreibt). Steht im Entscheidungs-Protokoll, nicht in einem separaten Kanal.

---

## Pflicht-Tore (Strenge mit Notausgang)

- Ein Zyklus gilt nicht als abgeschlossen, bevor sein definierter Ausgang existiert (z. B. Logbuch-Eintrag bei einer Entscheidung; Doku-Synchronität bei System-Berührung). Claude Code WEIGERT sich, einen neuen Zyklus zu starten, wenn der alte nicht sauber geschlossen ist. *(Spur + Sprung. Schritt hat keinen offenen Zustand.)*
- **Breakout-Klappe:** Codewort `BREAKOUT` (oder `QUICK-FIX`) setzt den Prozess bewusst aus, PROTOKOLLIERT die Aussetzung, erinnert ans Nachholen. Flexibilität bricht nie das System — sie wird aufgezeichnet.
- **Autonomie-Klappe:** Stoppt Claude Code im Korridor an einem der vier Auslöser, ist das kein Abbruch, sondern Korridor-Funktion. Er legt das Entscheidungs-Protokoll des bis dahin Gelaufenen vor und benennt die offene Wohin-/Kritikalitäts-Frage. Nach Mensch-Entscheidung läuft der Korridor weiter.
- **Doku-Synchronität:** siehe eigener Abschnitt oben — Korridor-Wand, Fall C bei Fehlen.

---

## Abgrenzung zu E3 (nicht selbstoptimierend)

Die Autonomie weicht E3 NICHT auf. E3 verbietet, dass das System sich selbst verändert (Regeln/Verfassung) ohne Freigabe — das bleibt. **Autonom ist nur die Ausführung freigegebener Arbeit innerhalb vom Menschen gezogener Grenzen, nie das Ziehen der Grenzen.** Die Verfassung und Claude Codes Leitplanken ändert weiterhin nur der Mensch per Freigabe.

---

## Backlog-Pflege

### Nordstern-Regel

**Genau eine aktive Mission trägt `nordstern: ja`** (in `_Betrieb/Missionen/00_aktive-missionen.md`, mit Ein-Satz-Definition des Zielzustands). Seeds außerhalb der Nordstern-Mission werden nicht gezogen, außer: (a) `klasse: security` mit `zugkraft: jetzt`, (b) ein Betriebs-Queue-Eintrag eskaliert nachweislich zu einem Blocker, (c) explizite Mensch-Anweisung. Neue Seeds ohne Nordstern-Bezug erhalten bei Anlage `status: eisbox` (Vorschlag durch den anlegenden Agenten, Mensch-Veto über Digest). Wechsel des Nordsterns ist Mensch-Entscheidung.

### Harte Security-Regel

**`security`-Seeds rutschen nicht ab.** Ein offener `security`-Seed ist per Definition mindestens `bald` — nie `irgendwann`. `wartung`-Seeds sind nie `jetzt`, außer sie blockieren nachweislich etwas. Beide als **Default mit Begründungszwang**: Wer abweicht, schreibt eine Zeile ins Seed.

### Pflege-Routine — Übersicht im selben Commit

`_Betrieb/Backlog/00_UEBERSICHT.md` + `.svg` sind ein Derivat der Seeds (Verfassung 03 SSOT), erzeugt von `scripts/backlog/generate_uebersicht.py` (Repo `prisment-platform`).

**Mutations-Punkte (Pflicht-Tor):** Legt Claude Code einen Seed **an** oder **archiviert** einen, läuft das Skript im **selben Commit** mit. Damit kann die Übersicht strukturell nicht veralten. Gleiches Muster wie Doku-Synchronität oben.

Hot-Files werden handgepflegt in `_Betrieb/Backlog/00_HOT-FILES.md` — getrennt von der generierten Übersicht.

### Seed-Status & Mission (laufende Pflege, Claude-Code-Hand)

Unter `_Betrieb/Missionen/` lebt ein Kanban über die Seeds. Es zeigt nur den echten Stand, wenn `status:` und `mission:` der Seeds **laufend** gepflegt werden — nicht erst beim Abschluss. **Einzige Schreibhand ist Claude Code** (E4); der Mensch übersteuert nie per Datei, sondern weist Claude Code an.

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

---

## Zwischenprojekte (Abzweigungen)

- Niemals „hineinwachsen" lassen. Stattdessen: laufenden Zyklus **pausieren**, Zwischenprojekt als eigenen Mini-Zyklus **einschieben**.
- Pause erzeugt einen **Pause-Snapshot** (wo stehen wir, nächster Schritt, geltende Annahmen).
- Wiederaufnahme erzwingt eine **Re-Sync-Prüfung** (Pflicht-Tor): Claude Code vergleicht Snapshot mit aktuellem Systemzustand und meldet betroffene Schritte. Kein Weiterarbeiten vor Re-Sync.
- Abzweigungen landen im zentralen **Backlog** mit Seed-Datei.

**Unterschied Eskalation ↔ Abzweigung:** Eskalation = *dasselbe* Vorhaben steigt in der Stufe. Abzweigung = ein *anderes* Vorhaben drängt sich dazwischen → pausieren + einschieben.
