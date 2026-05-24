# Dokument-Templates: Stufen-Ergänzung (Sprung · Schritt)
**Ort:** `_Betrieb/Templates/`
**Zweck:** Verschlankte Vorlagen für die Stufen **Sprung** und **Schritt** (siehe `_Betrieb/Verfassung/00_Iterationszyklus.md`). Ergänzt `Dokument-Templates.md` (das die volle **Spur** abdeckt). Gemeinsamer Front-Matter-Header bleibt identisch — nur Felder, die der Typ nicht braucht, werden weggelassen.

---

## Gemeinsamer Header (Ergänzung um Stufen-Felder)

Jede Spec/Notiz trägt zusätzlich zu den bestehenden Feldern:

```yaml
stufe:           sprung          # spur | sprung | schritt   (PFLICHT)
art:             bugfix          # feature | bugfix | recherche | prozess | wartung   (OPTIONAL, nur Etikett)
```

`stufe` ist Pflicht und steuert den Prozess. `art` ist optional und steuert NICHTS — reines Filter-Etikett.

---

## 1. SPRUNG-SPEC — kombiniert (Phase 3, erstellt vom Chat-Architekt)

Eine einzige Datei statt getrennter Fakten/Spec/Machbarkeit. Claude Code erhebt nötige Fakten und prüft Machbarkeit inline; nur grobe Abweichungen gehen zurück in den Chat. **Ein Stopp:** Mensch gibt diese Spec frei, dann zieht Claude Code durch bis zur Abschluss-Notiz.

```markdown
---
typ:             spec
spec_id:         PLAT-007
titel:           "…"
geltungsbereich: plattform
stufe:           sprung
art:             bugfix
risikoklasse:    sicher
zyklus_phase:    3
status:          offen
stand:           2026-05-24
seed:            "[[…]]"   # falls aus Backlog
---

## Zusammenfassung
[2–3 Sätze: Ziel + Kern der Lösung. Verdichtet, was unten steht.]

## Ziel & Soll-Zustand
[Was soll nach Abschluss WAHR sein? Konkret genug für Claude Code —
betroffene Komponenten, Pfade, Dienste namentlich. Fakten, soweit für die
Entscheidung nötig, hier inline mit Quelle (bewiesen, nicht angenommen).]

## Akzeptanzkriterien
[Prüfbare Zustände. Tiefe skaliert mit Risikoklasse — beim Sprung meist knapp:
 - [ ] …
 - [ ] …]

## Rollback-Erwartung
[Wohin im Fehlerfall zurück? Bei „sicher": Auto-Rollback-Pfad genügt.]

## Nicht Teil dieser Spec
[Knappe Abgrenzung gegen Hineinwachsen. Was auftaucht, aber ausgeklammert
wird → Backlog-Abzweigung, nicht hier. Wird es kritisch → Eskalation zu Spur
vorschlagen.]
```

**Inline-Machbarkeit:** Claude Code ergänzt bei Übernahme knapp seine Arbeitsliste + meldet Abweichungen — kein eigenes Machbarkeits-Dokument. Entpuppt sich der Sprung als Spur → Eskalation vorschlagen (Verfassung 00), nicht stillschweigend größer fahren.

---

## 2. SPRUNG-ABSCHLUSS-NOTIZ — kurz (Phase 7/9)

Kein voller Ziel-Abgleich-Apparat. Reiner Abgleich + Abweichungen, ohne die Spec nachzuerzählen.

```markdown
---
typ:             abschluss
spec_id:         PLAT-007
titel:           "…"
geltungsbereich: plattform
stufe:           sprung
risikoklasse:    sicher
zyklus_phase:    9
status:          abgeschlossen
stand:           2026-05-24
---

## Ergebnis
[2–3 Sätze: erreicht / Ziel erfüllt?]

## Ziel-Abgleich
[Jedes Akzeptanzkriterium kurz: [x]/[ ] + Nachweis (Commit/Stichprobe).
Keine Wiederholung der Spec-Begründung.]

## Abweichungen / Erkenntnisse
[Nur falls vorhanden. Logbuch-Eintrag NUR, wenn eine bewusste Entscheidung
mit Warum fiel. Sonst entfällt das Logbuch beim Sprung.]
```

---

## 3. SCHRITT — kein Dokument, nur Log-Zeile

Kein Spec, kein Abschluss, keine Phase 9. Claude Code führt aus, committet, hängt **automatisch ohne Nachfrage** eine Zeile an `<Bereich>/Schritt-Log.md`:

```markdown
# Schritt-Log — <Bereich>

| Datum | Was | Commit |
|-------|-----|--------|
| 2026-05-24 | Whiptail-Pending-Kernel-Dialog im Apply-Pfad unterdrückt | a1b2c3d |
| 2026-05-24 | toten Ordner Intern/Systemzustand/Strategie/alt entfernt | e4f5g6h |
```

Das ist das einzige Artefakt eines Schritts. Auffindbar über Obsidian, ohne Arbeitsgedächtnis/Archiv zu belasten.
