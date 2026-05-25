---
typ: backlog_seed
titel: "Geposted-Section in der PWA — echte Engagement-Visualisierung als Motivations-Loop"
geltungsbereich: prisment
risikoklasse: sicher
status: offen
klasse: feature
zugkraft: bald
stufe: sprung
beruehrt: ["pwa/pwa-web/", "pwa/pwa-api/"]
stand: 2026-05-25
erzeugt_durch: ROADMAP_SMA-Zerlegung (Schritt 44.6, Grubi-Live-Erkenntnisse 2026-05-12)
abhaengig_von:
  - "[[_Betrieb/Backlog/seeds/seed-sma-meta-api-integration]]"
---

## Anlass

Aus dem Grubi-Live-Test 2026-05-12: ein echter Mandant braucht einen
sichtbaren Erfolgs-Loop. Heute landet ein veröffentlichter Post in der
"Gepostet"-Liste mit Stub-Daten ("—" oder "bald"). Damit fehlt die emotionale
Belohnung, die im B2B-SaaS für Self-Posting-Tools der Hauptmotivator ist
(„hat mein Post funktioniert?").

Schritt 44.6 in der alten Roadmap war als eigenständiger Bauteil geführt —
faktisch hängt er aber an echten Metriken aus der Meta Graph API.

## Ziel (Soll-Zustand, grob)

Eigener Bereich in der PWA (oder Verfeinerung der bestehenden
`/posts/published`-Liste) mit:

- Übersicht aller geposteten Posts in chronologischer Reihenfolge
- Pro Post: wann gepostet, aktuelles Engagement (Likes, Kommentare, Reach
  wenn verfügbar), Format-Badge, Säulen-Zuordnung
- Visuelles Element, das Erfolg sichtbar macht (Trend-Indikator, Top-Post-
  des-Monats-Highlight o.ä. — in der Spec gestaltet)
- Verknüpfung zur Voice-Learning-Signal-Anzeige („dieser Post hat XYZ
  in deinen Voice-Anchor eingespielt") als positives Feedback

Bewusste UX-Anweisung aus Grubi-Live-Test: **soll motivieren** — Aesop-Ruhe,
keine bunten Dashboards, aber sichtbarer Fortschritt.

## Offene Fragen für die spätere Spec

- **Eigenständiger Tab vs. Verfeinerung von `/posts/published`?** Heute
  reicht ein Filter, aber wenn die Section eigene Logik (Trends,
  Highlights) bekommt, lohnt sich ein eigener Bereich.
- **Welche Metriken in der Übersicht, welche im Detail?** Reach +
  Engagement-Rate als Standard, Detail-View pro Post mit kompletter
  Insights-Auflistung? (Hängt am Meta-API-Schema, das im Seed-`meta-api`
  geklärt wird.)
- **Trend über Zeit:** rollende 7-Tage-Performance vs. Monatsvergleich?
- **Welche Action-Trigger:** Boost-Vorschlag bei Über-Performance? "Mehr
  davon"-Signal an Redaktionsplan-Agent? (Erste Stufe: nur Anzeige,
  Actions im zweiten Bündel.)

## Stufe / Risiko

**Sprung.** UI/UX-Feature in der PWA, klare Abgrenzung, kein
Architektur-Eingriff. Risikoklasse `sicher` (Read-only-View auf bereits
existierende Daten, sobald Meta-API live ist). Backwards-kompatibel mit
heutiger Stub-Anzeige bis Meta-API durch ist.

## Trigger

Bald — aber erst nach `seed-sma-meta-api-integration`. Vorher ist der
Bauteil mit Stub-Daten ohne Mehrwert gegenüber der heutigen `/posts/
published`-Liste.
