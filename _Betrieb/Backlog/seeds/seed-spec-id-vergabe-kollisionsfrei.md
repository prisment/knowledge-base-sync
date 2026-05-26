---
typ: backlog_seed
titel: "Spec-ID-Vergabe kollisionsfrei machen (Race-Schutz für parallele Worktree-Spuren)"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: prozess
zugkraft: bald
stufe: sprung
beruehrt: ["scripts/backlog/", "_Betrieb/Verfassung/01_Spec-Format.md", "Architekten-CLI-Helfer"]
stand: 2026-05-26
erzeugt_durch: ID-Kollision PLAT-019 live aufgetreten 2026-05-26 (parallele Sessions reservierten gleichzeitig dieselbe Nummer)
---

## Anlass

Echter Live-Konflikt-Wirkungs-Beweis am 2026-05-26 — der im PLAT-015-
Pilot strukturell vorbereitete, aber nicht eingetretene Konflikt-Pfad
ist jetzt aufgetreten. **Form:** zwei parallele Sessions vergaben
unabhängig dieselbe Spec-ID `PLAT-019`:

- Session A (Koordinations-Session, main): „generate_uebersicht.py
  Worktree-aware" — abgeschlossen, archiviert in
  `Plattform/Archiv/PLAT-019/`.
- Session B (vermutlich wt/os-patching-Worktree): „Nightly +
  Alarm-Pfad wiederbeleben" — aktiv, Spec im Arbeitsgedächtnis, Bündel
  1+2 schon nach `main` gemergt.

Beide Sessions sahen PLAT-018 als letzte vergebene ID und reservierten
unabhängig PLAT-019. Der Inline-Konflikt-Klassifikator (PLAT-015
Bündel 4) hat das nicht abgefangen, weil die Dateien an **unterschied-
lichen Pfaden** liegen (`Archiv/PLAT-019/` vs. `Arbeitsgedaechtnis/`)
— kein Datei-Konflikt, sondern **Identifier-Konflikt**.

## Soll-Zustand

Die Spec-ID-Vergabe wird kollisionsfrei, ohne dass der Mensch sie
manuell synchronisieren muss. Drei Implementierungs-Optionen, die
spätere Spec wählt:

### Pfad (A) — `next-spec-id <bereich>`-Helfer-Skript

Skript am Server unter `scripts/backlog/next-spec-id.sh` (oder `.py`),
das:
1. `git fetch --all --quiet` ausführt (alle Remote-Refs aktuell).
2. Sucht in **allen Branches** (`main` + alle `wt/*`) nach Spec-IDs
   im Format `PLAT-NNN` / `PRIS-NNN` / `INT-NNN`. Quellen: Front-
   Matter `spec_id:`-Felder in `*/Arbeitsgedaechtnis/*_SPEC.md` und
   `*/Archiv/*/*_SPEC.md`.
3. Liefert die nächste freie Nummer als stdout.
4. Optional: reserviert die Nummer via Marker-Datei (z.B.
   `_reservation_PLAT-020_<timestamp>_<branch>.tmp`) — verhindert
   eine zweite Vergabe in der Vergabe-Lücke zwischen Aufruf und
   Spec-Erstellung. Marker wird bei Spec-Commit automatisch
   entfernt.

**CLAUDE.md-Anweisung:** beim Spec-Erstellen IMMER zuerst
`next-spec-id <bereich>` aufrufen, nie selbst raten.

### Pfad (B) — Verfassungs-Regel + zentrale Vergabe-Datei

Eine Datei `_Betrieb/Backlog/00_NEXT-IDS.md` führt den Counter pro
Bereich (PLAT/PRIS/INT). Jede Spec-Vergabe inkrementiert sie + Commit.
Konflikte sind hier als **echter** Datei-Konflikt sichtbar — beim
Phase-9-Merge zweier parallelen Spuren würde der Counter-Inkrement
in derselben Datei-Zeile als Inhalts-Konflikt auflaufen → Inline-
Klassifikator schlägt Alarm + Architekt-Stopp.

**Vorteil:** nutzt die bestehende Konflikt-Mechanik statt eigene
Reservierungs-Logik.
**Nachteil:** zusätzliche Datei, jeder Spec-Lauf braucht extra Commit.

### Pfad (C) — Zeitstempel-IDs statt fortlaufender Nummer

Spec-IDs werden Format-frei mit Zeitstempel: `PLAT-2026-05-26-1402`
(Bereich-Jahr-Monat-Tag-HHMM). Garantiert kollisionsfrei, weil zwei
Sessions nicht in derselben Minute starten.

**Vorteil:** trivial, kein Skript, keine Datei-Pflege.
**Nachteil:** weniger lesbar; sortierfreundlich aber unhandlich in
Chat-Verweisen („PLAT-019" ist kürzer und memorierbarer als
„PLAT-2026-05-26-1402").

## Empfehlung für die Spec

**Pfad (A) als Default, (B) als Fallback wenn (A) Tooling-Aufwand zu
hoch.** (C) verworfen, weil Lesbarkeit + Memorierbarkeit der heutigen
laufenden Nummern wertvoll ist.

Begründung Default (A):
- Nutzt bestehende git-Mechanik (`fetch --all` + grep).
- Skript-Aufruf am Spec-Anfang ist klare Pflicht-Tor-Stelle.
- Marker-Reservierung schließt das Vergabe-Lücken-Race.
- Keine zusätzliche Datei-Pflege im Backlog.

## Korrekturschritt zur live aufgetretenen Kollision (separate Action)

PLAT-019 (Session B, „Nightly + Alarm-Pfad") wird auf **PLAT-020**
umnummeriert. Banner-Hinweis ist in der parallelen Spec-Datei
verankert (Header oben). Die Korrektur passiert in der laufenden
Session B (nicht von hier aus), bevor sie ihren Phase-9-Merge macht
— sonst verschmelzen die zwei PLAT-019-Identifier in der History.

## Akzeptanzkriterien (für die spätere Spec)

- [ ] Aufruf `next-spec-id PLAT` (oder gewählte Mechanik) liefert eine
      garantiert freie Nummer — auch unter Berücksichtigung aller
      offenen `wt/*`-Branches.
- [ ] Test: synthetische Doppel-Vergabe (zwei Aufrufe in schneller
      Folge ohne Commit dazwischen) liefert ZWEI verschiedene Nummern
      (Marker-Reservierung greift).
- [ ] CLAUDE.md global referenziert die Mechanik als Pflicht-Schritt
      beim Spec-Erstellen.
- [ ] Bestehende Specs/Archive bleiben unverändert (Mechanik gilt nur
      für **neue** Vergaben).
- [ ] Bei Konflikt im Reservierungs-Marker (Race zwischen zwei
      Sessions): klare Fehlermeldung mit Architekten-Stopp, keine
      stille Doppel-Vergabe.

## Offene Fragen für die spätere Spec

- **Marker-Reservierung im Backlog oder im jeweiligen
  Arbeitsgedächtnis?** Backlog wäre zentral, Arbeitsgedächtnis bereichs-
  lokal. Erste Tendenz: Backlog (eine Stelle, alle Bereiche).
- **Marker-Cleanup bei abgebrochener Spec:** wenn Session B die
  reservierte Nummer doch nicht nutzt (z.B. weil sie abbricht), wann
  wird der Marker entfernt? TTL (24h) oder manuell?
- **PRIS- und INT-Bereich:** ist die Race-Wahrscheinlichkeit dort
  ähnlich, oder nur PLAT relevant (weil Plattform-Spuren parallel-
  intensiver sind)? Vermutlich gilt dieselbe Mechanik bereichsweit.

## Stufe / Risiko

**Sprung.** Klar abgegrenzt — ein Skript + eine CLAUDE.md-Zeile +
optional eine zentrale Datei. Risikoklasse `sicher` — additive
Sicherheits-Schicht, keine Auth-/Datenpfad-Berührung. Falls Pfad (A)
mit Marker-Reservierung: Hook-Erweiterung nötig, könnte zum kleinen
Sprung wachsen.

## Trigger

`bald` — vor der nächsten echten Mehrfach-Parallel-Vergabe-Situation,
damit die jetzige Kollision nicht zur Wiederholung wird. Akut-Status
nicht nötig, weil die heutige Kollision durch Banner-Hinweis +
manuelle Umnummerierung lösbar ist; aber das nächste Mal sollte sie
strukturell verhindert werden.
