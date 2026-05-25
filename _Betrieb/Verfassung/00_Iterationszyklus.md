---
typ: verfassung
titel: "Iterationszyklus"
stand: 2026-05-24
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
5. **Machbarkeitsanalyse** — Claude Code prüft die Spec gegen die Realität und schreibt sich seine eigene Arbeitsliste/Bündelung (`report_art: machbarkeit`). Grobe Abweichungen gehen zurück in den Chat. *(Spur. Beim Sprung: inline in Phase 3, nur Abweichungen zurück in den Chat.)*
6. **Bundled Execution** — Arbeit in Bündeln, Bündelgröße risikoabhängig. Der Mensch prüft Zwischenstände und testet (manuell oder per Claude Code je nach Risiko), bevor es weitergeht. *(Alle Stufen. Beim Schritt: Claude Code führt direkt aus, committet, schreibt die Schritt-Log-Zeile.)*
7. **Abschluss-Verifikation** — Abschluss-Doku gegen die Ziele der Spec prüfen, im Chat gegengecheckt. *(Spur: volle Abschluss-Doku. Sprung: kurze Abschluss-Notiz.)*
8. **Retrospektive** — „Was nächstes Mal besser?" Auffangnetz für Optimierung (siehe `02_Rollen-Protokoll.md` → Optimierung passiert auch jederzeit, nicht nur hier). *(Spur. Sprung nur bei Bedarf.)*
9. **Abschluss** — Wissen einarbeiten (siehe unten), commit, Arbeitsgedächtnis ins Archiv. *(Spur + Sprung.)*

## Lebenszyklus-Bewegung beim Abschluss (Phase 9)

Von Claude Code vorgeschlagen, vom Menschen freigegeben:
- Dauerhaft Gültiges wird in **Systemzustand** bzw. **Verfassung eingearbeitet** (eingearbeitet, nicht kopiert — bleibt EINE Quelle).
- Das „Warum" wird als **Logbuch-Eintrag** verewigt (Format: siehe Logbuch). Das Logbuch ist **zentral** in `_Betrieb/Logbuch/` (siehe `02_Rollen-Protokoll.md` und Logbuch E24).
- Der Rest (Specs, Reports, Deviation-Logs) wandert ins **Archiv**.
- Das Arbeitsgedächtnis ist danach leer für den nächsten Zyklus.

Beim **Sprung** ist Phase 9 schlanker: Abschluss-Notiz ins Archiv, Logbuch-Eintrag nur, wenn eine bewusste Entscheidung mit Warum gefallen ist. Beim **Schritt** entfällt Phase 9 ganz — die Schritt-Log-Zeile + Commit sind der Abschluss.

## Pflicht-Tore (Strenge mit Notausgang)

- Ein Zyklus gilt nicht als abgeschlossen, bevor sein definierter Ausgang existiert (z. B. Logbuch-Eintrag bei einer Entscheidung). Claude Code WEIGERT sich, einen neuen Zyklus zu starten, wenn der alte nicht sauber geschlossen ist, und erinnert daran. *(Gilt für Spur + Sprung. Schritt hat keinen offenen Zustand.)*
- **Breakout-Klappe:** Das Codewort `BREAKOUT` (oder `QUICK-FIX`) setzt den Prozess bewusst aus, PROTOKOLLIERT die Aussetzung und erinnert später ans Nachholen. Flexibilität bricht nie das System — sie wird aufgezeichnet.

## Backlog-Pflege

### Harte Security-Regel

**`security`-Seeds rutschen nicht ab.** Ein offener `security`-Seed ist per Definition mindestens `bald` — er darf nie auf `irgendwann` rutschen. `wartung`-Seeds sind nie `jetzt`, außer sie blockieren nachweislich etwas anderes. Beide Regeln gelten als **Default mit Begründungszwang**: Wer abweicht, schreibt eine Zeile ins Seed, warum.

Mechanik wie bei „kritisch erzwingt Spur" (E23): Disziplin durch Default, nicht durch Sperre. Flexibilität bleibt, aber das Abweichen ist sichtbar.

### Pflege-Routine — Übersicht im selben Commit

`_Betrieb/Backlog/00_UEBERSICHT.md` + `.svg` sind ein Derivat der Seeds (Verfassung 03 SSOT). Sie werden vom Skript `scripts/backlog/generate_uebersicht.py` (im `prisment-platform` Repo) erzeugt.

**Mutations-Punkte (Pflicht-Tor):** Wenn Claude Code einen Seed **anlegt** oder **archiviert**, läuft das Skript im **selben Commit** mit. Damit kann die Übersicht strukturell nicht veralten.

Das ist die Anwendung des Phase-9-Pflicht-Tors aus `ROADMAP_lebende-bereichs-doku` Schritt 8 („Zyklus, der das System ändert, aktualisiert die abgeleitete Ansicht im selben Commit") auf den Backlog — kein neues Muster.

Hot-Files (strang-übergreifend nur seriell anfassbare Pfade) werden handgepflegt in `_Betrieb/Backlog/00_HOT-FILES.md` — getrennt von der generierten Übersicht, damit das Skript handgepflegtes Wissen nicht überschreibt.

## Zwischenprojekte (Abzweigungen)

- Niemals „hineinwachsen" lassen. Stattdessen: laufenden Zyklus **pausieren**, Zwischenprojekt als eigenen Mini-Zyklus **einschieben**.
- Pause erzeugt einen **Pause-Snapshot** (wo stehen wir, nächster Schritt, geltende Annahmen).
- Wiederaufnahme erzwingt eine **Re-Sync-Prüfung** (Pflicht-Tor): Claude Code vergleicht Snapshot mit aktuellem Systemzustand und meldet, welche Schritte der pausierten Spec durch zwischenzeitliche Änderungen betroffen sind. Kein Weiterarbeiten vor Re-Sync.
- Abzweigungen landen im zentralen **Backlog** (`_Betrieb/Backlog/`) mit Seed-Datei.

**Unterschied Eskalation ↔ Abzweigung:** Eskalation = *dasselbe* Vorhaben steigt in der Stufe (Sprung → Spur). Abzweigung = ein *anderes* Vorhaben drängt sich dazwischen → pausieren + einschieben.
