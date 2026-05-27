---
typ: verfassung
titel: "Iterationszyklus"
stand: 2026-05-26
aenderung: "nur nach oben (nie schlechter), nur durch bewusste Freigabe des Menschen"
---

# 00 — Iterationszyklus

Der verbindliche Arbeitsablauf für jeden Zyklus. Alle drei Akteure (Mensch, Chat-Architekt, Claude Code) kennen ihn, damit Phasen-Übergänge nicht jedes Mal neu erklärt werden müssen.

## Stufen (Prozess-Tiefe skaliert mit dem Vorhaben)

Nicht jedes Vorhaben braucht denselben Prozess-Umfang. Es gibt drei Stufen, getrennt nach **Zeremonie-Bedarf**, nicht nach Thema. Die Stufe bestimmt, wie viele der neun Phasen durchlaufen werden und welche Dokumente entstehen.

| Stufe | Wofür | Dokumente | Phasen |
|---|---|---|---|
| **Spur** | Gestaltet Architektur, berührt Kundendaten/Auth/Netz-Topologie, oder Risikoklasse `kritisch`. Großes mit Wirkung über den Einzelfall hinaus. | Voll: Faktensammlung + Spec + Machbarkeit + Abschluss-Doku | alle 9 |
| **Sprung** | Konkrete, abgegrenzte Aufgabe mit überschaubarem Risiko. Ein klar umrissener Bugfix, eine einzelne Feature-Erweiterung. | Verschlankt: kombinierte Spec (Fakten + Soll inline) + kurze Abschluss-Notiz | 1, 3*, 4, 6, 7, 9 |
| **Schritt** | Trivial, reversibel, isoliert, in einem Rutsch erledigt. Typo, Doku-Update, ein Config-Wert, toter Ordner. | Keines. Eine Zeile in `<Bereich>/Schritt-Log.md` (automatisch, ohne Nachfrage). | 1, 6 |

\*Beim Sprung verschmelzen Phase 2 (Fakten) und Phase 5 (Machbarkeit) in die Spec-Erstellung (Phase 3): Claude Code erhebt nötige Fakten und prüft Machbarkeit inline, die kombinierte Spec entsteht direkt darauf. Nur grobe Abweichungen gehen zurück in den Chat.

### Einstufung gehört zur Beratung, nicht zur Alleinentscheidung des Menschen

Die Stufe wird **nicht vom Menschen allein geraten**. Der Mensch kann den Aufwand oft nicht einschätzen — was leicht aussieht, hängt manchmal an viel. Daher:

- Bei jedem Auslöser (Phase 1) schlagen **Chat-Architekt und/oder Claude Code die Stufe vor** — als Teil der Beratung, mit kurzer Begründung, was tatsächlich daran hängt.
- Der Mensch kann die vorgeschlagene Stufe **revidieren**, trägt die Einschätzung aber nicht allein.
- Die Stufe steht als Pflichtfeld `stufe:` im Front Matter jeder Spec/Notiz (Format: `01_Spec-Format.md`).

### Keine Planungs-Ebene über der Spur (PLAT-022, E36)

Es gibt genau drei Stufen — Spur, Sprung, Schritt. **Eine Spur IST das große Vorhaben.** Sie erhebt in Phase 2/5 alle Aspekte und formt in Phase 3 eine präzise Spec. Folge-Arbeit wird als Seed abgezweigt, nie als übergeordnetes Schirm-Dokument geführt. Der Begriff „Roadmap" ist als Framework-Artefakt abgeschafft. Strategie-/Produkt-Dokumente, die das Wort „Roadmap" inhaltlich verwenden (z. B. `Prisment/Systemzustand/Strategie/prisment_roadmap_businessplan.md`), sind davon ausgenommen — das ist nicht Framework-Ebene.

### Harte Regel gegen Stufen-Inflation

- **Risikoklasse `kritisch` und `sicherheitskritisch-akut` erzwingen immer Spur.** Beide können nie als Sprung oder Schritt gefahren werden — das schützt davor, Gefährliches aus Bequemlichkeit klein zu fahren. (Lücke ergänzt 2026-05-25 mit PLAT-012, aufgedeckt bei der Backlog-Migration über `seed-os-patching-broken`.)
- Die Versuchung, sich alles als „Schritt" schönzureden, ist genau die menschliche Trägheit, vor der dieses Framework an mehreren Stellen warnt. Ehrliche Einstufung ist Disziplin, kein Formalismus.

### Eskalation einer Stufe während des laufenden Vorhabens

Öffnet sich während der Arbeit ein Fass (ein Sprung entpuppt sich als Spur, weil Kritisches aufkommt — wie in PLAT-001, wo der Q2-Echttest die Allowlist-Frage aufriss):

- Chat-Architekt **und** Claude Code schlagen die Höherstufung **beratend vor** (nicht der Mensch muss es bemerken).
- Bei Zustimmung des Menschen: **Stopp**, kurzer Vermerk im laufenden Dokument („hochgestuft von Sprung → Spur, weil …"), dann mit voller Zeremonie weiter.
- Das ist kein Scheitern, sondern das System, das funktioniert. Verwandt mit dem Pausieren+Einschieben unten, aber ohne neuen Zyklus — dasselbe Vorhaben läuft auf höherer Stufe weiter.
- Herunterstufung gibt es nicht automatisch. Wer einmal als Spur erkannt wurde, bleibt Spur.

## Die neun Phasen

1. **Auslöser** — Idee, Problem, Produktarbeit oder geplanter Prozess. **Hier wird die Stufe vorgeschlagen + festgelegt** (siehe oben).
2. **Diskussion & Faktensammlung** — Chat-Architekt diskutiert die Lösung; Claude Code liefert Reports aus dem ECHTEN System (nie aus Annahme). Ergebnis: Faktensammlungs-Report (`report_art: fakten`). *(Spur. Beim Sprung: inline in Phase 3.)*
3. **Spec-Erstellung** — Die Lösung wird in eine Spec geformt (Format: `01_Spec-Format.md`). Erstellt vom Chat-Architekt. *(Spur + Sprung.)*
4. **Übergabe** — Spec geht via Claude Code ins Repo; Übergabe-Prompt an Claude Code. *(Spur + Sprung.)*
5. **Machbarkeitsanalyse** — Claude Code prüft die Spec gegen die Realität und schreibt sich seine eigene Arbeitsliste/Bündelung (`report_art: machbarkeit`). **Pflicht bei der Bündelung:** jedes Bündel erhält einzeln ein `kritisch:`-Flag (`sicher` / `kritisch` / `sicherheitskritisch-akut`), geprüft gegen die Kritikalitäts-Liste in `01_Spec-Format.md`. Die Spec-weite `risikoklasse:` ist eine **Obergrenze** für diese Einstufung, ersetzt sie aber nicht — eine `kritisch`-Spec kann und soll `sicher`-Bündel enthalten. Nur-nach-oben gilt pro Bündel (E26). Grobe Abweichungen gehen zurück in den Chat. *(Spur. Beim Sprung: inline in Phase 3, nur Abweichungen zurück in den Chat.)*
6. **Bundled Execution (autonom im freigegebenen Korridor)** — Arbeit in Bündeln, Bündelgröße risikoabhängig. Nach der Spec-Freigabe arbeitet Claude Code die Bündel **autonom im Korridor** ab (siehe Abschnitt „Autonome Ausführung im freigegebenen Korridor" unten). Er stoppt synchron nur an den zwei Pflicht-Stopps und bei einem der vier Stopp-Auslöser; sonst läuft er bis zum Entscheidungs-Protokoll durch. `kritisch`/`sicherheitskritisch-akut`-markierte Bündel bleiben synchrone Stopps. *(Alle Stufen. Beim Schritt: Claude Code führt direkt aus, committet, schreibt die Schritt-Log-Zeile.)*
7. **Abschluss-Verifikation** — Abschluss-Doku gegen die Ziele der Spec prüfen, im Chat gegengecheckt. *(Spur: volle Abschluss-Doku. Sprung: kurze Abschluss-Notiz.)*
8. **Retrospektive** — „Was nächstes Mal besser?" Auffangnetz für Optimierung (siehe `02_Rollen-Protokoll.md` → Optimierung passiert auch jederzeit, nicht nur hier). *(Spur. Sprung nur bei Bedarf.)*
9. **Abschluss** — Wissen einarbeiten (siehe unten), commit, Arbeitsgedächtnis ins Archiv. *(Spur + Sprung.)*

## Autonome Ausführung im freigegebenen Korridor

Nach der Freigabe der letzten gemeinsam erarbeiteten Phase (meist die Spec) arbeitet Claude Code autonom — aber innerhalb eines Korridors, dessen Wände die Freigabe gezogen hat. Verlässt er den Korridor, stoppt er, egal wie weit er ist.

**Abgrenzung zu E3 (nicht selbstoptimierend):** Dies weicht E3 NICHT auf. E3 verbietet, dass das System sich selbst verändert (Regeln/Verfassung) ohne Freigabe — das bleibt. Autonom ist nur die *Ausführung freigegebener Arbeit* innerhalb vom Menschen gezogener Grenzen, nie das Ziehen der Grenzen.

### Leitprinzip — Wohin/Wie

Der Mensch entscheidet das **Wohin** (Richtung, Wert, Strategie). Claude Code entscheidet das **Wie** (Technik, Implementierung, Methode). Reviews fließen entlang dieser Grenze: synchron nur beim Wohin, asynchron-informativ beim Wie. Bei Wohin-Fragen ist Claude Code strukturell unzuständig (ihm fehlt der Gesamtkontext von Geschäft und Leben) → da stoppt er. Das macht Stoppen vorhersehbar.

### Drei Stufen der Sichtbarkeit

1. **Stopp/Freigabe** (synchron, blockierend) — selten, nur Wohin + irreversibel.
2. **Information** (asynchron, kein Veto) — Claude Code lief weiter, Mensch liest später. Information landet in **Doku / Commit / Log**, nicht in Zwischen-Chat-Statusberichten. Wenn der Mensch unterwegs etwas wissen muss, ist es Stopp Stufe 1, nicht Information.
3. **Stille** (gar nichts im Chat) — **aktiv** für die feste Silent-Whitelist unten. Was geglückt-wie-geplant lief, erzeugt keinen Chat-Output; es landet nur in Doku/Commit/Log.

**Umkehr-Logik (tragender Satz):** Konformität ist die Default-Annahme. **Gemeldet wird die Abweichung, nicht die Einhaltung.** „Ich habe mich an die Spec gehalten" ist kein Output — das wird vorausgesetzt. Output entsteht nur, wo etwas *nicht* ging.

**Boden der Stille:** Stille gilt ausschließlich für Geglücktes-wie-geplant. Jedes Nicht-Können bricht sie sofort — Spec-Widerspruch → Fall C (Stopp); physisch-nicht-möglich → Mensch-Handlung (vorgezogen, oder bei Entdeckung im Lauf sofort gemeldet).

**Silent-Whitelist** (Claude Code prüft nur Zugehörigkeit, schätzt nicht ein):
- Schritt-Log-Zeile schreiben
- Archiv-Verschiebung (Phase 9)
- Übersicht-/SVG-Regenerierung (abgeleitete Ansicht)
- Commit + Push reiner Doku-Änderungen
- Phasenübergangs-Hausarbeit im Korridor (5→6, 6→7)
- Spec↔Machbarkeit-Abgleich Fall A/B (Vermerk nur in Doku/Protokoll)
- ausgeführte Befehle/SQL als Beleg (in Commit/Doku)

### Die Stopp-Auslöser

Grundregel: Stopp nur, wenn eine Entscheidung **strategisch UND schwer reversibel** ist — oder wenn Claude Code physisch nicht weiterkann. Lackmustest: „Kann ich das selbst entscheiden, ohne das Wohin zu kennen, und ist es billig rückrollbar?" Zweimal ja → kein Stopp, **auch bei `kritisch`**.

1. **Richtungs-Gabelung mit Wert-Urteil** — welcher Weg hängt vom Wohin ab; reine Technik-Wahl ist nie Stopp.
2. **Schwer umkehrbar** — DB-Schema, Daten löschen, Außenwirkung, Geld.
3. **Scope-Sprengung** — Sprung → Spur; Stopp + Eskalation.
4. **Mensch muss physisch handeln** — sudo jenseits der NOPASSWD-Liste, UI-Klick, manueller Test, Vertragsklick, Snapshot-Anstoß. Das ist KEIN Wohin-Stopp, sondern ein Kann-nicht-Stopp; er wird vorgezogen (siehe „Vorgezogene Mensch-Handlungen").

**Risikoklasse ist kein Stopp-Auslöser mehr.** `kritisch` löst keinen Stopp aus Prinzip aus — es verpflichtet zu Vorsichtsmaßnahmen während der Ausführung (Backup vorher, tiefe Verifikation, dokumentierter Restore-Pfad) und läuft dann autonom durch. Synchron gestoppt wird nur, wenn zusätzlich Auslöser 1–4 oder Fall C greift. Ausnahme: `sicherheitskritisch-akut` behält einen unbedingten Vor-Stopp (siehe Stufen-Staffelung + `01_Spec-Format.md`).

### Vorgezogene Mensch-Handlungen

Stopps des Typs „Mensch muss physisch handeln" (Auslöser 4) werden in Phase 5 gesammelt und **an den Anfang des Bündels bzw. der Serie gelegt** — als eine Liste „dafür brauche ich dich, bevor ich loslaufe" (inkl. etwaiger Pauschal-Freigaben, siehe unten). Der Mensch arbeitet sie in einem Rutsch ab, dann läuft Claude Code autonom durch. Wird eine solche Handlung erst im Lauf entdeckt (in Phase 5 nicht absehbar), **bricht sie die Stille sofort und wird gemeldet** — niemals still weggesteckt.

### Serien (gleichartige Operation auf mehreren Instanzen)

Sind N Bündel dieselbe Operation auf N Instanzen (z. B. 5× Agent umschalten, 3× Cluster-Drop), fasst Claude Code sie in Phase 5 zu einer **Serie** zusammen statt zu N Einzelbündeln. Eine Serie hat: einen **Vor-Stopp** am Anfang (trägt alle vorgezogenen Mensch-Handlungen der Serie und — falls die Serie `sicherheitskritisch-akut` ist — die akut-Freigabe), einen **autonomen Durchlauf**, und **einen** Sektions-Bericht am Ende. Das Zusammenfassen ist Claude Codes Wie-Entscheidung; der Vor-Stopp ist die eine Mensch-Freigabe.

### Korridor-Wand: Spec-Treue (Fall A/B/C)

Treffen Fakten/Machbarkeit auf die Spec:
- **A — bestätigen:** Autopilot bis zum Schluss.
- **B — präzisieren** (kein Widerspruch, nur Detail): weiter, **still** — Vermerk nur im Protokoll, kein Chat-Output. Nur Fall C bricht durch.
- **C — widersprechen / Hardstop:** **Stopp**, egal wann. Die Freigabe-Grundlage ist hinfällig; eine geänderte Spec ist eine Wohin-Frage → Mensch.

Lackmustest pro Phase: „Tue ich noch das, was die freigegebene Spec sagt — oder etwas, WEIL die Spec so nicht funktioniert?" Letzteres = Fall C = Stopp.

### Die zwei zwingenden Stopps

1. **Spec-Freigabe** (vorne) — hier steckt der Gesamtüberblick des Menschen.
2. **Korridor-Bruch** (dynamisch, nur bei Fall C).

### Pauschal-Freigaben pro Zyklus

Wiederkehrende, pro-Vorkommen-freigabepflichtige Operationen können am Zyklus-Anfang **pauschal** für den ganzen Zyklus freigegeben werden, statt bei jedem Vorkommen erneut zu stoppen. Pauschalierbar ist nur **Reversibles mit aus der Spec bekanntem Scope**: Compose-Edits, Container-Rebuilds, `environment_a`-Restarts. **Nie pauschalierbar** (bleibt Stopp pro Vorkommen, weil Auslöser 2): DB-Migration/Datenlöschung in Produktion, Traefik-/Foundation-/Netz-Konfiguration, Datei-Löschung außerhalb des `knowledge-base`-Repos. Die Pauschal-Freigabe gehört in den Vor-Stopp des Bündels/der Serie.

Dazwischen kein synchroner Stopp **und keine Wie-Rückfragen im Chat**. Phasenübergänge (Phase 5→6, 6→7, 7→9) und Folge-Aufräumschritte, die sich zwingend aus dem Bündel ergeben (Archivierung, Status-Update, abhängige Schritt-Log-Einträge), gehören in den Korridor — werden durchgezogen, nicht erneut angefragt. Rückfragen sind nur an den definierten Wänden (Spec-Freigabe vorne, Fall-C-Hardstop dynamisch).

**Insbesondere Phase 5 → Phase 6:** Liegt die Machbarkeit ohne grobe Abweichung vor (Fall A/B), geht Claude Code direkt in die Ausführung über — **keine „Machbarkeit freigeben?"-Rückfrage**. Synchron gestoppt wird nur an den beiden zwingenden Stopps, an einem der vier Stopp-Auslöser und — bei der Spur — an jedem als `sicherheitskritisch-akut` markierten Bündel. Eine **grobe Abweichung in der Machbarkeit IST ein Fall C** (die Spec funktioniert so nicht) und geht als solcher zurück in den Chat.

### Stufen-Staffelung

- **Schritt:** autonom komplett (war nie Freigabe-Thema).
- **Sprung:** Spec-Freigabe vorne (das bestehende „ein Stopp"), durchziehen, Protokoll hinten.
- **Spur:** autonom durch `sicher`- UND `kritisch`-Bündel (`kritisch` = mit Vorsichtsmaßnahmen, kein Stopp aus Prinzip); synchroner Vor-Stopp nur an `sicherheitskritisch-akut`-Bündeln sowie bei jedem der Stopp-Auslöser 1–4 / Fall C.

### Entscheidungs-Protokoll (Review-Format)

Der Mensch reviewt Entscheidungen, nicht Code. **Der Chat-Output am Bündel-/Zyklus-Ende ist ausschließlich das Entscheidungs-Protokoll** — vier Punkte in dieser Reihenfolge:

1. **Was ich gebaut habe** — ein Satz, das Ziel des Bündels.
2. **Entscheidungen** — die Gabelungen, an denen ich nicht gestoppt habe, je eine Zeile: „X statt Y, weil …". Wenn keine echten Gabelungen, dann „keine" — nicht stillschweigend leer lassen.
3. **Was du wissen solltest** — Information, kein Veto nötig.
4. **Wo ich unsicher war** — der eigentliche Review-Fokus des Menschen (darf leer sein).

**Inhaltlich, nicht referenzierend.** Die vier Punkte werden direkt im Chat ausgeschrieben — **keine „siehe Spec / siehe File / siehe Abschluss-Doku"-Verweise.** Der Mensch ist sowieso in der CLI, dort kommt das Schnellste an — wenn der Inhalt in der Datei steht und nicht im Chat, muss er Branch/File aufrufen für was, das in drei Sätzen im Chat stehen könnte. Belegmaterial (Akzeptanzkriterien-Häkchen, Diffs, Hash-Listen) bleibt weiter in der Datei; die **Substanz der Entscheidungen** und **die offenen Fragen** stehen im Chat-Text. Ausnahme nur für rein deskriptive Verweise („Details siehe `<Datei>` Z. 42–60") — und auch dort nicht ohne kurze Vorab-Substanz im Chat.

**Nicht in den Chat:** Akzeptanzkriterien-Häkchen, Phasen-Verifikations-Tabellen, Datei-Listen, Commit-Hash-Aufzählungen, „was ich geprüft habe"-Belege, Status-Updates pro Phase. Das ist Belegmaterial — gehört in Abschluss-Doku / Schritt-Log / Logbuch, nicht in den Review-Anker des Menschen. Der Mensch hat diese Belege eingerichtet, **damit er sie nicht lesen muss**.

Code-Diff und Doku-Diff bleiben verfügbar, sind aber Kür. Revision erfolgt geballt im Nachhinein (auf Branch billig), statt vorab häppchenweise freizugeben.

## Lebenszyklus-Bewegung beim Abschluss (Phase 9)

Von Claude Code vorgeschlagen, vom Menschen freigegeben:
- Dauerhaft Gültiges wird in **Systemzustand** bzw. **Verfassung eingearbeitet** (eingearbeitet, nicht kopiert — bleibt EINE Quelle).
- Das „Warum" wird als **Logbuch-Eintrag** verewigt (Format: siehe Logbuch). Das Logbuch ist **zentral** in `_Betrieb/Logbuch/` (siehe `02_Rollen-Protokoll.md` und Logbuch E24).
- Der Rest (Specs, Reports, Deviation-Logs) wandert ins **Archiv**.
- Hatte der Zyklus einen **Backlog-Seed als Auslöser**, wird in derselben Phase-9-Bewegung sein `status:` auf `abgeschlossen` gezogen. Ein Seed ohne Folge-Glieder wandert danach ins Archiv des Zyklus — **gemeinsam mit dem vollständigen Zyklus-Satz** (Spec, Abschluss-Notiz, Machbarkeit, Fakten, Deviation-Logs — soweit vorhanden); das Kehraus-Tool `scripts/backlog/phase9_seed_archive.py` ist allein zuständig, der abschließende Agent lässt die Dateien im Arbeitsgedächtnis liegen (Detail: `01_Spec-Format.md` Abschnitt „Autonome Halde"). Ein Seed, der Vorgänger einer noch offenen `abhaengig_von`-Kette ist, bleibt als abgeschlossener Lese-Anker in `seeds/` stehen; der gesamte Satz inkl. Spec wartet im Arbeitsgedächtnis, bis das letzte Glied der Kette abschließt — dann gehen alle Sätze gemeinsam in einen Archiv-Ordner. (Teil-abgearbeitet → bleibt im Backlog mit Hinweis-Block + Querverweis auf die Spec.)
- **Doku-Ebene synchronisieren:** Detail-Doku (`<Bereich>/Systemzustand/<Topik>/`) gegen die realen Änderungen auf Ist-Stand bringen, dann die abgeleitete `<Bereich>/Systemzustand/00_Uebersicht/` nachziehen — Verweistabellen, SVGs, Architektur-Tabellen. Detail bleibt SSOT, Übersicht ist abgeleitete Ansicht; beide synchron. (Pflicht-Tor: siehe unten „Doku-Synchronität".)
- **Skill-Kandidat-Erkennung:** Trat im Zyklus eine wiederholte Schleife auf (mehrfaches Raten-Scheitern-Neuversuchen an demselben technischen Verfahren), die ein fixiertes Verfahren verhindert hätte, schlägt Claude Code am Zyklus-Ende einen **Skill-Seed** vor (`klasse: prozess`, Kandidat benannt, Schleife als Anlass dokumentiert). **Erkennung und Vorschlag** sind automatisch; die **Anlage des Skills** erfolgt nie selbsttätig, sondern durch Architekten-Freigabe und über die E4-Schreibrichtung (Chat entwirft Inhalt, Claude Code schreibt). Ein Skill ist eine Leitplanke — „nie selbstoptimierend" (E3) gilt unverändert. Der Vorschlag steht im Entscheidungs-Protokoll des Abschlusses, nicht in einem separaten Kanal. (Schwester der Selbstverbesserungs-Regel in der globalen CLAUDE.md — gleiche Mechanik, gleicher Geist.)
- Das Arbeitsgedächtnis ist danach leer für den nächsten Zyklus.

Beim **Sprung** ist Phase 9 schlanker: Abschluss-Notiz ins Archiv, Logbuch-Eintrag nur, wenn eine bewusste Entscheidung mit Warum gefallen ist. Beim **Schritt** entfällt Phase 9 ganz — die Schritt-Log-Zeile + Commit sind der Abschluss.

**Kontext-Hygiene (Phase-9-Ende):** Ist der Zyklus sauber geschlossen (alle Pflicht-Tore erfüllt, kein offener Stopp), schlägt Claude Code dem Architekten `/clear` vor und legt es in der CLI-Eingabe vor — Ausführung bleibt beim Menschen, nie automatisch. Begründung im Vorschlag: dauerhaftes Wissen ist persistent im Repo (Systemzustand/Logbuch/Schritt-Log), die Session-Historie ist toter Kontext für den nächsten Zyklus. Bei offenem Zustand (Stopp/Fall C/unbeantwortete Frage) wird `/clear` NICHT vorgeschlagen.

## Pflicht-Tore (Strenge mit Notausgang)

- Ein Zyklus gilt nicht als abgeschlossen, bevor sein definierter Ausgang existiert (z. B. Logbuch-Eintrag bei einer Entscheidung). Claude Code WEIGERT sich, einen neuen Zyklus zu starten, wenn der alte nicht sauber geschlossen ist, und erinnert daran. *(Gilt für Spur + Sprung. Schritt hat keinen offenen Zustand.)*
- **Breakout-Klappe:** Das Codewort `BREAKOUT` (oder `QUICK-FIX`) setzt den Prozess bewusst aus, PROTOKOLLIERT die Aussetzung und erinnert später ans Nachholen. Flexibilität bricht nie das System — sie wird aufgezeichnet.
- **Autonomie-Klappe:** Stoppt Claude Code im Korridor an einem der vier Auslöser, ist das kein Abbruch, sondern Korridor-Funktion. Er legt das Entscheidungs-Protokoll des bis dahin Gelaufenen vor und benennt die offene Wohin-/Kritikalitäts-Frage. Nach Mensch-Entscheidung läuft der Korridor weiter.
- **Doku-Synchronität (Phase 9):** Wenn ein Zyklus den **realen Systemzustand** eines Bereichs ändert — Container kommt/geht/wechselt Image, n8n-Workflow, Skript, `docker-compose.yml`, Auth-/Netz-Topologie, neue Komponente — ODER bestehende Detail-Doku unterhalb `<Bereich>/Systemzustand/**` (außerhalb `00_Uebersicht/`) umarbeitet, MUSS im selben Zyklus VOR dem Phase-9-Commit: **(a)** die betroffene Detail-Doku im `Systemzustand/` auf den neuen Ist-Stand gebracht werden (SSOT zuerst), **und (b)** die abgeleitete `00_Uebersicht/00_Bereich.md` (ggf. thematische Sub-Übersicht inkl. SVG) nachgezogen werden. Beide Ebenen sind Korridor-Wand: fehlt (a) oder (b) beim Phase-9-Abschluss = Fall C = synchroner Stopp. Begründete Ausnahme (Bereich bewusst undokumentiert, z. B. Intern leer) gehört in die Abschluss-Doku. *(Gilt für Spur, Sprung UND Schritt — sobald der reale Systemzustand berührt wird. Nur rein doku-interne Trivialitäten ohne System-Bezug, z. B. Typo-Fix, sind ausgenommen.)* Generalisiert das Backlog-Pflege-Muster („Übersicht im selben Commit", siehe unten) auf den Systemzustand — handgepflegt statt skript-generiert, gleicher Gedanke. Schwellenwerte:

| Reale Änderung im Bereich | Pflicht-Update Detail-Doku | Pflicht-Update Übersicht |
|---|---|---|
| Container kommt/geht / Image-Wechsel | Detail-Doku im betroffenen Topik | Sub-Übersicht + ggf. Architektur-SVG |
| Neuer/entfallener n8n-Workflow | Workflow-Doku im Topik | Onboarding-/Operative-Doku-Übersicht |
| Agent-Pipeline-Änderung | Detail-Doku Agent/Pipeline | Agenten-Sub-Übersicht + SVG |
| Pricing/Strategie-Change | Detail-Doku Strategie | Strategie-Sub-Übersicht |
| Auth/Sicherheit-Topologie-Change | Detail-Doku Sicherheit | Architektur-SVG |
| Doku-Datei in `Systemzustand/<Topik>/` neu/gelöscht/umbenannt | (die Änderung selbst) | `00_Bereich.md` Detail-Quellen-Tabelle |
| Reines Wording/Typo in Detail-Datei | (kein) | **kein** Pflicht-Update |

## Backlog-Pflege

### Harte Security-Regel

**`security`-Seeds rutschen nicht ab.** Ein offener `security`-Seed ist per Definition mindestens `bald` — er darf nie auf `irgendwann` rutschen. `wartung`-Seeds sind nie `jetzt`, außer sie blockieren nachweislich etwas anderes. Beide Regeln gelten als **Default mit Begründungszwang**: Wer abweicht, schreibt eine Zeile ins Seed, warum.

Mechanik wie bei „kritisch erzwingt Spur" (E23): Disziplin durch Default, nicht durch Sperre. Flexibilität bleibt, aber das Abweichen ist sichtbar.

### Pflege-Routine — Übersicht im selben Commit

`_Betrieb/Backlog/00_UEBERSICHT.md` + `.svg` sind ein Derivat der Seeds (Verfassung 03 SSOT). Sie werden vom Skript `scripts/backlog/generate_uebersicht.py` (im `prisment-platform` Repo) erzeugt.

**Mutations-Punkte (Pflicht-Tor):** Wenn Claude Code einen Seed **anlegt** oder **archiviert**, läuft das Skript im **selben Commit** mit. Damit kann die Übersicht strukturell nicht veralten.

Das ist die Anwendung des Phase-9-Pflicht-Tors („Zyklus, der das System ändert, aktualisiert die abgeleitete Ansicht im selben Commit") auf den Backlog — kein neues Muster.

Hot-Files (strang-übergreifend nur seriell anfassbare Pfade) werden handgepflegt in `_Betrieb/Backlog/00_HOT-FILES.md` — getrennt von der generierten Übersicht, damit das Skript handgepflegtes Wissen nicht überschreibt.

## Zwischenprojekte (Abzweigungen)

- Niemals „hineinwachsen" lassen. Stattdessen: laufenden Zyklus **pausieren**, Zwischenprojekt als eigenen Mini-Zyklus **einschieben**.
- Pause erzeugt einen **Pause-Snapshot** (wo stehen wir, nächster Schritt, geltende Annahmen).
- Wiederaufnahme erzwingt eine **Re-Sync-Prüfung** (Pflicht-Tor): Claude Code vergleicht Snapshot mit aktuellem Systemzustand und meldet, welche Schritte der pausierten Spec durch zwischenzeitliche Änderungen betroffen sind. Kein Weiterarbeiten vor Re-Sync.
- Abzweigungen landen im zentralen **Backlog** (`_Betrieb/Backlog/`) mit Seed-Datei.

**Unterschied Eskalation ↔ Abzweigung:** Eskalation = *dasselbe* Vorhaben steigt in der Stufe (Sprung → Spur). Abzweigung = ein *anderes* Vorhaben drängt sich dazwischen → pausieren + einschieben.
