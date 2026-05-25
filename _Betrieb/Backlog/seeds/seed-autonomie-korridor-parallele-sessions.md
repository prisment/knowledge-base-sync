---
typ: backlog_seed
titel: "Autonome Ausführung im freigegebenen Korridor (Freigabe-Modell v2) + parallele Sessions via Worktrees"
geltungsbereich: alle
risikoklasse: kritisch
status: offen
art: prozess
klasse: prozess
zugkraft: jetzt
stufe: spur
beruehrt: ["_Betrieb/Verfassung/", "_Betrieb/Backlog/"]
stand: 2026-05-25
erzeugt_durch: Chat-Architekt (Diskussion Bundle-Autonomie + Parallelität, nach Framework-Stabilisierung)
---

## Anlass

Das Framework läuft nach wenigen Tagen spürbar stabiler; das System ist
selbstheilend genug, dass der Mensch nicht mehr jede Kleinigkeit kontrollieren
muss. Damit verschiebt sich der Engpass: Nicht die Geschwindigkeit von Claude
Code bremst, sondern die **Review-Bandbreite des Menschen**. Heute (Phase 6,
Verfassung 00) prüft der Mensch Zwischenstände und gibt frei, *bevor* es
weitergeht — bei Sprung „ein Stopp" (Spec-Freigabe), bei Spur mehrere Tore.

Der Mensch bekommt dabei zu viele Reviews, die er nicht braucht (technische
Details, die er nicht beurteilen kann und will), und zu wenig Fokus auf das,
was nur er entscheiden kann (strategische Richtung). Ziel ist nicht „mehr
umsetzen", sondern **schneller vorankommen bei gleicher Qualität und Sicherheit**.

Zwei Hebel, die am selben Engpass ziehen, werden in EINEM Spur-Zyklus
adressiert, weil sie zusammenhängen:
- **Phase 1 (zuerst):** Autonome Ausführung im freigegebenen Korridor — weniger
  synchrone Stopps, Review verschiebt sich nach hinten.
- **Phase 2 (danach):** Parallele Sessions via Git-Worktrees — erst sinnvoll,
  wenn Phase 1 sitzt (wer nicht weiß, wo EIN Agent autonom laufen darf, kann
  ZWEI nicht loslassen).

## Abgrenzung zu E3 (selbstoptimierend) — KEIN Widerspruch

**Kritisch, damit kein künftiger Leser E3 für aufgeweicht hält:** E3 verbietet,
dass das System sich SELBST verändert (Regeln, Verfassung) ohne Mensch-Freigabe.
Das bleibt vollständig in Kraft. Dieser Zyklus ändert NICHT, wer die Verfassung
ändert (weiterhin nur Mensch per Freigabe), sondern nur, wie freigegebene
*Ausführung* abläuft. Deshalb der bewusste Begriff **„autonome Ausführung im
freigegebenen Korridor"**, nicht „Selbstoptimierung". Die Autonomie betrifft das
Abarbeiten innerhalb von Grenzen, die der Mensch vorher gezogen hat — nie das
Ziehen der Grenzen selbst.

---

# PHASE 1 — Autonome Ausführung im freigegebenen Korridor

## Leitprinzip (gehört als Grundsatz in die Verfassung)

> **Der Mensch entscheidet das Wohin (Richtung, Wert, Strategie). Die KI
> entscheidet das Wie (Technik, Implementierung, Methode). Reviews fließen
> entlang dieser Grenze: synchron nur beim Wohin, asynchron-informativ beim Wie.**

KI ist stark im Komplizierten (Technik) und strukturell schwach im „eigentlich
Offensichtlichen", weil ihr der Gesamtkontext von Geschäft und Leben fehlt — den
hat nur der Mensch. Das ist keine wegtrainierbare Schwäche, sondern eine feste
Arbeitsteilung. Claude Code soll wissen: Bei Wohin-Fragen ist er strukturell
unzuständig → da stoppt er. Das macht Stoppen *vorhersehbar* statt willkürlich.

## Die drei Stufen der Sichtbarkeit (ersetzt „prüft, bevor es weitergeht")

1. **Stopp / Freigabe (synchron, blockierend):** Claude Code darf nicht weiter,
   bevor der Mensch entschieden hat. Teuer (Mensch muss reinschalten) → selten.
2. **Information (asynchron, kein Veto):** Claude Code hat etwas getan, der
   Mensch *soll es wissen*, es läuft aber weiter. Mensch liest, wann er will.
3. **Stille (gar nichts):** Routinearbeit, die der Mensch nie sehen will.

**Start-Entscheidung des Menschen:** Beginn OHNE Stille — alles ist mindestens
Information. Stille (Stufe 3) wird später eingeführt, wenn die Protokolle zeigen,
was ohnehin nie relevant war. Sicherer Einstieg. (Begründung Mensch: lieber
weniger lesen das er wirklich liest, als alles überfliegen; und: durch Specs/
Fakten/Machbarkeit/Logbuch/Schritt-Log ist ohnehin ALLES dauerhaft dokumentiert
— das akute Chat-Reporting braucht nur das Nötigste.)

## Wann Claude Code synchron STOPPT — vier Auslöser

Grundregel: **Stopp nur, wenn eine Entscheidung strategisch UND schwer
reversibel ist.** Lackmustest, den Claude Code sich stellt: „Kann ich das selbst
entscheiden, ohne zu wissen, wo der Mensch strategisch hinwill, und ist es billig
rückrollbar?" Zweimal ja → kein Stopp. Mindestens ein Nein → Stopp oder (bei nur
Reversibilitäts-Nein, aber strategisch klar) Information.

1. **Richtungs-Gabelung mit Wert-Urteil.** Zwei legitime Wege, und welcher
   richtig ist, hängt vom Wohin ab, nicht von Technik. (Technik-Wahl wie
   State-Lib ist NIE Stopp.)
2. **Schwer umkehrbar.** DB-Schema-Migration, Daten löschen, Außenwirkung
   (Deploy zu Kunden, öffentlicher Content, gesendete Mails), Geld.
3. **Risikoklasse `kritisch` / `sicherheitskritisch-akut`.** Auth, Tenant-
   Isolation, Security, Kundendaten, Secrets — Stopp auch bei technisch kleiner
   Einzeländerung, weil Schaden-bei-Fehler groß ist. (Knüpft an bestehende
   Risikoklassen der Verfassung an, erfindet keine neue Achse.)
4. **Scope-Sprengung.** Der Zyklus entpuppt sich größer als gedacht (Sprung →
   Spur). Dann Stopp + Eskalation (bestehendes Eskalations-Prinzip Verfassung 00).

Alles andere — technische Implementierung, Refactoring, Tests, Bugfixes, Layout,
Copy-Entwürfe — wird durchgearbeitet, höchstens informiert.

## Das Korridor-Modell — wie der Autopilot konkret läuft

Der Autopilot ist KEIN Schalter („ab jetzt macht er alles"), sondern ein
**Korridor mit Wänden**: autonom, solange er innerhalb der Grenzen bleibt, die
die Spec-Freigabe abgesteckt hat. Verlässt er den Korridor → Stopp, egal wie weit.

Die freigegebene **Spec ist die Korridor-Beschreibung** (das Wohin + die
Grenzen). Die Freigabe der Spec IST die Autopilot-Freigabe: nicht „mach mal",
sondern „bewege dich autonom innerhalb dieser Grenzen".

Drei Fälle, was passiert, wenn die **Fakten/Machbarkeit** auf die Spec treffen:

- **Fall A — Fakten bestätigen die Spec.** Machbarkeit grün. → **Autopilot bis
  zum Schluss.** Keine Zwischenstopps. Am Ende das Entscheidungs-Protokoll.
- **Fall B — Fakten präzisieren (kein Widerspruch, nur Detail).** Spec: „speichere
  Präferenz"; Fakten: „es gibt schon eine Präferenz-Tabelle, häng dran." Richtung
  stimmt, Weg wird konkret. → **Autopilot läuft weiter, vermerkt die Präzisierung
  im Protokoll** (Information). Das ist das „Wie".
- **Fall C — Fakten WIDERSPRECHEN der Spec / Machbarkeit findet Hardstop.** Das
  Fundament der Freigabe ist hinfällig (angenommener Datenpunkt fehlt, Weg geht
  nicht). → **Stopp, egal wie früh/spät.** Sonst entstünde Spec V2, die der
  Mensch nie gesehen hat. Das ist eine Wohin-Frage (Ziel anders erreichen, oder
  Ziel ändern?) → gehört dem Menschen.

Lackmustest pro Phase: **„Tue ich noch das, was die freigegebene Spec sagt — oder
etwas, das ich tun muss, WEIL die Spec so nicht funktioniert?"** Ersteres: weiter.
Zweiteres: Stopp (Fall C).

## Die zwei zwingenden Stopps (und nur die)

Im ganzen Korridor-Zyklus bleiben genau zwei synchrone Pflicht-Stopps:
1. **Spec-Freigabe (vorne).** Hier steckt der ganze Gesamtüberblick des Menschen.
2. **Korridor-Bruch (dynamisch, nur falls Fall C eintritt).** Sonst läuft es bis
   zum Abschluss-Protokoll durch.

Dazwischen: kein synchroner Stopp. Phasenübergänge (Fakten → Machbarkeit → Bau)
sind informativ protokolliert, nicht freizugeben. Plus die vier Stopp-Auslöser
oben, falls sie *innerhalb* der Ausführung auftauchen (meist schon in der Spec
als kritisch markiert → vorhersehbar).

## Start-Trigger des Autopiloten — präzise

Scharf ab dem Moment der Freigabe der **letzten gemeinsam erarbeiteten Phase**.
Bei vielen Seeds ist das die Spec; bei komplexeren, wo Fakten gemeinsam im Chat
erarbeitet werden, erst danach. Der Mensch sagt, wo die gemeinsame Arbeit endet
— ab da Korridor. Formel: „Wir machen/erarbeiten gerade Seed XY" → ab Freigabe
ist alles Folgende Autopilot, bis Zyklus fertig oder Korridor bricht.

## Review-Format: Entscheidungs-Protokoll statt Code-Diff

Der Mensch reviewt **nicht Code, sondern Entscheidungen** (Wohin/Wie-Grenze).
Claude Code legt nach einem Bundle / am Zyklus-Ende KEIN 600-Zeilen-Diff vor,
sondern:

> **Was ich gebaut habe** — 1 Satz (Ziel des Bundles).
> **Entscheidungen, die ich getroffen habe** — die Gabelungen, an denen ich
>   NICHT gestoppt habe, je 1 Zeile + Begründung: „X statt Y, weil …"
> **Was du wissen solltest** — Information, kein Veto nötig.
> **Wo ich unsicher war** — falls vorhanden; das ist der eigentliche Review-Fokus.

Der Mensch liest das in Minuten, sieht sofort, ob eine getroffene Entscheidung
gegen seine Richtung läuft → revidiert geballt (auf einem Branch billig).
Sonst durchgewunken. Das Code-Diff existiert, ist aber Kür, nicht Pflicht.

Das ist die „Information vs. Freigabe"-Teilung in Reinform: Das Protokoll
*informiert* über alle Entscheidungen; nur die strategisch-irreversiblen wurden
vorher zur *Freigabe* eskaliert.

## Stufen-Staffelung — der Autopilot wirkt je Stufe verschieden

- **Schritt:** war nie Freigabe-Thema. Autopilot komplett, später Stille-Stufe.
- **Sprung:** größter Gewinn. Spec-Freigabe vorne (gibt es schon: „ein Stopp"),
  durchziehen, Entscheidungs-Protokoll hinten.
- **Spur:** autonom durch ihre `sicher`-Teile, ABER die in der Spec als
  `kritisch`/`sicherheitskritisch-akut` markierten Bundles bleiben synchrone
  Stopps. Nicht „Spur = alles manuell" (zu langsam), nicht „Spur = voll autonom"
  (zu riskant), sondern **autonom bis auf die markierten kritischen Stellen.**

## Kritikalität pro Bundle — feste Liste + Claude-Code-Urteil (nur nach oben)

Damit „Spur autonom bis auf kritische Bundles" funktioniert, markiert die Spec
vorne pro Bundle ein `kritisch`-Flag. Definition zweistufig:

### Feste Liste — IMMER kritisch (Claude Code erkennt nur, ob berührt; schätzt nicht)

**Sicherheit & Zugang:** Auth-Logik (Login/Session/Token/Passwort); Tenant-
Isolation/RLS; Berechtigungen/Rollen/Zugriffskontrolle; Secret-Handling (Keys,
Env, Credentials); Cloudflare-/Edge-Sicherheitsschicht.

**Daten-Integrität & -Verlust:** DB-Schema-Migrationen (besonders irreversible:
Spalten/Tabellen löschen, Typänderungen); alles, was bestehende Kundendaten
ändert/löscht; die zentrale Datenpunkt-Definition aus dem Data-Integrity-Seed
(sobald existent — ändern = alle Agents ändern).

**Außenwirkung (irreversibel):** Deploy auf Produktion / was Kunden live sehen;
öffentlicher Content (Landingpage live, Posts); gesendete Mails/Nachrichten an
echte Empfänger; alles mit Geldfluss.

**Abhängigkeiten & Lieferkette (Next.js-Lehre):** Dependency-Major-Updates /
Framework-Versionssprünge; neue externe Dependencies überhaupt.

### Graubereich — Claude-Code-Urteil über zwei Testfragen

Was die Liste nicht trifft, prüft Claude Code mit zwei festen Fragen:
1. **Reversibilität:** „Billig zurückrollbar, ohne bleibenden Schaden?" Nein → kritisch.
2. **Wohin-Abhängigkeit:** „Hängt die richtige Wahl vom Wohin ab statt von Technik?" Ja → kritisch.
Eine „kritisch" → Stopp. Beide unkritisch → Autopilot.

### Nur-nach-oben-Regel (schließt das Schlupfloch)

Claude Code darf NUR nach oben einstufen, nie nach unten. Er kann Nicht-Listen-
Dinge als kritisch markieren (Vorsicht erlaubt), aber NIEMALS einen Listen-Treffer
herunterstufen, um im Autopilot zu bleiben. Die Liste ist Untergrenze; sein
Urteil kann sie nur verschärfen. Symmetrisch darf auch der Mensch in der Spec nur
hochstufen, einen Listen-Treffer nicht herunterstufen.

### Spec-Vermerk: nur Ergebnis + Halbsatz, nicht die Herleitung

Pro Nicht-Listen-Bundle notiert Claude Code nur `kritisch: ja/nein` + einen
Halbsatz Begründung — NICHT die ausformulierten zwei Testfragen. Die Fragen sind
sein Werkzeug (Wie), nicht der Lesestoff des Menschen (der will das Ergebnis).
Hält die Spec lesbar. Kommt eine Einstufung komisch vor, fragt der Mensch nach.

### Die Liste lebt (passt zum selbstoptimierenden Ziel — im E3-konformen Sinn)

Die Kritikalitäts-Liste ist nicht fix. Sie wächst nach dem Phase-9-Pflicht-Tor-
Muster: Richtet ein Bundle Schaden an, das NICHT auf der Liste stand, wird die
Kategorie nachträglich aufgenommen (Mensch gibt frei — E3-konform). Der Next.js-
Vorfall ist genau so ein Lernfall (er brachte „Dependency-Updates" auf die Liste).
So kalibriert sich die Liste an der Realität statt an Vorab-Paranoia und wird
weder zu lang (alles kritisch → kein Autopilot) noch zu kurz (Lücken → Schaden).

---

# PHASE 2 — Parallele Sessions via Git-Worktrees

Erst angehen, wenn Phase 1 sich bewährt hat. (Mensch: „erst dann ist sie wirklich
möglich, aber bringt dann auch was." Technisch unkompliziert, professionelle
Standard-Arbeitsweise — kann zeitlich zügig nach Phase 1 folgen.)

## Mechanik (für die spätere Spec dokumentiert)

Ein Repo, mehrere Arbeitsverzeichnisse, jedes auf eigenem Branch, gemeinsame
`.git`-Datenbank:
```
~/prisment              Branch: main
~/prisment-feature      Branch: feature-xy     git worktree add ~/prisment-feature -b feature-xy
~/prisment-landingpage  Branch: landingpage     git worktree add ~/prisment-landingpage -b landingpage
```
- **Agent A** arbeitet in `~/prisment-feature` (committet auf `feature-xy`),
  **Agent B** in `~/prisment-landingpage` (committet auf `landingpage`).
- Schreibzugriff-Kollision physisch unmöglich: getrennte Ordner, jeder Agent
  sieht den anderen nicht. **Kein Lock nötig.**
- Getrennte Commit-Historien → keine Commit-Verwirrung.

## Die drei Probleme des Menschen — gelöst

1. **Schreibzugriff auf dieselbe Datei** (einziges echtes technisches Problem):
   gelöst durch *disjunkte Footprints*, nicht durch Technik zur Laufzeit. Zwei
   Stränge sind parallelisierbar ⟺ ihre berührten Dateien/Ordner schneiden sich
   nicht. Das berechnet das Übersichts-Skript aus dem `beruehrt:`-Feld (siehe
   Priorisierungs-Seed) → aus der Prio-Übersicht wird auch eine
   *Parallelisierungs-Karte*.
2. **Commit-Verwirrung:** gelöst durch getrennte Branches/Worktrees (trivial).
3. **Mentale Last des Menschen** (der wahre Begrenzer): gelöst durch
   *Strang-Paarung*, nicht durch Technik — eine TIEFE Aufgabe (Logik, Denken) +
   eine FLACHE (Copy/Layout, Gucken). Zwei tiefe Stränge parallel überfordern
   den Menschen, egal wie gut die Agents sind.

## Verworfen: Laufzeit-Koordination

Lock-Dateien / Agent-zu-Agent-Kommunikation werden NICHT gebaut. Das wäre ein
verteiltes System mit allen Failure-Modes (verklemmte Locks, verwaiste
Nachrichten, Reihenfolge-Abhängigkeiten) — fragil für einen Einzelnen. Trennung
erfolgt **vorab durch disjunkten Schnitt** (Worktrees + getrennte Footprints),
nicht **zur Laufzeit durch Verhandlung.** Konflikte konstruktiv unmöglich machen
statt dynamisch managen — Selbstheilungs-Prinzip auf Parallelität angewandt.

## Merge als eigenes Bundle (kein Webhook-Automatismus zum Start)

Das Mergen macht NICHT einer der Arbeits-Agents (er ist „verliebt" in seinen
Code), sondern eine **dritte, nüchterne Integrations-Session** (oder der Mensch).
Ablauf:
1. Branch A → `main`: glatt (disjunkt) oder Konfliktmeldung.
2. Branch B → `main`: ebenso.
3. Health-Check auf `main`.
4. **Merge-Report** an den Mensch: „beide sauber integriert, Health grün" ODER
   „Konflikt in `config.ts`: A wollte X, B wollte Y, ich schlage Z vor — deine
   Entscheidung" (Konflikt = Wohin-Frage = Stopp).

**Getriggert wird der Merge bewusst** („beide Stränge durch, jetzt integrieren"),
NICHT per Git-Webhook-Automatik. Webhook-→-Auto-Agent wäre wieder Laufzeit-
Koordination (dasselbe Muster wie Lock-Dateien, verworfen) und verfrühte
Automatisierung an einer selten genug auftretenden Stelle. Webhook-Automatik
kann SPÄTER als Flag draufgesetzt werden, wenn das manuelle Modell sich bewährt
(`seed-feature-flag-zustand`-Muster).

## Branch als billige Sandbox — verstärkt Phase 1

Solange ein Strang auf seinem Branch lebt, berührt er das laufende System nicht;
`main` bleibt stabil. Ein verkorkster Branch wird einfach verworfen
(`git worktree remove` + Branch löschen) — nichts Produktives berührt. Das macht
das „einfach mal machen, danach revidieren" aus Phase 1 erst sicher: Ein Branch
ist die billig-rückrollbare Sandbox, die Post-hoc-Review absichert.

## Parallelitäts-Grenze (verbindet beide Phasen)

- Zwei **Schritt/Sprung**-Stränge parallel: unproblematisch.
- Zwei **Spur**-Stränge mit `kritisch`-Kernen parallel: **NIEMALS** — sonst
  jongliert der Mensch zwei kritische Reviews gleichzeitig.
- Skalierung auf 3–4 Stränge erst, nachdem genau EIN Paar (tief + flach)
  erprobt wurde und der Mensch weiß, dass er den Doppelstrang mental trägt.

---

## Verfassungs-Verankerung (was wohin — „nur nach oben")

- **`00_Iterationszyklus.md`:** Phase 6 umschreiben (von „Mensch prüft
  Zwischenstände, bevor es weitergeht" → Korridor-Modell mit den zwei zwingenden
  Stopps + Stufen-Staffelung). Neuer Abschnitt „Autonome Ausführung im
  freigegebenen Korridor" mit Leitprinzip + drei Sichtbarkeits-Stufen + vier
  Stopp-Auslösern + Fall A/B/C. Ausdrückliche E3-Abgrenzung.
- **`02_Rollen-Protokoll.md`:** Wohin/Wie-Grundsatz als Rollen-Grenze (Mensch =
  Wohin, Claude Code = Wie). Entscheidungs-Protokoll als Claude-Code-Pflicht.
  Kein Widerspruch zu „Optimierung nie selbstoptimierend" — explizit absichern.
- **`01_Spec-Format.md` + Templates:** `kritisch`-Flag pro Bundle in der
  Arbeitsliste/Machbarkeit; Kritikalitäts-Liste als Referenz (oder eigene
  Verfassungsdatei, falls zu lang — in der Spec entscheiden).
- **Logbuch:** neuer Eintrag (E-Nummer) — das WARUM des Freigabe-Modells v2,
  inkl. der E3-Abgrenzung, damit der Kontroll-Grundsatz nachvollziehbar bleibt.

**Anhang — fertig formulierte Patches:** Die konkreten Verfassungs-Einschübe
(Phase 6, Rollen-Protokoll, Spec-Format, Logbuch-Eintrag) sind als Vorlage in
`seed-autonomie-korridor-parallele-sessions__verfassungs-vorlage.md` vorab
formuliert. **Erst nach Spec-Freigabe dieses Zyklus** anwenden — die Vorlage
ist Spec-Substanz, kein Direkt-Patch.

## Bezug zu bestehenden Seeds / Entscheidungen

- **`seed-backlog-priorisierung-uebersicht`:** liefert `beruehrt:` + Hot-Files +
  Parallelisierungs-Karte, die Phase 2 voraussetzt. Querverweis beidseitig.
- **E6/E12/E22 (nächtliche Autonomie):** dieselbe Grundhaltung „Autonomie für
  reversibel/sicher, harte Grenze für kritisch" — hier auf die Tag-Arbeit
  übertragen statt nur den Nachtlauf. Konsistenz wahren.
- **`seed-assistant-agent`:** dessen „Lesen vs. Handeln / reversibel vs.
  irreversibel"-Schnitte sind dieselbe Logik wie die vier Stopp-Auslöser hier —
  bei dessen Spec auf dieses Modell aufsetzen, nicht neu erfinden.

## Stufe / Risiko

**Spur.** Schreibt das Rollen- und Freigabe-Protokoll im Kern um (Phase 6,
Rollen-Grenzen) — Wirkung weit über den Einzelfall. Risikoklasse `kritisch`:
betrifft, wie Claude Code mit `kritisch`/`sicherheitskritisch-akut`-Arbeit
umgeht — ein Fehler im Modell könnte autonome Ausführung an einer kritischen
Stelle zulassen. Erzwingt Spur ohnehin (Verfassung 00).

## Trigger

HOCH — der Review-Engpass ist akut (Mensch bekommt zu viele unnötige Reviews,
während Wichtiges wartet). Größter Geschwindigkeitsgewinn bei gleicher Sicherheit.
Reihenfolge-Empfehlung: NACH dem Priorisierungs-Seed (der die Prio-Übersicht +
`beruehrt:` liefert, auf die Phase 2 baut), aber Phase 1 kann unabhängig davon
starten. Phase 1 vollständig erproben, bevor Phase 2 scharf geschaltet wird.

## Offene Fragen für die spätere Spec

- Kritikalitäts-Liste: in `01_Spec-Format` integrieren oder eigene Verfassungs-
  datei `06_Kritikalitaet.md`? (Länge entscheidet.)
- Entscheidungs-Protokoll: eigenes Template oder Erweiterung der Abschluss-Notiz?
- Stille-Stufe (Stufe 3): ab wann und für welche Kategorien einführen — eigener
  Folge-Mini-Zyklus, sobald die Protokolle Kandidaten zeigen.
- Merge-Integrations-Session: feste Rolle in `02_Rollen-Protokoll` oder ad-hoc?
- Worktree-Namenskonvention + wo die Worktrees auf der Platte liegen (Konsistenz
  mit Backup/Spiegel-Regeln, `10_Kunden/`-Schutz beachten).
