---
typ: verfassung
titel: "Projekt-Ebene & Orchestrierung"
stand: 2026-05-30
aenderung: "nur nach oben, nur durch bewusste Freigabe"
---

# 07 — Projekt-Ebene & Orchestrierung

## Was ein Projekt ist (und was nicht)

Ein **Projekt** ist ein Behälter über mehreren Arbeitseinheiten mit **genau einem
fixen, maschinell prüfbaren Wohin**, dessen Wie-Zerlegung emergent ist — die
Folge-Arbeiten zeigen sich erst beim Anfassen. Ein Projekt wird autonom über
mehrere Spuren/Sprünge/Schritte hinweg abgewickelt durch den **Orchestrator**,
innerhalb eines vom Menschen ratifizierten **Abweichungs-Kontrakts**.

**Projekt ist KEINE vierte Stufe.** Die Stufen-Achse (Spur/Sprung/Schritt) misst
Zeremonie-Tiefe *einer* Einheit. Das Projekt ist eine **Ebene darüber**,
orthogonal. Arbeit *im* Projekt behält ihre Stufe.

**Abgrenzung zur abgeschafften Roadmap (E36):** Eine Roadmap bündelte *mehrere
unabhängige Wohins* zum Scheduling — verboten. Ein Projekt hat **genau ein
Akzeptanzkriterium**; alle seine Seeds dienen diesem einen Ziel. Das ist der
Unterscheider. Ein-Ziel-Behälter ist erlaubt, Mehr-Ziel-Schirm nicht.

**Hierarchie:** Mission (loser Filter-Slug, keine Prozess-Wirkung) > **Projekt**
(ein fixes Wohin, prozess-tragend) > Seeds > Specs.

## Der geschachtelte Zyklus

Der Iterationszyklus (00) wird nicht ersetzt — er wird rekursiv angewandt.

```
PROJEKT (ein fixes Wohin; Kontrakt vom Menschen ratifiziert)
│
├─ Akt 1  Projekt-Sondierung (CC) ──── Frame + Abweichungs-Kontrakt
├─ Akt 2  Kontrakt-Freigabe (MENSCH) ◄─ EINZIGER synchroner Frontstopp
└─ Akt 3  Orchestrator-Loop:
      │
      └─ pro Iteration ein innerer Drei-Akt-Zyklus je Seed:
            inner-Akt 1  Seed-Sondierung   (CC, kalt)
            inner-Akt 2  Freigabe          (ORCHESTRATOR statt Mensch — nur Wie, im Kontrakt)
            inner-Akt 3  Abarbeitung       (WORKER, eigener Worktree, bestehender Korridor)
      ▲
      └─ Schleife bis: Ziel erreicht → Report+Mail
                       Eskalation     → Stopp+Mail
                       Token-Limit    → Sleep+Resume (Zustand liegt im Journal)
```

Der Orchestrator besetzt die **Akt-2-Freigabe-Rolle der inneren Zyklen** — aber
nur für Wie, nur innerhalb des Kontrakts. Die menschliche Akt-2-Rolle wandert auf
die Projekt-Ebene: der Mensch ratifiziert den Kontrakt einmal. Wohin/Wie bleibt
exakt erhalten: Mensch besitzt den äußeren Akt 2 (Wohin als Kontrakt),
Orchestrator den inneren Akt 2 (Wie im Kontrakt). E3 intakt — alle Grenzen zieht
weiter der Mensch; der Orchestrator führt nur darin aus.

## Rollen-Identität wird pro Aufruf injiziert

Orchestrator und Worker sind beide `claude -p`-Kaltstarts und lesen `CLAUDE.md`
identisch. Die Rolle wird daher **nicht** aus `CLAUDE.md` erkannt, sondern vom
**Einstiegsprompt** gesetzt, den der Loop übergibt (gleiches Muster wie die
nächtliche Security-Routine in 04). Die Orchestrator-Rolle IST dieses Modul (07) +
der Kontrakt; der Worker IST das bestehende Arbeitstier (02) + die eine Spec.
`CLAUDE.md` trägt nur einen Zeiger + den Fail-safe „ohne expliziten
Orchestrator-Prompt bist du ein normaler Worker" (siehe CLAUDE-global.md-Ergänzung).

## Abweichungs-Kontrakt (Herz der Sicherheit)

Vom Menschen in Akt 2 ratifiziert. Drei Pflichtteile:

1. **Fixes Akzeptanzkriterium** — maschinell prüfbarer Endzustand. Daran misst der
   Orchestrator „Ziel erreicht".
2. **In-bounds (Wie — Orchestrator entscheidet autonom).**
3. **Out-of-bounds (Wohin + `akut` — harter Stopp/Eskalation).**

## Freigabe-Vollmacht des Orchestrators (Option B)

- **`sicher` und `kritisch` laufen autonom** unter Orchestrator-Freigabe.
  `kritisch` mit der bestehenden Pflicht-Vorsicht (Backup vorher, tiefe
  Verifikation, dokumentierter Restore-Pfad). Begründung: der ratifizierte
  Kontrakt IST der mensch-freigegebene Korridor, in dem die Regel aus 00/01
  („`kritisch` = autonom mit Vorsicht, kein Stopp aus Prinzip") gilt.
- **`sicherheitskritisch-akut`, out-of-bounds und echte Wohin-Gabelungen
  eskalieren immer** — unabhängig vom Urteil des Orchestrators.

## Emergente Seeds im Lauf

- Stößt ein Worker auf ein neues, separat zu lösendes technisches Problem, legt er
  am Ende seines Laufs einen Seed im Seed-Set des Projekts an (`projekt:`-getaggt),
  mit Risikoklasse-Vorschlag. Der nächste kalte Orchestrator findet ihn und
  priorisiert.
- **Sicherheit ohne Mensch-Ratifikation der Klasse:** Es gilt **nur-nach-oben** —
  ein emergenter Seed kann nicht heruntergestuft werden. `kritisch`/`akut`-Vorschlag
  → automatische Eskalation (der Orchestrator gibt `akut` ohnehin nicht frei).
  Die **feste Liste** (01) greift mechanisch (RLS/Auth/Kundendaten etc. werden per
  Code erkannt, nicht per Urteil). Nur `sicher`/`kritisch`-Seeds laufen autonom
  weiter; alles Gelistete self-eskaliert. **Restrisiko:** eine *ungelistete*
  gefährliche Kategorie — dieselbe Lücke wie heute, nur ohne Mensch im Lauf. Daher:
  feste Liste vor jedem Projekt-Lauf vollständig halten.

## Eskalations-Trigger (→ Mensch)

Der Orchestrator stoppt den Loop und meldet, wenn:
1. eine Entscheidung out-of-bounds liegt oder `akut` klassifiziert,
2. die richtige Wahl vom Wohin abhängt statt von Technik (echte Gabelung),
3. das Akzeptanzkriterium nur erreichbar wäre, indem das Wohin geändert wird,
4. ein Seed nach `max_versuche` (Kontrakt) nicht lösbar ist,
5. der Maschinen-Beweis eines Workers rot bleibt und kein in-bounds-Pfad greift.

## Erkennung: Ab wann ist es ein Projekt?

Beratend vorgeschlagen (CC/Chat-Architekt), vom Menschen ratifiziert — wie `stufe`.
Zwei Einstiege: (a) Seed wird als `typ: projekt` geboren; (b) **Eskalation
Spur→Projekt** im Lauf (analog Sprung→Spur, nur-nach-oben). Kette:
Schritt→Sprung→Spur→**Projekt**.

**Kriterien (UND-Gate, #1 ist hartes Tor):**
1. **Ein maschinell prüfbares Akzeptanzkriterium existiert?** Nein → kein Projekt
   (offene Exploration ist Wohin-Arbeit, läuft nie autonom).
2. **Jetzt vollständig in Bündel zerlegbar?** Ja → normale Spur.
3. **Absehbar mehrere Spuren?** Nein → Spur/Sprung.

**Lackmustest:** Sind die Unbekannten technisch (mehrere Wege nach Rom, egal
welcher) oder Wert-Gabelungen (welcher Weg IST die Frage)? Technisch →
projektfähig. Wert-Gabelungen → nicht.

## Projekt-Abschluss

Der Orchestrator **schließt nie selbst ab.** Der Loop produziert den
Abschluss-Report (Missions-Journal) und meldet per Mail. **Verankerung**
(Logbuch, Doku-Synchronität, Archiv, Seed-Status) ist Mensch-ratifiziert (E3) —
der Mensch startet eine frische Session und gibt frei oder lässt einen Fall
nach-sondieren.

## Kontextbindung

Gilt für den aktuellen Kontext (Solo-Gründer, Zeit als Engpass, Abo-Modell mit
Token-Pausen). Sobald zahlende Kunden mit SLA bestehen, sind Freigabe-Vollmacht
(B) und Eskalationsschwellen neu zu bewerten.
