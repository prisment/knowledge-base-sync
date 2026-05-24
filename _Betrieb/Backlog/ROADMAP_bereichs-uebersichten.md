---
typ: roadmap
titel: "Bereichs-Übersichten je Geltungsbereich erstellen"
stufe: spur
geltungsbereich: alle
risikoklasse: sicher
status: in_arbeit
stand: 2026-05-24
seed: "[[Backlog/seed-bereichs-uebersichten]]"
---

# Roadmap — Bereichs-Übersichten

**Ziel:** Je Geltungsbereich existiert unter `<Bereich>/Systemzustand/Uebersicht/`
eine kuratierte, optisch hochwertige Übersicht (MD + SVG), die für Mensch und
Chat-Architekt als schneller Bereichs-Einstieg dient und über den GitHub-Sync ins
Projekt-Wissen gelangt. Pflege-Disziplin ist in der Verfassung verankert.

**Stand:** 2026-05-24 — Initiative gestartet, Plattform-Pilot in Vorbereitung.

## Stufeneinstufung

**Spur** (vom Mensch bestätigt, 2026-05-24). Begründung: Wirkung über Einzelfall
hinaus (alle drei Bereiche), Verfassungs-Änderung nötig (Pflege-Pflicht),
mehrtägig, ändert was im GitHub-Sync sichtbar wird.

## Schritte

1. 🔄 **Plattform-Pilot (PLAT-008)** — Vollausstattung Plattform-Übersichten
   (00_Bereich + Architektur-SVG + Infrastruktur + geteilte Dienste +
   Sicherheits-Architektur), inkl. Verfassungs-Änderung (Pflege-Pflicht).
   *Aktiv.*
2. ⬜ **Prisment-Übersichten (PRIS-XXX)** — Bestand umziehen
   (`Architektur/00_PRISMENT_ARCHITEKTUR_UEBERBLICK.md` + SVG →
   `Uebersicht/00_Bereich.md` + `01_Architektur.svg`) und gegen Pilot-Format
   anpassen. Plus thematische Übersichten (Agenten, Onboarding-Pipeline, PWA,
   Strategie) gemäß Seed.
3. ⬜ **Intern-Übersichten (INT-XXX)** — interner Betrieb + Prozess-Inventar.
4. ⬜ **Sync-Whitelist final prüfen** — sicherstellen, dass alle drei
   `Uebersicht/`-Pfade enthalten sind (sind sie heute schon — Re-Check beim
   Abschluss reicht).

## Aktueller Schritt — Detail

**Schritt 1: Plattform-Pilot (PLAT-008).** Nächste Aktion: Faktensammlungs-Report
für Plattform — Inventar dessen, was tatsächlich unter
`Plattform/Systemzustand/` lebt, damit die Übersichten den IST-Stand verdichten
können (keine Erfindung).

## Verfassungs-Änderung (Teil von Schritt 1)

Vom Mensch freigegeben: in **beiden** Verfassungsdateien knapp ergänzen, vorher
auf Aktualität prüfen.

- `00_Iterationszyklus.md` Phase 9: Trigger „Systemzustands-Änderung im Bereich"
  → Übersicht prüfen + ggf. im selben Commit aktualisieren ODER bewusste Notiz
  warum nicht.
- `02_Rollen-Protokoll.md`: stehende Claude-Code-Pflicht ergänzen.

## Offene Fragen / Entscheidungen

- Konkrete Sub-Übersichten je Bereich werden im jeweiligen Schritt geschärft,
  nicht hier vorab.
- SVG-Iterations-Budget: erster Wurf + 1–2 Schliffe vor Mensch-Freigabe.

## Notizen für die nächste Session

Wenn Session in Schritt 1 abbricht: Stand steht in der laufenden Spec PLAT-008
unter `Plattform/Arbeitsgedaechtnis/`. Roadmap-Status hier reflektiert nur
abgeschlossene Schritte.
