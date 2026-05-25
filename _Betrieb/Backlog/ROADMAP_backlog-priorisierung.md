---
typ: roadmap
titel: "Backlog-Priorisierung (Klasse × Zugkraft + harte Security-Regel) + generierte Übersicht"
geltungsbereich: alle
risikoklasse: sicher
status: in_arbeit
klasse: prozess
zugkraft: jetzt
stufe: spur
beruehrt: ["_Betrieb/Backlog/", "_Betrieb/Verfassung/", "scripts/backlog/"]
stand: 2026-05-25
seed: "[[Backlog/seed-backlog-priorisierung-uebersicht]]"
bereich_arbeitsgedaechtnis: Plattform
---

# Roadmap — Backlog-Priorisierung

**Ziel:** Verfahren (Klasse × Zugkraft + harte Security-Regel + `block:`-Gruppierung + `beruehrt:`-Feld + Hot-Files) als Verfassungs-Felder verankern, ein dummes Skript baut daraus eine lesbare Übersicht + Abhängigkeits-/Parallelisierungs-Karte. Macht die Priorisierung ALLER anderen Seeds erst lesbar.
**Stand:** 2026-05-25

## Schritte

1. ✅ Seed eingeordnet + Einschübe eingearbeitet (commit `44523d4`)
2. ✅ Phase 2 — Faktensammlung (commit `4e53d22`)
3. ✅ Phase 3 — Spec-Entscheidungen (Architekt im Chat, 2026-05-25)
4. ✅ Phase 4 — Spec freigegeben (PLAT-012_SPEC, mit Sortier-Korrektur + AK 6 umformuliert)
5. 🔄 **Phase 5 — Machbarkeit + Bündel B1–B5** — *aktuell*
   - ✅ B1 — Verfassungs-Patches (commit `960f51b` knowledge-base)
   - ✅ B2 — Übersichts-Skript (commit `0a3af09` prisment-platform, Idempotenz verifiziert)
   - ⏸ **B3 — Migrations-Sammel-Tabelle (Pflicht-Stopp)**
   - ⬜ B4 — Erst-Generierung + 00_HOT-FILES.md
   - ⬜ B5 — Routine-Anbindung
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

Spec liegt unter `Plattform/Arbeitsgedaechtnis/PLAT-012_SPEC.md`. Wartet auf
**Spec-Freigabe** (Pflicht-Stopp Phase 6 vorne). Danach Phase 5 (Machbarkeit
+ Bündel-Vorschlag B1–B5) durch Claude Code.

**Architekten-Entscheidungen 2026-05-25 in der Spec verarbeitet:**
- Fünfte Klasse `prozess`: JA.
- `block:`-Feld vorerst NICHT einführen; Gruppierung über `geltungsbereich`.
- Sortierung: `klasse` × `zugkraft`, gruppiert nach `geltungsbereich`.
- `seed-pretooluse-hook-allowlist` → `zugkraft: bald` (Korrektur).
- Restliche offene Fragen 3–10 von Claude Code beantwortet (siehe Spec).

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
