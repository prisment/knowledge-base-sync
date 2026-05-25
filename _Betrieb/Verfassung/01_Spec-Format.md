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

## Kritikalität pro Bündel (steuert Autonomie in Phase 6)

Damit Phase 6 autonom laufen kann (`00_Iterationszyklus.md`, Abschnitt „Autonome Ausführung im freigegebenen Korridor"), markiert die Arbeitsliste/Machbarkeit pro Bündel ein `kritisch`-Flag: Bündel ohne Flag laufen autonom, markierte Bündel sind synchrone Stopps.

**Feste Liste — immer kritisch** (Claude Code erkennt nur, ob berührt; schätzt hier nicht ein):

- **Sicherheit & Zugang:** Auth (Login/Session/Token/Passwort), Tenant-Isolation/RLS, Berechtigungen/Rollen, Secret-Handling (Keys/Env/Credentials), Cloudflare-/Edge-Sicherheitsschicht.
- **Daten-Integrität & -Verlust:** DB-Schema-Migrationen (besonders irreversible), alles was Kundendaten ändert/löscht, die zentrale Datenpunkt-Definition (sobald sie existiert).
- **Außenwirkung (irreversibel):** Produktions-Deploy, öffentlicher Content, gesendete Mails/Nachrichten, Geldfluss.
- **Abhängigkeiten/Lieferkette:** Dependency-Major-Updates, Framework-Versionssprünge, neue externe Dependencies.

**Graubereich — Claude-Code-Urteil** über zwei Testfragen:
1. Billig rückrollbar ohne bleibenden Schaden? **Nein → kritisch.**
2. Hängt die richtige Wahl vom Wohin ab statt von Technik? **Ja → kritisch.**

**Nur-nach-oben:** Claude Code und der Mensch dürfen nur **hoch**stufen. Ein Listen-Treffer kann nicht weggeurteilt werden. (Korrespondiert mit dem „Tut NICHT" des Arbeitstiers in `02_Rollen-Protokoll.md`: „stuft Kritikalität nie nach unten ab, um im Autopilot zu bleiben".)

**Spec-Vermerk:** pro Nicht-Listen-Bündel nur `kritisch: ja/nein` + Halbsatz Begründung — nicht die ausformulierten Testfragen (die sind Werkzeug, nicht Lesestoff).

**Die Liste lebt:** wächst per Phase-9-Pflicht-Tor (Mensch gibt frei, E3-konform), wenn ein Schaden eine fehlende Kategorie aufdeckt.

## Bündelung gehört NICHT in die Spec

Die Spec definiert das WAS (Ziel, Soll-Zustand, Akzeptanzkriterien). Die Bündelung/Arbeitsliste erarbeitet sich Claude Code selbst im Machbarkeits-Report — er kann Abhängigkeiten am echten System besser einschätzen. *(Beim Sprung: inline in der kombinierten Spec, knapp.)*

## Backlog-Felder (Seeds & Roadmaps)

Seeds und ROADMAPs in `_Betrieb/Backlog/` führen zusätzlich zu den allgemeinen Front-Matter-Feldern drei Pflichtfelder, über die das Übersichts-Skript priorisiert und das Backlog lesbar wird:

- **`klasse:`** — WAS für ein Bedarf. Fünf Werte:
  - `security` — Sicherheit.
  - `qualitaet` — Stabilität/Integrity. Voraussetzung, dass Features tragen.
  - `feature` — Kundenwert, neue Fähigkeit.
  - `prozess` — Framework-eigene Vorhaben (Verfassung, Backlog, Skills, Feature-Flags, Autonomie). Eigene Klasse, weil Bedarfs-Klassen das Fundament nicht abbilden.
  - `wartung` — Kosmetik, Aufräumen, Drift.

- **`zugkraft:`** — WIE bald nötig. Drei Werte:
  - `jetzt` — blockiert, akut, oder Kunde wartet.
  - `bald` — absehbar gebraucht, kein Blocker.
  - `irgendwann` — sinnvoll, kein Datum.

- **`beruehrt:`** — Liste der Pfade/Ordner, die der Seed bei Umsetzung anfasst. **Ordner-Ebene** (z. B. `langgraph/`, `pwa/`) — gröber, robuster, weniger Pflege als datei-genau. Bei unklarem Footprint leer/grob, wird beim Spec-Start präzisiert.

**Vergabe-Mechanik** (analog `stufe`): Chat-Architekt/Claude Code schlägt mit Begründung vor, Mensch revidiert/gibt frei. Niemals vom Menschen allein geraten.

**Querverweis:** Das `kritisch`-Flag pro Bündel (für autonome Korridor-Ausführung) folgt derselben Vergabe-Mechanik wie `klasse`/`zugkraft`. Definition siehe Abschnitt „Kritikalität pro Bündel" oben.

## Schritt-Log (Stufe Schritt)

Jeder Geltungsbereich hat eine Datei `<Bereich>/Schritt-Log.md`. Claude Code hängt **automatisch, ohne Nachfrage** pro ausgeführtem Schritt eine Zeile an:

```
| 2026-05-24 | <was, ein Satz> | <commit-hash> |
```

Das ist das Sicherheitsnetz gegen „verschwunden in der Git-Historie": ein Schritt ist über Obsidian auffindbar, ohne dass er ein eigenes Arbeitsdokument oder einen Archiv-Eintrag erzeugt. Kein Logbuch, kein Abschluss, keine Phase 9.
