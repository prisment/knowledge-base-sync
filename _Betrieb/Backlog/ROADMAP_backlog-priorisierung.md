---
typ: roadmap
titel: "Backlog-Priorisierung (Klasse × Zugkraft + harte Security-Regel) + generierte Übersicht"
stufe: spur
geltungsbereich: alle
risikoklasse: sicher
status: in_arbeit
stand: 2026-05-25
seed: "[[Backlog/seed-backlog-priorisierung-uebersicht]]"
bereich_arbeitsgedaechtnis: Plattform
---

# Roadmap — Backlog-Priorisierung

**Ziel:** Verfahren (Klasse × Zugkraft + harte Security-Regel + `block:`-Gruppierung + `beruehrt:`-Feld + Hot-Files) als Verfassungs-Felder verankern, ein dummes Skript baut daraus eine lesbare Übersicht + Abhängigkeits-/Parallelisierungs-Karte. Macht die Priorisierung ALLER anderen Seeds erst lesbar.
**Stand:** 2026-05-25

## Schritte

1. ✅ Seed eingeordnet + Einschübe eingearbeitet (commit `44523d4`)
2. 🔄 **Phase 2 — Faktensammlung** (Claude Code) — *aktuell*
3. ⬜ Phase 3 — Spec (Architekt im Chat)
4. ⬜ Phase 4 — Übergabe Spec ins Repo
5. ⬜ Phase 5 — Machbarkeitsanalyse (Claude Code)
6. ⬜ Phase 6 — Bundled Execution:
   - B1 Verfassungs-Patches (`01_Spec-Format`, `00_Iterationszyklus`, Templates)
   - B2 Übersichts-Skript im `prisment-platform` Repo (`scripts/backlog/`)
   - B3 ★ Migration aller Alt-Seeds + ROADMAPs (Pflicht-Stopp: Sammel-Tabelle)
   - B4 Erst-Generierung `_Betrieb/Backlog/00_UEBERSICHT.md` + SVG
   - B5 Routine-Anbindung (Skript-Aufruf an Mutations-Punkten)
7. ⬜ Phase 7 — Abschluss-Verifikation
8. ⬜ Phase 8 — Retrospektive
9. ⬜ Phase 9 — Abschluss (Logbuch-Eintrag, Archiv)

## Aktueller Schritt — Detail

Phase 2 läuft. Claude Code erhebt:
- IST-Front-Matter aller Seeds + ROADMAPs in `_Betrieb/Backlog/`.
- Hot-File-Kandidaten im `prisment-platform` Repo.
- Vorschlags-Tabelle `klasse`/`zugkraft`/`block`/`beruehrt` pro Seed + Kurzbegründung — als Grundlage für die Migrations-Sammel-Freigabe in Phase 6 B3.

Output: `Plattform/Arbeitsgedaechtnis/report_fakten_backlog-priorisierung_2026-05-25.md`.

Danach Stopp → Übergabe an Architekt für Phase 3.

## Offene Fragen / Entscheidungen (für Phase 3)

- **Fünfte Klasse `prozess`/`framework`?** (mehrere Seeds hängen davon ab)
- **Block-Namen final** gegen die reale Seed-Liste (4 aus dem Seed + Vorschläge aus den Fakten).
- **`beruehrt:`-Granularität** (Ordner vs. datei-genau).
- **Hot-File-Liste:** wo gepflegt (Übersichts-Sektion vs. Verfassung).
- **`kritisch`-Flag-Definition:** kommt aus Autonomie-Seed, hier nur Querverweis-Verankerung.
- **Sortier-Logik** exakt (Sekundär-Sortierung bei gleicher Klasse+Zugkraft).
- **SVG-Form** (stdlib-Box-Pfeil vs. Mermaid).
- **Roadmaps in der Übersicht:** eigener Block oder als Block-Köpfe?

## Notizen für die nächste Session

Wenn Phase 2 abgeschlossen ist und der Faktenreport im Arbeitsgedächtnis liegt:
Architekt geht in den Chat, formt Spec. Claude Code wartet.
