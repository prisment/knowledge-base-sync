---
typ: verfassung
titel: "Iterationszyklus"
stand: 2026-05-23
aenderung: "nur nach oben (nie schlechter), nur durch bewusste Freigabe des Menschen"
---

# 00 — Iterationszyklus

Der verbindliche Arbeitsablauf für jeden Zyklus. Alle drei Akteure (Mensch, Chat-Architekt, Claude Code) kennen ihn, damit Phasen-Übergänge nicht jedes Mal neu erklärt werden müssen.

## Die neun Phasen

1. **Auslöser** — Idee, Problem, Produktarbeit oder geplanter Prozess.
2. **Diskussion & Faktensammlung** — Chat-Architekt diskutiert die Lösung; Claude Code liefert Reports aus dem ECHTEN System (nie aus Annahme). Ergebnis: Faktensammlungs-Report (`report_art: fakten`).
3. **Spec-Erstellung** — Die Lösung wird in eine Spec geformt (Format: `01_Spec-Format.md`). Erstellt vom Chat-Architekt.
4. **Übergabe** — Spec geht via Claude Code ins Repo; Übergabe-Prompt an Claude Code.
5. **Machbarkeitsanalyse** — Claude Code prüft die Spec gegen die Realität und schreibt sich seine eigene Arbeitsliste/Bündelung (`report_art: machbarkeit`). Grobe Abweichungen gehen zurück in den Chat.
6. **Bundled Execution** — Arbeit in Bündeln, Bündelgröße risikoabhängig. Der Mensch prüft Zwischenstände und testet (manuell oder per Claude Code je nach Risiko), bevor es weitergeht.
7. **Abschluss-Verifikation** — Abschluss-Doku gegen die Ziele der Spec prüfen, im Chat gegengecheckt.
8. **Retrospektive** — „Was nächstes Mal besser?" Auffangnetz für Optimierung (siehe `02_Rollen-Protokoll.md` → Optimierung passiert auch jederzeit, nicht nur hier).
9. **Abschluss** — Wissen einarbeiten (siehe unten), commit, Arbeitsgedächtnis ins Archiv.

## Lebenszyklus-Bewegung beim Abschluss (Phase 9)

Von Claude Code vorgeschlagen, vom Menschen freigegeben:
- Dauerhaft Gültiges wird in **Systemzustand** bzw. **Verfassung eingearbeitet** (eingearbeitet, nicht kopiert — bleibt EINE Quelle).
- Das „Warum" wird als **Logbuch-Eintrag** verewigt (Format: siehe Logbuch).
- Der Rest (Specs, Reports, Deviation-Logs) wandert ins **Archiv**.
- Das Arbeitsgedächtnis ist danach leer für den nächsten Zyklus.

## Pflicht-Tore (Strenge mit Notausgang)

- Ein Zyklus gilt nicht als abgeschlossen, bevor sein definierter Ausgang existiert (z. B. Logbuch-Eintrag bei einer Entscheidung). Claude Code WEIGERT sich, einen neuen Zyklus zu starten, wenn der alte nicht sauber geschlossen ist, und erinnert daran.
- **Breakout-Klappe:** Das Codewort `BREAKOUT` (oder `QUICK-FIX`) setzt den Prozess bewusst aus, PROTOKOLLIERT die Aussetzung und erinnert später ans Nachholen. Flexibilität bricht nie das System — sie wird aufgezeichnet.

## Zwischenprojekte (Abzweigungen)

- Niemals „hineinwachsen" lassen. Stattdessen: laufenden Zyklus **pausieren**, Zwischenprojekt als eigenen Mini-Zyklus **einschieben**.
- Pause erzeugt einen **Pause-Snapshot** (wo stehen wir, nächster Schritt, geltende Annahmen).
- Wiederaufnahme erzwingt eine **Re-Sync-Prüfung** (Pflicht-Tor): Claude Code vergleicht Snapshot mit aktuellem Systemzustand und meldet, welche Schritte der pausierten Spec durch zwischenzeitliche Änderungen betroffen sind. Kein Weiterarbeiten vor Re-Sync.
- Abzweigungen landen im zentralen **Backlog** (`_Betrieb/Backlog/`) mit Seed-Datei.
