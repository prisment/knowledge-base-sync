---
typ: backlog_seed
titel: "Backlog-Priorisierung (Klasse × Zugkraft + harte Security-Regel) + generierte Übersicht mit Abhängigkeits-Graph"
geltungsbereich: alle
risikoklasse: sicher
status: offen
prioritaet: HOCH
stufe: spur
art: prozess
stand: 2026-05-25
erzeugt_durch: Chat-Architekt (Diskussion Backlog-Überblick nach Seed-Wachstum)
---

## Anlass

Der zentrale Backlog (`_Betrieb/Backlog/`) wächst schneller, als der Mensch ihn
im Kopf halten kann (~15 Seeds + 2 Roadmaps, flach nebeneinander). Es fehlt
(a) ein konsistentes Priorisierungs-Verfahren und (b) eine lesbare Sicht auf den
Berg. Die `prioritaet`-Felder sind heute uneinheitlich (mal „HOCH", mal
„niedrig", mal leer), `abhaengig_von` existiert, wird aber nirgends sichtbar
gemacht.

Der Next.js-Vorfall hat die Lehre geschärft: **Security sofort, nie aufschieben.
Kosmetik hinten. Features nur die, die jetzt gebraucht werden. Feature-Qualität
(Integrity) genauso weit vorne.** Diese Lehre soll strukturell im Framework
verankert sein, statt bei jedem Seed neu erinnert zu werden — analog zu
„Risikoklasse kritisch erzwingt Spur" (E23): eine Regel, die Disziplin
automatisiert.

## Ziel (Soll-Zustand)

Drei zusammengehörige Teile, in einem Zug umgesetzt:

### 1. Priorisierungs-Verfahren — zwei Achsen, in Sekunden vergebbar

Zwei neue Front-Matter-Felder pro Seed (analog zur Stufen-Einstufung: KI schlägt
vor + begründet, Mensch revidiert/gibt frei):

**`klasse:`** — WAS für ein Bedarf:
- `security` — Sicherheit. Eigene Klasse wegen der „sofort"-Lehre.
- `feature` — Kundenwert, neue Fähigkeit.
- `qualitaet` — Stabilität/Integrity. Kein Feature, aber Voraussetzung, dass
  Features funktionieren (z. B. der Data-Integrity-Seed lebt hier).
- `wartung` — Kosmetik, Aufräumen, Drift (Dateisystem-Bereinigung, Diun-Rückbau,
  Doku-Re-Writes).

**`zugkraft:`** — WIE bald nötig:
- `jetzt` — blockiert, akut, oder Kunde wartet.
- `bald` — absehbar gebraucht, kein Blocker.
- `irgendwann` — sinnvoll, kein Datum.

### 2. Die harte Security-Regel (Verfassung)

> **`security` + `jetzt` schlägt alles.** Ein offener `security`-Seed ist per
> Definition mindestens `bald` — er darf nie auf `irgendwann` rutschen.
> `wartung` ist nie `jetzt`, außer sie blockiert nachweislich etwas anderes.

Mechanik wie bei „kritisch erzwingt Spur": **Default mit Begründungszwang.** Will
der Mensch einen `security`-Seed ausnahmsweise nicht `jetzt`/`bald` fahren, muss
eine Zeile im Seed das warum festhalten. Hält ehrlich, ohne zu fesseln —
Flexibilität bleibt (Mensch gibt frei wie immer), aber das Abweichen ist
sichtbar, nicht still.

### 3. Gruppierung über Feld, nicht über Ordner

Neues Front-Matter-Feld **`block:`** für thematische Cluster. KEINE neuen Ordner
(das wäre die themenbasierte Struktur, die E1 verworfen hat). Ein Ort, Filtern
statt Trennen — gleiche Logik wie zentraler Backlog (E14) und zentrales Logbuch
(E24). Faktisch existierende Cluster heute:
- `nightly` — diun-rueckbau, pretooluse-hook, apt-get-pin, pending-kernel,
  compose-recreate, os-patching (6 Seeds am selben Nacht-Mechanismus).
- `lebende-doku` — bereichs-uebersichten, dateisystem-bereinigung,
  sma-doku-rewrite, skills.
- `produkt-code` — data-integrity, code-security.
- `agent-erweiterung` — assistant-agent.

(Block-Namen sind Vorschlag — in der Spec gegen die reale Seed-Liste schärfen.)

### 4. Migration aller bestehenden Seeds (eigener Pflicht-Block, größter Aufwand)

Alle ~15 vorhandenen Seeds + 2 Roadmaps bekommen `klasse`/`zugkraft`/`block`
nachgetragen. Das ist NICHT Beiwerk, sondern der zeitintensivste Teil des
Zyklus — Urteilsarbeit pro Seed (KI schlägt Einstufung + Begründung vor, Mensch
gibt frei), nicht maschinelles Befüllen.

**Pflicht-Stopp-Tor:** Claude Code legt die vorgeschlagene Einstufung ALLER
Alt-Seeds als EINE Tabelle vor (Seed → klasse → zugkraft → block → kurze
Begründung). Der Mensch sieht sie in einem Rutsch durch und gibt frei/revidiert —
KEINE 15 Einzel-Freigaben nacheinander (das würde sich ziehen und den
Geschwindigkeits-Vorteil fressen). Erst nach dieser Sammel-Freigabe werden die
Felder in die Dateien geschrieben.

**Rückkopplung auf die Felddefinition:** Fällt bei der Migration auf, dass eine
Klasse fehlt, eine Zugkraft-Stufe unscharf ist oder die harte Security-Regel
einen Alt-Seed falsch zwingt, fließt das zurück in Teil 1/2 (die Definition wird
geschärft, bevor sie festgeschrieben wird). Die Migration ist der erste echte
Test der Felder gegen die Realität — bewiesen, nicht angenommen.

### 5. Generierte Übersicht — `_Betrieb/Backlog/00_UEBERSICHT.md` + SVG

Abgeleitete Ansicht (Verfassung 03 SSOT — verdichtet die Seeds, ist nie zweite
Wahrheit). Ein **dummes Skript** (Python-stdlib, kein LLM, kein Claude-Aufruf):
liest alle Backlog-`.md`, parst Front Matter (`klasse`, `zugkraft`, `block`,
`abhaengig_von`, `status`, `stufe`), erzeugt:
- **Tabelle**, sortiert nach Zugkraft × Klasse (security-jetzt oben,
  wartung-irgendwann unten), gruppiert nach `block`.
- **Abhängigkeits-Graph als SVG** aus den `abhaengig_von`-Feldern — zeigt
  grafisch, was was blockiert.

Form analog zu den Bereichs-Übersichten (`seed-bereichs-uebersichten`): MD + SVG,
generiert, im Sync sichtbar. Alle operativen Seeds in einen Unterordner seeds verschieben. So dass unter Backlog nur die ubersicht sichtbar ist.

## Architektur-Klarstellung (wichtig, damit nichts verwechselt wird)

**Das Skript ist dummer Code — es ruft nichts auf, es startet keine Instanz.**
Es zählt und sortiert bereits geurteilte Felder, es denkt nicht. Das *Urteilen*
(Klasse/Zugkraft vergeben) passiert beim Seed-Anlegen durch Mensch/Chat-Architekt/
Claude Code. Die *Übersicht* addiert nur das bereits Geurteilte auf. Ein Derivat
berechnet man, man halluziniert es nicht — deshalb kein Modell im Spiel.

**Claude Code ist Auslöser, nicht Motor.** Er führt `python3 generate_uebersicht.py`
als Bash-Aufruf aus, wie `git commit`. Es gibt keine „eigene Instanz" — ein dummes
Skript, beliebige Auslöser.

## Die Routine — verankert an den echten Mutations-Punkten

Der Backlog ändert sich nur, wenn ein Seed angelegt oder archiviert wird. Beides
geht durch Claude Codes Schreibhand (E4 — einzige Schreibhand ins Repo). Drei
Auslöser, alle kostenlos (dasselbe Skript):
1. Claude Code **legt** einen Seed an → Skript läuft → Seed + frische Übersicht
   im SELBEN Commit.
2. Claude Code **archiviert** einen Seed (Phase 9) → Skript läuft → mit-committet.
3. **Manuell** im Terminal jederzeit → Skript (außer der Reihe schauen).

Dadurch kann die Übersicht **strukturell nicht veralten** — sie lebt im selben
Commit wie die Änderung, die sie abbildet. **Kein Nightly nötig** (das räumt eine
Andock-Abhängigkeit am noch-kaputten Nightly weg).

Bewusst NICHT als Routine bei jedem einzelnen Vorgang außerhalb dieser zwei
Mutationen — das wäre Overhead ohne Mutation.

## Verfassungs-Verankerung (was wohin — „nur nach oben")

- **`01_Spec-Format.md`:** die neuen Front-Matter-Felder `klasse`, `zugkraft`,
  `block` als verbindliche Keys (analog `stufe`/`art`). Ins Template
  (`Dokument-Templates*.md`) übernehmen.
- **`00_Iterationszyklus.md`:** die harte Security-Regel + die Pflege-Routine in
  Phase 9 / beim Seed-Anlegen. **Ist ein Spezialfall des geplanten Phase-9-
  Pflicht-Tors** aus `ROADMAP_lebende-bereichs-doku` Schritt 8 („Zyklus, der das
  System ändert, aktualisiert die abgeleitete Ansicht im selben Commit") — kein
  neues Muster, Anwendung eines bereits geplanten auf den Backlog.
- **Das Skript selbst** gehört NICHT in die Knowledge-Base (Verfassung 03 / E8),
  sondern ins Code-Repo. Die Verfassung verweist nur auf Existenz + Auslöser.

## Bezug zu bestehenden Seeds / Mustern

- `seed-feature-flag-zustand`: falls Teil-Komponenten gebaut, aber noch nicht
  scharf sind (unwahrscheinlich hier, da alles in einem Zug läuft).
- `ROADMAP_lebende-bereichs-doku` Schritt 8: dieselbe Pflege-Mechanik
  (Pflicht-Tor + Derivat-Regenerierung) — Konsistenz wahren, nicht doppelt
  erfinden.

## Stufe / Risiko

**Spur.** Begründung trotz kleinem Skript: Der gestaltende Kern ist eine
**Verfassungs-Änderung** (neue Pflicht-Felder + harte Priorisierungs-Regel +
Pflege-Routine) mit Wirkung über den Einzelfall hinaus — das ist per Definition
Spur (Verfassung 00), unabhängig davon, dass die Code-Komponente klein ist. Das
Skript+Routine läuft als Umsetzungs-Bündel innerhalb der Spec.

Risikoklasse `sicher`: reines Lese-/Schreib-Derivat im Backlog-Ordner, kein
Eingriff in laufende Dienste, kein Kundendaten-Bezug. (Die Stufe Spur kommt hier
aus der Verfassungs-Wirkung, nicht aus der Risikoklasse — sauberer Fall, wo
`sicher` trotzdem Spur ist.)

`art: prozess` — reines Filter-Etikett, ohne Prozess-Wirkung.

## Trigger

HOCH — der Überblicksverlust ist akut und wächst mit jedem neuen Seed (heute
allein +3). Sinnvoll als einer der nächsten Zyklen, weil er die Priorisierung
ALLER anderen Seeds erst lesbar macht — er ist das Werkzeug, mit dem die
Reihenfolge von Data-Integrity, Code-Security & Co. überhaupt sauber bestimmt
wird.

## Offene Fragen für die spätere Spec

- Block-Namen + Zuschnitt gegen die reale Seed-Liste final schärfen.
- SVG-Graph-Form: einfache Box-Pfeil-Darstellung (stdlib-generiert) vs.
  Mermaid-Quelle, die separat gerendert wird? (stdlib hält die Abhängigkeit
  klein, Mermaid wäre lesbarer editierbar — Abwägung in der Spec.)
- Sortier-Logik exakt definieren: Wie werden gleichrangige Seeds (gleiche
  Klasse+Zugkraft) sekundär sortiert (Datum? Block? abhaengig_von-Tiefe)?
- Umgang mit Roadmaps (`ROADMAP_*.md`) in der Übersicht — eigene Sektion oder
  als „Block-Köpfe" mit ihren Seeds darunter?
- Wo lebt das Skript im Code-Repo, und ruft Claude Code es über einen festen
  Pfad auf (Konsistenz mit der Schreibhand-/Allowlist-Logik)?
