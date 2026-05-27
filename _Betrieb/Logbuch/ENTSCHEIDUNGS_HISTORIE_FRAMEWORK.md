# Entscheidungs-Historie: Framework-Design
**Zweck:** Hält fest, WARUM die zentralen Design-Entscheidungen des Frameworks so getroffen wurden. Damit künftige Iterationen nicht bereits geführte Diskussionen wiederholen. Dies ist der erste Logbuch-Eintrag und gleichzeitig ein Beispiel für das Logbuch-Format selbst.
**Kontext der Entstehung:** Erarbeitet im selben Chat-Zyklus wie der Prisment-Security-Refactor (CVE-2025-55182-Incident → Cloudflare-Migration). Das Framework-Bedürfnis entstand AUS diesem Zyklus, weil er die Grenzen der bisherigen Arbeitsweise sichtbar machte.

**Inhaltsumfang (Stand 2026-05-24, nach Framework-v2):** Diese Datei ist ab E24 das **zentrale** Logbuch für alle Geltungsbereiche — sie hält sowohl Framework-Design-Entscheidungen (überwiegend E1–E18, E23, E24) als auch betriebliche Entscheidungen pro Bereich (E19–E22 betreffen Plattform/PLAT-001) in einer einzigen chronologischen Liste. Bereichseigene `Logbuch/`-Ordner wurden mit E24 abgeschafft. Eine thematische Untergliederung (z. B. „Framework-Design" vs. „Betrieb") ist nicht eingezogen, weil sie die Chronologie zerreißt — ob sie sinnvoll wäre, ist offen (siehe Architekten-Frage im Framework-v2-Umbau).

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

## E15 — Nachtlauf: „Denken frei, Hand eng" (Paradigma der nächtlichen Autonomie)

**Auslöser:** Bei der Umsetzung von BOOT-001 Bündel E stand die Frage, wie die nächtliche Routine sicher Tools nutzen kann. Erster Vorschlag (Plan A): kein LLM nachts, nur starre Bash-Skripte. Zweiter (Plan B): voller Claude Code mit allen Tools. Beide falsch.

**Entscheidung:** Claude Code läuft nachts als beurteilendes LLM, ABER mit einem **hart begrenzten, eingefrorenen Tool-Set** (`~/.claude-nightly/settings.json`). Das LLM darf frei urteilen (Versionen prüfen, Changelogs lesen, Logs triagieren, Anomalien einschätzen), aber nur enge Operationen ausführen — die erlaubten Docker-/Inspektions-/Mail-Werkzeuge SIND selbst die „Routine" (E13), keine zusätzliche Indirektion über Bash-Wrapper.

**Warum:** Container-Updates, App-Dependency-Reviews und Anomalie-Triage sind durchgehend beurteilende Tätigkeiten — Skripte, die nur `pull && restart && curl /health` machen, produzieren die Health-Check-Lüge, die Verfassung 04 verbietet. Reine Skripte fallen damit als Lösung weg. Andererseits: ein LLM mit freiem Tool-Set wäre über Prompt-Injection im Changelog kapern bar. Antwort: Denken nicht beschränken (sonst wertloser Lauf), Hand strukturell beschränken (eingefrorenes Tool-Set + OS-Rechtegrenze). Eine Injection kann nichts Destruktives auslösen, weil das Werkzeug nicht in der Hand liegt — nicht weil das LLM brav ist.

**Verworfene Alternativen:** (a) Plan A „nur Skripte" — verfehlt die beurteilende Natur der Aufgabe. (b) Plan B „volles LLM" — Injection-Risiko ohne strukturelle Bremse.

**Verhältnis zu E12/E13:** Setzt E12 (OS-Rechte) und E13 (Repo definiert AUSWAHL nicht FÄHIGKEIT) konkret in der Settings-Datei um. Tool-Allowlist IST die Allowlist-Definition; eine doppelte Indirektion über Bash-Wrapper-Skripte hätte tote Referenzen erzeugt.

---

## E16 — Sicherheitsgrenze über Write-Tool-Deny, nicht über Bash-Pattern-Matching

**Auslöser:** Im Bündel-E-Verifikationstest griffen die Write-Allow-Patterns in `--print/default`-Modus nicht. Erste Reaktion-Option: Bash-Schreibvehikel wie `tee` als Workaround zulassen. Architekt hat das verworfen.

**Entscheidung:** Mode `acceptEdits`, **Write-Tool** übernimmt das Schreiben strukturell, **Bash bleibt schreibfrei** (kein `tee/echo/cat/ls/touch` mehr in Allow). Sicherheitsgrenze ist eine umfassende Deny-Liste auf Pfade, dual gepflegt mit absoluten UND cwd-relativen Patterns (Claude Code matched Pfade unter cwd als relative, sonst greift's nicht). Filesystem-verifiziert: 9 sensitive Schreibversuche → alle blockiert.

**Warum:** `Bash(tee ...)` durchbricht die saubere Trennung „Bash inspiziert, Write schreibt". Bash-Pattern-Matching ist eine schwächere Garantie als ein strukturell auf Pfade begrenztes Write-Tool — verkettbare Kommandos, Flag-Varianten, Pfad-Tricks, Shell-Redirects mit `>`. Bei einem nachts unbeaufsichtigten, injection-exponierten Lauf zählt die strukturelle Grenze, nicht Pattern-Match-Akrobatik.

**Verworfene Alternative:** Option A `Bash(tee ...)` als enges Schreib-Vehikel. Eng, aber semantisch unsauber und über Shell-Redirect-Umwege angreifbar.

**Caveat:** `acceptEdits`-Mode macht die Deny-Liste zur einzigen Grenze (kein „ask"-Netz). Folge: jede Deny-Erweiterung künftig sorgfältig pflegen — Lücke in der Deny = Schreibrecht. Initiale Verifikation hat alle definierten Schutzbereiche bestätigt.

---

## E17 — GitHub-Sync auf kuratierte Übersichten begrenzen, nicht voller Systemzustand

**Auslöser:** In Bündel D stand der Plan, den gesamten `<Bereich>/Systemzustand/**` ins GitHub-Sync-Repo zu spiegeln. Architekt korrigierte vor der Anlage.

**Entscheidung:** Nicht der ganze Systemzustand, sondern pro Bereich ein dedizierter `<Bereich>/Systemzustand/00_Uebersicht/`-Unterordner mit kuratierten MD- und SVG-Übersichten. Whitelist entsprechend umgestellt. Bereichs-Übersichten werden als eigener Folgezyklus aufgebaut (Backlog-Seed `seed-bereichs-uebersichten.md`).

**Warum:** Voller Systemzustand wäre Datenvolumen, hohe Volatilität (jede Detail-Änderung triggert Sync-Drift), Mandantennähe-Risiken (in Architektur-/Workflow-Dokus stecken Container-Namen, Pfade, Tenant-IDs). Übersichten sind ABGELEITETE Sicht (vgl. Verfassung 03 SSOT) — verdichten, was im Systemzustand steht, und sind beim Aufschreiben bewusst über das, was geteilt wird. Doppelnutzen: dieselben Übersichten sind auch für den Menschen die schnelle Verdichtung des Bereichs.

**Verworfene Alternative:** Voller Systemzustand. Verworfen wegen Volumen, Volatilität und unkontrollierter Sichtbarkeit.

**Kontextbindung:** Falls die Übersichten in der Praxis als zu schmal empfunden werden (Architekt sieht im Chat zu wenig), kann gezielt zusätzliche Systemzustand-Pfade in die Whitelist aufgenommen werden. Strenge Erweiterung, nicht pauschales „alles wieder rein".

---

## E18 — Reaktive Notbremse bewusst ausgeschlossen (Mail ist Sackgasse)

**Auslöser:** Beim cf-alarm-Setup in Bündel E die berechtigte Frage des Architekten: „Kann ich auf die Mail antworten und du würdest wirklich den Dienst abschalten?" — und die Folgefrage nach dem Risiko, wenn Claude per Mail Befehle entgegennehmen könnte.

**Entscheidung:** Nein. Die Alarm-Mail ist strukturell eine Sackgasse — kein Mail-Inbox-Listener, kein Reply-Parser, kein Pfad von der Mailbox zum Tool-Set. Das „Maßnahmen-Angebot zur Bestätigung" im Mail-Body ist als Vorlage für die nächste Chat-Session formuliert, nicht als ausführbares Kommando. Autonomes Eingreifen bei Cloudflare-Anomalie ist explizit ausgeschlossen und in einen separaten späteren Zyklus (Backlog) ausgelagert.

**Warum:** E-Mail-Adressen sind trivial spoofbar; SMTP authentifiziert den `From:`-Header praktisch nicht. Selbst mit DKIM/SPF/DMARC strikt + Body-Token wäre der Mail-Kanal als Trigger destruktiver Aktionen die schwächste Stelle eines sonst dreifach abgesicherten Setups (OS-Rechtegrenze + Tool-Freeze + Sig-Check des Webhooks). Verfassung 04 sagt zudem explizit: „autonomes Eingreifen ist bewusst in ein separates späteres Projekt ausgelagert — eine automatische Stopp-Befugnis ist selbst ein Risiko: ein provozierter Fehlalarm würde zum Denial-of-Service über die eigene Automatik".

**Kontextbindung:** Sobald die Erkennungs-Pipeline (Bündel E) lang genug live ist, dass Fehlalarm-Raten und Reaktions-Muster bekannt sind, kann ein eigener Zyklus eine eng definierte Notbremse erwägen — aber nicht via Mail-Reply, sondern via klar autorisiertem Kanal.

---

## E19 — „Health-Check-Lüge" bei Container-Recreate (Verfassung-04-Anwendung auf Apply-Seiteneffekte)

**Auslöser:** In PLAT-001 Bündel 3.1 (Docker-CE-Update) kollabierte beim Daemon-Restart ein latenter `db_authentik`-Netzwerk-Anker im laufenden `internal_postgres`-Container. Saubere Recovery via `docker compose up -d --force-recreate postgres-hub` — Postgres-Container war anschließend „healthy", aber die Stichprobe „Datenbestand vollständig?" hätte ohne expliziten Check gefehlt.

**Entscheidung:** Bei DB-Containern (und allen Containern mit persistentem State) ist `docker ps` / Service-up-Status **kein** Beweis für Datenbestands-Integrität. Pflicht-Pattern: nach jedem Apply-Bündel mit DB-Seiteneffekt (auch ungeplant) eine Inhalts-Stichprobe (Anzahl Repos / Workflows / User / Datenvolumen) als Beweis, dass die Daten noch da sind. Health-Check-Definition ist domänenspezifisch, nicht generisch.

**Warum:** Container-Lifecycle-Mechanismen können Daten technisch intakt lassen (Bind-Mount-Verifikation: 2.1 G unverändert) UND trotzdem über Schema-/Netzwerk-/Volume-Anker-Bruch funktional kompromittieren. Verfassung 04 sagt „bewiesen, nicht angenommen" — ein generischer `is-running`-Check ist eine Annahme über Daten, kein Beweis.

**Verworfene Alternative:** „Container healthy reicht als Health-Check". Verworfen durch konkreten Vorfall: hätte den Postgres-Bruch erst beim ersten Workflow-Run sichtbar gemacht — Stunden nach Apply, mit unklarer Ursache-Wirkung-Kette.

**Kontextbindung:** Gilt für alle Apply-Bündel mit DB-Container-Recreate, auch wenn das Recreate ein **ungeplanter Seiteneffekt** des eigentlichen Apply-Ziels ist. Ist als Pflicht-Block in `nacht-aufgaben.md` verankert.

---

## E20 — Docker bleibt blacklisted/manuell (Container-Lifecycle-Seiteneffekt als Begründung)

**Auslöser:** PLAT-001 Spec v2 hat in Block 3 die Entscheidung „Docker dauerhaft im nightly-Pfad oder weiter manuell" offen gelassen, mit Entscheidung NACH dem Apply.

**Entscheidung:** Docker bleibt manuell-getestet (Default), KEIN autonomer nightly-Pfad. Auch nicht in PLAT-002 mit Hook.

**Warum:** Docker-Updates rebooten den Daemon und können beliebig komplexe Container-Lifecycle-Seiteneffekte auslösen, die je nach laufender Topologie unterschiedlich kollabieren (siehe E19 / Postgres-Recreate). Diese Seiteneffekte sind nicht durch ein generisches Health-Check-Pattern abdeckbar — sie erfordern Domänenwissen über die jeweilige Topologie zum Zeitpunkt des Updates. Ein autonomer Lauf hätte keinen sinnvollen Health-Beweis. Außerdem: Docker-Frequenz ist niedrig (Patches ~monatlich), Komfortgewinn durch Automatik gering, Risiko hoch.

**Verworfene Alternative:** Docker als „normalen" 1b-Pfad behandeln nach Spec v2 Block 1. Verworfen durch konkreten Vorfall in 3.1: der Postgres-Recreate war kein Bug von Docker, sondern ein latenter Topologie-Zustand, der nur beim Daemon-Restart sichtbar wird. Nicht vorhersagbar, nicht generisch zu testen.

**Kontextbindung:** Falls künftig mehrere Docker-Updates ohne Seiteneffekte durchlaufen (5+ Bündel beobachtet), kann das neu bewertet werden. Aktuell n=1 (PLAT-001), und das eine hatte einen Seiteneffekt — keine Datenbasis für Automatisierung.

---

## E21 — Tailscale bleibt manuell (Third-Party + eigener-Zugangsweg-Risiko)

**Auslöser:** PLAT-001 Spec v2 Block 3 forderte Tailscale-Update + dokumentierte Pfad-Zuordnung danach.

**Entscheidung:** Tailscale bleibt manuell-getestet, KEIN autonomer nightly-Pfad. Auch nicht in PLAT-002 mit Hook.

**Warum:** Tailscale ist Third-Party-Repo (eigene apt-Quelle, eigenes Signing-Key-Lebenszyklus) UND **der primäre Fernzugriffsweg** auf den Server. Ein fehlgeschlagenes Tailscale-Update kann den eigenen Zugangsweg kappen — der nightly hätte dann keinen Rollback-Pfad mehr für sich selbst, weil der menschliche Rückfall-Zugang (SSH über Tailscale) gerade nicht verfügbar ist. Der Architekt muss bei Tailscale-Updates anwesend sein, um den Rückfall (Hetzner-Web-Konsole) zur Verfügung zu haben.

**Verworfene Alternative:** Tailscale wie andere `-updates`-Pakete behandeln. Verworfen, weil das Risiko nicht im Update selbst liegt, sondern in der Kombination „Update-Fehler + kein Rückfall-Zugang ohne Menschen vor Ort".

**Kontextbindung:** Falls ein zweiter unabhängiger Fern-Zugangsweg existiert (z. B. SSH über öffentliche IP mit Key + 2FA), kann das neu bewertet werden. Aktuell ist Tailscale der einzige Pfad.

---

## E22 — settings.json ist im `acceptEdits`-Modus eine Deny-Blacklist für Bash, keine Allowlist (E13 strukturell erst mit PreToolUse-Hook)

**Auslöser:** PLAT-001 Q2: Beim Schärfen des nächtlichen Tool-Sets für den `-updates`-Auto-Pfad (Block 1b autonom) sollte `Bash(sudo apt-get install:*)` als spezifischer allow gegen `Bash(sudo:*)` deny gestellt werden. Vier Echttests (`claude --print --settings $NIGHTLY_SETTINGS --permission-mode acceptEdits`):
1. Baseline mit `Bash(sudo:*)` deny + KEIN sudo-allow + Prompt „führe `sudo --version` aus" → blockiert.
2. Konflikt mit `Bash(sudo:*)` deny + `Bash(sudo --version)` allow + selber Prompt → ebenfalls blockiert. **→ deny dominiert auch strikt spezifischeres allow.**
3. Mit `Bash(sudo:*)` aus deny entfernt + zwei spezifische sudo-allows + Prompt „führe `sudo whoami` aus" (NICHT in allow) → ging durch. **→ Bash-Calls außerhalb der deny-Liste werden durchgelassen, allow-Liste hat keinen filternden Effekt.**
4. Selber Prompt mit `whoami` (kein sudo, gar nicht in allow oder deny) → ging durch. Bestätigung.

**Entscheidung:** `settings.json` im `acceptEdits`-Modus ist für Bash strukturell **eine Deny-Blacklist**, keine Allowlist. E13 („Repo definiert AUSWAHL aus OS-Allowlist, nie FÄHIGKEIT") ist auf der Bash-Tool-Ebene **erst mit einem PreToolUse-Hook** strukturell realisiert. Bis dahin: jede Bash-Tool-Erweiterung, die im nightly Allowlist-artige Garantien voraussetzt (z. B. „`sudo apt-get install`-Recht nur in genau dieser Form"), wird **NICHT scharf geschaltet** — die Mechanik wird gebaut, aber der autonome Lauf wartet auf den Hook.

**Warum:** Eine Deny-Blacklist gegen `sudo`-Formen ist durch Shell-Form-Variation schlagbar (`sudo bash -c`, `sudo sh -c`, `sudo find -exec`, `sudo install`, lokales `.deb` mit Maintainer-Skript, neuer ungelisteter Aufruf) — exakt das E16-Antimuster, das für den nachts unbeaufsichtigten, injection-exponierten Lauf bewusst verworfen wurde. Bei E16 hatte die Lösung „Write-Tool als strukturelle Pfad-Grenze" die Schreib-Ebene gelöst; die Bash-Ausführungs-Ebene bleibt offen, bis ein analoger struktureller Mechanismus existiert.

**Verworfene Alternative (Option C in PLAT-001 Q2):** Deny-Blacklist gefährlicher sudo-Subkommandos breit machen (`sudo rm:*`, `sudo dd:*`, `sudo systemctl stop:*` etc.) und produktiv scharf schalten, bevor der Hook gebaut ist. Verworfen, weil das exakt die Pattern-Akrobatik wäre, die E16 schon einmal bewusst verworfen hat — schwächere Garantie, durch eine vergessene Shell-Form kippbar, im injection-exponierten Nachtlauf scharf geschaltet. Komfortgewinn (autonomes `-updates`-Patchen ein paar Wochen früher) rechtfertigt das Risiko nicht: `-updates` sind aktuell alle eingespielt, `-security` läuft autonom über unattended weiter.

**Kontextbindung:** Sobald PLAT-002 den PreToolUse-Hook gebaut und unter Aufsicht bewiesen hat, kann die Mechanik in `nacht-aufgaben.md` (1b autonom, Reboot autonom) scharf geschaltet werden. Bis dahin gilt: jede Erweiterung der Bash-allow-Liste in `~/.claude-nightly/settings.json` hat keinen filternden Effekt und sollte aus E13-Klarheits-Gründen **trotzdem** gepflegt werden (Dokumentations-Wert: „diese Tools sollten erlaubt sein, sobald die Allowlist-Mechanik existiert"). Wer in der Übergangszeit Bash-Tool-Set ändert, muss die Deny-Liste prüfen, weil DORT die echte Grenze sitzt.

## E23 — Warum drei Prozess-Stufen (Spur / Sprung / Schritt) statt einer Einheitsgröße

  

**Auslöser:** Nach den ersten beiden Zyklen (BOOT-001, PLAT-001) fiel auf: Das Framework kennt nur EINEN Gang — den vollen Neun-Phasen-Zyklus mit Faktensammlung, Spec, Machbarkeit, Abschluss-Doku. Für große Vorhaben (Data-Integrity-Pipeline, Cloudflare-Härtung) richtig. Aber für kleine, konkrete Aufgaben (ein Bugfix wie der Whiptail-Dialog, ein Doku-Update) verbrennt dieselbe Zeremonie unverhältnismäßig viel Zeit. Geschwindigkeit ist das oberste Ziel des Solo-Gründers; die Einheitsgröße bremste sie bei kleinen Tasks strukturell.

  

**Entscheidung:** Drei Stufen, getrennt nach **Zeremonie-Bedarf** (nicht nach Thema):

- **Spur** = voller Zyklus (alle 9 Phasen, alle Dokumente). Für Architektur-Gestaltendes, Kundendaten/Auth/Netz, oder Risikoklasse `kritisch`.

- **Sprung** = verschlankt (kombinierte Spec mit inline-Fakten/Machbarkeit, kurze Abschluss-Notiz, ein Stopp). Für konkrete, abgegrenzte Aufgaben mit überschaubarem Risiko.

- **Schritt** = prozessfrei (kein Dokument, nur eine automatische Zeile in `<Bereich>/Schritt-Log.md`). Für triviales, reversibles, isoliertes Zeug.

  

Harte Regeln: Risikoklasse `kritisch` erzwingt immer Spur (kein Klein-Fahren aus Bequemlichkeit). Einstufung ist **Beratung** (Chat-Architekt + Claude Code schlagen vor + begründen, was wirklich dranhängt), nicht Mensch-Alleinentscheidung — der Mensch revidiert. Eskalation während des Laufs (Sprung → Spur) wird beratend vorgeschlagen, bei Zustimmung Stopp + Vermerk + voll weiter.

  

**Warum:** Zwei Achsen wurden bewusst getrennt: **Größe/Zeremonie** (Spur/Sprung/Schritt — prozesssteuernd) und **Art** (feature/bugfix/recherche/… — nur Filter-Etikett `art:` im Front Matter, ohne Prozess-Wirkung). Profis trennen das ebenso (ITIL Standard/Normal/Emergency Change nach Risiko; Agile Epic/Story/Task nach Größe; SRE toil vs. Projektarbeit). Eine Bug-/Feature-Matrix hätte die Stufen vervierfacht — genau die Komplexität, die der Solo-Gründer nicht will. Drei Schubladen, in die jeder Task in Sekunden fällt. Die Einstufung als Beratungspflicht schützt davor, dass der Mensch Aufwand unterschätzt (PLAT-001 war als Patch gedacht und entpuppte sich zu Recht als Spur, weil Reboot-Autonomie + Allowlist-Frage aufkamen).

  

**Verworfene Alternative:** (a) Vier Stufen — verworfen, kein Beispiel fiel aus den drei heraus, Zusatzstufe wäre Komplexität ohne Anlass. (b) Achse „Art" prozesssteuernd machen — verworfen, weil ein Bugfix winzig (Typo) oder riesig (Next.js-CVE-Hack) sein kann; die Art sagt nichts über den Aufwand.

  

**Kontextbindung:** Skaliert mit der Praxis. Wenn sich nach mehreren Zyklen zeigt, dass eine Stufe systematisch fehlt oder überflüssig ist, neu bewerten (über Phase 9, nie nach unten).

  

---

  

## E24 — Warum das Logbuch zentral ist (revidiert E14-Teilaspekt)

  

**Auslöser:** Widerspruch zwischen Regel und gelebter Praxis aufgedeckt: E14 hielt fest „Logbuch bleibt bewusst **verteilt**" (pro Bereich), und die Bereichs-Wegweiser (`Plattform/CLAUDE.md` etc.) zeigen auf bereichseigene `Logbuch/`-Ordner. Tatsächlich wurden aber ALLE Einträge (E1–E22, inklusive reiner Plattform-Betriebsentscheidungen wie E19 Postgres, E20 Docker, E21 Tailscale) zentral in `_Betrieb/Logbuch/` geschrieben. Die Praxis hat zwei Zyklen lang intuitiv zentralisiert.

  

**Entscheidung:** Das Logbuch existiert genau EINMAL, zentral in `_Betrieb/Logbuch/`. Keine bereichseigenen Logbücher. Die leeren `<Bereich>/Logbuch/`-Ordner werden entfernt, die Wegweiser angepasst. Bereichszuordnung künftig über Gliederung/Feld innerhalb der einen Datei, nicht über getrennte Dateien.

  

**Warum:** (1) Die gelebte Praxis war klüger als die Theorie — sie hat sich am echten Gebrauch bewährt. (2) Ziele Geschwindigkeit + weniger Komplexität: ein Ort zum Nachschlagen, keine „in welches Logbuch?"-Entscheidung pro Eintrag, triviale Quervernetzung (E22 → E13/E16 in einer Datei statt über Dateigrenzen). (3) Die ursprüngliche E14-Sorge „Zumüllen des zentralen Logbuchs" ist bei aktuellem Volumen (24 Einträge in der gesamten Historie) irrelevant; falls je nötig, löst Gliederung nach Bereich das Auffinden ohne separate Dateien. (4) Konsistent mit E14s eigener Backlog-Logik (zentral, Bereichszuordnung über Feld) — und das übergreifende Logbuch wird ohnehin ins Chat-Briefing gespiegelt (E11), die Bereichs-Logbücher nicht.

  

**Verworfene Alternative:** Verteilt durchziehen (Verfassung-Treue über Praxis). Verworfen, weil das die intuitiv bewährte Praxis der Theorie geopfert hätte und beide Solo-Gründer-Ziele (Geschwindigkeit, weniger Komplexität) verschlechtert.

  

**Verhältnis zu E14:** Revidiert NUR den Logbuch-Teilaspekt von E14. Der Kern von E14 (zentraler Backlog) bleibt unangetastet und wird durch diese Entscheidung sogar bestätigt (gleiche Logik).

  

**Folge-Hinweis:** Die zentrale Datei heißt `ENTSCHEIDUNGS_HISTORIE_FRAMEWORK.md` — der Name verspricht *Framework*-Historie, enthält aber auch Plattform-Betriebsentscheidungen (E19–E21). Beim Umbau entweder umbenennen (allgemeiner) oder intern klar zwischen „Framework-Design" und „Betrieb pro Bereich" gliedern, damit keine stille Namens-Inkonsistenz bleibt.

  

**Kontextbindung:** Sobald ein Team existiert (mehrere Hände, unabhängig wachsende Bereichs-Historien), kann gesplittet werden. Für den Solo-Gründer mit allem in einer Hand gewinnt zentral.

---

## E25 — Telegram + Twilio aus dem Produktbetrieb entfernen (PLAT-009, 2026-05-25)

**Auslöser:** Während PLAT-008 (Plattform-Doku-Bereinigung) sollte als triviales Aufräumen `Twillo.md` gelöscht werden. Beweisführung zum Twilio-Tod legte offen: Telegram + Twilio leben quer durch System: 3 produktive n8n-Workflows mit 16 Telegram-Nodes (allen voran „T: Telegram Router v5" mit 11), Code in 5 langgraph-Codebases, env-Vars in compose, n8n-DB-Credential, ~40 Doku-Erwähnungen. „Schritt 24" (2026-05-11) hatte schon angefangen Telegram zu entfernen, war aber halbfertig — redaktionsplan komplett ausgelassen, content/analytics-Prompts noch telegram-spezifisch, Workflows produktiv.

**Entscheidung:** Telegram und Twilio sind aus dem Produktbetrieb komplett raus. Externe Provider (Telegram-Bot bei BotFather, Twilio-Account) bleiben extern existent (Mensch-Eigentum, für „irgendwann mal"), werden aber nicht mehr genutzt und nicht referenziert. PLAT-008 wird pausiert, PLAT-009 als eigene Spur (`sicherheitskritisch-akut`, weil Code-Eingriff + compose + aktive Workflows) eingeschoben. Mandanten-Kommunikation läuft ausschließlich über PWA-Push + Email-Fallback.

**Warum:** (1) DSGVO-Aufwand für Telegram-Bot-Hosting (Drittland-Datenfluss, AVV) nicht mehr tragbar. (2) Kunden-Einrichtungs-Last (Bot-Token, Chat-ID einrichten) war Hürde im Onboarding. (3) Architektur-Komplexität: `telegram-router.json` war 2383 Zeilen Lava, 6 von 8 letzten Bug-Fixes waren Telegram-Routing. (4) Halbfertige Bereinigung („Schritt 24") ist eigene Risikoquelle — Doku und Code sagen Unterschiedliches, Mensch und KI verlieren das Vertrauen in beide. (5) Eine kuratierte Übersicht (PLAT-008-Ziel) aus widersprüchlichem Code+Doku ist wertlos. Telegram-EOL ist Voraussetzung dafür, dass PLAT-008 sinnvoll abgeschlossen werden kann.

**Verworfene Alternative:** Telegram nur in der Doku als „weg" markieren, Code/Workflows weiterlaufen lassen bis nächste Code-Iteration. Verworfen, weil das exakt die E2-Verletzung wäre („Doku darf nicht lügen") und der Drift erst recht zementiert würde.

**Folge-Hinweis:** WhatsApp wird im Code/Content als Kommunikations-Label und Stil (Kontakt-Kanal, „WhatsApp-Nachricht-Stil") weiter genutzt — losgelöst von Twilio-Integration. Bleibt drin, ist nicht von dieser Entscheidung berührt.

**Kontextbindung:** Sollte irgendwann ein Use-Case auftauchen, der Telegram/Twilio wirklich braucht (z.B. bewusste Reaktivierung als Notification-Kanal), wird das ein NEUER Eintrag mit eigener Begründung — nicht stillschweigend wieder einführen.

---

## E26 — Cloudflare-WAF (OWASP) für Access-Apps deaktiviert (BREAKOUT in PLAT-009, 2026-05-25)

**Auslöser:** Während PLAT-009 Phase 2 konnte der Mensch nicht auf `n8n.prisment.de` zugreifen — Cloudflare-Block-Page nach erfolgreichem Access-Login. Diagnose via Cloudflare-Firewall-Events: OWASP-Rule 949110 „Inbound Anomaly Score Exceeded" blockte legitime n8n-UI-Requests (CSS, JS, /favicon.ico, /healthz, /rest/projects). Klassischer False Positive: n8n-UI sendet POSTs mit JSON-Payloads, die OWASP-Regexen treffen (Code-Nodes mit JS, SQL-Keywords etc.), Anomaly-Score addiert sich auf, dann Block.

**Entscheidung:** Skip-Rule für alle 8 Cloudflare-Access-geschützten Hosts (`n8n`, `git`, `ai`, `crm`, `admin`, `konzept`, `agent-content`, `analytics-admin`) in der Custom-Firewall-Ruleset: deaktiviert für diese Hosts WAF (inkl. OWASP), Bot Fight, Security Level, Rate Limits, UA-Blocks, Hotlink, Zone Lockdown. Cloudflare Access (Identity-Login als Auth) und DDoS-L7-Schutz bleiben aktiv. Public-Hosts (`prisment.de`, `app.prisment.de`, `assets.prisment.de`, `analytics.prisment.de`) unverändert mit voller WAF.

**Warum:** (1) Architektur-Erkenntnis — WAF lohnt sich für **öffentliche** Ziele mit anonymem Angriffs-Traffic. Hinter Cloudflare Access ist anonymer Traffic vor dem Origin blockiert; WAF läuft nur gegen die authentifizierten Admin-Sessions. (2) Doppelter Gürtel ohne Mehrwert — Access ist die Auth, WAF davor produziert nur False Positives bei Web-Apps mit reichhaltigen Payloads (n8n, Twenty-CRM, Open WebUI, gitea — alle haben hochkomplexe POST-Bodies). (3) Konsistent mit bestehendem Pattern bei cf-alarm (HMAC-Sig als Auth, WAF-Skip seit BOOT-001 Bündel E). (4) Cloudflare bewirbt diesen Skip-Pattern explizit für Access-Apps.

**Verworfene Alternative:** OWASP-Override nur für die konkrete Rule 949110 entschärfen. Verworfen, weil das halbe Lösung wäre — andere OWASP-Rules würden bei nächstem Workflow-Edit triggern, derselbe Mensch-Debug-Aufwand wieder.

**Status:** Als QUICK-FIX/BREAKOUT eingeschoben, nicht in der PLAT-009-Spec vorgesehen. Aber als sauber dokumentierte Architektur-Entscheidung mitgenommen.

**Kontextbindung:** Falls jemals eine Access-App auf „öffentlich für alle eingeloggten Google-Konten" o.ä. erweitert wird, ist die Skip-Regel neu zu bewerten. Aktuelle Policy: alle 8 Apps mit `include: email == korbinian.schnall@prisment.de`. Solange das so eng ist, gilt: WAF schützt nur vor dem authentifizierten Admin = sinnlos.

---

## E27 — VERALTET-Header-Pattern als zulässige Form der Doku-Bereinigung (PLAT-008/009, 2026-05-25)

**Auslöser:** Während PLAT-009 Bündel 6 (Doku-Bereinigung nach Telegram-EOL) und PLAT-008 Bündel 4 standen 9 Dokus an, deren Re-Write den jeweiligen Spur-Rahmen gesprengt hätte: 8 große SMA-Dokus (eine mit 76 Telegram-Treffern, eine mit 52) plus `Plattform/Systemzustand/Geteilte-Dienste/KI-Wissenspipeline.md` (komplett im pre-CF-MIG + pre-Mattermost-Modell verfasst). Halbherzige Suchen-Ersetzen-Bereinigung hätte teilkonsistente Doku produziert, die Leser irreführt — exakt die E2-Verletzung („Doku darf nicht lügen"). Komplett-Re-Write wäre der saubere Weg, aber nicht im laufenden Spur-Scope leistbar.

**Entscheidung:** Es ist zulässig, eine inhaltlich überholte Doku-Datei mit einem **VERALTET-MARKIERUNG-Header** zu versehen, der explizit nennt (a) ab welchem Zeitpunkt/Zyklus die Inhalte überholt sind, (b) wo der heutige Soll-Zustand stattdessen steht, (c) auf welchen Backlog-Seed der eigentliche Re-Write zeigt. Die Datei bleibt sonst inhaltlich unangetastet im Systemzustand. Der Re-Write läuft als eigener Folge-Zyklus (eigene Stufe, eigene Spec).

**Warum:** (1) **Ehrliches Markieren ist keine Lüge.** Der Leser (Mensch + Chat-Architekt) sieht beim ersten Blick, dass die Inhalte historisch sind und nicht zum aktuellen System passen. Damit ist E2 erfüllt — die Doku behauptet nichts Falsches mehr, sie deklariert ihren eigenen Veralterungsstand. (2) **Re-Writes haben eigene Stufen-Charakteristik.** Ein 500-Zeilen-SMA-Doku-Re-Write hat eigene Akzeptanzkriterien, eigene Soll-Zustand-Diskussion, eigenen Mensch-Review-Bedarf — gehört in eine eigene Spec, nicht als Anhängsel an einen Bereinigungs-Zyklus. (3) **Alternative wäre schlechter:** entweder Pseudo-Bereinigung (Suchen-Ersetzen ohne Kontext → halbkonsistente Doku, schlimmer als markiert-veraltet) oder die laufende Spur ewig ausdehnen (verzögert Abschluss, blockiert nachgelagerte Schritte). (4) **Begrenzte Lebensdauer:** Der VERALTET-Header verweist auf einen Backlog-Seed. Solange der Seed offen ist, ist die Markierung berechtigt. Wird er nicht abgearbeitet, ist das ein Backlog-Pflege-Problem, kein Doku-Pattern-Problem.

**Verworfene Alternative 1 — Pseudo-Bereinigung:** ein paar Suchen-Ersetzen-Edits, Datei „sieht" gepflegt aus, aber Pipelines/Soll-Zustand-Beschreibungen passen nicht zur Realität. Verworfen weil E2-Verstoß.

**Verworfene Alternative 2 — Datei sofort löschen statt markieren:** Wissensverlust ohne Ersatz. Die historischen Inhalte sind oft als Verlaufs-/Begründungs-Kontext wertvoll (z.B. „warum wurde Telegram-Pipeline gebaut"), auch wenn das System sie nicht mehr lebt. Verworfen weil Kontext-Verlust.

**Pattern-Definition (verbindlich für künftige Anwendung):**

```markdown
> ⚠️ **VERALTET-MARKIERUNG (<Zyklus-ID>, <Datum>):** <ein Satz, was
> sich am System geändert hat und wann.> Telegram-Erwähnungen / X /
> Y in dieser Datei beschreiben den historischen Stand vor <Zyklus>
> (<Datum>) und müssen in einem eigenen Re-Write-Zyklus aktualisiert
> werden — siehe Backlog-Seed `seed-<thema>-rewrite.md`.
```

Direkt nach erster H1-Überschrift einfügen. Frontmatter bleibt
unverändert (insbesondere `stand:` zeigt auf den Zeitpunkt der
Markierung, nicht den Zeitpunkt des ursprünglichen Inhalts).

**Pflicht-Beigabe:** der Backlog-Seed muss existieren, bevor der VERALTET-Header gesetzt wird. Sonst ist es eine Sackgasse.

**Kontextbindung:** Wenn die Häufigkeit dieses Patterns überhand nimmt (z.B. mehr als ~10% aller Systemzustand-Dateien tragen VERALTET-Header), ist das ein Signal, dass die zugrundeliegende Pflege-Mechanik versagt — dann gehört eine Verfassungs-Frage in den Backlog (z.B. „warum entstehen Re-Write-Bedarfe schneller als sie abgearbeitet werden"). Aktuell (Stand 2026-05-25, 9 markierte Dateien) ist die Quote noch klein und durch den großen Telegram-EOL-Schnitt erklärbar.

---

## E28 — Backlog-Priorisierung über Klasse × Zugkraft + harte Regel + generiertes Derivat (PLAT-012, 2026-05-25)

**Auslöser:** Der zentrale Backlog war auf 19 Seeds + 3 aktive Roadmaps gewachsen. Das `prioritaet`-Feld war Freitext (mal „HOCH", mal „niedrig (erst nach …)", mal leer), Abhängigkeiten zwischen Seeds existierten nur in `abhaengig_von:`, wurden aber nirgends sichtbar gemacht, und es gab keine konsistente Antwort auf „was kommt als nächstes?". Der Next.js-Sicherheitsvorfall lieferte zusätzlich die Lehre: Security darf strukturell nicht zwischen Wartung und Feature versinken. Der Mensch konnte die Reihenfolge nicht mehr im Kopf halten, und die Faktensammlung zeigte, dass 6 von 19 Seeds (Framework-Arbeit) in keine der vier Bedarfs-Klassen security/feature/qualitaet/wartung sauber passten.

**Entscheidung:** Drei neue Pflichtfelder für Seeds und Roadmaps — `klasse` (5 Werte: `security`/`qualitaet`/`prozess`/`feature`/`wartung`), `zugkraft` (`jetzt`/`bald`/`irgendwann`), `beruehrt` (Ordner-Ebene). Fünfte Klasse `prozess` für Framework-eigene Arbeit. Harte Regel: `security`-Seeds nie auf `irgendwann`, `wartung`-Seeds nie auf `jetzt` (Default mit Begründungszwang). Risikoklasse `kritisch` UND `sicherheitskritisch-akut` erzwingen Spur (bestehende Regel erweitert). Sortier-Primat: `zugkraft` schlägt `klasse` — Dringlichkeit vor Bedarfsart; Klassen-Reihenfolge `security → qualitaet → prozess → feature → wartung`. Generiertes Backlog-Derivat (`00_UEBERSICHT.md` + farbcodiertes Grid-SVG) wird beim Anlegen/Archivieren eines Seeds im selben Commit aktualisiert, technisch erzwungen durch einen pre-commit-Hook im knowledge-base Repo.

**Warum:** (1) **Klasse × Zugkraft ist die einfachste vollständige Aufspaltung.** Klasse beantwortet WAS, Zugkraft beantwortet WANN — jede Kombination ist sinnvoll, keine ist redundant. Freitext-`prioritaet` enthielt beides vermischt und war deshalb nie maschinell auswertbar. (2) **Fünfte Klasse `prozess` empirisch belegt.** Bei der Migration mussten 6 der 19 Seeds in `prozess` einsortiert werden (Backlog-Priorisierung, Autonomie-Korridor, Skills, Feature-Flag-Zustand, Bereichs-Übersichten, Dateisystem-Bereinigung). Eine vierte-Klassen-Lösung hätte Framework-Arbeit mit Kosmetik vermischt — semantisch unsauber. (3) **Zugkraft schlägt Klasse**, weil Dringlichkeit immer über Bedarfsart steht: ein `prozess`+`jetzt` (Engpass blockiert) gehört vor ein `feature`+`irgendwann`. (4) **`prozess` vor `feature`** in der Klassen-Reihenfolge, weil Framework-Arbeit Fundament für alles andere ist — Features auf wackligem Fundament zu bauen ist teurer als die Reihenfolge umzukehren. (5) **Pre-commit-Hook statt nur Norm**, weil die Erfahrung aus PLAT-001/-009 zeigt: rein normative Pflicht-Tore werden vergessen, sobald sie nicht technisch erzwungen sind. Der Hook macht das, was die Verfassung ohnehin sagt, automatisch.

**Verworfene Alternative 1 — `prioritaet`-Freitext schärfen.** Hätte das Strukturproblem nicht gelöst: Freitext bleibt nicht maschinell verarbeitbar, Mischung von WAS+WANN bleibt drin. Verworfen, weil das Werkzeug (Übersichts-Skript) zwei unabhängige Achsen braucht.

**Verworfene Alternative 2 — `block:`-Feld jetzt einführen.** Im ursprünglichen Seed-Vorschlag enthalten, vom Architekten in der Spec verworfen: bei 19 Seeds reicht `geltungsbereich` als Gruppierung. Wird zum Folge-Seed, falls die Übersicht später unübersichtlich wird; dann mit max. 4–5 Blöcken. Verworfen, weil Kosten (Pflege + Vergabe-Entscheidung pro Seed) den heutigen Nutzen übersteigen.

**Verworfene Alternative 3 — Vierter Geltungsbereich `betrieb`.** Kurz erwogen, weil viele `prozess`-Seeds inhaltlich Framework sind, formal aber als `alle` getaggt. Vom Architekten verworfen: logisch ist es dasselbe, und ein vierter Bereich würde die Sync-Whitelist, das ID-Schema und die Bereichs-Logik aufwerten ohne klaren Mehrwert.

**Verworfene Alternative 4 — Nightly-Anbindung des Skripts.** Wäre eine vierte Auslösebahn (neben Anlegen, Archivieren, manuell) gewesen. Verworfen, weil das Nightly-System aktuell nicht stabil ist (siehe `seed-os-patching-broken`); eine kaputte Nightly würde stille Übersicht-Drift erzeugen.

**Kontextbindung:** Wenn der Backlog auf >50 Seeds wächst und die `geltungsbereich`-Gruppierung in der Übersicht unübersichtlich wird, ist das das Signal, das `block:`-Feld nachzuziehen (Verworfene Alternative 2 reaktivieren). Wenn `beruehrt:`-Overlaps häufig zwischen verschiedenen Strängen entstehen und parallele Sessions konkret blockieren, ist es Zeit für die echte Parallelisierungs-Karte (heute nur Spalten-Trennung als Heuristik). Beides eigene Folge-Zyklen.

---

## E29 — Autonome Ausführung im freigegebenen Korridor (PLAT-013, 2026-05-25)

**Auslöser:** Das Framework lief nach wenigen Tagen stabil genug, dass der Engpass von der Agent-Geschwindigkeit zur **Review-Bandbreite des Menschen** wanderte. Der Mensch bekam zu viele Reviews, die er fachlich nicht beurteilen kann/will (Technik), und zu wenig Fokus auf das, was nur er kann (strategische Richtung). Die empirische Auswertung der letzten fünf Spuren (PLAT-001, -008, -009, -010, -012) zeigte: ~30–40% der synchronen Stopps waren Wie-Detail-Fragen, die kein menschliches Wohin-Urteil brauchten.

**Entscheidung:** Nach Spec-Freigabe arbeitet Claude Code autonom innerhalb eines **Korridors**, dessen Wände die Freigabe gezogen hat (Verfassung 00, Abschnitt „Autonome Ausführung im freigegebenen Korridor"). Zwei zwingende Stopps (Spec-Freigabe vorne + Korridor-Bruch dynamisch bei Fall C), vier Stopp-Auslöser (Richtungs-Gabelung mit Wert-Urteil, schwer reversibel, Risikoklasse `kritisch`/`sicherheitskritisch-akut`, Scope-Sprengung), Stufen-Staffelung (Spur stoppt an als `kritisch` markierten Bündeln). Review wandert vom Code-Diff zum **Entscheidungs-Protokoll** (Erweiterung der Abschluss-Notiz, kein neues Template). Pro Bündel ein `kritisch:`-Flag, das die Kritikalitäts-Liste in `01_Spec-Format` ausfüllt (vier Cluster: Sicherheit/Daten/Außenwirkung/Lieferkette + zwei Testfragen für den Graubereich). **Nur-nach-oben** für die Kritikalität, verankert auch im „Tut NICHT" des Arbeitstiers in `02_Rollen-Protokoll`.

**Warum kein Widerspruch zu E3 (Optimierung — nie selbstoptimierend):** E3 verbietet, dass das System sich SELBST verändert (Regeln/Verfassung) ohne Freigabe — das gilt unverändert. Hier wird nur die *Ausführung* freigegebener Arbeit autonom, nie das *Ziehen der Grenzen*. Der bewusste Begriff ist „autonome Ausführung im freigegebenen Korridor", nicht „Selbstoptimierung". Die Verfassung ändert weiterhin nur der Mensch per Freigabe.

**Warum es sicher ist:** (1) **Wohin/Wie-Grenze** (Mensch = Richtung, Claude Code = Technik) macht Stoppen vorhersehbar statt willkürlich. (2) **Feste Kritikalitäts-Liste + Nur-nach-oben-Regel** schließen das Schlupfloch „Claude Code redet Kritisches klein, um durchzulaufen". (3) **Fall-C-Hardstop** (Spec widerspricht der Realität) bricht den Korridor sofort, egal wie spät. (4) **Stufen-Staffelung**: Spur stoppt an jedem `kritisch`-Bündel ohnehin — Autonomie greift nur auf `sicher`-Bündel. (5) **Selbst-Bootstrap-Schutz:** PLAT-013 selbst lief im alten Phase-6-Modus, das Korridor-Modell schaltet erst beim ersten Folgezyklus scharf (PLAT-011-Wiederaufnahme).

**Verworfene Alternative 1 — Pauschal größere Bündel ohne Risiko-Staffelung.** Verworfen, weil kritische Arbeit dann unbeaufsichtigt liefe. Die Stopp-Auslöser-Liste ist der Schutz gegen genau das.

**Verworfene Alternative 2 — Stille-Stufe sofort einführen.** Verschoben. Start mit „alles ist mindestens Information"; Stille wird datengestützt eingeführt, wenn die Protokolle der ersten Korridor-Spuren zeigen, was ohnehin nie relevant war. Sicherer Einstieg.

**Verworfene Alternative 3 — Stopp bei `klasse: security` zusätzlich zu `risikoklasse: kritisch`.** Verworfen (Architekten-Entscheidung): sonst würden triviale Security-Wartungs-Bündel unnötig stoppen. Das echte Risiko fängt die Kritikalitäts-Liste; `klasse` ist Bedarfs-Etikett für die Priorisierung, nicht Risiko-Aussage.

**Verworfene Alternative 4 — Phase 2 (Worktrees) gemeinsam mit dem Korridor freigeben.** Verworfen: erst Korridor an mindestens 3 realen Spuren erproben (Wirkung der Stopp-Auslöser, Tragfähigkeit des Entscheidungs-Protokolls, Vollständigkeit der Kritikalitäts-Liste), dann Worktrees als Folge-Seed (`seed-worktrees-parallele-sessions.md`).

**Kontextbindung:** (a) **Stille-Stufe** scharfstellen, sobald die Information-Protokolle der ersten Spuren genug Daten liefern, was ohnehin nie relevant war. Eigener Folge-Mini-Zyklus. (b) **Sprung-Renaissance** prüfen: in den 14 Tagen vor PLAT-013 wurde keine einzige Stufe Sprung benutzt — nach 3 Korridor-Spuren bewerten, ob Sprung lebendig ist oder strukturell tot (Beobachtungspunkt in der PLAT-013-Abschluss-Doku). (c) **Kritikalitäts-Liste lebt** — Phase-9-Pflicht-Tor erweitert sie bei Bedarf, E3-konform (Mensch gibt frei).

---

## E30 — Doku-Synchronität als Phase-9-Pflicht-Tor, system-zentriert (PLAT-011, 2026-05-25)

**Auslöser:** Drei aufeinanderfolgende Zyklen zeigten Drift zwischen realer System-Veränderung und der nachgezogenen Doku (PLAT-008 Telegram-Reste in Detail-Doku; PRIS-016 leere Helpdesk-Übersicht; PRIS-015 Authentik-SVG-Drift). Disziplin allein reicht nicht — das System brauchte einen strukturellen Mechanismus. Backlog hatte sein eigenes Pflicht-Tor („Übersicht im selben Commit", siehe Verfassung 00 „Backlog-Pflege"); Systemzustand hatte keins. Damit war das Derivat (Backlog-Übersicht) härter geschützt als der Systemzustand selbst.

**Entscheidung:** Neue Bullet in der „Pflicht-Tore"-Sektion von `00_Iterationszyklus.md`: Doku-Synchronität ist Pflicht-Tor in Phase 9, **system-zentriert**. Auslöser ist die reale System-Änderung (Container, Workflow, Skript, Compose, Topologie) oder Umarbeitung bestehender Detail-Doku — nicht primär die Doku-Datei. Pflicht ist explizit zweistufig: **(a)** Detail-Doku im `Systemzustand/` zuerst (SSOT), **(b)** abgeleitete `00_Uebersicht/` hinterher (Verweistabellen, SVGs, Sub-Übersichten). Beide Ebenen sind Korridor-Wand: Diff beim Phase-9-Abschluss = Fall C = synchroner Stopp. Gilt für **Spur, Sprung UND Schritt**, sobald der reale Systemzustand berührt wird; nur rein doku-interne Trivialität (Typo) bleibt ausgenommen. Schwellenwerte als kompakte Tabelle direkt unter der Bullet. Generalisiert das bestehende Backlog-Pflege-Muster (handgepflegt statt skript-generiert). Parallel: neue Bullet in der „Lebenszyklus-Bewegung (Phase 9)"-Sektion (Detail zuerst, dann Übersicht) + Querverweis in der Arbeitstier-Zeile in `02_Rollen-Protokoll.md` (keine Doppeldefinition, nur Konsistenz).

**Warum system-zentriert statt doku-zentriert (zentral):** Ein doku-zentrierter Auslöser („wenn Datei unterhalb `Systemzustand/` geändert wird") lässt system-direkte Änderungen ohne Detail-Doku-Touch durchschlüpfen — genau die Lücke, die in PLAT-008/PRIS-016 sichtbar wurde. Reale System-Änderung als Auslöser dreht die Beweislast: der Zyklus muss aktiv ausweisen, dass keine Doku-Pflicht entstand.

**Warum Schritt nicht pauschal ausgenommen:** Ein Image-Tag-Bump kann ein Schritt sein, ändert aber den realen Systemzustand. Würde Schritt pauschal ausgenommen, wäre genau diese hochfrequente Klasse von System-Berührung dauerhaft ungeschützt. Die Verfassungs-Stufen-Definition (Schritt = trivial/reversibel/isoliert) schließt das nicht aus, daher gilt die Pflicht auch hier — Schwelle ist „System berührt", nicht „Zeremonie-Tiefe".

**Verworfene Alternative 1 — Nightly Drift-Check (Python-Stdlib-Skript mit vier Drift-Klassen, Eintrag in `nacht-aufgaben.md` als Aufgabe 6, Klassen: Container vs. Doku / n8n-Workflows vs. Doku / Bereichs-Übersicht-Vollständigkeit / VERALTET-Marker-Halbwertzeit).** Verworfen, weil Plaintext-grep auf MD/SVG fragil ist (Treffer/Nicht-Treffer hängen an Schreibweise und Markup), die Ziel-Dateiliste selbst drift-anfällig wäre (jeder neue Übersichts-Pfad müsste nachgezogen werden) und der Wartungsaufwand unverhältnismäßig zur aktuellen Systemgröße (~27 Container, überschaubare Doku-Menge). Drift wird stattdessen durch das Pflicht-Tor + Beim-Arbeiten-Drüberfallen abgedeckt. **Folge-Option** (kein Auftrag jetzt): ein schlanker Klasse-C-only-Check (Bereichs-Übersichts-Vollständigkeit — alle `.md` in `Systemzustand/**` müssen in `00_Bereich.md` referenziert sein) ist später denkbar; wenn die Pflicht-Tor-Disziplin im Alltag nicht trägt, kann das als Folge-Seed entstehen. Nicht jetzt anlegen.

**Verworfene Alternative 2 — Pflicht-Tor nur für Übersicht (Detail-Doku weich im Rollen-Protokoll).** Verworfen, weil dann das Derivat härter geschützt wäre als die SSOT — strukturell verkehrt. Erste Spec-Fassung hatte genau diese Form; im zweiten Wurf geschärft.

**Verworfene Alternative 3 — Schwellenwert-Tabelle in `_Betrieb/Templates/` auslagern.** Verworfen für „kompakt direkt unter die Bullet". Die Tabelle ist Teil des Pflicht-Tor-Verständnisses (gibt die Zeile „Typo zählt nicht" mit), nicht Implementierungs-Detail; sie gehört in den Lesefluss der Verfassung. Sieben Zeilen passen.

**Kontextbindung:** (a) Wenn die Pflicht-Tor-Disziplin im Alltag belastet (zu viele Stopps, „Pflicht-Hin- und -Herziehen" für Mini-Änderungen), nach 3–5 Korridor-Spuren bewerten und ggf. die Schwellenwert-Tabelle nachschärfen. (b) Der Klasse-C-only-Drift-Check als Folge-Option (siehe Verworfene Alternative 1) kommt nur, wenn empirisch sichtbar wird, dass Drift trotz Tor durchrutscht — sonst nicht. (c) Querverweis: Backlog-Pflege-Sektion in `00_Iterationszyklus.md` und `ROADMAP_lebende-bereichs-doku` Schritt 8.

---

## E31 — Worktree-Phase scharf geschaltet trotz übersprungenem Drei-Spuren-Gate (PLAT-014, 2026-05-25)

**Auslöser:** `seed-worktrees-parallele-sessions.md` definiert ein hartes Voraussetzungs-Gate vor Spec-Start: „Mindestens drei reale Korridor-Spuren unter PLAT-013-Mechanik gefahren und ausgewertet." Stand 2026-05-25 sind post-PLAT-013 nur **zwei** Spuren archiviert (PRIS-015, PRIS-016). Die laufenden Plattform-Specs (HAERTUNGS_SPEC_PRISMENT, ROADMAP_cloudflare_migration) sind vor PLAT-013 gestartet und liefern keine saubere Korridor-Datenbasis. Architekt entscheidet, das Gate bewusst zu überspringen und PLAT-014 (Worktrees) direkt zu starten.

**Entscheidung:** Drei-Spuren-Gate wird übersprungen. PLAT-014 startet als kombinierte Spur-Spec mit Pilot-Paar-Akzeptanzkriterium (zwei disjunkte `sicher`-Spuren durchlaufen Worktree-Mechanik gleichzeitig). Mengen-Regel „nie zwei `risikoklasse: kritisch`-Spuren parallel" wird in Verfassung 01 verankert. Keine Anzahl-Obergrenze für gleichzeitige Spuren — die Grenze setzt der Mensch über seine Stopp-Auslöser-Aufmerksamkeit. Pilot zählt als erste Erfahrungs-Sammlung, die die ausgefallene Drei-Spuren-Datenbasis nachholt.

**Warum trotz Gate-Übersprung sicher:** (1) Der Bedarf nach Parallelität ist real geworden — der Backlog enthält nach der SMA-Roadmap-Zerlegung 15 zusätzliche Seeds, viele mit disjunkten `beruehrt:`-Mengen. Serielle Abarbeitung würde Monate kosten. (2) Worktree-Mechanik ist additiv, jede Sicherheits-Schicht ist einzeln deaktivierbar (siehe PLAT-014 Rollback-Erwartung). (3) Der ursprüngliche Gate-Zweck war die Bewertung der Korridor-Stopp-Auslöser; deren Tragfähigkeit zeigt sich auch im Worktree-Pilot, also nicht verloren — nur in anderer Reihenfolge erarbeitet. (4) Pilot-Paar ist als `sicher`-Paar definiert; die kritische Mengen-Regel wird über das Skript erzwungen, nicht über Disziplin.

**Folge-Korrektur (Voraussetzung für die Mengen-Regel):** Architekt stellt bei der Spec-Diskussion fest, dass `risikoklasse: kritisch` heute inflationär vergeben wird (DSGVO, Data-Integrity, LLM-Kosten, Meta-API, PreToolUse-Hook etc. tragen das Flag, sind aber faktisch nicht kritisch im Sinne von Unwiederbringlichkeit). Ohne Schärfung wäre die Mengen-Regel „nie zwei kritisch parallel" sofort blockierend. Neuer Seed `seed-kritisch-schaerfen.md` (Sprung, jetzt) läuft direkt nach PLAT-014-Spec-Freigabe und sichtet alle bestehenden Seeds + ergänzt Verfassung 01 um Negativ-Beispiele + Sparsamkeits-Klausel. PLAT-014 selbst bleibt `kritisch` (Branch-/Tree-/Auth-Topologie-Änderung — Listen-Treffer).

**Verworfene Alternative 1 — Auf dritte Spur warten.** Verworfen, weil die nächste sauber-PLAT-013-konforme Spur (vermutlich PLAT-002 PreToolUse-Hook oder der Skalierungs-Härtungs-Seed) selbst mehrere Wochen läuft. Der Engpass ist nicht „zu wenig Datenpunkte für die Korridor-Beobachtung", sondern „zu viele unabhängige Themen, alles seriell". Eine dritte Spur abzuwarten wäre Bürokratie ohne Erkenntnisgewinn — die Erkenntnisse liefert der Worktree-Pilot selbst.

**Verworfene Alternative 2 — Worktrees nur als Werkzeug-Vorarbeit (kein Pilot, kein produktiver Einsatz).** Verworfen, weil eine Werkzeug-Kette ohne Pilot-Run nie beweist, dass sie trägt. Der Pilot ist die einzige ehrliche Form der Tragfähigkeits-Prüfung — kostet wenig zusätzlich, ergibt aber Beweismaterial.

**Verworfene Alternative 3 — Anzahl-Obergrenze (z.B. max. 2 gleichzeitige Spuren).** Verworfen auf Architekten-Wunsch. Die Begrenzung ist nicht Git-mechanisch (Worktrees skalieren), sondern Mensch-Aufmerksamkeit-mechanisch. Der Mensch ist die natürliche Stopp-Instanz und entscheidet bewusst, wie viele Spuren parallel laufen. Eine Verfassungs-Zahl wäre Pseudo-Sicherheit — die echte Steuerung ist die Mengen-Regel auf `kritisch` plus das Disjunktheits-Skript.

**Kontextbindung:** (a) Korridor-Beobachtungs-Auswertung (`seed-korridor-beobachtung.md`) wird verschoben, bis Pilot + 1–2 weitere Worktree-Spuren Daten geliefert haben. (b) Wenn der Pilot zeigt, dass die Mengen-Regel oder das Disjunktheits-Skript regelmäßig zu False-Positives führt (zwei Spuren werden als „kollidiert" gemeldet, obwohl sie es nicht sind), Schärfung in Folge-Mini-Zyklus. (c) Wenn `seed-kritisch-schaerfen` zeigt, dass faktisch keine Spec ehrlich `kritisch` ist, ist die Mengen-Regel im Alltag unauffällig — das ist OK, sie greift als Schutz für Ausnahmen, nicht als Standardfall.

---

## E32 — Bündel-Kritikalität verbindlich in Phase 5 + Phase-5→6-Übergang explizit als Nicht-Stopp (Verfassungs-Sprung, 2026-05-25)

**Auslöser:** PLAT-014-Vorbereitung deckte zwei Lücken im Korridor-Modell auf, die das Stopp-Verhalten unvorhersehbar machten. (1) Die Spec-weite `risikoklasse:` sagt nichts darüber, welche einzelnen Bündel innerhalb der Spec kritisch sind. Phase 5 erzeugt zwar die Bündelung, verpflichtete aber nicht zur Pro-Bündel-Einstufung — Folge: bei einer `kritisch`-Spur war faktisch undefiniert, an welchen Bündeln synchron gestoppt wird. Default „alles kritisch" hätte den Korridor leergelaufen, Ad-hoc-Entscheidung beim Drauftreffen wäre die typische Trägheits-Klasse gewesen, vor der das Framework an mehreren Stellen warnt. (2) Phase 5 sagte „grobe Abweichungen gehen zurück in den Chat", sagte aber NICHT explizit, dass ein sauberes Ergebnis OHNE Rückfrage weiterläuft. Diese Schweige-Lücke lädt einen Gewohnheits-Stopp ein („Machbarkeit liegt vor, freigeben?"), der die Korridor-Mechanik aushöhlt.

**Entscheidung:** Zwei chirurgische Ergänzungen an `00_Iterationszyklus.md`, plus eine Konsistenz-Schärfung in `01_Spec-Format.md`. **(A) Pro-Bündel-`kritisch:`-Flag wird in Phase 5 Pflicht**, drei-wertig (`sicher` / `kritisch` / `sicherheitskritisch-akut`) statt bisher binär. Spec-weite `risikoklasse:` ist Obergrenze für die Bündel-Einstufung, ersetzt sie aber nicht — eine `kritisch`-Spec kann und soll `sicher`-Bündel enthalten. Nur-nach-oben gilt pro Bündel (E26-konform). **(B) Phase-5→6-Übergang explizit als Nicht-Stopp verankert** unter „Die zwei zwingenden Stopps". Liegt die Machbarkeit ohne grobe Abweichung vor (Fall A/B), geht Claude Code direkt in die Ausführung über — keine „Machbarkeit freigeben?"-Rückfrage. Grobe Machbarkeits-Abweichung IST ein Fall C (die Spec funktioniert so nicht) und geht als solcher zurück in den Chat. **Konsistenz in 01:** Beschreibung des `kritisch:`-Flags von „markiert" auf „setzt verbindlich in Phase 5" geschärft; Wertebereich von `ja/nein` auf die drei Risikoklasse-Werte erweitert (vereinheitlicht mit der Spec-weiten Risikoklasse — eine Werte-Skala statt zwei).

**Warum kein Widerspruch zu Vorhandenem:** Beide Patches schärfen das bestehende Korridor-Modell (E26 Listen-/Nur-nach-oben-Mechanik, E29 Autonome Korridor-Ausführung), erfinden nichts Neues. Die Stufen-Staffelung „Spur stoppt an `kritisch`-Bündeln, läuft durch `sicher`-Bündel" stand schon in E29 — funktioniert aber erst zuverlässig, wenn die Bündel-Kritikalität überhaupt verbindlich erhoben wird. Patch A liefert die strukturelle Voraussetzung für eine Regel, die vorher in der Luft hing. Patch B macht aus einer Schweige-Lücke eine sichtbare Wand: Phase 5 endet, Phase 6 beginnt, ohne dass die Gewohnheits-Frage „freigeben?" den Korridor zerreißt.

**Verworfene Alternative 1 — Bündel-Flag binär lassen (`kritisch: ja/nein`).** Verworfen, weil die Spec-weite `risikoklasse:` drei Werte führt und eine Zweit-Skala für Bündel die Lesbarkeit zerreißt. Drei einheitliche Werte machen den Vergleich „ist dieses Bündel mindestens so kritisch wie die Spec?" maschinell und visuell trivial.

**Verworfene Alternative 2 — Patch B im Abschnitt „Stufen-Staffelung" verankern statt unter „Die zwei zwingenden Stopps".** Verworfen, weil die Schweige-Lücke ein Stopp-Verhalten-Problem ist, kein Stufen-Problem. Die natürliche Heim-Sektion ist die, wo die zwingenden Stopps aufgezählt werden — dort fehlte der Hinweis „und Phase 5→6 ist KEIN solcher Stopp". Direkt unter die Aufzählung passt der Negativ-Satz ohne Doppeldefinition.

**Verworfene Alternative 3 — Beispiel-Blöcke in der Verfassung mit konkreten Bündel-Listen ergänzen.** Verworfen auf Architekten-Wunsch. Beispiele leben im Logbuch und in den Specs selbst (PLAT-014 wird der erste echte Anwendungsfall), nicht im Regeltext. Verfassung bleibt prinzipien-knapp.

**Kontextbindung:** (a) Die Mengen-Regel „nie zwei `kritisch`-Spuren parallel" aus PLAT-014 setzt voraus, dass `kritisch` ehrlich vergeben wird — `seed-kritisch-schaerfen.md` läuft parallel als Folge-Zyklus. (b) Wenn nach 3–5 Spec-Zyklen mit Phase 5 das Pro-Bündel-Flag in der Praxis durchrutscht (Claude Code „vergisst" die Pflicht), Ergänzung als Pflicht-Tor wie bei der Backlog-Übersicht. (c) Korridor-Beobachtungs-Auswertung (`seed-korridor-beobachtung.md`) bewertet ohnehin auch die Stopp-Auslöser-Schärfe — dort wird sichtbar, ob Patch B die Gewohnheits-Stopps wirklich eliminiert hat.

---

## E33 — PLAT-014 Pivot: Werkzeug-Schicht steht, Bedien-Modell wandert nach PLAT-015 (2026-05-26)

**Auslöser:** PLAT-014 lieferte planmäßig die Werkzeug-Schicht (Disjunktheits-Skript `check_parallel.py`, Helfer `~/bin/wt`, pre-commit-Hook für `10_Kunden/`-Schutz, Verfassungs-Abschnitt „Parallel-Lauf von Spuren"). Beim Pilot-Pair-Pick (Bündel 5, Wohin-Stopp) wurden zwei Probleme zugleich sichtbar: (1) **kein Backlog-Paar ist gleichzeitig Sprung-reif + disjunkt + Hot-File-frei** — apt-get-pin ist Erinnerungs-Seed für PLAT-002, diun-rueckbau hat unerfüllte Vorbedingung „nightly stabil". (2) Der Architekt artikulierte beim Pilot-Pick ein **anderes Bedien-Modell** als von der Spec vorausgesetzt: vollautonome Parallelarbeit, in der die Werkzeuge nicht vom Mensch aufgerufen werden, sondern eine zentrale Mechanik sie automatisch befolgt — Auto-Worktree-Anlage bei neuer Session, maschinelle Mengen-Regel beim Session-Start, inline-Konflikt-Lösung bei Phase-9-Merge.

**Entscheidung:** PLAT-014 wird ehrlich abgeschlossen mit 4/7 erfüllten Akzeptanzkriterien (Werkzeug-Schicht) und 3/7 vertagten (Pilot, Phase-9-Merge, Pilot-Auswertung). Die vertagten Kriterien werden in PLAT-015 (Spur, kritisch) neu gefasst — vom Mensch-Entlastungs-Ende her statt vom Werkzeug-Ende. PLAT-015 enthält: (a) Auto-Worktree-Anlage als Default für neue Aufträge, (b) maschinelle Durchsetzung der Mengen-Regel „nie zwei `kritisch` parallel" beim Session-Start, (c) Inline-Konflikt-Lösung in der laufenden Session statt separatem Merge-Agent — die Session, die den Konflikt sieht, löst ihn (rein technische Konflikte) oder eskaliert (inhaltliche). Verzicht auf separaten Konflikt-Agent: spart eine Architektur-Schicht; die Session hat den vollen Kontext der bisher gelaufenen Bündel und kann technische Konflikte (Whitespace, getrennte Sections, E-Nummern-Reihenfolge) inline lösen.

**Warum kein Versagen, sondern Korridor-Funktion:** (a) Fall C wurde sauber erkannt (die eigene Spec funktioniert so nicht, weil das Pilot-Akzeptanzkriterium auf unreifen Backlog-Seeds aufbaut). Stopp + zurück in den Chat ist genau die definierte Korridor-Reaktion. (b) Werkzeug-Schicht ist Bauteil, das in PLAT-015 unverändert weiter gebraucht wird — keine verworfene Arbeit. (c) Bedien-Modell-Wechsel passierte am natürlichsten Punkt: bei der ersten echten Anwendung, nicht erst bei drei Pilot-Spuren. Frühe Korrektur statt nachgezogener.

**Verworfene Alternative 1 — Synthetischer Pilot mit unreifen Seeds durchziehen.** Verworfen, weil das ein Pseudo-Wirkungs-Beweis gewesen wäre: zwei Worktrees parallel öffnen, in jedem eine Schein-Spec ausführen, mergen, „Pilot bestanden" deklarieren. Hätte gegen Korridor-Geist verstoßen (Korridor stoppt bei Fall C, nicht bei Bequemlichkeit) und das ehrliche Lernen über das Bedien-Modell verhindert.

**Verworfene Alternative 2 — Separater Konflikt-Merge-Agent (Architekten-Vorschlag in der Diskussion).** Vom Architekten selbst entkräftet zugunsten der inline-lösenden Session. Begründung: die Session hat den Kontext der gelaufenen Bündel ohnehin, eine zweite Agent-Schicht müsste denselben Kontext rekonstruieren — doppelter Aufwand für gleichen Effekt. Auflösungs-Quote bleibt vergleichbar, weil rein technische Konflikte selten ein „Wohin"-Urteil brauchen.

**Verworfene Alternative 3 — PLAT-014-Spec umschreiben statt PLAT-015 als Folge-Spec.** Verworfen, weil das die abgeschlossenen 4/7-Bauteile künstlich offen halten würde. Die Werkzeug-Schicht ist ein eigener Wert; sie als „erst halb fertig" zu deklarieren wäre Doku-Sediment. Saubere Trennung: Werkzeuge geschlossen, Bedien-Modell als eigene Spec.

**Kontextbindung:** (a) PLAT-015 muss `seed-kritisch-schaerfen` als Voraussetzung mitführen — die maschinelle Mengen-Regel ist nur sinnvoll, wenn `kritisch` nicht inflationär vergeben ist (sonst blockiert sie fast jede Parallelität). (b) Beim ersten echten Drei-Sessionen-parallel-Lauf wird sichtbar, ob die Inline-Konflikt-Lösung die typischen Konflikte (E-Nummern, 00_UEBERSICHT-Regenerat, Schritt-Log-Append) sauber erwischt — falls nicht, Schärfung im Folge-Mini-Zyklus. (c) `~/bin/wt` ist nach wie vor unversioniert; PLAT-015 muss die Auto-Anlage-Mechanik so bauen, dass sie auch ohne diesen Helfer-Skript funktioniert (z.B. direkt `git worktree add`), sonst Skript-Versionierung als Vorarbeit zwingen. (d) `risikoklasse: niedrig` im `seed-apt-get-download-pin` ist Verfassungs-Wertebereich-Verletzung — als Befund in `seed-kritisch-schaerfen` mitführen.

---

## E34 — Mengen-Regel als Mensch-Sache: keine maschinelle Wand für Parallel-Spuren (PLAT-015, 2026-05-26)

**Auslöser:** Bei der Spec-Diskussion zu Bündel 3 (`session-start-check.sh` + Hook-Konfiguration) fragte der Architekt: „Brauche ich das überhaupt?". Begründung: Anzahl gleichzeitiger Sessions und Anzahl gleichzeitiger `kritisch`-Spuren sollen in seiner Verantwortung liegen. Wenn Konzentrationsprobleme oder häufende Merge-Konflikte auftauchen, schraubt er die Parallelität selbst zurück. System soll flexibel beschleunigen, nicht Bürokratie aufbauen. Mengen-Regel ist „nur eine Regel für mich selbst", keine maschinelle Wand.

**Entscheidung:** Maschinelle Mengen-Regel verworfen. Konsequenzen, alle in einem Rutsch umgesetzt: (a) Verfassung 01 Abschnitt „Parallel-Lauf von Spuren" auf Selbst-Disziplin umgeschrieben — `check_parallel.py` bleibt als freiwilliges Werkzeug, keine Pflicht-Tor-Sprache, keine Anzahl-Obergrenze, kein Hook. (b) Globale CLAUDE.md Block „Maschinelle Mengen-Regel beim Session-Start" entfernt, ersetzt durch knappen Verweis auf `check_parallel.py`. (c) PLAT-015 Spec und Machbarkeit angepasst: Bündel 3 entfällt komplett (`session-start-check.sh` wird nicht gebaut, kein Hook-Eintrag in `settings.json`). (d) `10_Kunden/`-Pre-commit-Hook bleibt — das ist Datenschutz, keine Mengen-Steuerung.

**Architekten-Erweiterung im selben Schritt — neuer Seed = neuer Worktree, auch in derselben Session.** Der Architekt schärfte zugleich die Auto-Worktree-Logik: innerhalb einer laufenden Session kann er mehrere Seeds nacheinander abarbeiten; jeder Seed bekommt seinen eigenen Worktree, vorher muss die aktuelle Spur in Phase 9 sauber abgeschlossen sein (Inline-Konflikt-Lösung greift wenn nötig). Konsequenz: Phase-9-Merge passiert nach **jedem** Seed/Spec-Abschluss, nicht nur am Session-Ende. Verfassung 01 und globale CLAUDE.md führen diese Regel jetzt explizit.

**Warum kein Widerspruch zu Vorhandenem:** (a) E29 (Korridor-Mechanik) verlangt Stopp an `kritisch`-Bündeln, nicht maschinelle Wände gegen Parallelität — diese Trennung bleibt intakt. (b) PLAT-014-Werkzeuge (`check_parallel.py`, `wt`-Helfer, `10_Kunden/`-Hook) bleiben in voller Funktion; nur der Pflicht-Charakter des Disjunkt-Checks fällt. (c) PLAT-015 reduziert sich auf das, was Mensch-Entlastung wirklich bringt — Auto-Worktree-Anlage und Inline-Konflikt-Lösung — und verzichtet auf die Schutz-Schicht, deren Wert der Mensch nicht spürt.

**Verworfene Alternative 1 — `UserPromptSubmit`-Hook (Claudes Empfehlung in der Diskussion).** Verworfen, weil der Architekt die Mengen-Regel als Selbst-Verantwortung ansieht, nicht als systemische Sicherheit. Skript-Latenz wäre tragbar, aber das ist nicht der Grund — der Grund ist, dass Bürokratie-Schichten den Effekt eines flexiblen Beschleunigungssystems untergraben.

**Verworfene Alternative 2 — `SessionStart`-Hook.** Verworfen aus demselben Grund. Plus: Verfügbarkeit war ohnehin unsicher.

**Verworfene Alternative 3 — Mengen-Regel als CLAUDE.md-Anweisung an Claude (Soft-Wand).** Verworfen, weil Claude die Regel dann bei jedem Session-Start in seinem Kontext mitziehen müsste. Doppelarbeit ohne Mehrwert: der Architekt erinnert sich an seine eigene Selbst-Regel auch ohne Claude-Erinnerung.

**Kontextbindung:** (a) Wenn nach 3–5 echten Parallel-Spuren sichtbar wird, dass die Selbst-Disziplin doch reißt (z.B. wiederholte Merge-Konflikte aus disjunkt-aussehenden Spuren), Re-Evaluation — die Entscheidung ist umkehrbar per neuem Eintrag, nicht hartkodiert. (b) `seed-kritisch-schaerfen` (Bündel 1 von PLAT-015) ist trotzdem sinnvoll geblieben — er war nicht für die Mengen-Regel da, sondern für die ehrliche Backlog-Aussagekraft. Steht. (c) Falls externer Druck (Multi-Mensch-Setup, Audit-Anforderung) später eine maschinelle Wand verlangt, ist `check_parallel.py` als Helfer schon da — Hook-Konfiguration wäre dann ein 10-Minuten-Schritt, nicht ein eigener Zyklus.

---

## E35 — Kein Cloudflare-Access-SSH als dritter Backup-Pfad (ROADMAP_cf_migration Block 7.2, 2026-05-26)

**Auslöser:** Nach Abschluss des SSH-Lockdowns auf Tailscale-IP (Block 7.1) stand die offene Bewertung aus der Roadmap an: lohnt sich ein dritter, von Tailscale und Hetzner-Rescue unabhängiger SSH-Pfad über Cloudflare Access (cloudflared-Brücke mit kurzlebigen Zertifikaten + MFA)? Roadmap-Empfehlung war „nein", Entscheidung formal noch offen.

**Entscheidung:** Nein, kein Cloudflare-Access-SSH. Die zwei bestehenden Pfade — Tailscale-SSH (täglich genutzt) + Hetzner Rescue (Notfall, out-of-band/BIOS-Level) — bleiben als alleinige SSH-Plane. Blockstatus „Block 7.2" in der Roadmap auf abgeschlossen, Phase-7-Backlog der Roadmap bleibt davon unberührt.

**Warum:** (a) **Pfad-Diversität wird verwässert, nicht gestärkt** — CF-SSH liefe über dieselbe Cloudflare-Infrastruktur wie alle Tool-Logins (Block 3.3, Block 4 OIDC). Cloudflare-Account-Block oder globaler CF-Outage nähme CF-SSH und CF-Access-Tools simultan offline — das ist kein zweiter unabhängiger Pfad, sondern ein gebündelter Single-Point-of-Failure. (b) **Hetzner Rescue ist überlegen** — BIOS/IPMI-Level, funktioniert auch wenn das Hauptsystem unten ist, Network-Stack hängt, eigener Konfig-Fehler den Boot blockiert. CF-SSH funktioniert nicht ohne gebootetes Hauptsystem. (c) **Komplexität ohne Mehrwert** — cloudflared-SSH-Setup mit CA-Keys und ssh-config-ProxyCommand fügt eine weitere Komponente (mit eigener Fehleroberfläche) hinzu, deren Nutzen sich nur dann realisiert, wenn Tailscale UND Rescue gleichzeitig ausfallen — ein Szenario, in dem auch CF mit ziemlicher Sicherheit nicht erreichbar wäre.

**Verworfene Alternative 1 — CF-Access-SSH einrichten, weil es bereits am Stack ist.** Verworfen, weil „weil es da ist" kein Sicherheits-Argument ist. Jede zusätzliche Authentifizierungsfläche ist eine zusätzliche Angriffsfläche; Mehrwert muss positiv begründet sein, nicht über Bequemlichkeit.

**Verworfene Alternative 2 — CF-Access-SSH später aktivieren, wenn ein Co-Admin dazukommt (MFA-Audit-Logs).** Aktuell verworfen, bleibt aber als Wiederaufgreif-Trigger im Backlog (Co-Admin-Onboarding). Heißt: heute „nein", aber nicht „nie".

**Kontextbindung:** (a) Re-Evaluation bei Co-Admin-Onboarding oder bei externer Audit-Anforderung, die MFA-protokollierte SSH-Logins verlangt. (b) Falls Tailscale dauerhaft instabil wird (länger als ein Quartal), Rescue als alleiniger Backup neu bewerten und ggf. WireGuard-Self-Host (Headscale) oder CF-Access-SSH als Ersatz prüfen. (c) Entscheidung ist umkehrbar per neuem Eintrag.

---

## E36 — Roadmap-Ebene als Framework-Begriff abgeschafft (PLAT-022, 2026-05-26)

**Auslöser:** Das alte Framework kannte eine „Roadmap"-Ebene über den Vorhaben. Mit dem neuen Stufen-Modell (Spur/Sprung/Schritt) ist sie redundant — eine Spur IST das große Vorhaben, eine Ebene darüber ist Doppelplan. Trotzdem überlebten Roadmap-Konzept-Stellen an mehreren Orten: Vorlage in `Dokument-Templates.md` mit `typ: backlog_seed | roadmap`, Generator-Logik in `generate_uebersicht.py` mit eigener Klassifikation + SVG-Legende, Verfassung-01-Abschnitt „Backlog-Felder (Seeds & Roadmaps)", globaler CLAUDE.md-Sonderfall-Halbsatz „Bestehende ROADMAP_*-Dateien bleiben, bis sie auslaufen", zwei aktive ROADMAP-benannte Dateien (`ROADMAP_cloudflare_migration` + tote Verlinkungen auf gelöschte `ROADMAP_SMA`), Sprachgebrauch in HAERTUNGS_SPEC („Roadmap-Resterampe"), Hochstufungs-Empfehlung in `seed-aktualitaets-vollabdeckung` Z. 170–176. Architekt am 2026-05-26: Roadmap-Ebene ist Relikt, restlos raus — inhaltliche Strategiedokumente, die zufällig „Roadmap" heißen, bleiben.

**Entscheidung:** Roadmap-Ebene als Framework-Begriff abgeschafft. (a) **Neue Klausel in Verfassung 00 Stufen-Abschnitt** „Keine Planungs-Ebene über der Spur" — genau drei Stufen, eine Spur IST das große Vorhaben, Folge-Arbeit als Seeds. (b) **Verfassung 01 + Templates + globale CLAUDE.md** sprachlich + strukturell bereinigt (kein `typ: roadmap` mehr, kein „Seeds & Roadmaps", kein „Sonderfall ROADMAP_-Dateien"). (c) **`generate_uebersicht.py`** ohne Roadmap-Typ-Logik + SVG-Legende — eine Entry-Klassifikation (`backlog_seed`). (d) **`ROADMAP_cloudflare_migration.md`** archiviert nach `Plattform/Archiv/PLAT-CF-MIGRATION/PLAT-CF-MIGRATION_SPEC.md` (Architekten-Entscheid Option ii: faktisch durch, Rest-Backlog lebt als Einzel-Seeds, keine Inhalts-Zerlegung). (e) **HAERTUNGS_SPEC (Arbeitsgedaechtnis + Spiegel)** Sprachpolitur: „Roadmap-Resterampe" → „Phase-7-Backlog", CF-Verweise umgebogen. (f) **Bereichs-Übersichten Prisment** tote ROADMAP_SMA-Links umgebogen auf Backlog-Übersicht + Einzel-Seeds. (g) **`seed-bereichs-uebersichten.md`** als abgeschlossen ins Archiv (toter `ueberfuehrt_in:`-Anker, Pflicht-Tor lebt in Verfassung 00 Phase 9). (h) **`seed-aktualitaets-vollabdeckung.md`** Hochstufungs-Empfehlung durch Schnitt-Logik-bei-Spec-Start ersetzt (vorrangig: PLAT-021 startet darauf).

**Strategie-/Produkt-Doku ausgenommen:** `Prisment/Systemzustand/Strategie/prisment_roadmap_businessplan.md` (Produkt-Fahrplan) und „PWA-Roadmap (noch in Arbeit)" in `03_Onboarding-Pipeline.md` bleiben — das ist nicht Framework-Ebene. Verfassungs-Klausel benennt diese Ausnahme explizit.

**Warum kein Widerspruch zu Vorhandenem:** Roadmap-Ebene war schon vor PLAT-022 funktional ausgehöhlt — `ROADMAP_SMA.md` wurde am 2026-05-25 in 15 Seeds zerlegt (Commit `9700a2c`), `ROADMAP_lebende-bereichs-doku.md` war seit PLAT-011 nur noch im Archiv. Die Bereinigung schärft formal, was inhaltlich schon da war. Die neue Klausel macht die organische Praxis zur Regel.

**Verworfene Alternative 1 — Roadmap-Ebene als „nur historische Wort"-Begriff parken.** Verworfen, weil Vorlagen (`Dokument-Templates.md` `typ: roadmap`) + Generator-Logik das Konzept aktiv am Leben halten. Wort-allein lassen reicht nicht — die Mechanik muss weg, sonst legt der nächste Zyklus ohne Bewusstsein wieder eine an.

**Verworfene Alternative 2 — ROADMAP_cloudflare_migration in Einzel-Specs zerlegen wie zuvor ROADMAP_SMA.** Verworfen (Architekten-Entscheid Option ii), weil die Substanz schon durch (Blöcke 1–7.2 erledigt, Phase-7-Backlog-Reste als Einzel-Seeds extrahiert). Eine Inhalts-Zerlegung wäre Schein-Arbeit ohne neuen Wert. Archivierung als Spec-Dokument hält die History sauber.

**Verworfene Alternative 3 — `prisment_roadmap_businessplan.md` mit-umbenennen, weil „roadmap" im Namen.** Verworfen — das ist Produkt-Fahrplan, kein Framework-Konstrukt. Wortgebrauch außerhalb des Framework-Vokabulars ist neutral. Die neue Verfassungs-Klausel benennt diese Ausnahme explizit, damit künftige Lesart nicht ins Schwurbel rutscht.

**Kontextbindung:** (a) Wenn künftig jemand eine ROADMAP_-benannte Datei anlegen will, blockiert der Default in der globalen CLAUDE.md („Keine separaten ROADMAP-Dateien anlegen") plus die Verfassungs-Klausel. Der Bypass ist eine bewusste Architekten-Entscheidung mit Logbuch-Eintrag, kein Pfad ohne Spur. (b) Falls die Phase-9-Pflicht-Tor-Sprache (Bereichs-Übersicht-Aktualisierung) im Alltag belastet — beobachten, nicht jetzt schärfen. (c) `~/.claude/CLAUDE.md` bleibt nicht-versionierter Architekten-Pfad — wenn Server-Wechsel kommt, ist die Datei verloren. Folge-Schritt-Kandidat (Befund schon offen seit PLAT-014/015), bleibt offen.

---

## E37 — Stopp-Mechanik von Risikoklasse entkoppelt + Silent-Stufe scharfgeschaltet (Verfassungs-Sprung, 2026-05-26)

**Auslöser:** Zu viele Stopps „aus Prinzip" — markiert als `kritisch`, gestoppt, obwohl keine Wohin-Frage anstand. Wurzel: Auslöser 3 band den Stopp an die Risikoklasse statt an ein Ereignis. Zugleich war die Stille-Stufe seit E29 bewusst abgeschaltet („Start ohne Stille"); die Information-Protokolle haben inzwischen gezeigt, was ohnehin nie relevant war.

**Entscheidung:** (1) Risikoklasse ist kein Stopp-Auslöser mehr. `kritisch` = Pflicht-Vorsicht (Backup/Verifikation/Restore) + autonomer Durchlauf; die echten Stopps hängen an Auslöser 1 (Wohin-Gabelung), 2 (irreversibel), 4 (Mensch muss physisch handeln) + Fall C. (2) `sicherheitskritisch-akut` behält einen unbedingten Vor-Stopp für alles, wo das Versagen schon im Verifikationsfenster live wirkt (Tenant/RLS, Live-Auth, irreversible Migration). (3) Mensch-Handlungen werden vorgezogen an den Bündel-/Serien-Anfang. (4) Serien fassen gleichartige Operationen zu einem Vor-Stopp + autonomem Durchlauf + einem Bericht zusammen. (5) Pauschal-Freigaben pro Zyklus für reversible, scope-bekannte Operationen. (6) Stille scharfgeschaltet über eine feste Whitelist; tragender Satz: gemeldet wird die Abweichung, nicht die Einhaltung. Boden: Nicht-Können bricht die Stille sofort.

**Warum kein Widerspruch zu Vorhandenem:** Die beiden „extrem wichtigen" Stopps des Architekten bleiben unangetastet — die Wohin-Gabelung (Auslöser 1) und die Wohin-Änderung (Fall C). Entfernt wird nur die Redundanz: Auslöser 3 stoppte genau die Fälle, die 1/2/4 ohnehin fangen, plus die unnötigen. Das ist die in E29 verschobene Stille, jetzt datengestützt eingeführt (verworfene Alternative 2 aus E29 aufgelöst).

**Verworfene Alternative 1 — neues STOP-A/B/C-Vokabular (Vorschlag des Vor-Agenten).** Verworfen: überschneidet sich namentlich mit dem bestehenden Fall A/B/C bei anderer Bedeutung → Lesbarkeits-Unfall. Substanz in die vorhandenen Begriffe gegossen.

**Verworfene Alternative 2 — Stille voll an (Claude entscheidet, was Routine ist).** Verworfen: das ist das Schlupfloch „Claude redet etwas klein, um durchzulaufen". Stattdessen feste Whitelist (Architekt entscheidet vorab, was still ist), analog zur Kritikalitäts-Liste.

**Verworfene Alternative 3 — alle „Nur EINMAL fragen"-Trigger pauschalierbar.** Verworfen: DB-Migration/Datenlöschung Prod, Traefik/Netz und Datei-Löschung außerhalb KB sind Auslöser-2-Fälle (irreversibel) und bleiben pro Vorkommen.

**Kontextbindung:** (a) Erste Anwendung ist dieser Zyklus selbst (lief autonom durch, ein Entscheidungs-Protokoll am Ende). (b) Globale CLAUDE.md wurde in Bündel 0 erstmals versioniert — vorher außerhalb von Git (Folge-Schritt-Kandidat aus PLAT-015 Bündel 2 damit erledigt). (c) Falls die Silent-Whitelist im Alltag etwas verschluckt, das hätte sichtbar sein sollen → Whitelist-Punkt schärfen oder streichen.
---

## E39 — `auth.tenant_memberships`: kategorisch kein RLS (Brücken-Tabelle definiert Zugehörigkeit, kann nicht durch sie gefiltert werden) — PRIS-017, 2026-05-26

> **Nachträgliche Header-Umnummerierung (2026-05-26 abends):** ursprünglich als E38 gepusht; nach PLAT-002-Merge auf E39 verschoben, weil PLAT-002 parallel ebenfalls E38 reservierte. Body unverändert (Verfassungs-Klausel „Einträge werden nie geändert" gewahrt — Korrektur betrifft nur die Header-Nummer wegen Multi-Branch-Kollision).

**Auslöser:** Phase-5-Machbarkeit zu PRIS-017 (Tenant-Isolation) brachte als grobe Abweichung 1 zurück: wörtliche fail-closed RLS auf `auth.tenant_memberships` mit `tenant_id`-Match bricht den Login. Drei Konsumenten betroffen — pwa-web NextAuth-Adapter (`pwa/pwa-web/auth.ts:106`), pwa-api Membership-Lookup (`pwa/pwa-api/app/auth.py:28`), orchestrator.py 2951/3031 (User-Lookup pro Tenant). Alle laufen zur Login-Zeit oder erzeugen den Tenant-Kontext erst.

**Entscheidung:** Kein RLS auf `auth.tenant_memberships`. Schutz ausschließlich über GRANT-Engerung: `tenant_app_user` bekommt nur SELECT; INSERT/UPDATE/DELETE nur `admin_user` + `pwa_migrator`.

**Warum kategorisch — nicht „App filtert ohnehin":** `tenant_memberships` ist die **Brücken-Tabelle User→Tenant**. Eine `tenant_id`-Policy ist nicht „pragmatisch ungünstig", sondern **kategorisch falsch**: die Tabelle **definiert** die Mandanten-Zugehörigkeit; sie kann nicht **durch** diese Zugehörigkeit gefiltert werden. Der Membership-Lookup **erzeugt** den Tenant-Kontext, **kann ihn nicht voraussetzen**. Eine USER-basierte Policy verschiebt das Problem nur — beim NextAuth-`createUser`-Pfad existiert noch nicht einmal die User-ID, die zum Filtern dienen sollte. Die kategorische Begründung verhindert, dass spätere Spec-Erweiterungen dieselbe Falle stellen („wir machen jetzt doch RLS, App-Code ist sauber genug"). Brücken-Tabellen brauchen Schreib-Pfad-Schutz, keinen Lese-Pfad-Filter.

**Der GRANT-Schutz ist der eigentliche Hebel** — er adressiert das echte Risiko: ein kompromittierter Tenant-Kontext (Angreifer hat Tenant-A-Session) darf keine fremde Mitgliedschaft anlegen können (kein INSERT auf `tenant_memberships` für Tenant B). Das ist Schreibseite. Lese-Disziplin (`WHERE user_id = $1` im App-Code) ist Komplement, nicht der Hebel — diese Disziplin ist ohnehin Spec-übergreifender Bestandteil der Tenant-Isolation und nicht spezifisch für tenant_memberships.

**Verworfene Alternative 1 — USER-basierte Policy (`USING (user_id = current_setting('app.current_user'))`).** Verworfen, weil sie das Problem nur verschiebt: beim `createUser`/`getUser`-Pfad gibt es keinen User-Kontext (User existiert noch nicht oder wird gerade erst erstellt). Außerdem App-Eingriffe in 5+ Stellen plus NextAuth-Adapter-Wrapper. Nutzen gering, weil der echte Schutz Schreibseite ist.

**Verworfene Alternative 2 — Hybrid (kein RLS, aber CHECK-Constraint beim INSERT für tenant_id-Konsistenz).** Verworfen als Doppelung: die GRANT-Engerung erreicht dasselbe — wer kein INSERT-Recht hat, kann auch keinen konsistenten/inkonsistenten Eintrag schreiben.

**Verworfene Alternative 3 — Wörtlich Spec-Ziel 1 umsetzen.** Verworfen, weil Login bricht (Faktencheck-belegt). Wäre Spec ohne Live-Check.

**Kontextbindung:** (a) Wenn später eine neue Tabelle mit Brücken-Funktion (Multi-Tenant-Mapping) angelegt wird, gilt dieselbe Mechanik: GRANT-Schutz, kein RLS. (b) Wenn der Code-Audit bei einer späteren Spur (z.B. Skalierungs-Schulden-Aufräumung) zeigt, dass `WHERE user_id`-Filter doch nicht überall greift, ist das ein Bug-Fix-Auftrag in der App, nicht ein Anlass, hier nachträglich RLS einzubauen. (c) Die Spec-Zielklausel „jede Tabelle mit Mandantenbezug" wird in PRIS-017 explizit eingeschränkt: Brücken-Tabellen sind ausgenommen.
---

## E40 — A3-Migrations-Lücke: `onboarded_at` wurde nicht in `agent_data.auth.users` mitmigriert (PRIS-016b, 2026-05-26)

> **Nachträgliche Header-Umnummerierung (2026-05-26 abends):** ursprünglich als E37 gepusht; nach PLAT-025-Merge auf E40 verschoben (Doppelnummer-Auflösung mit dem PLAT-025-E37). Body unverändert.

**Auslöser:** Beim Daten-Diff in PRIS-016b Bündel 0.1 fiel auf, dass `customer_postgres.pwa_app.users` für beide Live-User produktive `onboarded_at`-Zeitstempel hielt (2026-05-12 14:23:26 / 18:40:59), während `customer_postgres.agent_data.auth.users` für dieselben User `onboarded_at = NULL` zeigte. Die App-Code-Suche bestätigte: `onboarded_at` wird aktiv genutzt (NextAuth-Adapter setzt `token.onboarded`, pwa-api `/me`-Endpoint, orchestrator.py Folge-Posts-Logik via `WHERE u.onboarded_at IS NOT NULL`).

**Befund:** Die A3-Konsolidierung (14.05.2026) hat das Schema von `pwa_app` nach `agent_data.auth.*` migriert, aber den `onboarded_at`-Inhalt nicht mit übertragen. Das ist 12 Tage lang unbemerkt geblieben, weil pwa-web im Profile-Lock offline ist (keine echten Login-Flows zur Tageszeit). Hätte sich erst bei pwa-web-Reaktivierung (HAERTUNGS Phase 6) bemerkbar gemacht — User wären als "noch nicht onboarded" behandelt worden, Folge-Posts-Logik wäre falsch gefeuert. Wurde durch Aufräum-Diff abgefangen, nicht durch Audit.

**Was getan wurde:** PRIS-016b Bündel 0.1.b (`kritisch` eingestuft, weil Kundendaten-Änderung auf Live-DB) hat die zwei `onboarded_at`-Werte mit hartkodierten Statements vor dem Drop nach `agent_data.auth.users` migriert. Idempotenz-Guard `AND onboarded_at IS NULL` schützt vor Doppel-Schreibung. Restic-Snapshot `90b093da` davor angelegt.

**Warum Logbuch-würdig:** Nicht für die Migration selbst (die ist trivial), sondern für das **Muster**: Schema-Konsolidierungen ohne strikten Spalten-für-Spalten-Daten-Abgleich hinterlassen unsichtbare Lücken. Die Lücke wurde 12 Tage später erst beim Aufräumen entdeckt, nicht beim Migrations-Test selbst.

**Kontextbindung:** (a) Wenn künftig eine Schema-Konsolidierung ähnlicher Größenordnung kommt (z.B. Skalierungs-Schulden-Aufräumung mit Tabellen-Zusammenführungen), muss ein Spalten-Diff-Test Pflicht-Tor sein, nicht „danach beim Aufräumen merken wir's schon". (b) Verdacht: weitere A3-Migrations-Lücken könnten in anderen `auth.*`/`public.*`-Tabellen schlummern (NULL-Spalten, wo Werte hätten landen sollen). Vor pwa-web-Reaktivierung (HAERTUNGS Phase 6) ein gezielter Sanity-Check auf "verdächtig viele NULLs in Spalten, die vom App-Code aktiv gelesen werden" sinnvoll — kandidat für eigenen Seed wenn Phase-6-Wiederanlauf naht.

---

## E38 — PreToolUse-Hook ist die echte Bash-Wand, settings-allow nur dokumentarisch (PLAT-002, 2026-05-26)

**Auslöser:** E22 (PLAT-001 Q2, vier Tests) hat bewiesen, dass die settings-`allow`-Liste für Bash im `acceptEdits`-Modus strukturell wirkungslos ist — settings ist eine Deny-Blacklist. E13 („Repo definiert AUSWAHL aus OS-Allowlist") war damit auf der Bash-Tool-Ebene **aspirational, nicht realisiert**. Eine Blacklist gegen `sudo` ist durch Form-Variation schlagbar (`sudo bash -c`, `sudo find -exec`, lokaler `.deb`-Pfad) — exakt das E16-Antimuster, das für den nachts-unbeaufsichtigten Lauf bewusst verworfen wurde.

**Entscheidung:** (1) PreToolUse-Hook `/usr/local/sbin/claude-allowlist-hook` (root-owned, claude-deploy execute-only) prüft jeden Bash-Tool-Call gegen Präfix-Allowlist in `/etc/claude/nightly-allowlist.conf` (root-owned, claude-deploy read-only). (2) `claude --settings`-Argument zeigt auf `/etc/claude/nightly-settings.json` (root-owned), alte `~/.claude-nightly/settings.json` ist Symlink dorthin. **Wer claude-deploy kapert, kann weder Hook noch dessen Aktivierung editieren** — sonst wäre die Wand eine Wand mit unverschlossener Tür daneben. (3) Hook-Default ist **defer** (exit 0 ohne JSON); JSON-allow nur bei eindeutigem Präfix-Match + Argument-Constraint ok. (4) settings-deny bleibt als zweite Linie für defer-Pfade.

**Phase-5-Befund (Spec-Präzisierung):** JSON-`permissionDecision: allow` **überschreibt** settings-deny strukturell. Defense-in-Depth gilt deshalb nur für defer-Pfade, nicht für allowed-Pfade. Hook-Disziplin: niemals JSON-allow mit broader-than-intended-Pattern.

**Bündel-4-Live-Befund:** Parse-Unsicherheits-Filter erkannte zunächst nur Subshell/Process-Substitution/Variable-Indirektion. Live-Test `date; ls /tmp` ging als ALLOW durch (Präfix `date` matched, Rest des Strings durch JSON-allow unkontrolliert). Filter erweitert um `;`, `&&`, `||`, Background-`&`. Heißt: jeder Befehl muss als eigener Bash-Call kommen.

**Verworfene Alternative 1 — settings.json-Hash-Check im Wrapper (statt root-Verlagerung).** Verworfen: verlagert das Schutzproblem auf Wrapper + Soll-Hash (beide claude-deploy-Bereich) und ist Laufzeit- statt OS-Garantie (TOCTOU-Rest).

**Verworfene Alternative 2 — Hook-Self-Check der erwarteten Konfiguration.** Verworfen: struktureller Zirkel — eine entfernte Hook-Registrierung legt den prüfenden Hook still.

**Verworfene Alternative 3 — settings-allow als ausreichende Wand (status quo).** Verworfen durch E22 — bewiesen wirkungslos für Bash, plus E16-Anti-Pattern durch Form-Variation.

**Kontextbindung:** (a) Allowlist-Erweiterungen sind ab jetzt sicherheitskritisch — Logbuch-Pflicht pro Erweiterung. (b) Apply-Autonomie-Politik (welche Allowlist-Stellen scharf geschaltet werden) ist `seed-apply-autonomie-pipeline` (PLAT-026), nutzt diese Mechanik. (c) Pipe `|` bleibt erlaubt (lesende Pipes nightly-üblich); Folge-Verschärfung möglich wenn Risiko sichtbar wird. (d) Allowlist-Drift zwischen Repo-Quelle (`_Betrieb/Skripte/pretooluse-hook/`) und live-Datei (`/etc/claude/`) ist möglich, weil Re-Install via Architekten-`sudo`-Klick läuft — kein Auto-Deploy. Folge-Schritt-Kandidat: Drift-Check-Cron.

**Nachzug 2026-05-26 (Architekten-Audit nach Bündel-4-Abschluss) — Fail-closed einheitlich über alle drei Wege:** Der Hook hatte ursprünglich nur den Parse-Filter fail-closed (exit 2 bei Subshell/Verkettung). Zwei weitere Stellen waren defer (exit 0 → settings-deny greift weiter): (1) Allowlist-Datei nicht lesbar, (2) JSON-Input leer/nicht parsebar. Architekten-Begründung der Korrektur: *defer auf settings-deny ist kein gültiger Failover, weil deny die in E22 widerlegte Blacklist ist.* Das tragende Prinzip der Spur ist „Allowlist statt Blacklist" — eine fail-open-Stelle bei fehlender Allowlist kehrt genau das um und fällt auf die Garantie-Klasse zurück, die diese ganze Spur als unzureichend bewiesen hat. Allowlist weg = Angriff oder System-Bug; ein still gefallener Nachtlauf ist ein E-Mail-Alarm am Morgen, ein durchgelassener Befehl ist eine kompromittierte Maschine — Asymmetrie eindeutig. Beide Stellen jetzt **fail-closed exit 2** mit stderr-Begründung. Damit gilt „bei Unsicherheit blockieren" einheitlich über (i) Parse-Filter, (ii) Allowlist-Existenz, (iii) Input-Validität — Konsistenz, die eine `kritisch`-Spur braucht.

---

## E41 — Phase 7.9 (Tailscale-Layer-4-Härtung) gestrichen als gegenstandslos (HAERTUNGS-Spec, 2026-05-26)

**Auslöser:** Phase 7.9 der HAERTUNGS_SPEC_PRISMENT stammt aus der Zeit der Layer-7-Tailscale-Whitelist (Phase 2 der ursprünglichen Spec, mit `tailscale-only@file`-Middleware auf Traefik-Ebene). Sie war damals als „später härtbar"-Folge-Frage gedacht: ein zweiter Traefik, der nur auf Tailscale-IP bindet (Layer-4-Versteck), statt der bestehenden Layer-7-Whitelist. Mit dem CF-Migrations-Pivot (PLAT-CF-MIGRATION) wurde Block 6 die Tailscale-Web-Whitelist explizit abgebaut — die Layer-7-Schicht, die Phase 7.9 ersetzen sollte, existiert nicht mehr. Plus: SSH-Lockdown (Phase 3 / ROADMAP_cf_migration Block 7.1) bindet `ssh.socket` bereits Layer-4 auf `100.100.69.78:22` exklusiv. Die Frage, was Phase 7.9 noch eigentlich härtet, hat keine Antwort mehr.

**Entscheidung:** Phase 7.9 gestrichen, kein Folge-Seed. Streichung im HAERTUNGS_SPEC dokumentiert (Spec-Tabelle in Phase 7 zeigt 7.9 als „obsolet, siehe Logbuch E41" statt „offen").

**Warum gegenstandslos:** Die Architektur-Schichtenmodell-Lage heute:
- **Tools** (Gitea, n8n, Twenty, Open WebUI, agent-content, konzept, admin): nicht mehr direkt ans Public-Internet, sondern hinter Cloudflare-Tunnel + CF Access. Kein Layer-7-Whitelist-Bedarf mehr, weil keine Layer-7-exponierte Tool-Route mehr existiert.
- **SSH**: Layer-4-Bind auf Tailscale-IP scharf (`ssh.socket` Drop-In `/etc/systemd/system/ssh.socket.d/listen.conf`, ListenStream=100.100.69.78:22). Public-IP-SSH refused. Das ist das, was 7.9 für Tools geplant hatte — bereits aktiv für SSH.
- **Public-Hostnamen** (prisment.de, app, assets, analytics, www): bewusst public hinter CF-Proxy/WAF. Layer-4-Verstecken wäre kontraintuitiv.

**Verworfene Alternative — als Seed im Backlog parken.** Verworfen: ein „Layer-4-Bind"-Seed ohne adressierten Layer-7-Pfad ist ein totes Item; würde nach Monaten als zombie-Punkt auftauchen ohne klaren Trigger. Sauberer Schnitt: streichen, in Phase 7 als „obsolet" markiert, im Logbuch nachvollziehbar warum.

**Kontextbindung:** Sollte das System wieder einen Layer-7-Tool-Pfad eröffnen (Cloudflare als zu unzuverlässig erweisen, Rückkehr zu direkt-Public-Tool-Routing), ist Layer-4-Bind eine sinnvolle Wieder-Aufnahme-Frage. Trigger dann: ROADMAP_cf_migration-Rückbau-Diskussion, nicht selbstständige Re-Aktivierung von 7.9.

---

## E42 — Pattern-Klon-Refactor muss den vollständigen Importer-Graph erfassen, nicht nur die Definitionsdatei (PRIS-017, 2026-05-26)

**Auslöser:** PRIS-017 Bündel 6+7 (content + interview-Agent auf zweistufige Pool-Architektur). Mein Patcher hat `_conn` in `db_sync.py` selbst zerlegt (in `_admin_conn` + `_tenant_conn`), aber `_conn`-Importer in **Subdateien** des Agents (z.B. `material_specs.py`, `nodes.py`, `archetyp_classifier.py`, ...) übersehen. content lief healthy, weil die `_conn`-Importer dort alle lazy (in Funktion-Bodies) waren — ImportError-Bombe ungezündet. interview brach im Container-Start, weil dort `from app.db_sync import _conn` als Modul-Top-Import stand. Bei vier kopierten Agents wäre der Bruch ohne Verifikation pro Agent fünffach geklont worden, bevor er sichtbar geworden wäre.

**Entscheidung:** Zwei Lehren als gemeinsamer Eintrag, weil sie miteinander wirken:

(1) **Pattern-Klon-Refactor-Pflicht:** Bei einem Refactor, der über mehrere kopierte Dateien geklont wird, muss der Audit den **vollständigen Importer-Graph** der refaktorierten Symbole erfassen — nicht nur die Definitionsdatei. Konkret: `grep -rn "from <module> import <symbol>"` in allen Aufrufer-Modulen, vor dem ersten Build des ersten Klons. Sonst wandert ein Bruch lazy durch fünf kopierte Files, bevor er beim ersten Container-Restart-with-Module-Top-Import sichtbar wird.

(2) **Verifikation pro Bündel stützt die Verfassungs-Linie „kritische Bündel nicht in Autopilot stufen":** Der Grund, warum der Bruch nach dem ersten Agent (interview) gefangen wurde statt nach dem dritten oder vierten, war die fünfschrittige Verifikation pro Agent (TEST1-TEST5) — kein Schluss-Bericht-Autopilot. Pattern-Bestätigung ist nicht das Recht, auf Verifikation zu verzichten, sondern ist das Werkzeug, das die Verifikation pro Klon kompakt macht.

**Warum kein Widerspruch zu E37 (Stopp-Mechanik):** E37 abolierte Stopps-aus-Bündel-Bürokratie zugunsten ereignis-getriggerter Stopps. Verifikation pro Bündel ist nicht „Stopp", sondern „Beweis vor Folge-Klon" — gehört in die Vorsichts-Maßnahmen-Pflicht des `kritisch`-Flags, nicht in eine Bürokratie-Wand. Pattern-Bestätigung-nach-erstem-Klon plus mechanischer Audit-Map vor zweitem Klon ist die saubere Anwendung.

**Verworfene Alternative — vollständige Audit-Map bei Spec-Schreibung erstellen.** Verworfen: Pattern-Klone basieren auf der Annahme „Strukturen sind identisch" — wenn die Spec-Schreibung schon die Audit-Map erfordert, ist der Pattern-Klon-Vorteil weg. Die richtige Stelle ist die **Phase-5-Machbarkeit**: dort Audit-Map als Pflicht-Teil aufnehmen, nicht in der Spec selbst. Konkret: bei Pattern-Klon-Specs muss die Machbarkeit eine Importer-Graph-Tabelle enthalten (pro betroffenes Symbol: alle Files die importieren, mit „Modul-Top vs lazy").

**Kontextbindung:** (a) Ergänzungs-Vorschlag für Verfassung 01 (Spec-Format) bzw. das Phase-5-Machbarkeits-Template: Klausel „Pattern-Klone brauchen Importer-Graph-Audit in der Machbarkeit". (b) Konkrete Anwendung beim nächsten Pattern-Klon-Refactor (z.B. wenn `seed-pwa-api-db-pfad-konsolidierung` läuft) — Audit-Map als Pflicht-Output der Machbarkeit. (c) Die fünfschrittige Test-Schablone aus PRIS-017 (leerer-Kontext-ValueError / Negativ / Positiv / Admin-Cross / Tx-Trap) ist wiederverwendbar für jeden RLS-Switch — verdient eine Doku-Vorlage, wenn ähnliche Switches noch kommen.

---

## E43 — A3-Migrations-Vollständigkeits-Lücke nicht endgültig geschlossen (PRIS-017 Glied 0 Rückblick, 2026-05-26)

**Auslöser:** Architekten-Rückfrage nach Zyklus-Abschluss zum A3-Migrations-Check in PRIS-016b Bündel 0.1 (siehe E40 — `onboarded_at` als gerettete Spalte). Konkret: hat der Daten-Diff in Bündel 0.1 das `pwa_app.users`-Schema **vollständig** (alle Spalten) gegen `agent_data.auth.users` abgeglichen, oder nur `onboarded_at`?

**Befund (ehrlich nachgelesen):** Nur die vier Spalten `id, email, name, onboarded_at` wurden verglichen, plus `tenant_memberships` mit `user_id, tenant_id, accepted_at, invited_at`. **Kein vollständiger Spalten-Diff** (`\d`-vs-`\d`) zwischen `customer_postgres.pwa_app.users` und `customer_postgres.agent_data.auth.users`. Heißt: weitere übersehene Spalten in der zu droppenden `pwa_app.users` (etwa Flags, Zeitstempel, neue Spalten die in A3 vergessen wurden) konnten möglicherweise existieren und sind mit dem DROP nicht mehr aus der laufenden DB prüfbar. Restic-Snapshot `90b093da` (12:50 Uhr, vor Bündel 0.1.b) enthält den `pg_dump db_customer_pwa_app.dump` — daraus kann das Schema nachträglich rekonstruiert werden, aber das ist nicht-trivialer Aufwand.

**Entscheidung:** Bekannte Rest-Unsicherheit, kein nachträglicher Fix. Begründung: (a) das einzige festgestellte Anwendungs-Risiko (onboarded_at-Branching-Logik in NextAuth-Adapter / `/me`-Endpoint / orchestrator.py Folge-Posts-Logik) ist abgehandelt. (b) Wenn weitere übersehene Spalten kritisch wären, wäre das in der 12-Tage-A3-Phase zwischen Migration und Aufräum-Diff aufgefallen (pwa-web ist offline → kein NextAuth-Flow, aber Agents UND pwa-api lesen auch aus `auth.users` — ein wirklich-genutztes Feld wäre durch leere Werte bemerkt worden). (c) Restic-Snapshot ist die Hintertür, falls bei pwa-web-Reaktivierung etwas auf eine fehlende Spalte hindeutet.

**Warum jetzt im Logbuch, nicht im E40-Eintrag mit-ergänzt:** Verfassungs-Klausel „Einträge werden nie geändert". E40 dokumentiert die Migration des `onboarded_at`-Wertes; diese Notiz ergänzt die methodische Rest-Unsicherheit als eigener Eintrag — ehrliche Trennung „was wir gemacht haben" vs „was wir wissentlich nicht geprüft haben".

**Kontextbindung:** (a) Vor pwa-web-Reaktivierung (HAERTUNGS Phase 6) ein gezielter Sanity-Check: pro Spalte in `auth.users` (und ggf. weiteren auth.*-Tabellen) prüfen, ob NULL-Werte für die zwei Live-User auffällig viele sind. Falls ja: ins pg_dump aus Snapshot 90b093da schauen, ob die Originaldaten da waren. (b) Methodisch lehrt der Fall: bei DB-Konsolidierungen ist ein automatisierter `\d`-vs-`\d`-Schema-Diff vor dem Aufräumen Pflicht. Verbindung zu E40-Kontextbindung (a) — denselben Befund aus methodischer Sicht.

---

## E44 — Skill-System etabliert: technische Verfahren als SKILL.md im Repo, Lifecycle als Phase-9-Glied (PLAT-028, 2026-05-26)

**Auslöser:** `seed-skills-evaluieren` (stufe sprung) eskaliert zu Spur, weil die Aufgabe von „eine Skill bauen" auf CLAUDE.md-Vollbereinigung + Doku-Scan + dauerhafte Lifecycle-Verfassungsregel wuchs. Trigger-Präzisierung des Seeds: „erst bei 3. Auftreten bauen" wird erweitert auf „fehleranfällig-wiederholt ODER permanent-teuer (always-on in CLAUDE.md)" — Klasse-A-Verfahren erfüllen das zweite Kriterium, kosten Token bei jedem Schritt jedes Agents.

**Entscheidungen (chronologisch, mit Warum):**

(1) **Lade-Pfad — Spec-Annahme `~/.claude/skills/` war falsch.** Phase-2-Faktencheck in v2.1.126 zeigte: Skills laufen aus `~/.claude/commands/*.md` (eigene) oder dem Plugin-Marketplace. Canary-Test in neuer Session bewies aber: direkter Repo-Pfad `<repo>/.claude/skills/<name>/SKILL.md` lädt model-invoked. **Gewählt: Repo-Pfad** statt Marketplace-Konstrukt (architekten-favorisiert 2b-iii) — eine Datei, eine Quelle, kein Plugin-Boilerplate. Verworfen: (a) Symlink von `~/.claude/skills/` auf Repo (Marketplace-2b-iii) — überflüssig, sobald direkter Pfad bewiesen ist. (b) eigenes lokales Plugin mit `extraKnownMarketplaces` — mehr Mechanik ohne Mehrwert für Solo-Betrieb.

(2) **Klassen-Trennung (Spec-konform):** Klasse A (Verfahren, wandert raus) vs. Klasse B (Sicherheits-Leitplanken, bleibt hart always-on) vs. Klasse C (Verfassungs-Router, bleibt) vs. Klasse D (Identität, bleibt). **Leitplanken-Auslagerung in Skills verworfen** (E13/E16-Antimuster): Skill-Trigger ist unzuverlässig — nicht-geladene Leitplanke = Agent ohne Leitplanken = Sicherheitsvorfall. Bei Verfahrens-Skills ist der Fehlfall „Claude versucht es ohne Skill" harmlos.

(3) **CLAUDE-global.md-Bereinigung mit Schranke:** Klasse-A-Zeilen werden erst entfernt, wenn der korrespondierende Skill nachweislich model-invoked greift. Sammeltest (6/6 Skills) ersetzte pro-Skill-Beweis — auf Architekten-Lockerung („nicht jeden einzeln"). Resultat: 8 Skills, CLAUDE-global 443 → 369 Zeilen (~17 % schlanker), Router-Stubs als Brücke.

(4) **Doku-Scan B3 ergänzend:** keine weiteren Klasse-A-Kandidaten außerhalb CLAUDE-global. Bereichs-CLAUDE.md sind reine Router (0 Befehlszeilen); Sicherheits-Runbooks sind Leitplanken (E13/E16-Logik). Bestehender `docker-update`-Stumpf im Repo gehört zum abgegrenzten späteren Sprung (eigene SKILL.md-Struktur mit Schritte/Health-Check/Rollback).

(5) **Lifecycle-Regel (B4) in Verfassung 00 Phase-9-Sektion:** Skill-Kandidat-**Erkennung** + **Vorschlag** automatisch am Zyklus-Ende; **Anlage** strikt durch E4-Schreibrichtung + Architekten-Freigabe — „nie selbstoptimierend" (E3) unverändert. Schwester der CLAUDE.md-Selbstverbesserungs-Regel: gleiche Mechanik, gleicher Geist, kein neues Muster. Verworfen: Auto-Anlage von Skills beim Abschluss (bricht E3).

(6) **Sync-Whitelist:** Skill-Quellen sollen für den Chat-Architekten sichtbar sein → `.claude/skills/**` explizit in `_Betrieb/Skripte/sync_to_github.sh` FILTER aufgenommen + `_Betrieb/sync-whitelist.md` ergänzt. Eine Zeile pro Stelle, minimal-invasiv.

**Kontextbindung:** (a) Der erste „echte Bedarf"-Trigger im Live-Betrieb wird als kurzer Schritt-Log-Eintrag bestätigt — initialer Sammeltest (6/6) belegt nur die Mechanik. (b) Wenn die Lifecycle-Regel im nächsten Zyklus zum ersten Mal einen Skill-Seed produziert, prüfen, ob der Vorschlag-Output-Ort (Entscheidungs-Protokoll des Abschlusses) ergonomisch genug ist — oder ob ein zusätzliches Feld im Abschluss-Doku-Template helfen würde. (c) Verfahrens-Skills aus dem Original-Seed (Docker-Update, Rollback-Pinning, Recreate-Health-Check) bleiben abgegrenzter späterer Sprung, andere SKILL.md-Struktur als reine Nachschlage-Verfahren.

---

## E45 — Härtungs-Vorgang als Incident-Response formal geschlossen (PRIS-018, 2026-05-26)

**Auslöser:** Phase 6 der HAERTUNGS_SPEC_PRISMENT (PWA-Wiederanlauf) ist das letzte unerledigte Tor zwischen CVE-2025-55182-Incident und „technisch empfangsbereit für Kunde #2". Mit PRIS-018 wurde es durchgefahren: `app.prisment.de` proxied=true, Managed-Rulesets greifen, `pwa_web` läuft mit `next 15.5.18` / `react 19.0.6`, Login E2E grün, NextAuth verbindet mit der schmalen `nextauth_user`-Rolle (NOSUPERUSER, NOBYPASSRLS).

**Entscheidung:** Der CVE-2025-55182-Incident-Vorgang wird formal als geschlossen markiert. Die Phasen 0–6 der Härtungs-Spec sind alle abgehakt. Phase 7 (Logging-Forensik, Renovate, Image-Pinning, Test-Cluster-Neuaufbau, Notausgang-Doku) wandert von „Incident-Nacharbeit" in „normales Backlog" — die Seeds existieren schon und werden nach gewöhnlicher Zugkraft priorisiert, nicht mehr unter Incident-Druck.

**Warum jetzt:** (a) Eintrittskarte für Kunde #2 ist physisch gestellt — PWA online, Tenant-isoliert, gepatcht, hinter CF. (b) Phase 7 sind echte Verbesserungen, aber kein Incident-Stopper mehr — die Trennung „Incident-Notwendig vs. nice-to-have" zu verlieren, würde den Druck verschleppen und gleichzeitig nichts mehr bewegen. (c) Das schließt formal das Kapitel ab, das im November 2025 mit dem Scanner-Hit begann und 5,5 Monate Schatten-Existenz hatte.

**Restschulden, ehrlich notiert** (nicht Incident-blockierend, aber registriert):
- `pwa_user.rolsuper = true` (PRIS-017 Bündel 12 hat nur BYPASSRLS entzogen; SUPERUSER impliziert es) → Seed `seed-pwa-user-superuser-entzug` (plattform, qualitaet, bald, kritisch, schritt).
- A3-Migrations-Vollständigkeits-Rest aus E43 — Sanity-Check vor produktivem Schreibverkehr, falls neuer Daten-Druck entsteht.
- `pwa_api` wurde beim `compose up --build pwa-web` mit-recreated (Compose-Topologie-Lese-Trigger). Lief sauber neu hoch, healthy. Nicht eingeplant, aber nicht schädlich — `pwa-api` ist stateless gegen Postgres, und Tenant-Pools werden lazy aufgebaut.

**Kontextbindung:** (a) Wenn in den nächsten 14 Tagen kein Incident-Folgesymptom auftaucht (kein 500er-Cluster, keine WAF-False-Positive, kein DB-Login-Fehler), ist das die Schließung empirisch bestätigt. (b) Sobald Kunde #2 onboarded ist, lebt die Härtungs-Spec als historisches Dokument im Systemzustand weiter — sie wird nicht mehr aktiv gepflegt, sondern nur referenziert.

---

## E46 — Image-Pinning-Default: pure Versions-Tag, nicht `:tag@sha256:digest` (PLAT-030, 2026-05-26)

**Auslöser:** PLAT-030 hat die 7 externen `:latest`/`:main`-Compose-Einträge auf konkrete Versionen gepinnt. Beim Edit stellte sich die Frage: pure Versions-Tag (`gitea/gitea:1.25.5`) oder Hybrid mit Digest-Anker (`gitea/gitea:1.25.5@sha256:f846d26…`)? Hybrid wäre strikter reproduzierbar — bei Tag-Bewegung in der Registry würde derselbe Tag denselben Layer ergeben.

**Entscheidung:** Pure Versions-Tag als Default. Digest-Pin nur dort, wo kein semantischer Tag existiert (im aktuellen Stand genau ein Service: Open WebUI `:main`).

**Warum:** (a) Renovate ist das primäre Ziel der Pinning-Aktion — PLAT-026 (Apply-Autonomie) baut darauf auf. Renovate-PRs gegen `:tag@sha256` haben anderes Verhalten als gegen pure `:tag` (PR-Vorlage ist „Tag-Update + Digest-Update" zusammen, nicht „Tag-Update"). Pure Tag ist der eindeutigere Renovate-Eingang. (b) Striktere Reproduzierbarkeit durch Digest-Pin ist real, aber der Schutz greift nur bei Registry-Manipulation eines bereits veröffentlichten Tags — ein sehr seltenes Angriffsbild, das die Hauptkosten (Renovate-Friktion) nicht rechtfertigt. (c) Wenn später Reproduzierbarkeit harte Anforderung wird (z.B. Compliance-Audit), ist der Hybrid-Switch eine kleine Erweiterung, nicht ein Architektur-Bruch.

**Verworfene Alternativen:**
- **Hybrid `:tag@sha256:digest` für alle:** zu viel Renovate-Friktion gegen sehr geringen Gewinn.
- **Reiner Digest-Pin (`image@sha256:…`, ohne Tag):** macht Diun/Renovate-Erkennung gegen Tag-Index unmöglich — Updates wären unsichtbar bis zum Manifest-Check.
- **Floating-Pin (`gitea/gitea:1.25`):** Auto-Patch beim Pull = Sollwert-Drift unkontrolliert, kein PR-Sichtbarkeit.

**Kontextbindung:** Wenn PLAT-026 zeigt, dass Renovate gegen Hybrid-Pins genauso gut PRs öffnet, wird die Default-Entscheidung obsolet — bis dahin ist „pure Tag" die operative Default. Open-WebUI bleibt Sonderfall, weil Upstream kein semver liefert; bewegt sich Upstream auf semver-Tags, fällt auch dieser Sonderfall.

---

## E47 — pg_hba.conf-Auth-Bypass geschlossen (PLAT-029, 2026-05-26)

**Auslöser:** `seed-pg-hba-haerten` (Klasse security, Zugkraft jetzt) — in der CVE-2025-55182-Auswertung als „aktiv ausgenutzter Bypass-Pfad" markiert. Beide produktiven Postgres-Cluster (`internal_postgres`, `customer_postgres`) hatten `trust 127.0.0.1/32` (+ `::1/128`) in `pg_hba.conf`: jede Code-Execution im PG-Container (Extension-Bug, Injection in geladener Extension, kompromittierter Sidecar) wurde damit zu passwortlosem Superuser. Zusätzlich erlaubte `host all all all scram-sha-256` Connects aus jedem beliebigen Docker-Network.

**Entscheidung:** `trust` für `host`-Einträge entfernt → `scram-sha-256` auf 127.0.0.1/32 + ::1/128. `host all all all` ersetzt durch subnetz-spezifische Whitelist pro Cluster: internal (db_gitea/n8n/twenty/pwa = 172.23/24/25/28.0.0/16), customer (db_customer = 172.29.0.0/16). Umsetzung per `pg_reload_conf()`, kein Restart, Backup-Datei `pg_hba.conf.prev-<timestamp>` direkt im Daten-Volume.

**Warum jetzt:** (a) Architekt hatte den Bypass „eigenständig dringend" markiert; das CVE-Incident-Kapitel (E45) ist formal geschlossen, aber die Audit-Restschulden lebten weiter — pg_hba war die größte davon. (b) Die Voraussetzungs-Inventur (`pg_rollen_inventar.md`) war frisch fertig: ohne sie hätte der Cut auf `scram-sha-256` blind passwortlose Rollen brechen können.

**Entscheidungen (chronologisch, mit Warum):**

(1) **Stufen-Hochstufung Sprung → Spur.** Seed war als `stufe: sprung` formuliert (vor PLAT-012-Lücken-Ergänzung). `risikoklasse: sicherheitskritisch-akut` erzwingt nach Verfassung 00 zwingend Spur — also vollständiger 9-Phasen-Zyklus, Spec/Report/Abschluss-Doku, Phase-9-Merge im eigenen Worktree. Korrektur im Spec-Frontmatter (`stufe: spur`) und Vermerk im Body.

(2) **Reihenfolge `internal` zuerst, `customer` zweite.** Internal trägt mehr abhängige Services (gitea, n8n, twenty) — Bruch wird schnell sichtbar. Customer (Kundendaten-Cluster) bekommt das Sicherheitsnetz aus dem ersten Reload und einen menschlichen Stop dazwischen. Verworfen: parallel oder customer-first (würde Mandanten-Pfad unnötig zuerst riskieren).

(3) **`pwa_user`-Orphan in `internal_postgres` vorab auf NOLOGIN.** Befund aus `pg_rollen_inventar.md`: Login-Rolle ohne DB-Owner, Pre-PRIS-017-Relikt. Architekt-Entscheidung: vorab stilllegen statt im großen Reload mitlaufen lassen — falls wider Erwarten doch ein Connect existiert, bricht er an kontrollierter, einzeln reversibler Stelle (`ALTER ROLE pwa_user LOGIN`). Verworfen: erst nach dem Reload (würde Bruch versteckt machen) bzw. droppen (irreversibel ohne klaren Bedarf).

(4) **db_pwa-Subnetz (172.28.0.0/16) trotz leerem Netz in der Whitelist.** Aktuell hängt kein Client-Container an db_pwa, aber das Netz ist für PWA-Connects reserviert. Whitelist-Zeile ist harmlos (kein Sicherheits-Verlust), spart aber späteres Nachpflegen wenn ein PWA-Container an das Netz kommt. Verworfen: nur tatsächlich-aktive Subnetze whitelisten (würde Folge-Spur erzwingen, sobald PWA-Container ans Netz kommt).

(5) **`local all all trust` (Unix-Socket im Container) bleibt in dieser Spec drin.** Wording in der Spec wurde explizit von „kosmetisch" zu „bewusst akzeptierter Rest-Bypass" korrigiert: gegen den `docker exec`-Pfad ist Härtung tatsächlich kosmetisch (wer root im Container ist, hat sowieso Datenzugriff), gegen den **In-Prozess-Code-Execution-Pfad** (Extension-Exploit als `postgres`-User → Unix-Socket → passwortloser Superuser) wirkt sie als realer Rest-Bypass. Reduktion verlangt Verfahrens-Klärung (welche PG-internen Tasks brauchen `local`-Connects, in welchem Auth-Modus ersetzt man `trust`) → eigener Folge-Seed `seed-pg-hba-local-haerten`. Verworfen: ad-hoc-Mit-Härtung in dieser Spur (würde Scope sprengen, ohne Klärung würden interne PG-Wartungs-Tasks brechen).

(6) **Reload (`pg_reload_conf()`), nicht Restart.** PG akzeptiert HBA-Reload ohne Postmaster-Restart; bei Syntax-Fehler lehnt PG den Reload ab und behält die laufende Config — kein Outage-Risiko durch Tippfehler. Trockenprüfung vor dem `t`-Reload per `SELECT * FROM pg_hba_file_rules` (zeigt vorgemerkte Datei mit error-Spalte) — beide Cluster zeigten 0 Fehler.

**Restschulden, ehrlich notiert** (nicht PLAT-029-blockierend, aber registriert):
- `local all all trust` bleibt aktiv → Folge-Seed `seed-pg-hba-local-haerten` (plattform, security, bald, sicherheitskritisch-akut, spur).
- Side-Befund in customer-Logs direkt vor Reload: `FATAL: database "pwa_app" does not exist`. Pre-existing, nicht durch PLAT-029 ausgelöst, Auth war erfolgreich (`pwa_app` liegt historisch in `internal_postgres`, nicht in customer). Folge-Punkt: Service mit hartkodiertem `pwa_app` finden und korrigieren. Nicht jetzt vergeben, weil out-of-scope.

**Kontextbindung:** (a) Wenn in den nächsten 14 Tagen kein Auth-Folgesymptom auftritt (keine Container-Connect-Bruchstellen, keine versehentlich vergessene `host all all all`-Quelle), ist die Härtung empirisch bestätigt. (b) Wenn ein neuer Client an `db_pwa`-Netz kommt (172.28.0.0/16) oder ein neues Subnetz für PG-Clients angelegt wird, muss `pg_hba.conf` aktiv nachgepflegt werden — das wandert in den Compose-Drift-Check-Pfad (PLAT-001).

---

## E48 — Code-Security-Fundament für Produkt-Code etabliert (PLAT-031, 2026-05-27)

**Auslöser:** Faktensammlung (`FAKTEN-integrity-security.md`) zeigte 6 offene npm-CVEs in pwa-web (1× high `nodemailer`), kein CI für Produkt-Code, kein Update-Pfad für Produkt-Deps (strukturelle Vorfall-Ursache des Next.js-Hacks nicht geschlossen), RLS in `customer_postgres` nur in Code-Pattern vorbereitet, DB aber leer.

**Entscheidung:** Spec PLAT-031 als Spur über 6 Bündel ausgeführt — alle Akzeptanzkriterien grün ohne Architekt-Eingriff. Mehrere operative Gabelungen unterwegs:

(1) **`npm audit fix` verboten, gezielte Einzel-Anhebung gewählt.** Würde `next` von 15.5.18 auf 9.3.3 zurückstufen (Major-Downgrade, zerstört die PWA). Stattdessen: `next-auth` beta.31, `@auth/pg-adapter` 1.11.2, `nodemailer` via `overrides`-Feld auf 8.0.9 (peer-Konflikt mit next-auth via `--legacy-peer-deps` akzeptiert — next-auth nutzt nodemailer nur bei Email-Provider, hier ungenutzt). `postcss` via overrides auf 8.5.15 (next pinnt transitiv 8.4.49 mit XSS-CVE). Verworfen: Major-Bump `next` auf 16 oder Wechsel zu Auth.js OAuth-only — beides hätte App-Code berührt, Scope-Sprengung.

(2) **fastapi-Pfad: 0.115.0 → 0.115.14, NICHT 0.136.** Fastapi 0.115.x bringt starlette 0.46.2 — schließt CVE-2024-47874 (high). Drei verbleibende starlette-CVEs (PYSEC-2026-161, CVE-2025-54121, CVE-2025-62727) brauchen starlette ≥0.49.1/1.0.1, was fastapi 0.116+/0.136+ erzwingt (Cross-Minor-Sprung, größere API-Drift-Fläche). Entscheidung: per `--ignore-vuln` in CI-Workflow namentlich dokumentiert, eigene Folge-Arbeit. Verworfen: 0.136-Sprung im selben Bündel — kein Zeit-/Test-Budget für Cross-Minor-Verifikation aller 6 Services.

(3) **Update-Pfad: nightly-Schedule auf bestehendem CI-Workflow statt Renovate-self-hosted.** Renovate ist auf PR-Workflow optimiert — prisment fährt explizit no-PR/direct-to-main. Renovate-Issue-Modus liefert weniger als der ohnehin gebaute CI: ein `on: schedule '0 4 * * *'` re-auditiert den gemergten Code-Stand gegen frische CVE-DBs (osv + npm). Plus Gitea-Mailer (SMTP-Pwd aus secrets gemountet) für Fail-Notification an info@prisment.de. Vorteil: keine zweite Container-Pflege-Last, gleiche Detektions-Tiefe. Verworfen: Renovate-self-hosted (mehr Pflege, kein PR-Workflow-Nutzen), reine Diun-Erweiterung (Diun monitort Container-Images, nicht Lockfiles).

(4) **Ruff-Starter-Set: nur Pyflakes (`F`), Style-Regeln (`E`/`W`) bewusst aus.** Erst-Lauf fand 159 Verstöße (75 auto-fixbar, davon 73× F401 unused imports). Pragmatik: F-Regeln (Bugs) hart enforced, Style iterativ wachsen in `03_Code-Standards.md`. **4× F821 echte Bugs in db_sync.py** entdeckt (alle 4 `update_boost_recommendation_status`-Kopien benutzen `tenant_id` ohne Parameter zu sein — würde bei Aufruf crashen, ist heute nur ungetriggert). Per `# noqa: F821 — TODO` markiert mit explizitem Verweis auf Data-Integrity-Spec (Folge-Zyklus), die ohnehin die tenant-id-Schemas typisiert anpackt. Verworfen: Bug-Fix in dieser Spur (out of scope, würde Pydantic-Modelle ändern wollen, die in der Folge-Spec entstehen), F821 global-disable (würde künftige echte Bugs verstecken).

(5) **ESLint: `react/no-unescaped-entities` deaktiviert.** Erste pwa-web-Lint fand 17 Verstöße, alle dieselbe Regel — straight quotes in JSX-Text. Rein kosmetisch, kein Security-Bezug. Alternative wäre 17 Dateien anfassen für `&quot;` — Diff-Lärm ohne Wert. Verworfen: pre-existing Verstöße fixen (out of scope) oder ganzes ESLint-Set lockern (würde künftige echte Lint-Bugs durchlassen). Andere Regeln bleiben hart enforced.

(6) **RLS-Bereitlegung über `_apply_tenant_rls(table)`-Funktion, nicht copy-paste pro Tabelle.** Eine Funktion in `06_rls.sql` definiert das Pattern (ENABLE + FORCE + Policy mit admin/tenant-Pfaden); pwa-Migrationen rufen `SELECT public._apply_tenant_rls('public.X')` je neue Tenant-Tabelle. Verfassung 03 SSOT: das Pattern lebt an EINER Stelle, keine Drift-Möglichkeit zwischen Tabellen. Verworfen: explizite `CREATE POLICY` pro Tabelle in Init-Scripts (würde bei neuer Tabelle in Migration vergessen werden können).

(7) **E39-Schutz für auth.tenant_memberships voll umgesetzt + im Trockenlauf verifiziert.** Test 4 des Smoke-Scripts: `tenant_app_user` darf SELECT (Login-Pfad funktioniert), aber INSERT/UPDATE/DELETE wird mit `permission denied` blockiert. Bestätigt die Logbuch-E39-Entscheidung im Code-Pfad.

(8) **customer_postgres pwa_app-vs-agent_data-Mismatch nicht in dieser Spur.** Compose hat `POSTGRES_DB=pwa_app`, Doku + Code-Pattern (db.py) sagt `agent_data`. Bekannter Pre-PLAT-031-Drift (taucht im Logbuch E47 als Side-Befund auf). Init-Scripts sind DB-Name-agnostisch (`current_database()` + Schema-relative SQLs) und laufen mit beidem; der Name-Cut ist eigene Folge-Arbeit.

**Restschulden, ehrlich notiert:**
- Starlette-CVE-Trio (3 moderate): fastapi 0.115→0.136 Cross-Minor-Bump → Seed `seed-fastapi-starlette-cve-bump.md` (zugkraft: spaeter, Architekt-Urteil: Form-/Header-DoS hinter CF-WAF kein Schmerz).
- langchain-core CVE-Trio (3 in helpdesk): braucht langchain-core 1.x mit langchain-anthropic-Bump zusammen → Seed `seed-langchain-core-1x-bump.md` (zugkraft: spaeter).
- 4× F821 tenant_id-Bug in update_boost_recommendation_status: Übergabe an Data-Integrity-Spec (Folge-Zyklus), die die Pydantic-Modelle ohnehin überarbeitet.
- customer_postgres ist heute leer → RLS faktisch noch nicht scharf; greift beim realen DB-Init (gekoppelt an Kunde #2 / Produktiv-DB-Initialisierung, eigener Anlass).
- pwa-web npm ci braucht `--legacy-peer-deps` wegen nodemailer-Override; CI hat das Flag explizit. Folge: wenn next-auth-Stable v5 (non-beta) verfügbar wird, beide Constraints prüfen.

**Negativ-Test der Gates (2026-05-27, nachgereicht auf Architekten-Bitte):** Commit `9475907` setzte fastapi in pwa-api absichtlich auf 0.115.0 zurück (bringt starlette 0.38.6 mit high CVE-2024-47874). CI-Run Task 114 ([`actions_log/admin/prisment-platform/72/114.log.zst`]) — Output enthält wörtlich: `Found 1 known vulnerability, ignored 2 in 1 package — starlette 0.38.6 CVE-2024-47874 0.40.0` und `❌ Failure - Main pip-audit (CVE blocks)` → `🏁 Job failed`. Damit ist der harte Fail nicht aus Konfig-Lesung abgeleitet, sondern in der Live-Pipeline bewiesen. Revert per `e3c68e0`. Spec-Akzeptanzkriterium Bündel 3 (Negativ-Fall) damit empirisch erfüllt.

**Kontextbindung:** (a) Wenn nightly-Re-Audit in den nächsten 30 Tagen keine Mail an info@prisment.de erzeugt, ist die Mailer-Konfiguration empirisch bestätigt. (b) Wenn ein CVE-Update später `--ignore-vuln`-Einträge in `ci.yml` obsolet macht, bei dieser Gelegenheit Eintrag entfernen + dokumentieren (Hygiene). (c) Wenn pwa-Migrationen neue `public.X`-Tenant-Tabellen anlegen, ohne `_apply_tenant_rls(...)` aufzurufen, sieht der CI-Smoke das nicht — Drift-Risiko. Erwägen: Pre-merge-Check, der CREATE TABLE in public-Schema gegen vorhandene RLS-Policy prüft.

---

## E49 — PRIS-019 Plan-Abnahme + Pauschal-Freigaben (2026-05-27)

**Auslöser:** Spec PRIS-019 (Data-Integrity-Architektur LangGraph-Agents) ist freigegeben, Plan-Abnahme inkl. Architekt-Vorgaben zu vier offenen Punkten. Autonomer Lauf gewünscht, ohne Schritt-für-Schritt-Rückfragen.

**Entscheidung:**

(1) **Phase 5 (Machbarkeit) parallel zu PLAT-031 erlaubt.** Machbarkeit schreibt keinen Code, kollidiert nicht mit Security-Worktree. Phase-6-Code-Bündel bleiben aber gesperrt, bis PLAT-031 in Phase 9 ist. (Inzwischen ist Security fertig — Wand fällt.)

(2) **VoiceDB-Quelle: Default Postgres** mit Abweichungs-Erlaubnis nur bei hartem Grund. Begründung: PLAT-031 hat tenant-isolierte RLS in Postgres bereitgelegt; Gitea-Lesepfad wäre ein zweiter Datenpfad an der Isolation vorbei. Phase 5 prüft Postgres konkret; will sie abweichen → Spec-Abweichung, zurück in den Chat. Verworfen: Quelle in Phase 5 völlig offen lassen (würde ohne Richtung beginnen).

(3) **Legacy-Strategie B2: Erkennung-mit-Abbruch, keine Migration.** Begründung: Migration würde Alt-Werte raten — genau das stille Mist-Ergebnis, das diese Spur abstellen soll. Bei einem Tester (Grubis-Weine) ist die Legacy-Menge winzig, Re-Run via Abbruch ist sauberer Pfad. Telemetrie aus B-Telemetry liefert die reale Zahl bevor B-Legacy-V6 gebaut wird; dreistellige Mengen → Rückmeldung. Verworfen: Migration (Aufwand für stille Werte-Raterei).

(4) **Pauschal-Freigabe für PRIS-019:** docker-compose-Edits, Rebuilds, Container-Restarts der Agent-Services (interview, content, analytics, redaktionsplan) für den gesamten Zyklus freigegeben — Claude Code holt sie nicht pro Bündel nach. Synchroner Stopp nur bei Fall C, `sicherheitskritisch-akut`, Spec-Abweichung. Verworfen: pro-Bündel-Freigabe (würde Autonomie-Modus aushebeln).

**Zwei Plan-Ergänzungen in die Spec aufgenommen:**

(5) **F821-tenant_id-Bugs in B-SSOT eingelöst.** Die vier `# noqa: F821`-Marker in `update_boost_recommendation_status`, die PLAT-031 mit TODO an Integrity verwiesen hat, werden im SSOT-Bündel über das typisierte `SessionData`-Modell aufgelöst. Akzeptanzkriterium: kein `# noqa: F821` mehr, ruff-F clean. Verhindert, dass die Restschuld aus PLAT-031 hängen bleibt.

(6) **Telemetrie-Sammelfenster vor B-Legacy-V6.** B-Telemetry läuft vor B-Legacy-V6 und sammelt während des Spur-Verlaufs reale V6-vs-Legacy-Run-Zahlen. So entscheidet B-Legacy-V6 auf Fakten statt Schätzung. Kein expliziter Stopp; das natürliche Zeitfenster zwischen den Bündeln reicht.

**Kontextbindung:** (a) Falls Phase 5 von Default-Postgres oder Legacy-Abbruch abweichen will → Spec-Abweichung, zurück in den Chat. (b) Falls dreistellige Alt-Sessions in der Telemetrie auftauchen, vor B-Legacy-V6 Rückmeldung. (c) Pauschal-Freigaben gelten nur für PRIS-019; jede andere Spur braucht eigene Freigaben.

---

## Format-Hinweis (für künftige Logbuch-Einträge)

Jeder Eintrag: **Was war die Frage/der Auslöser → Was wurde entschieden → Warum (inkl. verworfener Alternativen) → ggf. Kontextbindung (wann neu zu bewerten).** Knapp, aber das "Warum" vollständig genug, dass man die Entscheidung nicht erneut diskutieren muss. Einträge werden nie geändert — wenn eine Entscheidung revidiert wird, kommt ein NEUER Eintrag, der auf den alten verweist.
