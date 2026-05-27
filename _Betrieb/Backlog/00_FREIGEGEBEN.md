---
typ: backlog_freigegeben
titel: "Freigegebene, autonom ziehbare Seeds — handgepflegt, kettenbewusst"
gepflegt_von: Mensch (handgepflegt, NICHT generiert)
stand: 2026-05-27
---

# Freigegebene Halde

**Definition (Verfassung 01, Abschnitt „Autonome Halde"):** Seeds, deren
freigegebene Spec `risikoklasse: sicher` trägt und die im Seed-Frontmatter
`autonom_ziehbar: ja` haben. Beides UND, sonst gehört der Seed nicht hierher.

**Struktur:**
- **Ein Block = eine Abhängigkeitskette.** Innerhalb eines Blocks regelt das
  bestehende `abhaengig_von` der Seeds die Reihenfolge.
- **Unabhängige Stränge** stehen als getrennte Blöcke.
- **Reihenfolge der Blöcke = Priorität** (vom Menschen gesetzt — das „Wohin").

**Blocker-Regel (Verfassung 01):** Ein Stopp-Auslöser, Fall C oder eine
Abschluss-Notiz mit Nacharbeitsforderung **friert die betroffene Kette ein**.
Kein abhängiges Folge-Glied wird gezogen. Unabhängige Stränge bleiben unberührt.

**Pflege:** Strikt handgepflegt (analog `00_HOT-FILES.md`). Das Übersichts-Skript
liest und überschreibt diese Datei nicht. Aufnahme/Streichung durch den
Architekten, jeweils im selben Commit wie die Auslöser-Änderung (Spec-Freigabe
bzw. Blocker-Klärung).

**Abgrenzung:** Diese Datei sagt nur, **was** ziehbar ist. Die Worker-/Loop-
Mechanik (wer zieht, wann, wie viele parallel) ist nicht hier geregelt und
wird als eigene spätere Spur gebaut.

---

## Ketten

<!--
Vorlage für einen Block:

### Block N — kurzer Titel der Kette

- [[_Betrieb/Backlog/seed-erstes-glied]]
- [[_Betrieb/Backlog/seed-zweites-glied]]   # `abhaengig_von: seed-erstes-glied`
- [[_Betrieb/Backlog/seed-drittes-glied]]   # `abhaengig_von: seed-zweites-glied`

Jeder Eintrag muss im Seed `autonom_ziehbar: ja` tragen und eine freigegebene
sicher-Spec haben.
-->

_(noch leer — wird vom Architekten befüllt)_
