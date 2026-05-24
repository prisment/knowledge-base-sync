---
typ: verfassung
titel: "Spec-, Report- & Abschluss-Format"
stand: 2026-05-24
aenderung: "nur nach oben, nur durch bewusste Freigabe"
---

# 01 — Spec-Format (und Report / Abschluss-Doku)

Die Arbeitsdokumente des Zyklus folgen einem modularen Standard: ein gemeinsamer YAML-Front-Matter-Header, plus typspezifischer Körper. Die vollständigen Vorlagen liegen in `_Betrieb/Templates/Dokument-Templates.md` — diese Verfassungsdatei legt die verbindlichen Regeln fest, das Template liefert die ausfüllbare Form.

**Welche Dokumente entstehen, hängt von der Stufe ab** (siehe `00_Iterationszyklus.md`):
- **Spur:** Faktensammlung + volle Spec + Machbarkeit + volle Abschluss-Doku.
- **Sprung:** kombinierte Spec (Fakten/Soll inline) + kurze Abschluss-Notiz.
- **Schritt:** kein Dokument, nur eine Zeile in `<Bereich>/Schritt-Log.md`.

## Verbindliche Regeln

1. **Eine Wahrheit, Zusammenfassung ist nur Ansicht.** Die Zusammenfassung am Dokumentanfang verdichtet, was darunter steht — sie ergänzt NIE Inhalt, der unten fehlt.
2. **Bewiesen, nicht angenommen.** Jeder Ist-Fakt in einem Report nennt seine Quelle (Befehl/Datei/Pfad).
3. **Front-Matter-Keys sind verbindlich identisch** über alle Dokumente. Immer `risikoklasse`, nie mal `risiko` mal `risk`. Neue Felder werden im Template ergänzt, nie ad-hoc.
4. **Spec ist die Wahrheit; Report und Abschluss-Doku erben ihre `spec_id`.** Damit ist die Kette maschinell verkettbar.
5. **`stufe` ist Pflichtfeld** in jeder Spec und Abschluss-Notiz. Sie wird beratend vorgeschlagen (Chat-Architekt/Claude Code), vom Menschen revidierbar, nie vom Menschen allein geraten (siehe `00_Iterationszyklus.md`).

## Pflicht-Felder im Front Matter

Zusätzlich zu den bestehenden Feldern:

- **`stufe:`** — `spur` | `sprung` | `schritt`. Pflicht. Bestimmt Prozess-Tiefe.
- **`art:`** — OPTIONALES Etikett zur Kategorie: `feature` | `bugfix` | `recherche` | `prozess` | `wartung` (oder weitere nach Bedarf). **Rein zum Filtern/Wiederfinden — hat KEINEN Einfluss auf den Prozess.** Die Stufe steuert den Prozess, nicht die Art. Ein Bugfix kann Schritt oder Spur sein; die `art` sagt nichts über den Aufwand.

## ID-Schema

`<KÜRZEL>-<laufende Nummer>`, fortlaufend pro Geltungsbereich.
- `PLAT` — Plattform
- `PRIS` — Prisment
- `INT` — Intern

Beispiel: `PRIS-014`, `PLAT-003`. Schritte erhalten KEINE ID (sie leben nur in `Schritt-Log.md` + Commit).

## Akzeptanzkriterien skalieren mit Risikoklasse

Kriterien sind prüfbare Zustände, keine Tätigkeiten. WIE geprüft wird, ist Claude Codes operative Sache.
- **sicher:** knapp, z. B. „Dienst läuft (Health-Check grün)".
- **kritisch:** vollständige Liste, z. B. „DNS vollständig migriert / alter Anbieter nicht mehr autoritativ / Config gesichert / Routen X, Y, Z getestet".

## Bündelung gehört NICHT in die Spec

Die Spec definiert das WAS (Ziel, Soll-Zustand, Akzeptanzkriterien). Die Bündelung/Arbeitsliste erarbeitet sich Claude Code selbst im Machbarkeits-Report — er kann Abhängigkeiten am echten System besser einschätzen. *(Beim Sprung: inline in der kombinierten Spec, knapp.)*

## Schritt-Log (Stufe Schritt)

Jeder Geltungsbereich hat eine Datei `<Bereich>/Schritt-Log.md`. Claude Code hängt **automatisch, ohne Nachfrage** pro ausgeführtem Schritt eine Zeile an:

```
| 2026-05-24 | <was, ein Satz> | <commit-hash> |
```

Das ist das Sicherheitsnetz gegen „verschwunden in der Git-Historie": ein Schritt ist über Obsidian auffindbar, ohne dass er ein eigenes Arbeitsdokument oder einen Archiv-Eintrag erzeugt. Kein Logbuch, kein Abschluss, keine Phase 9.
