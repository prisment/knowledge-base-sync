---
typ: verfassung
titel: "Spec-, Report- & Abschluss-Format"
stand: 2026-05-28
aenderung: "nur nach oben, nur durch bewusste Freigabe"
---

# 01 — Spec-Format (und Report / Abschluss-Doku)

Die Arbeitsdokumente des Zyklus folgen einem modularen Standard: ein gemeinsamer YAML-Front-Matter-Header, plus typspezifischer Körper. Die vollständigen Vorlagen liegen in `_Betrieb/Templates/Dokument-Templates.md` — diese Verfassungsdatei legt die verbindlichen Regeln fest, das Template liefert die ausfüllbare Form.

**Welche Dokumente entstehen, hängt von der Stufe ab** (siehe `00_Iterationszyklus.md`):
- **Spur:** Sondierung + volle Spec + volle Abschluss-Doku. (Machbarkeit ist Teil der Sondierung, kein eigenes Dokument mehr.)
- **Sprung:** kombinierte Spec (Fakten/Soll inline; **Claude Code schreibt sie selbst in Akt 1**) + kurze Abschluss-Notiz.
- **Schritt:** kein Dokument, nur eine Zeile in `<Bereich>/Schritt-Log.md`.

## Verbindliche Regeln

1. **Eine Wahrheit, Zusammenfassung ist nur Ansicht.** Die Zusammenfassung am Dokumentanfang verdichtet, was darunter steht — sie ergänzt NIE Inhalt, der unten fehlt.
2. **Bewiesen, nicht angenommen.** Jeder Ist-Fakt in einem Report nennt seine Quelle (Befehl/Datei/Pfad).
3. **Front-Matter-Keys sind verbindlich identisch** über alle Dokumente. Immer `risikoklasse`, nie mal `risiko` mal `risk`. Neue Felder werden im Template ergänzt, nie ad-hoc.
4. **Spec ist die Wahrheit; Report und Abschluss-Doku erben ihre `spec_id`.** Damit ist die Kette maschinell verkettbar.
5. **`stufe` ist Pflichtfeld** in jeder Spec und Abschluss-Notiz. Sie wird beratend vorgeschlagen (Chat-Architekt/Claude Code), vom Menschen revidierbar, nie vom Menschen allein geraten (siehe `00_Iterationszyklus.md`).
6. **Restschulden tragen ihre Belegstelle.** Restschuld-Formulierungen in Abschluss-Dokus nennen die konkrete Belegstelle (Datei, Zeile oder Code-Sub-Pfad) wie Akt-1-Fakten. Eine Restschuld ohne Belegstelle ist nicht abgeschlossen — sie ist eine zweite Erhebung, die im Folge-Zyklus nochmal gemacht werden muss. Belegquelle für die Regel: Logbuch E64 (PRIS-022 L4).

## Pflicht-Felder im Front Matter

Zusätzlich zu den bestehenden Feldern:

- **`stufe:`** — `spur` | `sprung` | `schritt`. Pflicht. Bestimmt Prozess-Tiefe.
- **`art:`** — OPTIONALES Etikett zur Kategorie: `feature` | `bugfix` | `recherche` | `prozess` | `wartung` (oder weitere nach Bedarf). **Rein zum Filtern/Wiederfinden — hat KEINEN Einfluss auf den Prozess.** Die Stufe steuert den Prozess, nicht die Art. Ein Bugfix kann Schritt oder Spur sein; die `art` sagt nichts über den Aufwand.

## ID-Schema

`<KÜRZEL>-<laufende Nummer>`, fortlaufend pro Geltungsbereich.
- `PLAT` — Plattform
- `PRIS` — Prisment
- `INT` — Intern

Beispiel: `PRIS-014`, `PLAT-003`. Schritte erhalten KEINE ID (sie leben nur in `Schritt-Log.md` + Commit).

## Akzeptanzkriterien skalieren mit Risikoklasse

Kriterien sind prüfbare Zustände, keine Tätigkeiten. WIE geprüft wird, ist Claude Codes operative Sache.
- **sicher:** knapp, z. B. „Dienst läuft (Health-Check grün)".
- **kritisch:** vollständige Liste, z. B. „DNS vollständig migriert / alter Anbieter nicht mehr autoritativ / Config gesichert / Routen X, Y, Z getestet".

## Risikoklasse `kritisch` — Definition und Sparsamkeit

Risikoklasse-Werte: `sicher` / `kritisch` / `sicherheitskritisch-akut`. Andere Werte (`niedrig`, `mittel`, `hoch` etc.) sind **nicht im Wertebereich** — sind sie irgendwo erfasst, ist das ein Bestands-Drift und gehört korrigiert.

### Drei Aspekte — `kritisch` nur, wenn mindestens einer erfüllt ist

1. **Unwiederbringlichkeit:** ein Fehler kann nicht ohne Datenverlust, Vertrauensverlust oder Aussperrung zurückgerollt werden. Beispiele: SSH-Lockdown ohne Notausgang, irreversible DB-Migration auf Live-Kundendaten, einmal an Sub-Processors gesendete Klartextdaten, öffentlich versendete falsche Inhalte.
2. **Akut bewiesener Schmerz mit Außenwirkung:** es gibt einen realen Incident, nicht nur ein theoretisches Risiko. CVE-Vorfall mit dokumentierter Ausnutzung = ja. „Könnte mal CVE geben" = nein.
3. **Branch-/Tree-/Auth-Topologie:** der Zyklus ändert die Mechanik, mit der zukünftige Zyklen überhaupt sicher laufen können. Worktree-Mengen-Regel, PreToolUse-Hook scharf geschaltet, Tenant-Isolation mit fail-closed, eigenes Sicherheitsmodell für einen handelnden Agent.

### Negativ-Beispiele — was NICHT kritisch ist, auch wenn es so anfühlt

- **Architektur-Größe allein** ist nicht kritisch. Eine Spur kann groß sein und trotzdem `sicher` (z.B. Datenpunkt-Mechanik über mehrere Agents — Architektur-groß, aber rollback-fähig pro Bug).
- **Komplexität** ist nicht kritisch. Schwer zu bauen ≠ schwer zu reverten.
- **„Berührt sensible Daten"** ist nicht kritisch, wenn die Berührung sauber gekapselt ist. PII-Anonymisierungs-Layer ist additiv vor LLM-Calls; ein Bug ist Schicht-Bug, kein dauerhafter Datenverlust.
- **Externe Service-Anbindung** ist nicht kritisch, wenn sie additiv ist und ein Rollback-Pfad existiert (Token weglassen → Stub-Pfad wieder). Sie wird kritisch, sobald Endkunden-PII das System verlässt und nicht zurückholbar ist.
- **Doku-/Kategorisierungs-Arbeit** ist nie kritisch, auch wenn sie den Bestand stark verändert.

### Sparsamkeits-Klausel

Kritisch ist die Ausnahme, nicht die Norm. **Wenn mehr als die Hälfte des aktiven Backlogs `kritisch` ist, stimmt die Definition oder die Einstufung nicht — Schärfung oder Revision pflichtgemäß.** Inflation entwertet das Steuer-Signal: im Korridor-Modell (`00_Iterationszyklus.md`) bedeutet `kritisch` Pflicht-Vorsicht + autonomen Durchlauf, `sicherheitskritisch-akut` einen synchronen Vor-Stopp + harte Mengen-Regel beim Parallel-Lauf — bei Inflation der obersten Klasse kein Korridor, keine Parallelität, nur Bürokratie.

Die Schärfung steht im Backlog als `seed-kritisch-schaerfen` (Sprung, jetzt) und wird wiederholt, sobald die Quote wieder kippt.

### Begriffstrennung — Spec-Risikoklasse vs. Bündel-Flag

- **`risikoklasse:` im Spec-Front-Matter** ist die **Obergrenze** für die Bündel-Einstufung in Akt 1. Eine `kritisch`-Spec kann und soll `sicher`-Bündel enthalten; eine `sicher`-Spec kann nie kritische Bündel haben (Stufen-Inflation-Schutz; tauchen welche auf, ist die Spec falsch eingestuft und eskaliert).
- **Pro-Bündel-`kritisch:`-Flag in der Sondierung (Akt 1)** ist die operative Wand, an der der Korridor synchron stoppt. Drei Werte wie die Spec-Risikoklasse.

## Kritikalität pro Bündel (steuert Autonomie in Akt 3)

Damit Akt 3 autonom laufen kann (`00_Iterationszyklus.md`, Abschnitt „Akt 3 — Ausführung & Verankerung"), **setzt Claude Code in Akt 1 (Sondierung) pro Bündel verbindlich ein `kritisch:`-Flag** mit einem der drei Werte `sicher` / `kritisch` / `sicherheitskritisch-akut` — geprüft gegen die feste Liste unten.

Die drei Werte steuern Autonomie und Vorsicht, nicht mehr „Stopp ja/nein aus Prinzip":

- **`sicher`** — autonom, Standard-Verifikation, Auto-Rollback-Pfad.
- **`kritisch`** — autonom **mit Pflicht-Vorsicht**: Backup vor der Aktion, tiefe Verifikation, dokumentierter Restore-Pfad. **Kein Stopp aus Prinzip.** Synchron gestoppt wird nur bei Stopp-Auslöser 1–4 oder Fall C (siehe `00_Iterationszyklus.md`).
- **`sicherheitskritisch-akut`** — **unbedingter Vor-Stopp** („Architekt sitzt daneben"). Hierhin gehört alles, wo das Versagen schon im **Verifikationsfenster** (zwischen Ausführung und Prüfung) live wirkt, oder was von Natur aus irreversibel ist.

Die Spec-weite `risikoklasse:` ist **Obergrenze**, nicht Ersatz: eine `kritisch`-Spec kann und soll `sicher`-Bündel enthalten — nicht jede Aufgabe innerhalb einer kritischen Spec ist selbst kritisch. Eine `sicher`-Spec hingegen kann nie kritische Bündel haben (Stufen-Inflation-Schutz: würde so etwas auftauchen, ist die Spec falsch eingestuft und gehört eskaliert, siehe `00_Iterationszyklus.md` „Eskalation").

**Feste Liste — Claude Code erkennt nur, ob berührt; schätzt hier nicht ein.**

Test für die oberste Stufe: *Wirkt das Versagen schon im Verifikationsfenster live, oder ist es von Natur aus irreversibel?* Ja → `sicherheitskritisch-akut`. Nein, aber sensibel → `kritisch` (Vorsicht).

**`sicherheitskritisch-akut` (Vor-Stopp):**
- Tenant-Isolation / RLS (ein falscher Stand leakt live Mandantendaten).
- Auth in Produktion live (Login/Session/Token live umstellen).
- Kundendaten ändern/löschen; zentrale Datenpunkt-Definition.
- Irreversible DB-Schema-Migrationen.
- Produktions-Deploy mit Außenwirkung, gesendete Mails/Nachrichten, Geldfluss.

**`kritisch` (Vorsicht, autonom):**
- Übriges Secret-Handling / Key-Rotation (reversibel, mit Backup).
- Berechtigungen/Rollen reversibel; Cloudflare-/Edge-Sicherheitsschicht reversibel.
- Dependency-Major-Updates, Framework-Versionssprünge, neue externe Dependencies.

**Graubereich — Claude-Code-Urteil** über zwei Testfragen:
1. Billig rückrollbar ohne bleibenden Schaden? **Nein → mindestens `kritisch`.**
2. Hängt die richtige Wahl vom Wohin ab statt von Technik? **Ja → Stopp-Auslöser 1 (nicht über die Klasse, sondern über die Gabelung).**

**Nur-nach-oben:** Claude Code und der Mensch dürfen nur **hoch**stufen. Ein Listen-Treffer kann nicht weggeurteilt werden. (Korrespondiert mit dem „Tut NICHT" des Arbeitstiers in `02_Rollen-Protokoll.md`: „stuft Kritikalität nie nach unten ab, um im Autopilot zu bleiben".)

**Spec-Vermerk:** pro Nicht-Listen-Bündel nur `kritisch: sicher|kritisch|sicherheitskritisch-akut` + Halbsatz Begründung — nicht die ausformulierten Testfragen (die sind Werkzeug, nicht Lesestoff). Listen-Treffer brauchen keinen Halbsatz (die Liste IST die Begründung).

**Die Liste lebt:** wächst per Akt-3-Pflicht-Tor (Mensch gibt frei, E3-konform), wenn ein Schaden eine fehlende Kategorie aufdeckt.

## Parallel-Lauf von Spuren

Mehrere Spec-Zyklen können gleichzeitig laufen — in eigenen Git-Worktrees, jeweils eigener `tmux`-Session/Window, jeweils eigener Branch `wt/<short>` (Mechanik: PLAT-014, E31). Ebenso können innerhalb **einer** Session mehrere Seeds/Specs sequenziell abgearbeitet werden, jeder in seinem eigenen Worktree (PLAT-015, E34): **neuer Seed = neuer Worktree.**

**Selbst-Disziplin statt maschineller Wand (E34).** Anzahl gleichzeitiger Spuren und Anzahl gleichzeitiger `sicherheitskritisch-akut`-Spuren liegen vollständig in der Verantwortung des Architekten. Es gibt keine Obergrenze, keinen Hook, keine maschinelle Sperre. Wenn Konzentrationsprobleme oder häufende Merge-Konflikte auftauchen, schraubt der Architekt die Parallelität zurück. Begründung: Beschleunigung schlägt Bürokratie, der Architekt kennt die echten Konfliktquellen besser als ein Skript.

**Werkzeug zur freiwilligen Selbst-Prüfung:**
```
python3 /opt/infrastructure/environment_a/scripts/backlog/check_parallel.py <pfad1> <pfad2> [<pfad3> ...]
```
Drei Ausgabe-Klassen: ✓ `disjunkt` / ✗ `Schnittmenge zwischen Spuren` / ✗ `Hot-File-Treffer`. Das Skript ist Werkzeug, nicht Pflicht-Tor — der Architekt entscheidet, wann er es aufruft.

**`10_Kunden/`-Schutz bleibt technische Wand:** Der pre-commit-Hook im knowledge-base-Repo lehnt jeden Commit auf einem `wt/*`-Branch ab, der Dateien unter `10_Kunden/` berührt. Mandantendaten gehören nur auf `main`. Override `--no-verify` nur in begründeten Ausnahmen + Logbuch-Notiz. Begründung: das ist Datenschutz/Datenintegrität, nicht Mengen-Steuerung — bleibt deshalb hart.

### Autonome Halde (freigegebene sicher-Specs)

Aus dem Backlog ist nicht jeder Seed gleich gut maschinell ziehbar. Die Halde trennt das, was ohne Synchron-Stopp durchlaufen darf, vom Rest.

- **`autonom_ziehbar: ja`** ist **abgeleitet, nicht geurteilt**: ein Seed steht auf `ja` gdw. (a) eine freigegebene Spec für ihn existiert UND (b) diese Spec `risikoklasse: sicher` trägt. Der Agent protokolliert den Zustand, er entscheidet ihn nicht. Die Mensch-Hoheit sitzt an den zwei Vorbedingungen — Spec-Freigabe und Risikoklasse-Einstufung —, nicht am Feld selbst. Die Ableitung ist sicher, weil **nur-nach-oben** gilt (Abschnitt „Risikoklasse `kritisch`" oben): ein Agent kann sich keine Spec selbst freigeben und keine `kritisch`-Spec auf `sicher` herunterstufen.
- **Zeitpunkt der Ableitung:** Das Feld wird in **Akt 3 (Verankerung)** der jeweiligen Spec gesetzt — als Teil der ohnehin laufenden Abschluss-Bewegung des Agents, gemeinsam mit der Eintragung in `00_FREIGEGEBEN.md`. Kein separater Mechanismus, kein Skript.
- **Akt-3-Pflicht — Seed-Status synchron setzen, Ketten-Kehraus beim letzten Glied.** Der abschließende Agent zieht in Akt 3 den `status:` des eigenen Seeds (Pfad via `seed:`-Backreference der Spec) auf `abgeschlossen` — im selben Commit wie die Spec-Status-Setzung. Ist der Seed Vorgänger einer noch offenen `abhaengig_von`-Kette, bleibt er als abgeschlossener Lese-Anker in `_Betrieb/Backlog/seeds/` stehen, damit die Worker-Vorgänger-Prüfung ihn an stabiler Stelle findet (ohne synchronen Status bliebe ein Folge-Glied ewig wartend, obwohl der Vorgänger durch ist). Schließt das letzte offene Glied einer Kette ab, wandern alle abgeschlossenen Seeds dieser Kette gemeinsam ins Archiv (`<Bereich>/Archiv/<spec_id-des-letzten-Glieds>/`) — ein Kehraus, ein Ordner pro Kette. Ein Seed ohne Folge-Glieder wandert unverändert sofort beim eigenen Akt-3-Abschluss.
- **Vollständiger Zyklus-Satz, Tool-Zuständigkeit.** Der Archiv-Move umfasst nicht nur den Seed, sondern den gesamten Zyklus-Satz: Spec, Abschluss-Notiz, Machbarkeit, Fakten, Deviation-Logs (soweit vorhanden). Zuständig ist allein das Kehraus-Tool `scripts/backlog/phase9_seed_archive.py` — der abschließende Agent lässt die Dateien im Arbeitsgedächtnis liegen, das Tool räumt. Bei Kettengliedern bleibt der vollständige Satz im Arbeitsgedächtnis liegen, bis das letzte offene Glied der Kette abschließt; dann wandern alle Sätze gemeinsam in einen Archiv-Ordner. Das Liegenlassen ist gewollt, kein Drift — ein voreiliger Einzel-Move risse einen Lese-Anker weg, den ein paralleler Zweig noch braucht.
- **Halde-Datei `_Betrieb/Backlog/00_FREIGEGEBEN.md`** listet diese Seeds **handgepflegt-kettenbewusst**: unabhängige Stränge als getrennte Blöcke, innerhalb eines Blocks regelt das bestehende `abhaengig_von` die Reihenfolge. Reihenfolge = Priorität (vom Menschen gesetzt). Analog `00_HOT-FILES.md`: das Übersichts-Skript liest und überschreibt sie nicht.
- **Blocker-Regel:** Ein Blocker — Stopp-Auslöser nach `00_Iterationszyklus.md`, Fall C, oder eine Abschluss-Notiz, die Nacharbeit fordert — **friert die betroffene Abhängigkeitskette ein**. Kein abhängiges Folge-Glied wird gezogen, solange der Blocker offen ist. Unabhängige Stränge in anderen Blöcken bleiben unberührt.
- **Abgrenzung:** Die Halde definiert nur das „Was ist ziehbar". Die **Worker-/Loop-Mechanik** (wer zieht, wann, wie viele parallel, Lock-File etc.) ist **nicht Teil dieser Regel** und wird als eigene Spur gebaut.

## Bündelung gehört NICHT in die Spec

Die Spec definiert das WAS (Ziel, Soll-Zustand, Akzeptanzkriterien). Die Bündelung/Arbeitsliste erarbeitet sich Claude Code selbst in der Sondierung (Akt 1) — er kann Abhängigkeiten am echten System besser einschätzen. *(Beim Sprung: inline in der kombinierten Spec, knapp.)*

## Backlog-Felder (Seeds)

Seeds in `_Betrieb/Backlog/seeds/` führen zusätzlich zu den allgemeinen Front-Matter-Feldern drei Pflichtfelder, über die das Übersichts-Skript priorisiert und das Backlog lesbar wird:

- **`klasse:`** — WAS für ein Bedarf. Fünf Werte:
  - `security` — Sicherheit.
  - `qualitaet` — Stabilität/Integrity. Voraussetzung, dass Features tragen.
  - `feature` — Kundenwert, neue Fähigkeit.
  - `prozess` — Framework-eigene Vorhaben (Verfassung, Backlog, Skills, Feature-Flags, Autonomie). Eigene Klasse, weil Bedarfs-Klassen das Fundament nicht abbilden.
  - `wartung` — Kosmetik, Aufräumen, Drift.

- **`zugkraft:`** — WIE bald nötig. Drei Werte:
  - `jetzt` — blockiert, akut, oder Kunde wartet.
  - `bald` — absehbar gebraucht, kein Blocker.
  - `irgendwann` — sinnvoll, kein Datum.

- **`beruehrt:`** — Liste der Pfade/Ordner, die der Seed bei Umsetzung anfasst. **Ordner-Ebene** (z. B. `langgraph/`, `pwa/`) — gröber, robuster, weniger Pflege als datei-genau. Bei unklarem Footprint leer/grob, wird beim Spec-Start präzisiert.

- **`status:`** — `offen` | `in_arbeit` | `blockiert` | `review` | `abgeschlossen`. **Pflichtfeld.** Default `offen` bei Anlage. **Abgeleitet aus dem Akt-Zustand, nicht frei geurteilt** — Claude Code schreibt automatisch (Silent-Whitelist; Akt-Kopplung siehe `00_Iterationszyklus.md` Abschnitt „Seed-Status & Mission"). Werte:
  - `offen` — Seed liegt im Backlog, ungezogen.
  - `in_arbeit` — Seed gezogen, Akt 1 läuft oder Folgeakt.
  - `blockiert` — Stopp-Auslöser 1–4 / Fall C / Blocker einer Abhängigkeitskette offen.
  - `review` — Akt-3-Ausführung fertig, wartet auf Mensch-Verifikation, die nur der Mensch leisten kann (reales Browser-Fenster, App-Verhalten, visuelle UI).
  - `abgeschlossen` — Verankerung komplett, Pflicht-Tore erfüllt (Endwert; konsistent mit Abschnitt „Autonome Halde").

**Optionale Felder:**

- **`autonom_ziehbar:`** — `ja` | `nein`. **Default `nein`** (Feld darf weggelassen werden). **Abgeleiteter Zustand, kein eigenes Urteil**: `ja` gdw. (a) eine freigegebene Spec für den Seed existiert UND (b) diese Spec `risikoklasse: sicher` trägt. Die Mensch-Hoheit sitzt an diesen zwei Vorbedingungen, nicht am Feld selbst. Bedeutung, Zeitpunkt und Halde-Regeln im Abschnitt „Autonome Halde" unten.

- **`mission:`** — Kebab-case-Slug, der den Seed einer aktuell laufenden, missionalen Klammer zuordnet (z. B. `live-gang`, `kunde-2`, `framework`). Genau eine Mission pro Seed; leerer Wert (oder weggelassen) heißt „keine Mission". Wird im Obsidian-Dashboard zum Filtern und Bündeln genutzt; hat KEINEN Einfluss auf den Prozess. Liste der aktuell aktiven Missions-Slugs liegt in `_Betrieb/Missionen/00_aktive-missionen.md` (handgepflegt).

- **`spec_id:`** — Backreference auf die zugehörige Spec, sobald sie existiert (gesetzt in Akt 2, wenn der Seed in `in_arbeit` geht). **Format: Obsidian-Wikilink auf die Spec-Datei**, nicht nur die nackte ID — `spec_id: "[[Plattform/Arbeitsgedaechtnis/PLAT-046_SPEC]]"`. Begründung: ein Klick im Editor springt direkt zur Spec; die nackte ID zwingt zur manuellen Suche. Wert weggelassen, solange noch keine Spec existiert (Seed im Backlog ungezogen).

**Vergabe-Mechanik** (für `klasse` / `zugkraft` / `beruehrt` / `mission`, analog `stufe`): Chat-Architekt/Claude Code schlägt mit Begründung vor, Mensch revidiert/gibt frei. Niemals vom Menschen allein geraten. (`status` und `autonom_ziehbar` fallen nicht hierunter — sie werden abgeleitet/fortgeschrieben, nicht beraten-vergeben.)

**Sperrzone „Eigene Notizen":** Jeder Seed endet mit einer Sektion `## 📝 Eigene Notizen`. Diese Sektion ist Mensch-Sperrzone. Claude Code darf sie LESEN (Kontext für Entscheidungen), aber NIE überschreiben, löschen oder umformulieren. Bei Re-Writes oder Auto-Edits eines Seeds bleibt der Inhalt dieser Sektion byte-identisch erhalten. Tritt ein Konflikt auf (Seed-Re-Write würde die Sektion berühren), stoppt Claude Code synchron und fragt den Menschen. Stub-Form siehe `_Betrieb/Templates/Dokument-Templates.md` (Body-Schluss im Backlog-Seed-Abschnitt).

**Querverweis:** Das `kritisch`-Flag pro Bündel (für autonome Korridor-Ausführung) folgt derselben Vergabe-Mechanik wie `klasse`/`zugkraft`. Definition siehe Abschnitt „Kritikalität pro Bündel" oben.

## Schritt-Log (Stufe Schritt)

Jeder Geltungsbereich hat eine Datei `<Bereich>/Schritt-Log.md`. Claude Code hängt **automatisch, ohne Nachfrage** pro ausgeführtem Schritt eine Zeile an:

```
| 2026-05-24 | <was, ein Satz> | <commit-hash> |
```

Das ist das Sicherheitsnetz gegen „verschwunden in der Git-Historie": ein Schritt ist über Obsidian auffindbar, ohne dass er ein eigenes Arbeitsdokument oder einen Archiv-Eintrag erzeugt. Kein Logbuch, kein Abschluss, keine Verankerung.
