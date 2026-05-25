---
typ: verfassung
titel: "Spec-, Report- & Abschluss-Format"
stand: 2026-05-24
aenderung: "nur nach oben, nur durch bewusste Freigabe"
---

# 01 — Spec-Format (und Report / Abschluss-Doku)

Die Arbeitsdokumente des Zyklus folgen einem modularen Standard: ein gemeinsamer YAML-Front-Matter-Header, plus typspezifischer Körper. Die vollständigen Vorlagen liegen in `_Betrieb/Templates/Dokument-Templates.md` — diese Verfassungsdatei legt die verbindlichen Regeln fest, das Template liefert die ausfüllbare Form.

**Welche Dokumente entstehen, hängt von der Stufe ab** (siehe `00_Iterationszyklus.md`):
- **Spur:** Faktensammlung + volle Spec + Machbarkeit + volle Abschluss-Doku.
- **Sprung:** kombinierte Spec (Fakten/Soll inline) + kurze Abschluss-Notiz.
- **Schritt:** kein Dokument, nur eine Zeile in `<Bereich>/Schritt-Log.md`.

## Verbindliche Regeln

1. **Eine Wahrheit, Zusammenfassung ist nur Ansicht.** Die Zusammenfassung am Dokumentanfang verdichtet, was darunter steht — sie ergänzt NIE Inhalt, der unten fehlt.
2. **Bewiesen, nicht angenommen.** Jeder Ist-Fakt in einem Report nennt seine Quelle (Befehl/Datei/Pfad).
3. **Front-Matter-Keys sind verbindlich identisch** über alle Dokumente. Immer `risikoklasse`, nie mal `risiko` mal `risk`. Neue Felder werden im Template ergänzt, nie ad-hoc.
4. **Spec ist die Wahrheit; Report und Abschluss-Doku erben ihre `spec_id`.** Damit ist die Kette maschinell verkettbar.
5. **`stufe` ist Pflichtfeld** in jeder Spec und Abschluss-Notiz. Sie wird beratend vorgeschlagen (Chat-Architekt/Claude Code), vom Menschen revidierbar, nie vom Menschen allein geraten (siehe `00_Iterationszyklus.md`).

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

Kritisch ist die Ausnahme, nicht die Norm. **Wenn mehr als die Hälfte des aktiven Backlogs `kritisch` ist, stimmt die Definition oder die Einstufung nicht — Schärfung oder Revision pflichtgemäß.** Inflation entwertet das Steuer-Signal: im Korridor-Modell (`00_Iterationszyklus.md`) bedeutet `kritisch` einen synchronen Stopp pro Bündel + harte Mengen-Regel beim Parallel-Lauf — bei Inflation kein Korridor, keine Parallelität, nur Bürokratie.

Die Schärfung steht im Backlog als `seed-kritisch-schaerfen` (Sprung, jetzt) und wird wiederholt, sobald die Quote wieder kippt.

### Begriffstrennung — Spec-Risikoklasse vs. Bündel-Flag

- **`risikoklasse:` im Spec-Front-Matter** ist die **Obergrenze** für die Bündel-Einstufung in Phase 5. Eine `kritisch`-Spec kann und soll `sicher`-Bündel enthalten; eine `sicher`-Spec kann nie kritische Bündel haben (Stufen-Inflation-Schutz; tauchen welche auf, ist die Spec falsch eingestuft und eskaliert).
- **Pro-Bündel-`kritisch:`-Flag in der Machbarkeit (Phase 5)** ist die operative Wand, an der der Korridor synchron stoppt. Drei Werte wie die Spec-Risikoklasse.

## Kritikalität pro Bündel (steuert Autonomie in Phase 6)

Damit Phase 6 autonom laufen kann (`00_Iterationszyklus.md`, Abschnitt „Autonome Ausführung im freigegebenen Korridor"), **setzt Claude Code in Phase 5 (Machbarkeit) pro Bündel verbindlich ein `kritisch:`-Flag** mit einem der drei Werte `sicher` / `kritisch` / `sicherheitskritisch-akut` — geprüft gegen die feste Liste unten. `sicher`-Bündel laufen autonom; `kritisch`- und `sicherheitskritisch-akut`-Bündel sind synchrone Stopps in der Spur.

Die Spec-weite `risikoklasse:` ist **Obergrenze**, nicht Ersatz: eine `kritisch`-Spec kann und soll `sicher`-Bündel enthalten — nicht jede Aufgabe innerhalb einer kritischen Spec ist selbst kritisch. Eine `sicher`-Spec hingegen kann nie kritische Bündel haben (Stufen-Inflation-Schutz: würde so etwas auftauchen, ist die Spec falsch eingestuft und gehört eskaliert, siehe `00_Iterationszyklus.md` „Eskalation").

**Feste Liste — immer kritisch** (Claude Code erkennt nur, ob berührt; schätzt hier nicht ein):

- **Sicherheit & Zugang:** Auth (Login/Session/Token/Passwort), Tenant-Isolation/RLS, Berechtigungen/Rollen, Secret-Handling (Keys/Env/Credentials), Cloudflare-/Edge-Sicherheitsschicht.
- **Daten-Integrität & -Verlust:** DB-Schema-Migrationen (besonders irreversible), alles was Kundendaten ändert/löscht, die zentrale Datenpunkt-Definition (sobald sie existiert).
- **Außenwirkung (irreversibel):** Produktions-Deploy, öffentlicher Content, gesendete Mails/Nachrichten, Geldfluss.
- **Abhängigkeiten/Lieferkette:** Dependency-Major-Updates, Framework-Versionssprünge, neue externe Dependencies.

**Graubereich — Claude-Code-Urteil** über zwei Testfragen:
1. Billig rückrollbar ohne bleibenden Schaden? **Nein → kritisch.**
2. Hängt die richtige Wahl vom Wohin ab statt von Technik? **Ja → kritisch.**

**Nur-nach-oben:** Claude Code und der Mensch dürfen nur **hoch**stufen. Ein Listen-Treffer kann nicht weggeurteilt werden. (Korrespondiert mit dem „Tut NICHT" des Arbeitstiers in `02_Rollen-Protokoll.md`: „stuft Kritikalität nie nach unten ab, um im Autopilot zu bleiben".)

**Spec-Vermerk:** pro Nicht-Listen-Bündel nur `kritisch: sicher|kritisch|sicherheitskritisch-akut` + Halbsatz Begründung — nicht die ausformulierten Testfragen (die sind Werkzeug, nicht Lesestoff). Listen-Treffer brauchen keinen Halbsatz (die Liste IST die Begründung).

**Die Liste lebt:** wächst per Phase-9-Pflicht-Tor (Mensch gibt frei, E3-konform), wenn ein Schaden eine fehlende Kategorie aufdeckt.

## Parallel-Lauf von Spuren

Mehrere Spec-Zyklen können gleichzeitig laufen — in eigenen Git-Worktrees, jeweils eigener `tmux`-Session, jeweils eigener Branch `wt/<short>` (Mechanik: PLAT-014, E31).

**Mengen-Regel (hart, maschinell geprüft):** Nie zwei Spuren mit `risikoklasse: kritisch` oder `sicherheitskritisch-akut` parallel. Der Mensch ist die Stop-Instanz, kann sich nicht zerteilen. **Keine Anzahl-Obergrenze** für gleichzeitige Spuren insgesamt — die Grenze setzt der Mensch über seine Stopp-Auslöser-Aufmerksamkeit.

**Hot-File-Pflicht-Tor:** Wenn auch nur ein Pfad aus `_Betrieb/Backlog/00_HOT-FILES.md` in den `beruehrt:`-Mengen irgendeines parallel laufenden Strangs auftaucht, ist Parallelität ausgeschlossen — egal wie disjunkt der Rest ist. Hot-Files sind die Konflikt-Quellen, die jeder Strang berührt.

**Disjunktheits-Check vor Strang-Start (Pflicht):**
```
python3 /opt/infrastructure/environment_a/scripts/backlog/check_parallel.py <pfad1> <pfad2> [<pfad3> ...]
```
Drei Ausgabe-Klassen:
- ✓ `disjunkt` → parallel zulässig
- ✗ `Schnittmenge zwischen Spuren` → seriell zwingend (gemeinsamer `beruehrt:`-Pfad oder Mengen-Regel-Verstoß)
- ✗ `Hot-File-Treffer` → Parallelität ausgeschlossen

Das Skript ist die maschinelle Wand; menschliche Disziplin bleibt die zweite Wand für Fälle, die das Skript nicht sieht (z.B. shared Code-Patterns innerhalb gleicher Ordner-Ebene). Beim Strang-Start ist der Check Pflicht — Verstoß per Override geht nur mit ausdrücklicher Logbuch-Notiz.

**`10_Kunden/`-Schutz auf Worktree-Branches:** Der pre-commit-Hook im knowledge-base-Repo lehnt jeden Commit auf einem `wt/*`-Branch ab, der Dateien unter `10_Kunden/` berührt. Mandantendaten dürfen nur auf `main` bearbeitet werden. Override `--no-verify` nur in begründeten Ausnahmen.

## Bündelung gehört NICHT in die Spec

Die Spec definiert das WAS (Ziel, Soll-Zustand, Akzeptanzkriterien). Die Bündelung/Arbeitsliste erarbeitet sich Claude Code selbst im Machbarkeits-Report — er kann Abhängigkeiten am echten System besser einschätzen. *(Beim Sprung: inline in der kombinierten Spec, knapp.)*

## Backlog-Felder (Seeds & Roadmaps)

Seeds und ROADMAPs in `_Betrieb/Backlog/` führen zusätzlich zu den allgemeinen Front-Matter-Feldern drei Pflichtfelder, über die das Übersichts-Skript priorisiert und das Backlog lesbar wird:

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

**Vergabe-Mechanik** (analog `stufe`): Chat-Architekt/Claude Code schlägt mit Begründung vor, Mensch revidiert/gibt frei. Niemals vom Menschen allein geraten.

**Querverweis:** Das `kritisch`-Flag pro Bündel (für autonome Korridor-Ausführung) folgt derselben Vergabe-Mechanik wie `klasse`/`zugkraft`. Definition siehe Abschnitt „Kritikalität pro Bündel" oben.

## Schritt-Log (Stufe Schritt)

Jeder Geltungsbereich hat eine Datei `<Bereich>/Schritt-Log.md`. Claude Code hängt **automatisch, ohne Nachfrage** pro ausgeführtem Schritt eine Zeile an:

```
| 2026-05-24 | <was, ein Satz> | <commit-hash> |
```

Das ist das Sicherheitsnetz gegen „verschwunden in der Git-Historie": ein Schritt ist über Obsidian auffindbar, ohne dass er ein eigenes Arbeitsdokument oder einen Archiv-Eintrag erzeugt. Kein Logbuch, kein Abschluss, keine Phase 9.
