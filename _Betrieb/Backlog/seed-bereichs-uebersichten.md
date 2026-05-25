---
typ: backlog_seed
titel: "Bereichs-Übersichten je Geltungsbereich erstellen"
geltungsbereich: alle
risikoklasse: sicher
status: offen
stand: 2026-05-23
erzeugt_durch: BOOT-001
---

## Anlass

Der GitHub-Sync soll NICHT den gesamten Systemzustand spiegeln (Datenmenge,
Mandantennähe, Volatilität), sondern nur kuratierte Übersichten je Bereich.
Diese Übersichten haben Doppelnutzen:
1. Sie sind das, was der Chat-Architekt im Projekt-Wissen sieht
   (Eintauch-Hilfe in den Bereich).
2. Sie sind für den Menschen selbst die schnelle Verdichtung des Bereichs —
   „was ist hier, wie hängt es zusammen, wo finde ich was".

## Soll-Zustand (grob)

Je Bereich existiert `<Bereich>/Systemzustand/00_Uebersicht/` mit mindestens:

- `00_Bereich.md` — Zusammenfassung des Bereichs (was lebt hier, Kerngedanke).
- `01_Architektur.svg` (oder ähnlich) — visuelle Übersicht (Komponenten, Flüsse).
- Weitere thematische Übersichten je nach Bedarf (z. B. `Datenmodell.svg`,
  `Onboarding-Flow.svg`).

Pflege-Disziplin: jede Übersicht trägt `stand:` im Front Matter; bei
Inhaltsänderung im Bereich muss die Übersicht im selben Zyklus aktualisiert
werden (sonst entsteht stille Doppel-Wahrheit). Die Übersichten sind
**abgeleitete Ansicht**, nicht zweite Quelle — sie verdichten, was unter
`Systemzustand/` steht (Konzept SSOT, Verfassung 03).

## Konkrete Übersichten pro Bereich (Erstausstattung)

### Plattform
- Zusammenfassung Plattform (was ist Plattform, was nicht).
- Infrastruktur-Übersicht (Server, Container, Networks, Storage).
- Geteilte Dienste — Topologie + Abhängigkeiten.
- Sicherheits-Architektur — Schichten (OS, Foundation, Anwendungen, Cloudflare).

### Prisment
- Produkt-Überblick (was tut Prisment, für wen).
- Agenten-Architektur (4 LangGraph-Agents + n8n-Workflows, Datenflüsse).
- Onboarding/Inhalts-Pipeline (von Voice-DB bis Post).
- PWA-Wissensstand (Web/API, Test/Live, A3-DB-Topologie).
- Strategie/Business-Verdichtung.

### Intern
- Interner Betrieb — Übersicht (welche internen Dienste, welche Prozesse).
- Prozess-Inventar.

## Form

- Markdown + SVG (SVG bevorzugt für Architektur, weil zoom-fähig, diff-tauglich).
- Keine PNG-Screenshots als Hauptquelle (toter Inhalt).
- Konsistente visuelle Sprache gemäß `Prisment/Systemzustand/Marketing/prisment_brand_design_system.md`.

## Whitelist-Konsequenz

Nur `<Bereich>/Systemzustand/00_Uebersicht/**` ist im GitHub-Sync. Der restliche
Systemzustand bleibt lokal (im Gitea-Repo). Die Übersichten verlinken bei
Bedarf auf detaillierte Unterdokumente (lokal lesbar, im Chat per Pfad
benennbar).

## Offene Fragen für die spätere Spec(s)

- Eine Spec pro Bereich, oder eine Sammel-Spec für alle drei?
- Welche SVG-Editor-/Generator-Pipeline (handgezeichnet vs. Mermaid/Excalidraw)?
- Ableitungs-Routine: wie wird sichergestellt, dass Übersichten nicht stillschweigend veralten?
