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

**Stand:** 2026-05-25 — Plattform + Prisment doku-stabil (Detail + Übersicht). Intern abgehakt (leer, kein Inhalt). Nächster Schritt: PLAT-011 — Automatisierung der Doku-Pflege (Schritt 8).

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
3. ✅ **PLAT-010 — Übersichten Plattform.** Abgeschlossen 2026-05-25 (Archiv: `Plattform/Archiv/PLAT-010/`). 4 MD + 3 SVG in `00_Uebersicht/`. SVG-Stil-Konvention etabliert für Folge-Zyklen.
4. ✅ **PRIS-015 — SMA-Doku Re-Write nach Telegram-EOL.** Abgeschlossen 2026-05-25 (Archiv: `Prisment/Archiv/PRIS-015/`). 10 Dateien re-geschrieben, SMA_ROADMAP umgezogen nach `_Betrieb/Backlog/ROADMAP_SMA.md`, prisment_architektur.svg im Brand-konformen Plattform-Stil neu.
5. ✅ **PRIS-016 — Übersichten Prisment.** Abgeschlossen 2026-05-25 (Archiv: `Prisment/Archiv/PRIS-016/`). 8 Dateien in `00_Uebersicht/` (5 MDs + 3 SVGs, Vollausstattung), Architektur-Bestand umgezogen, Helpdesk-Doku rudimentär nachgezogen.
6. ✅ **INT — Bestandsaufnahme + Bereinigung Intern.** Abgehakt 2026-05-25 — Intern-Bereich ist leer (nur `.gitkeep`-Skelett), kein Inhalt zu bereinigen. Sobald Inhalte angelegt werden, kommt ein eigener Aufbau-Zyklus.
7. ✅ **INT — Übersichten Intern.** Abgehakt 2026-05-25 — keine Detail-Doku da, also keine Übersicht zu erstellen. Pattern (`00_Uebersicht/`) ist klar, wird angewandt sobald Intern-Inhalte da sind.
8. ⬜ **PLAT-XXX — Automatisierung der Doku-Pflege.**
   - Verfassungs-Änderung: Pflicht-Tor in Phase 9 (Zyklus, der echtes
     System ändert, MUSS **sowohl die betroffene Detail-Doku in
     `<Bereich>/Systemzustand/...` ALS AUCH die Übersicht in
     `<Bereich>/Systemzustand/00_Uebersicht/`** im selben Commit
     aktualisieren). Detail-Doku ist SSOT, Übersicht ist abgeleitete
     Ansicht — beide müssen synchron bleiben.
   - Nightly Drift-Check (Skript) für Änderungen außerhalb von Zyklen.
   - Verfassung-Ergänzung in `00_Iterationszyklus.md` + `02_Rollen-Protokoll.md`.
   - Bewusst ans Ende, weil wir erst durch Schritte 1–6 wissen, wo die Doku
     real driftet.

## Aktueller Schritt — Detail

**Schritt 4: PRIS-XXX — Bestandsaufnahme + Bereinigung Prisment.**
Reihenfolge offen (vom Mensch zu entscheiden): (a) erst SMA-Doku-Re-Write via `seed-sma-doku-rewrite-nach-telegram-eol.md`, dann PRIS-Bereinigung; (b) Intern vorziehen; (c) Schritt 8 (Automatisierung) vorziehen, weil das Pflicht-Tor jetzt formuliert werden kann.

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
