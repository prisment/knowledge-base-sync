---
typ: roadmap
titel: "Lebende Bereichs-Dokumentation (Bereinigung + Übersichten + Automatisierung)"
stufe: spur
geltungsbereich: alle
risikoklasse: sicher
status: in_arbeit
stand: 2026-05-24
seed: "[[Backlog/seed-bereichs-uebersichten]]"
vorgaenger_roadmap: "ROADMAP_bereichs-uebersichten.md (am 2026-05-24 umbenannt, Scope erweitert)"
---

# Roadmap — Lebende Bereichs-Dokumentation

**Ziel:** Pro Geltungsbereich existiert (a) eine aktuelle, mit der Realität
übereinstimmende Detail-Doku unter `<Bereich>/Systemzustand/`, (b) eine
kuratierte Übersicht unter `<Bereich>/Systemzustand/00_Uebersicht/` (MD + SVG)
für Mensch und Chat-Architekt, und (c) eine Mechanik, die beides am Leben
hält — Pflicht-Tor in Phase 9 plus nightly Drift-Check.

**Stand:** 2026-05-25 — PLAT-008 (Bestandsaufnahme + Bereinigung Plattform) abgeschlossen (Archiv: `Plattform/Archiv/PLAT-008/`). PLAT-009 dito (Telegram-EOL). Nächster Schritt: PLAT-010 — Übersichten Plattform.

## Stufeneinstufung

**Spur** (vom Mensch bestätigt, 2026-05-24). Reichweite über alle drei
Bereiche, Verfassungs-Änderung, langlebig. Risikoklasse bleibt `sicher` —
keine Eingriffe in laufende Dienste, nur Doku + später Skripte.

## Warum so groß

Der ursprüngliche Plan war „Übersichten erstellen". Beim Auslegen wurde
klar: die Plattform-Doku ist stark veraltet, viele Dienste existieren nicht
mehr oder haben sich verändert. Eine Übersicht aus veralteter Doku ist
wertlos. Daher: pro Bereich erst aufräumen, dann verdichten, dann die
Pflege automatisieren.

## Schritte

1. ✅ **PLAT-008 — Bestandsaufnahme + Bereinigung Plattform.**
   Abgeschlossen 2026-05-25 (Archiv: `Plattform/Archiv/PLAT-008/`). 6 Bündel durch, Doku stabil als Basis für Übersichten.
2. ✅ **PLAT-009 — Einschub: Twilio + Telegram End-of-Life.** Abgeschlossen
   2026-05-25 (Archiv: `Plattform/Archiv/PLAT-009/`). Logbuch E25
   (Telegram-EOL) + E26 (WAF-Skip Access-Apps). Folge-Seed:
   `seed-sma-doku-rewrite-nach-telegram-eol.md`.
3. 🔄 **PLAT-010 — Übersichten Plattform** (Vollausstattung gemäß Seed: 00_Bereich, 01_Architektur, Infrastruktur, geteilte Dienste, Sicherheits-Architektur — MD + SVG).
4. ⬜ **PRIS-XXX — Bestandsaufnahme + Bereinigung Prisment.**
5. ⬜ **PRIS-XXX+1 — Übersichten Prisment** (Bestand `Architektur/00_PRISMENT_…`
   umziehen in `Uebersicht/`, Format vereinheitlichen, Sub-Übersichten:
   Agenten, Onboarding-Pipeline, PWA, Strategie).
6. ⬜ **INT-XXX — Bestandsaufnahme + Bereinigung Intern.**
7. ⬜ **INT-XXX+1 — Übersichten Intern** (interner Betrieb, Prozess-Inventar).
8. ⬜ **PLAT-XXX — Automatisierung der Doku-Pflege.**
   - Verfassungs-Änderung: Pflicht-Tor in Phase 9 (Zyklus, der echtes System
     ändert, MUSS Detail-Doku + Übersicht des Bereichs im selben Commit
     aktualisieren).
   - Nightly Drift-Check (Skript) für Änderungen außerhalb von Zyklen.
   - Verfassung-Ergänzung in `00_Iterationszyklus.md` + `02_Rollen-Protokoll.md`.
   - Bewusst ans Ende, weil wir erst durch Schritte 1–6 wissen, wo die Doku
     real driftet.

## Aktueller Schritt — Detail

**Schritt 3: PLAT-010 — Übersichten Plattform.**
Nächste Aktion: Phase 1 (Auslöser + Stufen-Vorschlag + Plan für Faktensammlung). Inhalte stehen jetzt auf stabilem Detail-Doku-Stand aus PLAT-008.

## Vorsichtsregel zur Bereinigung

In Schritten 1–6 wird keine Doku-Datei gelöscht, ohne dass je Einzelfall
„weg, weil X" plus Mensch-Freigabe vorliegt. Bei Unsicherheit Rückfrage.
Aufräumen ohne Rückfrage = Risiko, dass Wissen verschwindet, das nur nicht
erkannt wurde.

## Offene Fragen / Entscheidungen

- Konkrete Sub-Übersichten je Bereich werden im jeweiligen Übersichts-Schritt
  geschärft, nicht hier vorab.
- Form des Drift-Checks (Schritt 7): Plain-Bash, Python-Stdlib, n8n-Workflow?
  Entscheidung in der Spec zu Schritt 7.

## Notizen für die nächste Session

Wenn die Session in Schritt 1 abbricht: Stand steht in der laufenden Spec
PLAT-008 unter `Plattform/Arbeitsgedaechtnis/`. Roadmap-Status hier
reflektiert nur abgeschlossene Schritte.
