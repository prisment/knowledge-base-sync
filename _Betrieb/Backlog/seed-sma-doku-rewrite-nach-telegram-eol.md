---
typ: backlog_seed
titel: "SMA-Doku Re-Write nach Telegram-EOL (8 Dateien mit VERALTET-Markierung)"
geltungsbereich: prisment
risikoklasse: sicher
status: offen
prioritaet: mittel
stand: 2026-05-25
erzeugt_durch: PLAT-009 Bündel 6
---

## Anlass

PLAT-009 hat Telegram + Twilio aus dem Produktivbetrieb komplett entfernt
(n8n-Workflows weg, Code-Pfade weg, compose-env-Vars weg). Die nachstehenden
8 Doku-Dateien beschreiben noch den alten Telegram-Stand und wurden in
PLAT-009 Bündel 6 nur mit einer **VERALTET-Markierung** versehen, weil ihr
Re-Write den Rahmen von PLAT-009 gesprengt hätte (eine Datei hat >70
Telegram-Treffer, eine andere >50).

Ehrliches Markieren statt Pseudo-Bereinigung: Leser sehen sofort, dass die
Inhalte historisch sind und nicht zum aktuellen System passen. Aber der
eigentliche Re-Write steht aus.

## Betroffene Dateien

1. `Prisment/Systemzustand/Architektur/agents/langgraph-analytics.md`
   (Telegram als Ausgangskanal; dependencies, Pipeline-Nodes,
   `send_*_telegram`-Erwähnungen)
2. `Prisment/Systemzustand/Architektur/agents/langgraph-content.md`
   (Telegram-Versand-Pipeline, Tools, Output-Erwähnungen, n8n
   Telegram Router als Kommunikationspartner)
3. `Prisment/Systemzustand/Architektur/agents/langgraph-interview.md`
   (Modus 2 „Spontaneous" komplett auf Telegram-Webhook aufgebaut;
   Stateless-Begründung leitet sich aus Telegram-Nachrichten-Pattern ab)
4. `Prisment/Systemzustand/Architektur/SMA_Operative_Doku.md`
   (Telegram-Bot-Token-Setup, Webhook-Doku, Test-Bot-Switch)
5. `Prisment/Systemzustand/Architektur/Social Media Automation – Technische Dokumentation.md`
   (76 Telegram-Treffer durchgehend — das ist die Hauptdoku des Produkts)
6. `Prisment/Systemzustand/Marketing/prisment_brand_design_system.md`
   (Telegram-Bot-Sprache als eigener Touchpoint-Abschnitt, Telegram-Avatar,
   Voice-Regeln für Telegram-Templates)
7. `Prisment/Systemzustand/Produkt/PWA_KUNDEN_APP.md`
   (Begründung „Ersetzt Telegram-Bot" steht historisch korrekt drin, aber
   Soll-Zustand-Aussagen müssen sauber von Vergangenheits-Begründung
   getrennt werden)
8. `Prisment/Arbeitsgedaechtnis/SMA_ROADMAP.md`
   (52 Telegram-Treffer in abgeschlossenen ✅-Schritten und offenen
   ⬜-Schritten — nicht alle müssen weg, aber Schritt-Status muss
   re-evaluiert werden)

## Soll-Zustand

Pro Datei einzeln:
- ✅-Schritte mit Telegram bleiben als historischer Verlauf — sie sind
  korrekt, weil sie genau das beschreiben was damals passiert ist.
- ⬜-Schritte und Soll-Zustand-Beschreibungen werden auf den aktuellen
  PWA-Push-+-Email-Fallback-Stand umgeschrieben.
- Bei `langgraph-interview.md`: Modus 2 „Spontaneous" muss neu beschrieben
  werden — wie funktioniert spontaner Eingang heute ohne Telegram-Webhook?
- VERALTET-Header wird beim Re-Write entfernt.

## Auslöser für die Mini-Spec

Wann ist sinnvoll: nach PLAT-008 Re-Wiederaufnahme + Abschluss
(Plattform-Doku ist dann stabil) und vor dem nächsten Produkt-Iterations-
zyklus (sonst basiert Iteration auf veralteter Tech-Doku).

## Form

Stufe vermutlich Spur (cross-Datei, Soll-Zustand-Doku-Re-Write, prägt
Architekten-Wissen). Risikoklasse sicher (Doku-only).

## Verweis

Die VERALTET-Header in den 8 Dateien zeigen explizit auf diesen Seed.
