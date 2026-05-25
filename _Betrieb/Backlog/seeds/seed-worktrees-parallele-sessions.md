---
typ: backlog_seed
titel: "Parallele Sessions via Git-Worktrees (Phase 2 des Autonomie-Modells)"
geltungsbereich: alle
risikoklasse: kritisch
status: offen
klasse: prozess
zugkraft: bald
stufe: spur
beruehrt: ["_Betrieb/Verfassung/", "_Betrieb/Backlog/", "/home/claude-deploy/git/"]
stand: 2026-05-25
erzeugt_durch: PLAT-013 (Phase 2 des Autonomie-Seeds, ausgekoppelt)
basiert_auf_spec: "[[Plattform/Arbeitsgedaechtnis/PLAT-013_SPEC]]"
---

## Anlass

Phase 1 des ursprünglichen `seed-autonomie-korridor-parallele-sessions` ist mit PLAT-013 als Korridor-Modell verankert. **Phase 2** — parallele Sessions in Git-Worktrees, damit zwei Spuren mit disjunkten `beruehrt:`-Mengen gleichzeitig laufen können — wurde bewusst aus PLAT-013 ausgekoppelt: erst Korridor an realen Spuren erproben, dann Parallelität.

## Voraussetzung — keine Spec, bevor das erfüllt ist

**Mindestens 3 reale Korridor-Spuren** unter PLAT-013-Mechanik gefahren und ausgewertet. Bewertungsfragen vor Spec-Start:

- Sind die Stopp-Auslöser empirisch tragfähig (kein „hätte autonom laufen sollen", kein „hätte stoppen sollen")?
- Trägt das Entscheidungs-Protokoll den Mensch-Review ohne Code-Diff-Lesen?
- Ist die Kritikalitäts-Liste vollständig genug, oder wuchs sie über das Phase-9-Pflicht-Tor?

## Vorhandene Infrastruktur (steht bereit)

- **`tmux` 3.4** + Helfer-Skript `/home/claude-deploy/bin/tm`: Session-Konvention `tm wt <branch>` setzt Session-Name == Worktree-Branch-Name.
- **`_Betrieb/Backlog/00_HOT-FILES.md`**: handgepflegte Liste strang-übergreifend nur seriell anfassbarer Pfade.
- **`beruehrt:`-Feld** auf allen Seeds gepflegt (PLAT-012 B3).
- **MobaXterm-Workflow**: tmux-Windows als Multiplexer, eine SSH-Verbindung reicht.

## Offene Punkte für die Spec

1. **Worktree-Layout-Pfad:** `~/git/wt-<branch>/` (extern) vs. unterhalb knowledge-base. Empfehlung Claude: **extern**, weil `10_Kunden/`-Daten dann strukturell nicht im Worktree-Clone landen.
2. **`10_Kunden/`-Schutz im Worktree-Kontext:** Sicherstellen, dass ein paralleler Worktree mandantenpersonenbezogene Daten nicht versehentlich klont oder bearbeitbar macht (Mechanik: Worktree-Pfad außerhalb der Mandanten-Spiegel, `.gitattributes` / sparse-checkout falls nötig).
3. **`beruehrt:`-Disjunktheits-Check vor Strang-Start:** maschinell oder normativ? Optional Erweiterung des Übersichts-Skripts um eine „kann ich diese zwei Seeds parallel?"-Funktion (Vergleich der `beruehrt:`-Mengen + Hot-File-Berührung).
4. **Hot-File-Pflicht-Tor:** soll die Spec verlangen, dass Hot-File-Berührung von beiden Strängen vor Start ausgeschlossen ist?
5. **Merge-Integration:** wer macht den Re-Sync-Merge vom Worktree zurück nach `main`, wann ist er „bewusst ausgelöstes Bündel" vs. Routine?
6. **Skalierung:** zwei parallele Stränge zum Start sinnvoll; drei+ nur nach belastbarer Erfahrung mit zwei. Spec-Frage.
7. **Kritikalitäts-Kollision:** zwei Spuren parallel, beide mit `kritisch`-Bündeln gleichzeitig → für den Mensch nicht handhabbar. Regel? (Vorschlag: nie zwei `risikoklasse: kritisch`-Strange parallel.)

## Bezug zu bestehenden Seeds / Specs

- **`PLAT-013` (`PLAT-013_SPEC.md`)** als basiert-auf — diese Spur greift nur unter dem Korridor-Modell.
- **`PLAT-012`** lieferte die Datenbasis (`beruehrt:`, Hot-Files, pre-commit-Hook). Ohne sie wäre Phase 2 zu riskant.
- **`seed-autonomie-korridor-parallele-sessions__verfassungs-vorlage.md`** enthält Skizzen für die Worktree-Mechanik aus der ursprünglichen Diskussion. Beim Spec-Start als Ausgangspunkt prüfen.

## Stufe / Risiko

**Spur.** Risikoklasse `kritisch`: parallele Sessions berühren Branch-Topologie, Commit-Reihenfolge, potenzielle Hot-File-Konflikte und (bei Fehler) Kundendaten-Sichtbarkeit. Erzwingt Spur ohnehin.

## Trigger

`bald` — sobald die drei Bewertungsfragen oben mit Daten beantwortet werden können. Frühestens nach 3 abgeschlossenen Korridor-Spuren (also nicht direkt nach PLAT-013).
