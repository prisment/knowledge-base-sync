# Entscheidungs-Historie: Framework-Design
**Zweck:** Hält fest, WARUM die zentralen Design-Entscheidungen des Frameworks so getroffen wurden. Damit künftige Iterationen nicht bereits geführte Diskussionen wiederholen. Dies ist der erste Logbuch-Eintrag und gleichzeitig ein Beispiel für das Logbuch-Format selbst.
**Kontext der Entstehung:** Erarbeitet im selben Chat-Zyklus wie der Prisment-Security-Refactor (CVE-2025-55182-Incident → Cloudflare-Migration). Das Framework-Bedürfnis entstand AUS diesem Zyklus, weil er die Grenzen der bisherigen Arbeitsweise sichtbar machte.

---

## E1 — Warum vier Wissens-Kategorien statt der ursprünglich gedachten drei

**Ursprüngliche Idee (Mensch):** Drei Dimensionen — Arbeitsdoku / Überblick / Handbuch.

**Entscheidung:** Vier Kategorien, sortiert nach Lebensdauer (Verfassung / Systemzustand / Arbeitsgedächtnis / Logbuch), plus Hilfs-Kategorien Backlog + Archiv.

**Warum:** Die ursprüngliche Achse "nach Zielgruppe/Thema" vermischt Wissen unterschiedlicher Lebensdauer am selben Ort. Genau das erzeugte das "Sediment" in 08_Projekte (50+ Dateien flach, gültig und historisch ununterscheidbar). Die Achse "Lebensdauer" trennt sauber: flüchtig (darf archiviert werden), stabil (wird verteidigt), historisch (nie geändert). Das "Handbuch" wurde in ZWEI geteilt — Verfassung (Regeln, änderbar nach oben) und Logbuch (Entscheidungs-Historie, nie änderbar) — weil diese sich fundamental verschieden verhalten.

**Verworfene Alternative:** Beibehaltung der themenbasierten Ordnerstruktur (Architektur/Entwicklung/Design/Projekte). Verworfen, weil sie die Ursache des Sediments ist.

---

## E2 — Warum "EINE Wahrheit + Ansichten" statt zwei getrennter Dokus (Mensch vs. KI)

**Ursprüngliche Idee (Mensch):** Eine schöne Doku für mich (SVG, wenig Text), eine textreiche für die KI.

**Entscheidung:** Eine Single Source of Truth; die Mensch-Ansicht (SVG) wird daraus ABGELEITET.

**Warum:** Zwei getrennte Quellen reproduzieren das Synchronisationsproblem intern und driften garantiert auseinander. Eine veraltete Doku ist schlimmer als keine, weil man ihr vertraut. Die schöne SVG ist eine Lesebrille auf die Fakten, keine zweite Faktenquelle.

---

## E3 — Warum "mensch-im-Loop-optimierend" statt "selbstoptimierend"

**Ursprüngliche Formulierung (Mensch):** "selbstoptimierendes Framework".

**Entscheidung:** System schlägt vor, Mensch gibt frei. Keine autonome Selbstveränderung.

**Warum:** "Selbstoptimierung" wäre genau der Kontrollverlust, den der Mensch fürchtet. Das aus dem Security-Incident gelernte Prinzip — "bewiesen, nicht angenommen; Mensch gibt frei" — muss auch für die Veränderung des Systems selbst gelten. Sonst entzieht sich das System der Kontrolle, die gerade aufgebaut wurde. Die Verfassung ändert sich nur nach oben (nie schlechter werden), nur durch bewusste Freigabe.

---

## E4 — Warum Claude Code die EINZIGE Schreibhand ins Repo ist

**Frage des Menschen:** Kann der Chat direkt ins Repo schreiben? (Wunsch nach echtem Zwei-Wege-Sync.)

**Entscheidung:** Nein. Chat produziert Wissen, Claude Code schreibt. Einbahnstraße durch Claude Code.

**Warum:** (1) Technische Realität — der Chat hat keine Schreibrechte auf Produktionssysteme. (2) Das ist KEINE Lücke, sondern eine erwünschte Kontrollgrenze (Architekt zeichnet, Bauarbeiter baut). (3) Zwei schreibende Hände = Konflikt- und Inkonsistenzgefahr. Eine Schreibhand ist die saubere Architektur.

**Konsequenz:** "Perfekter Auto-Sync" ist nicht das Ziel. Reibungs-Reduktion ist es: von Textblock-Copy-Paste zu Datei-Transport, Claude Code sortiert ein.

---

## E5 — Warum Zwischenprojekte PAUSIEREN+EINSCHIEBEN statt HINEINWACHSEN

**Beobachteter Schmerz (Mensch):** Aus einem Projekt entstehen Abzweigungen; manche müssen sofort rein (Cloudflare wurde Voraussetzung). Hineinwachsen verwässert.

**Entscheidung:** Laufenden Zyklus pausieren, Zwischenprojekt als eigenen Mini-Zyklus einschieben. Plus: Pause-Snapshot + erzwungene Re-Sync-Prüfung bei Wiederaufnahme.

**Warum:** Hineinwachsen lassen erzeugt genau die Spec-Verwässerung und Inkonsistenz, die vermieden werden soll (bestätigt durch Erleben). Die Re-Sync-Prüfung ist nötig, weil ein eingeschobenes Projekt die Grundlage des pausierten verändern kann (Bsp.: Architektur-Umstellung auf Cloudflare ändert die Voraussetzungen der pausierten Schritte). Erkenntnis des Menschen, ins Framework aufgenommen.

---

## E6 — Warum nächtliche Autonomie inkl. einfacher Updates (entgegen Konzern-Best-Practice)

**Spannung:** Ein Konzern mit 1000 Kunden würde Claude Code keine autonomen nächtlichen Updates erlauben.

**Entscheidung:** Erlaubt — aber nur für die Risikoklasse "sicher" (reversibel, isoliert), mit vorab definiertem Erfolgskriterium + Rollback, Health-Check als Erfolgsbeweis, und Morgen-Report.

**Warum:** Bewusste, kontextabhängige Abwägung. Der Solo-Gründer hat ein anderes Optimierungsproblem als ein Konzern: Zeit ist der Engpass, nicht Ausfallsicherheit für tausende Kunden. Nachts, reversibel, mit Auto-Rollback = für diesen Kontext überwiegend Vorteile. Kritische Aktionen (DB, Netz, Kundendaten, Auth, Major-Upgrades) bleiben ausgeschlossen — das sind exakt die Bereiche, deren Bruch den ursprünglichen Incident verursachte.

**Wichtig:** Diese Entscheidung ist kontextgebunden. Wenn Prisment mehrere zahlende Kunden mit Produktivdaten hat, MUSS diese Risikoabwägung neu bewertet werden.

---

## E7 — Warum eine übergreifende Verfassung + projektspezifische Systemzustände

**Frage:** Ein Framework für alles, oder pro Projekt eines?

**Entscheidung:** "Wie wir arbeiten" ist übergreifend (eine Verfassung in `_Betrieb`). "Was gebaut ist" ist projektspezifisch (eigener Systemzustand/Arbeitsgedächtnis pro Geltungsbereich). Alle Geltungsbereiche folgen identischer innerer Struktur.

**Warum:** Die Arbeitsweise (Zyklus, Spec-Format, Rollen) ist universell und sollte nicht dupliziert werden. Die Architektur (z.B. Prisment-Tenant-Isolation) ist projektspezifisch. Identische innere Struktur über alle Geltungsbereiche, weil der Mensch das ausdrücklich wünscht (Wiedererkennbarkeit) und es die kognitive Last senkt.

---

## E8 — Warum Code NICHT in die Knowledge-Base gehört

**Befund:** `05_Entwicklung/langgraph/*.py` enthält echten Code. Mittlerweile existiert ein separates Code-Repo.

**Entscheidung:** Code raus aus der Knowledge-Base. Die Knowledge-Base dokumentiert ÜBER Code, enthält ihn nicht.

**Warum:** Code an zwei Orten = zwei Versionen = zweite Wahrheit = bricht das Single-Source-of-Truth-Prinzip. Die `.py`-Dateien sind veraltete Altlast-Snapshots. Bereinigung im Migrations-Zyklus.

---

## E9 — Warum die Verfassung genau EINMAL existiert (revidiert E7)

**Auslöser:** E7 hielt fest, jeder Geltungsbereich folge "identischer innerer Struktur" — was als "jeder hat auch eine eigene Verfassung" missverstanden werden konnte. Bei der Ausarbeitung der Ordnerstruktur wurde das präzisiert.

**Entscheidung:** Die Verfassung existiert genau EINMAL, übergreifend in `_Betrieb`. Die Geltungsbereiche (Plattform, Prisment, Intern) haben KEINE eigene Verfassung. Ihre identische innere Struktur umfasst nur: Systemzustand, Arbeitsgedächtnis, Logbuch, Backlog, Archiv (fünf Kategorien). Die Verfassung ist die übergreifende sechste, die nur einmal existiert.

**Warum:** Die Arbeitsweise (Zyklus, Spec-Format, Rollen, Standards) ist universell — eine projektspezifische Verfassung würde sie duplizieren und damit das Single-Source-of-Truth-Prinzip auf der Regelebene brechen. "Identisch" gilt nur unter den echten Geltungsbereichen (Prisment ≙ Intern ≙ Plattform), NICHT zwischen `_Betrieb` und ihnen — `_Betrieb` ist keine Geltungsbereich-Ebene, sondern die übergeordnete.

**Verhältnis zu E7:** Präzisiert, widerruft nicht den Kern von E7 (übergreifende Arbeitsweise + projektspezifischer Systemzustand bleibt). Korrigiert nur die irreführende Lesart "Verfassung pro Geltungsbereich".

---

## E10 — Warum eine dritte Geltungsbereich-Schicht "Plattform" (Abschalt-Test)

**Auslöser:** Prisment und interne Dienste laufen auf EINEM Single-Node-Server und teilen Komponenten (Docker-Host, Reverse-Proxy, Netz, gemeinsame DB, Backup). Die Zwei-Bereiche-Trennung (Prisment/Intern) konnte geteilte Infrastruktur nicht eindeutig zuordnen.

**Verworfene Alternative (Mensch vorgeschlagen):** Schnitt nach technischer Schicht — "Hardware/Container unter Intern, Software unter Prisment". Verworfen, weil die Achse "technische Schicht" quer zur Achse "Geltungsbereich" liegt: LangGraph-Agents sind Container, dienen aber 100% Prisment — sie würden fälschlich unter Intern landen. Das reproduziert die Verstreuung, die gerade abgeschafft wurde.

**Entscheidung:** Dritte Schicht `Plattform` = die geteilte Bühne (Host, Proxy, Netz, gemeinsame DB, Backup, Monitoring, übergreifende Security). Drei inhaltliche Geltungsbereiche: Plattform, Prisment, Intern. Zuordnung über den **Abschalt-Test**: "Wenn ich Prisment morgen abschalte — fällt das Asset weg?" Fällt weg → Prisment. Bleibt (auch andere brauchen es) → Plattform. Nur interner Geschäftsbetrieb → Intern.

**Warum:** Die richtige Schnittachse ist immer "WEM dient das Asset?", nie "WAS ist es technisch?". `Intern` vermischte unbewusst zwei Dinge — die physische Bühne und die internen Geschäftsvorgänge. Trennung löst geteilte Komponenten ohne Duplikat (jede geteilte Komponente genau einmal in Plattform dokumentiert; Projekt-Ansichten verweisen darauf — wahrt Single Source of Truth). Der Abschalt-Test bleibt bei künftigen Projekten stabil: Projekt #2 am geteilten Proxy ändert nichts an dessen Plattform-Zuordnung.

---

## E11 — Warum Briefing-Dateien nach Lebensdauer auf zwei Kanäle aufgeteilt werden

**Auslöser:** Frage, was der Mensch beim Chat-Start hochlädt — und die (falsche) Annahme, GitHub-Sync gäbe dem Chat live den aktuellen Repo-Stand.

**Klarstellung der Technik:** Zwischen Claude-Code-Commit und Chat-Kontext liegen ZWEI Versatz-Stellen: GitHub→Projekt-Wissen (Re-Import mit Zeitversatz) und Projekt-Wissen→Chat (Laden bei Sitzungsstart, danach eingefroren). Kein Live-Draht.

**Entscheidung:** Aufteilung nach Lebensdauer. Stabiler Unterbau (Verfassung komplett + übergreifendes Logbuch + Systemzustand des einen Geltungsbereichs) → Projekt-Wissen, gesynct, Versatz verkraftbar. Flüchtige Arbeitsschicht (Specs, Machbarkeitsanalysen, Abschluss-Dokus des laufenden Zyklus) → Gesprächskanal, kommt minutenfrisch über Claude Codes Antworten, KEIN Sync. Jede stabile Datei trägt `stand:`; der Chat nennt zu Sitzungsbeginn ungefragt die Stände.

**Warum:** Flüchtiges über den Sync zu schicken brächte doppelten Versatz — Gefahr veralteter Doku, der man vertraut (schlimmer als keine, E2). Der Gesprächskanal hat null Versatz, weil er die zwei Pufferzonen umgeht. "Ein Chat = ein Thema = ein Geltungsbereich" ist damit nicht nur Ordnung, sondern technisch sauber: minimiert das Fenster, in dem der eingefrorene Stand veraltet. Nur das übergreifende Logbuch ins Briefing (nicht die geltungsbereich-eigenen), weil Plattform-Entscheidungen auf Prisment wirken und die Querbezüge dort liegen — geltungsbereich-eigene Logbücher wären selten gebrauchter Ballast.

---

## E12 — Warum die kritische Autonomie-Grenze im OS sitzt, nicht im Prompt (+ Stufenplan)

**Auslöser:** Technische Implementation der nächtlichen Autonomie (E6). Frage: harte Grenze (technisch unmöglich) oder weiche (per Instruktion)?

**Korrektur der Incident-Lehre (Mensch):** Der ursprüngliche CVE entstand, weil monatelang nicht gepatcht wurde — durch zu WENIG Automatisierung, nicht zu viel. Nächtliche Patch-Autonomie hätte ihn verhindert. Der Incident ist KEIN Argument für enge Fesseln; menschliche Trägheit ist das Risiko.

**Entscheidung:** Die harte Grenze sitzt im OS — `claude-deploy` hat kein root, keinen Foundation-Layer-Zugriff (bereits umgesetzt). Oberhalb dieser Linie wird Claude Codes Disziplin vertraut. Tool-Zugriff passt Claude Code adaptiv selbst an. Implementation: Cron startet headless, nicht-interaktiven Lauf (getrennt von der menschlichen tmux-Session); Aufgaben + Abarbeitungs-Schleife liegen in Dateien (`nacht-aufgaben.md`, Schleife in `04_Sicherheits-Prinzipien.md`), nicht im Cron-Prompt. Stufenplan: (1) Nacht-Routine "sicher", (2) Cloudflare-API-Check nur beobachtend, (3) reaktive Notbremse — geparkt im Backlog.

**Warum:** Eine OS-Rechtegrenze umgeht kein Gedankengang und schützt doppelt — gegen Agenten-Fehler UND gegen einen gekaperten Account (der nachts unbeaufsichtigte Account ist auch der Angriffsziel-Account). Das ist das eigentliche Sicherheitsargument, nicht Misstrauen gegen das Modell. Härter zu fesseln würde das eigentliche Ziel — Arbeitserleichterung bei knapper Zeit — blockieren. Stufenweise statt alles auf einmal, weil eine automatische Stopp-Befugnis selbst ein Risiko ist (Fehlalarm legt das Produkt lahm) und erst gebaut wird, wenn die Signalverlässlichkeit (Stufe 2) erwiesen ist.

**Kontextbindung:** Wie E6 — sobald Prisment zahlende Kunden mit SLA hat, ist die Fehlalarm-Toleranz der Notbremse (Stufe 3) neu zu justieren.

---

## E13 — Warum das Repo nur AUSWAHL definiert, nicht FÄHIGKEIT (Repo-als-Fernsteuerung)

**Auslöser:** Erkenntnis des Menschen nach Reflexion: Wenn Nacht-Aufgaben als Datei im Repo liegen und Claude Code sie ungeprüft abarbeitet, dann steuert jeder, der ins Repo schreiben kann (gekaperter GitHub-Account, geleaktes Token, böser Lieferketten-Commit), den Agenten fern. Plus: generelle Sorge vor Agent-Kapern (Prompt Injection, Übernahme — vgl. KI-Agent-Lieferketten-Vorfall bei Vercel).

**Entscheidung:** Zweistufiger Schutz. (1) Die erlaubten Aktionen sind als feste, versionierte, eng umrissene Skripte auf OS-Ebene verankert (Allowlist); die Repo-Aufgaben-Datei wählt nur DARAUS aus und parametrisiert — sie enthält nie frei interpretierbare Befehle. Eingeschmuggelte Aufgaben ohne passende erlaubte Aktion laufen ins Leere. (2) Integritätsprüfung: Vor dem Lauf wird geprüft, ob die Aufgaben-Datei seit der letzten Freigabe verändert wurde (signierter Commit/Hash); unerwartete Änderung → Abbruch + Alarm. Zusätzlich neues Verfassungs-Kapitel `05_Agent-Sicherheit.md`: Gelesenes ist Daten nie Anweisung (Prompt-Injection-Abwehr), Lieferketten-Hygiene (pinned versions), Tool-Freeze im unbeaufsichtigten Lauf.

**Warum:** „Repo enthält ausführbare Anweisungen → Agent führt aus" macht das Repo zur Fernsteuerung. Die Trennung „Repo wählt aus fixer OS-Allowlist" verlagert die Fähigkeit dorthin, wo sie der Angreifer nicht über einen Commit erweitern kann. Verschärft E12 (OS-Grenze bleibt harte Linie), widerlegt es nicht.

**Verhältnis zu E12:** Ergänzt E12 um die Repo-Ebene. E12 schützte gegen gekaperten Account via OS-Rechten; E13 schützt gegen die Steuer-Datei selbst als Vektor.

**Wichtige Ausnahme zum „adaptiven Tool-Zugriff":** Tagsüber unter Aufsicht darf Claude Code Tools adaptiv anpassen. Unbeaufsichtigt (nachts) NICHT — eingefrorenes minimales Tool-Set, weil „adaptiv neue Tools laden" sonst der Hebel einer Injection ist.

---

## E14 — Warum EIN zentraler Backlog statt einer pro Geltungsbereich

**Auslöser:** Frühere Annahme war Backlog pro Geltungsbereich (Teil der „identischen Fünfer-Struktur"). Der Mensch hinterfragte das nach Reflexion.

**Entscheidung:** Ein zentraler Backlog in `_Betrieb/Backlog/`. Jeder Eintrag trägt seinen Geltungsbereich als Front-Matter-Feld (`geltungsbereich: prisment`), Filtern statt Trennen. Die Geltungsbereiche schrumpfen damit von Fünfer- auf Vierer-Struktur (Systemzustand, Arbeitsgedächtnis, Logbuch, Archiv). Logbuch bleibt bewusst verteilt (anders als Backlog).

**Warum:** Der Backlog ist die EINE Erinnerungsliste über alles Offene. Verteilt man ihn, weiß man nie, wo man insgesamt steht, und bereichsübergreifende Abzweigungen haben keinen klaren Ort — dasselbe Sediment-Problem, nur für offene Aufgaben. Backlog ist seiner Natur nach übergreifend (aktive Planungsebene), wie die Verfassung. Logbuch dagegen bleibt verteilt, weil es ein gezielt nachgeschlagenes Entscheidungs-Archiv ist (kein Gesamt-Überblick nötig) — Prisment-Entscheidungen interessieren nur bei Prisment-Arbeit.

**Verworfene Alternative:** Backlog pro Geltungsbereich. Verworfen wegen fehlendem Gesamtüberblick und unklarem Ort für Querschnitts-Themen.

---

## Format-Hinweis (für künftige Logbuch-Einträge)

Jeder Eintrag: **Was war die Frage/der Auslöser → Was wurde entschieden → Warum (inkl. verworfener Alternativen) → ggf. Kontextbindung (wann neu zu bewerten).** Knapp, aber das "Warum" vollständig genug, dass man die Entscheidung nicht erneut diskutieren muss. Einträge werden nie geändert — wenn eine Entscheidung revidiert wird, kommt ein NEUER Eintrag, der auf den alten verweist.
