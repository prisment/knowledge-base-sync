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

**Über der Spur liegt — orthogonal — die Projekt-Ebene** (`07_Projekt-Orchestrierung.md`). Sie führt zwei zusätzliche Dokument-Varianten + einen Seed-typ:
- **Seed-typ `projekt`** — ein Seed, der als Projekt geboren wird (statt `typ: backlog_seed`). Treibt die Sondierungs-Tiefe auf **Spur** (volle Projekt-Sondierung) und löst den Orchestrator-Pfad statt der interaktiven Abarbeitung aus. Alternativ entsteht ein Projekt per Eskalation Spur→Projekt (`00_Iterationszyklus.md`).
- **`typ: projekt-sondierung`** — die Projekt-Sondierung IST zugleich der **Abweichungs-Kontrakt** (Akt 1 + Frame in einem); vom Menschen in Akt 2 ratifiziert. Ausfüllbare Form: `_Betrieb/Templates/Dokument-Templates.md`, Abschnitt „Projekt-Sondierung / Abweichungs-Kontrakt".
- **`typ: missions-journal`** — der kalt-lesbare, verdichtbare Zustandsspeicher des Orchestrator-Loops (der Orchestrator liest primär den „AKTUELLER STAND"-Block). Form ebd., Abschnitt „Missions-Journal".

## Verbindliche Regeln

1. **Eine Wahrheit, Zusammenfassung ist nur Ansicht.** Die Zusammenfassung am Dokumentanfang verdichtet, was darunter steht — sie ergänzt NIE Inhalt, der unten fehlt.
2. **Bewiesen, nicht angenommen.** Jeder Ist-Fakt in einem Report nennt seine Quelle (Befehl/Datei/Pfad).
3. **Front-Matter-Keys sind verbindlich identisch** über alle Dokumente. Immer `risikoklasse`, nie mal `risiko` mal `risk`. Neue Felder werden im Template ergänzt, nie ad-hoc.
4. **Spec ist die Wahrheit; Report und Abschluss-Doku erben ihre `spec_id`.** Damit ist die Kette maschinell verkettbar.
5. **`stufe` ist Pflichtfeld** in jeder Spec und Abschluss-Notiz. Sie wird beratend vorgeschlagen (Architekt/Claude Code), vom Menschen revidierbar, nie vom Menschen allein geraten (siehe `00_Iterationszyklus.md`).
6. **Restschulden tragen ihre Belegstelle.** Restschuld-Formulierungen in Abschluss-Dokus nennen die konkrete Belegstelle (Datei, Zeile oder Code-Sub-Pfad) wie Akt-1-Fakten. Eine Restschuld ohne Belegstelle ist nicht abgeschlossen — sie ist eine zweite Erhebung, die im Folge-Zyklus nochmal gemacht werden muss. Belegquelle für die Regel: Logbuch E64 (PRIS-022 L4).

## Pflicht-Felder im Front Matter

Zusätzlich zu den bestehenden Feldern:

- **`stufe:`** — `spur` | `sprung` | `schritt`. Pflicht. Bestimmt Prozess-Tiefe.
- **`art:`** — OPTIONALES Etikett zur Kategorie: `feature` | `bugfix` | `recherche` | `prozess` | `wartung` (oder weitere nach Bedarf). **Rein zum Filtern/Wiederfinden — hat KEINEN Einfluss auf den Prozess.** Die Stufe steuert den Prozess, nicht die Art. Ein Bugfix kann Schritt oder Spur sein; die `art` sagt nichts über den Aufwand.
- **`laufzeit_ausfuehrer:`** — `worker` | `pruefer`. `architekt` ist nie gültig — Laufzeit-Tests
  führt der Architekt nie selbst aus (Rolle B, PLAT-157 Gate A). Zwei Trigger-Stufen:
  - **HART (grep-bar, Pflicht-Tor):** Trägt die Spec `kundensichtbar: true`, MUSS dieses Feld
    vorhanden und gültig sein. Fehlt es oder ist der Wert ungültig → Evaluator-Pass meldet
    [STRUKTURELL]. Deterministisch prüfbar: zwei Frontmatter-Felder, kein LLM-Urteil nötig.
  - **WEICH (Urteil):** Bei Specs mit Laufzeit-Test-AKs ohne `kundensichtbar: true` prüft der
    Evaluator per Urteil — meldet [STRUKTURELL] wenn `architekt` gesetzt oder Laufzeit-Test-AK
    ohne erkennbaren Ausführer vorliegt.

## ID-Schema

`<KÜRZEL>-<laufende Nummer>`, fortlaufend pro Geltungsbereich.
- `PLAT` — Plattform
- `PRIS` — Prisment
- `INT` — Intern

Beispiel: `PRIS-014`, `PLAT-003`. Schritte erhalten KEINE ID (sie leben nur in `Schritt-Log.md` + Commit).

**Eine ID = genau eine Spec.** Zwei unzusammenhängende Vorhaben bekommen nie dieselbe ID. Sonst kollidieren ihre Zyklus-Artefakte auf dem Dateinamen — `<ID>_EVAL_<n>.md`, `<ID>_RECHECK.md`, Abschluss-Doku — und der Evaluator-Pass des einen überschreibt den des anderen. (Präzedenz PRIS-094: zwei Specs unter einer ID → die EVAL-Datei der Analytics-Spec verdrängte die der Fokus-First-Spec, deren kalter Pass damit faktisch nie lief.) Folgt aus „ein Behälter = genau ein Wohin" (`00_Iterationszyklus.md`). Vor jeder neuen Spec gilt der `next-spec-id`-Skill als Pflicht — nie raten.

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

**Kollisions-Schutz, keine Anzahl-Sperre (E34-Revision, PLAT-103-Prinzip, Mensch-Freigabe 2026-06-22; Mechanik PLAT-132).** Kollidierende Arbeit wird gestoppt — aber **nie** allein wegen der Anzahl. **Der Schutz ist strukturell** (Stand PLAT-132): jeder Loop/Agent arbeitet in einem **isolierten Worktree** (eigener Branch); zwei Hände an derselben Datei kollidieren erst beim Landen, und dort schützt git hart — non-fast-forward-Push wird **abgelehnt** (Rebase erzwungen), Force-Push auf `main` ist geblockt (PLAT-085), Same-File-Edits werden als Merge-Konflikt **sichtbar** statt still überschrieben. SSOT: `Plattform/Systemzustand/parallel-agent-schutz.md`. **Disjunkte Arbeit wird nie gebremst** — der Unterschied zu einer Anzahl-Wand. Anzahl-Steuerung bleibt Architekten-Sache (kein Loop-Zähler, keine Obergrenze). *(Der frühere maschinelle Claim-acquire-Ledger `claim.py` ist mit dem Cockpit/Windows-Transport stillgelegt — PLAT-132; er war nie scharf im interaktiven Alltag und der autonome Orchestrator nutzte ihn nicht.)*

> **Historischer Vermerk — die abgelöste E34-Selbstdisziplin (vor PLAT-103):** „Anzahl gleichzeitiger Spuren und Anzahl gleichzeitiger `sicherheitskritisch-akut`-Spuren liegen vollständig in der Verantwortung des Architekten. Es gibt keine Obergrenze, keinen Hook, keine maschinelle Sperre. … Beschleunigung schlägt Bürokratie, der Architekt kennt die echten Konfliktquellen besser als ein Skript." Diese Wette galt für **interaktive** Parallelität mit Mensch-pro-Strang. Der autonome Mehr-Loop-Betrieb hat diesen Menschen-pro-Strang nicht; belegte Races (Doppel-Spec-ID, FAKTEN-Korruption, stiller Übersicht-Verlust beim Rebase) waren der Gegenbeweis. PLAT-103 nimmt E34 nur den **Kollisions**-, nicht den **Anzahl**-Teil — nur-nach-oben.

**PLAT-014-Mengenregel abgelöst (Mensch-Freigabe 2026-06-22).** Die geerbte Anzahl-Regel „nie zwei `kritisch`/`sicherheitskritisch-akut` parallel" (`KRITISCH_KLASSEN` in `check_parallel.py`) widerspricht dem Prinzip „nur Kollision, nie Anzahl" — zwei **disjunkte** `kritisch`-Specs würden sonst unnötig geblockt. Sie wird durch den strukturellen Kollisions-Schutz (Worktree-Isolation + git-non-ff-Ablehnung, s. o.) **ersetzt**. Die eigentliche Code-Entfernung liegt im `prisment-platform`-Repo (`scripts/backlog/check_parallel.py`) und ist ein eigener Folge-Schritt; diese Verfassungs-Stelle ist die maßgebliche Entscheidung.

**Werkzeug zur freiwilligen Selbst-Prüfung (Mensch-Hand):**
```
python3 /opt/infrastructure/environment_a/scripts/backlog/check_parallel.py <pfad1> <pfad2> [<pfad3> ...]
```
Drei Ausgabe-Klassen: ✓ `disjunkt` / ✗ `Schnittmenge zwischen Spuren` / ✗ `Hot-File-Treffer`. Bleibt als interaktives Werkzeug nützlich; den **harten** Kollisions-Schutz trägt jetzt die strukturelle Worktree-Isolation + git-non-ff-Ablehnung (`parallel-agent-schutz.md`), nicht mehr ein Claim-acquire.

**`10_Kunden/`-Schutz bleibt technische Wand:** Der pre-commit-Hook im knowledge-base-Repo lehnt jeden Commit auf einem `wt/*`-Branch ab, der Dateien unter `10_Kunden/` berührt. Mandantendaten gehören nur auf `main`. Override `--no-verify` nur in begründeten Ausnahmen + Logbuch-Notiz. Begründung: das ist Datenschutz/Datenintegrität, nicht Mengen-Steuerung — bleibt deshalb hart.

### Autonome Halde (freigegebene sicher-Specs)

Aus dem Backlog ist nicht jeder Seed gleich gut maschinell ziehbar. Die Halde trennt das, was ohne Synchron-Stopp durchlaufen darf, vom Rest.

- **`autonom_ziehbar: ja`** ist **abgeleitet, nicht geurteilt**: ein Seed steht auf `ja` gdw. (a) eine freigegebene Spec für ihn existiert UND (b) diese Spec `risikoklasse: sicher` trägt. Der Agent protokolliert den Zustand, er entscheidet ihn nicht. Die Mensch-Hoheit sitzt an den zwei Vorbedingungen — Spec-Freigabe und Risikoklasse-Einstufung —, nicht am Feld selbst. Die Ableitung ist sicher, weil **nur-nach-oben** gilt (Abschnitt „Risikoklasse `kritisch`" oben): ein Agent kann sich keine Spec selbst freigeben und keine `kritisch`-Spec auf `sicher` herunterstufen.
- **Zeitpunkt der Ableitung:** Das Feld wird in **Akt 3 (Verankerung)** der jeweiligen Spec gesetzt — als Teil der ohnehin laufenden Abschluss-Bewegung des Agents, gemeinsam mit der Eintragung in `00_FREIGEGEBEN.md`. Kein separater Mechanismus, kein Skript.
- **Akt-3-Pflicht — Seed-Status synchron setzen, Ketten-Kehraus beim letzten Glied.** Der abschließende Agent zieht in Akt 3 den `status:` des eigenen Seeds (Pfad via `seed:`-Backreference der Spec) auf `abgeschlossen` — im selben Commit wie die Spec-Status-Setzung. Ist der Seed Vorgänger einer noch offenen `abhaengig_von`-Kette, bleibt er als abgeschlossener Lese-Anker in `_Betrieb/Backlog/seeds/` stehen, damit die Worker-Vorgänger-Prüfung ihn an stabiler Stelle findet (ohne synchronen Status bliebe ein Folge-Glied ewig wartend, obwohl der Vorgänger durch ist). Schließt das letzte offene Glied einer Kette ab, wandern alle abgeschlossenen Seeds dieser Kette gemeinsam ins Archiv (`<Bereich>/Archiv/<spec_id-des-letzten-Glieds>/`) — ein Kehraus, ein Ordner pro Kette. Ein Seed ohne Folge-Glieder wandert unverändert sofort beim eigenen Akt-3-Abschluss.
- **Vollständiger Zyklus-Satz, Tool-Zuständigkeit.** Der Archiv-Move umfasst nicht nur den Seed, sondern den gesamten Zyklus-Satz: Spec, Abschluss-Notiz, Machbarkeit, Fakten, Deviation-Logs (soweit vorhanden). Zuständig ist allein das Kehraus-Tool `scripts/backlog/phase9_seed_archive.py` — der abschließende Agent lässt die Dateien im Arbeitsgedächtnis liegen, das Tool räumt. Bei Kettengliedern bleibt der vollständige Satz im Arbeitsgedächtnis liegen, bis das letzte offene Glied der Kette abschließt; dann wandern alle Sätze gemeinsam in einen Archiv-Ordner. Das Liegenlassen ist gewollt, kein Drift — ein voreiliger Einzel-Move risse einen Lese-Anker weg, den ein paralleler Zweig noch braucht.
- **Archiv-Tor erzwingt den Move (PLAT-091).** Damit „als abgeschlossen markiert" nicht mit „archiviert" verwechselt wird, gibt es ein Doppel-Layer-Tor (Mechanik + Skripte: `_Betrieb/CLAUDE.md`, Abschnitt „Archiv-Tor"): Layer 1 = pre-commit Block (6) blockt einen Abschluss-Commit mit Residuum im committenden Worktree; Layer 2 = nächtliches Bestands-Sweep (`archiv_tor_sweep.py`) meldet liegengebliebene abgeschlossene Specs in die Betriebs-Queue — auch wenn der Status über eine Hand ohne Hook (Obsidian) kippte. Der saubere Auflöse-Weg ist `scripts/backlog/akt3_abschluss.py <ID>` (Ein-Wort-Abschluss). Beide Layer nutzen `check_akt3_residuen.find_residuen`, respektieren also die Lese-Anker-Exemption (C).
- **Halde-Datei `_Betrieb/Backlog/00_FREIGEGEBEN.md`** listet diese Seeds **handgepflegt-kettenbewusst**: unabhängige Stränge als getrennte Blöcke, innerhalb eines Blocks regelt das bestehende `abhaengig_von` die Reihenfolge. Reihenfolge = Priorität (vom Menschen gesetzt). Analog `00_HOT-FILES.md`: das Übersichts-Skript liest und überschreibt sie nicht.
- **Blocker-Regel:** Ein Blocker — Stopp-Auslöser nach `00_Iterationszyklus.md`, Fall C, oder eine Abschluss-Notiz, die Nacharbeit fordert — **friert die betroffene Abhängigkeitskette ein**. Kein abhängiges Folge-Glied wird gezogen, solange der Blocker offen ist. Unabhängige Stränge in anderen Blöcken bleiben unberührt.
- **Abgrenzung:** Die Halde definiert nur das „Was ist ziehbar". Die **Worker-/Loop-Mechanik** (wer zieht, wann, wie viele parallel) ist **nicht Teil dieser Regel**. Der Kollisions-Schutz selbst ist seit PLAT-132 strukturell (Worktree-Isolation + git-non-ff, `parallel-agent-schutz.md`); der frühere Leitstand-Claim-Ledger (PLAT-103) ist mit dem Cockpit/Windows-Transport stillgelegt.

## Pflicht-Sektionen in Spec, Sondierung und Abschluss-Doku

### Auftrags-Treue (`## Auftrags-Treue`) — nur Spur

**Pflicht- und ERSTE Sektion jeder Spur-Spec — noch vor dem Wirkungs-Block.** Sie hält den Maßstab fest, gegen den die Spec geprüft wird: was der Mensch wörtlich verlangt hat, und wie weit diese Spec es deckt. Sie ist das **einzige**, was der Mensch zur Freigabe zwingend lesen muss (`00_Iterationszyklus.md`, Akt 2 „Auftrags-Treue ist das Mensch-Tor").

Zwei Teile:

1. **`### Auftrag (wörtlich, vom TT.MM.)`** — der Auftrag des Menschen, **nummeriert, in seinen Worten**, nicht in die Lösung umgedeutet. Aufgenommen beim Briefing (Akt 1, „Auftrag festhalten"), vom Menschen in einer Zeile bestätigt. Bei **Seed-Auslöser ohne frisches Briefing** IST der Seed-Text (Soll/Absicht) der Auftrag.
2. **`### Deckung`** — pro Auftragspunkt genau ein Urteil:
   - **✅ voll** — die Spec liefert den Punkt vollständig.
   - **🟡 teilweise** — + Halbsatz, *was fehlt*.
   - **❌ nicht** — + Halbsatz, *warum*.
   - **↪️ bewusst ersetzt durch …** — der Auftrag wird anders gelöst als verlangt.

**Korridor-Wand:** Fehlt der Block bei einer Spur, ist die Spec nicht freigabefähig (Fall C) — wie der Wirkungs-Block.

**Non-✅ = Wohin-Tor.** Jeder Punkt, der nicht ✅ ist (🟡/❌/↪️), ist eine **Wohin-Entscheidung** und braucht die ausdrückliche Freigabe des Menschen — er gibt sie (Restscope bewusst akzeptiert) oder vetot. Eine Spec, deren Deckung nicht durchweg ✅ oder ausdrücklich freigegeben ist, läuft **nicht** autonom in Akt 3 an.

**Generativen Auftrag nie still durch eine Liste ersetzen.** Verlangt der Auftrag etwas, das aus echten Daten *entstehen* soll („sondiere, welche X Sinn machen"), ist der Liefergegenstand die **Methode, die X erzeugt** — nie eine handverlesene Stichprobe. Eine statische Liste an Stelle einer generativen Anforderung ist immer ↪️ (Wohin-Amputation), nie ✅. (Präzedenz: PRIS-094, wo „aus echten Daten sondieren" still zu 6 fest einprogrammierten Sätzen wurde.)

**AK-Rückverfolgbarkeit:** Jeder ✅-/🟡-Auftragspunkt trägt **mindestens ein Akzeptanzkriterium**, das ihn prüft — so trifft die Akt-3-Verifikation den Auftrag, nicht nur die Spec. Ein Auftragspunkt ohne zugehöriges AK ist eine Deckungslücke.

### Wirkungs-Block (`## Wirkung`)

**`## Wirkung (für den Architekten)`** ist Pflichtsektion in jeder Spec, Sondierung und Abschluss-Doku — vom schreibenden Agenten für den Menschen formuliert (nie generiert oder transkludiert), maximal 6 Sätze, vier Punkte:
1. Was sich in der echten Welt ändert (Kunde / Geld / Risiko / Zeit).
2. Was passiert, wenn wir es nicht tun.
3. Was schiefgehen kann + Rückweg.
4. Die Entscheidungsfrage in einem Satz — oder „keine; nur Veto möglich".

Fachbegriffe nur, wenn die Entscheidung an ihnen hängt — dann mit Halbsatz-Erklärung. **Korridor-Wand: Fehlt der Block, ist das Dokument nicht freigabefähig (Fall C).**

### Einwände & Behandlung (`## Einwände & Behandlung`)

Nach jedem Evaluator-Pass ist **`## Einwände & Behandlung`** Pflichtsektion in Spec und kombinierter Spec. Format: Tabelle mit Spalten Einwand | eingearbeitet/verworfen | ein Satz Begründung. Kein Evaluator-Pass gelaufen → Sektion entfällt (aber erst nach abgeschlossenem Pass eintragen, nicht weglassen). Ein unbehandelter Evaluator-Einwand ohne Tabelleneintrag ist ein unvollständiges Dokument.

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

- **`status:`** — `offen` | `eisbox` | `in_arbeit` | `blockiert` | `review` | `abgeschlossen`. **Pflichtfeld.** Default `offen` bei Anlage. **Abgeleitet aus dem Akt-Zustand, nicht frei geurteilt** — Claude Code schreibt automatisch (Silent-Whitelist; Akt-Kopplung siehe `00_Iterationszyklus.md` Abschnitt „Seed-Status & Mission"). Werte:
  - `offen` — Seed liegt im Backlog, ungezogen.
  - `eisbox` — anerkannt und aufbewahrt, aber außerhalb des Tagesgeschäfts: zahlt nicht auf den aktiven Nordstern ein und ist nicht `security`+`jetzt`. Eisbox-Seeds erscheinen in der generierten Übersicht nur als eingeklappte Titelliste am Ende und als eigene Kanban-Spalte. Rückholung: Mensch-Entscheidung, vorgeschlagen u. a. vom wöchentlichen Richtungs-Review. Übergang `offen ↔ eisbox` ist beidseitig erlaubt — der einzige Status ohne Nur-vorwärts-Logik.
  - `in_arbeit` — Seed gezogen, Akt 1 läuft oder Folgeakt.
  - `blockiert` — Stopp-Auslöser 1–4 / Fall C / Blocker einer Abhängigkeitskette offen.
  - `review` — Akt-3-Ausführung fertig, wartet auf Mensch-Verifikation, die nur der Mensch leisten kann (reales Browser-Fenster, App-Verhalten, visuelle UI).
  - `abgeschlossen` — Verankerung komplett, Pflicht-Tore erfüllt (Endwert; konsistent mit Abschnitt „Autonome Halde").

**Optionale Felder:**

- **`autonom_ziehbar:`** — `ja` | `nein`. **Default `nein`** (Feld darf weggelassen werden). **Abgeleiteter Zustand, kein eigenes Urteil**: `ja` gdw. (a) eine freigegebene Spec für den Seed existiert UND (b) diese Spec `risikoklasse: sicher` trägt. Die Mensch-Hoheit sitzt an diesen zwei Vorbedingungen, nicht am Feld selbst. Bedeutung, Zeitpunkt und Halde-Regeln im Abschnitt „Autonome Halde" unten.

- **`mission:`** — Kebab-case-Slug, der den Seed einer aktuell laufenden, missionalen Klammer zuordnet (z. B. `live-gang`, `kunde-2`, `framework`). Genau eine Mission pro Seed; leerer Wert (oder weggelassen) heißt „keine Mission". Wird im Obsidian-Dashboard zum Filtern und Bündeln genutzt; hat KEINEN Einfluss auf den Prozess. Liste der aktuell aktiven Missions-Slugs liegt in `_Betrieb/Missionen/00_aktive-missionen.md` (handgepflegt).

- **`projekt:`** — Kebab-case-Slug (bzw. `projekt_id`-Kürzel), der den Seed einem laufenden **Projekt** zuordnet (`07_Projekt-Orchestrierung.md`). Anders als `mission:` ist das **prozess-tragend**: ein `projekt:`-getaggter Seed gehört in das Seed-Set genau eines Projekts, dient dessen einem fixen Wohin, und wird vom Orchestrator-Loop gezogen/priorisiert (nicht von einer interaktiven Session). Emergente Seeds, die ein Worker im Projekt-Lauf anlegt, tragen dieses Feld. Leer/weggelassen = kein Projekt. Unterschied zu `mission:`: Mission ist ein loser Filter ohne Prozess-Wirkung, Projekt ist ein Ein-Ziel-Behälter mit Orchestrator-Mechanik.

- **`spec_id:`** — Backreference auf die zugehörige Spec, sobald sie existiert (gesetzt in Akt 2, wenn der Seed in `in_arbeit` geht). **Format: Obsidian-Wikilink auf die Spec-Datei**, nicht nur die nackte ID — `spec_id: "[[Plattform/Arbeitsgedaechtnis/PLAT-046_SPEC]]"` (PLAT) bzw. `spec_id: "[[Prisment/Arbeitsgedaechtnis/PRIS-046_SPEC]]"` (PRIS). Begründung: ein Klick im Editor springt direkt zur Spec; die nackte ID zwingt zur manuellen Suche. Wert weggelassen, solange noch keine Spec existiert (Seed im Backlog ungezogen).

  **Pfad-Konvention für Spec-Dateien:** Specs landen immer als flache Dateien im Arbeitsgedächtnis des zuständigen Bereichs — NIEMALS in einem eigenen Unterordner im Bereichs-Root. Korrekt: `Plattform/Arbeitsgedaechtnis/PLAT-NNN_SPEC_*.md` bzw. `Prisment/Arbeitsgedaechtnis/PRIS-NNN_SPEC_*.md`. Falsch: `Prisment/PRIS-NNN/PRIS-NNN_SPEC.md`. Unterordner im Bereichs-Root sind ausschließlich dem Archiv (`Plattform/Archiv/PLAT-NNN/`) vorbehalten.

**Vergabe-Mechanik** (für `klasse` / `zugkraft` / `beruehrt` / `mission`, analog `stufe`): Architekt/Claude Code schlägt mit Begründung vor, Mensch revidiert/gibt frei. Niemals vom Menschen allein geraten. (`status` und `autonom_ziehbar` fallen nicht hierunter — sie werden abgeleitet/fortgeschrieben, nicht beraten-vergeben.)

**Sperrzone „Eigene Notizen":** Jeder Seed endet mit einer Sektion `## 📝 Eigene Notizen`. Diese Sektion ist Mensch-Sperrzone. Claude Code darf sie LESEN (Kontext für Entscheidungen), aber NIE überschreiben, löschen oder umformulieren. Bei Re-Writes oder Auto-Edits eines Seeds bleibt der Inhalt dieser Sektion byte-identisch erhalten. Tritt ein Konflikt auf (Seed-Re-Write würde die Sektion berühren), stoppt Claude Code synchron und fragt den Menschen. Stub-Form siehe `_Betrieb/Templates/Dokument-Templates.md` (Body-Schluss im Backlog-Seed-Abschnitt).

**Querverweis:** Das `kritisch`-Flag pro Bündel (für autonome Korridor-Ausführung) folgt derselben Vergabe-Mechanik wie `klasse`/`zugkraft`. Definition siehe Abschnitt „Kritikalität pro Bündel" oben.

## Schritt-Log (Stufe Schritt)

Jeder Geltungsbereich hat eine Datei `<Bereich>/Schritt-Log.md`. Claude Code hängt **automatisch, ohne Nachfrage** pro ausgeführtem Schritt eine Zeile an:

```
| 2026-05-24 | <was, ein Satz> | <commit-hash> |
```

Das ist das Sicherheitsnetz gegen „verschwunden in der Git-Historie": ein Schritt ist über Obsidian auffindbar, ohne dass er ein eigenes Arbeitsdokument oder einen Archiv-Eintrag erzeugt. Kein Logbuch, kein Abschluss, keine Verankerung.
