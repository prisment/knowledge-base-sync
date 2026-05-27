---
typ: backlog_seed
titel: "Multi-Persona-Onboarding sauber durchziehen (Audio-Strategie + Admin-UI + E2E)"
geltungsbereich: prisment
risikoklasse: sicher
status: offen
klasse: feature
zugkraft: bald
mission:         live-gang
stufe: sprung
beruehrt: ["langgraph/", "pwa/", "admin/admin_web/"]
stand: 2026-05-25
erzeugt_durch: ROADMAP_SMA-Zerlegung (Schritt 26, Freitag-Kunde-Trigger)
---

## Anlass

Die Persona-Architektur (Schritt 18 in der alten Roadmap, ✅) ist gebaut, aber
das Onboarding für Mandanten mit mehreren Personas läuft heute nur über
manuelle CLI-Aufrufe (`onboarding_import.py --persona persona_X`). Drei
konkrete Lücken aus Block 2.1-Test 2026-05-11:

1. **Admin-UI zeigt `persona_key`** ("persona_1") statt Name+Rolle, weil
   `personas.name`/`personas.rolle`-Spalten leer sind. Echte Quelle ist
   `tenant_configs.config.personas.personen[]` (JSONB).
2. **Onboarding-Pipeline extrahiert nur persona_1.** Mehrere Personen in
   einem Onboarding-Audio werden vom EXTRACTION_PROMPT erkannt (4 Fälle
   dokumentiert), aber nur die erste landet in mandant_config.
3. **Separater `--persona`-Modus muss pro Persona einzeln aufgerufen
   werden** mit eigenem Audio. Für Mandanten mit Inhaber-Duo (Freitag-Kunde
   war konkretes Beispiel) ist das Friktion.

## Ziel (Soll-Zustand, grob)

1. **Admin-UI-Persona-Anzeige.** Endpoint liest aus
   `tenant_configs.config.personas.personen[]` (JOIN mit `personas`-Tabelle
   nur für `voice_md`-Existenz). DB-Spalten `personas.name`/`personas.rolle`
   können als ungenutzte Legacy markiert werden (Migration-Cleanup später).
2. **Audio-Strategie-Entscheid + Implementierung.** Eine der drei Optionen:
   - (a) 1 Onboarding-Audio + N Persona-Audios (1 pro Person), PWA-Upload-UI
     bekommt "+Persona X"-Button
   - (b) 1 großes Audio mit Sprecher-Markern, EXTRACTION_PROMPT erkennt "ab
     hier spricht Person 2"
   - (c) Hybrid: Inhaber-Audio + optionale Persona-Add-Ons später
3. **E2E mit 2 Personas.** Test-Mandant anlegen → Redaktionsplan generieren
   → `assign_persona_slots()` annotiert beide Personas korrekt →
   Content-Agent generiert je Topic in der richtigen Stimme → Admin-UI
   zeigt beide.

## Offene Fragen für die spätere Spec

- **Audio-Strategie:** (a) vs. (b) vs. (c) — Entscheidung am echten
  Mandanten-Beispiel.
- **Legacy-Spalten `personas.name/.rolle`:** sofort entfernen (Migration)
  oder ungenutzt belassen, bis ein größerer Schema-Cleanup ansteht?
- **Persona-Anzahl-Obergrenze:** technisch unbegrenzt, sinnvoll 2–3.
  Brauchen wir UI-Limit?
- **Persona-Reihenfolge in `assign_persona_slots()`:** heute deterministisch
  nach Säulen-Funktion + `posts_pro_monat`. Reicht das oder braucht es
  Persona-Präferenz pro Säule (Persona A bevorzugt Säule vertrauen,
  Persona B bevorzugt expertise)?

## Stufe / Risiko

**Sprung.** Mittlerer Scope (3 Bausteine), berührt mehrere Komponenten
(Onboarding-Script + EXTRACTION_PROMPT + Admin-UI + ggf. PWA-Upload), aber
keine Architektur-Weiche. Risikoklasse `sicher` (additiv zur bestehenden
Persona-Mechanik, kein Migration-Pflicht-Pfad — Mandanten ohne
`personas.aktiv` bleiben unberührt).

## Trigger

Bald — sobald ein zweiter Mandant mit Persona-Duo onboardet werden soll.
Bis dahin reicht der manuelle CLI-Pfad für Tests.
