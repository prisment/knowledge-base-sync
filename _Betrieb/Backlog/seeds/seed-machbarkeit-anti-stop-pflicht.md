---
typ: backlog_seed
titel: "Machbarkeits-Format schärfen: Anti-Stop-Pflicht für Mensch-Schritte (kein versteckter Stop mitten in der Sequenz)"
geltungsbereich: alle
risikoklasse: sicher
status: offen
klasse: prozess
zugkraft: bald
mission:         framework
stufe: schritt
beruehrt:
  - "_Betrieb/Verfassung/00_Iterationszyklus.md"
  - "_Betrieb/Templates/Dokument-Templates.md"
stand: 2026-05-27
erzeugt_durch: "PLAT-026 Spec-Erarbeitung 2026-05-27 — Architekt-Beobachtung + Claude-Selbst-Reflexion"
---

## Anlass — beobachtetes Anti-Pattern bei PLAT-026

Bei der Erst-Skizze der PLAT-026-Bündel-Liste hat Claude (Opus 4.7) **zwei
Mensch-Mitwirkungs-Bündel mitten in eine ansonsten autonome Sequenz gelegt**:

- ursprünglich Bündel 7: „Backup-Skript flock-Integration (Architekt-Mitwirkung,
  läuft als root)" — saß zwischen kritischen Apply-Bauten
- ursprünglich Bündel 11: „Allowlist-Erweiterung für `raise-seed.py` (sudo)" —
  saß weit hinten, nach dem Hauptbau

**Wirkung:** wenn Claude die Sequenz autonom abarbeitet, bleibt er bei jedem
solcher Bündel stehen und wartet auf den Architekten. Das ist ein Stop-Punkt
— aber **nicht als solcher markiert**. Er fühlt sich für den Plan-Leser
(Mensch und Agent) wie ein autonomes Bündel an.

Der Architekt hat das beim Review erkannt und korrigiert: Mensch-Schritte als
**Bündel 0a / 0b vor dem ersten autonomen Bündel**, sodass die nachfolgende
Sequenz wirklich am Stück autonom durchläuft. PLAT-026 ist mit dieser
Korrektur freigegeben worden.

## Claude-Selbst-Reflexion (Opus 4.7, 2026-05-27)

Auf die Architekten-Frage „hättest du das von selbst so gemacht?" — ehrliche
Antwort: **nein**. Ich hatte unbewusst mit Stop-Pausen gerechnet. Mein
inneres Modell beim Bauen einer kritisch-Bündel-Sequenz war:
„Mensch-Schritt, wenn er gebraucht wird, eingestreut. Nach jedem
kritisch-Bündel ist es vermutlich ohnehin sinnvoll, kurz abnicken zu
lassen."

Beides ist **nach Verfassung PLAT-025 falsch.** Die Verfassung sagt klar:
`kritisch` heißt Vorsichtsmaßnahmen (Backup-Verifikation, Rollback-Pfad
bereit, Health-Check), **nicht** Synchron-Pause. Echte Stop-Punkte sind
abschließend definiert (Stopp-Auslöser, Fall C, sicherheitskritisch-akut,
explizite Architekten-Direktive).

**Wahrscheinliche Ursache der Drift:** Anthropic-Trainings-Bias auf
„Sicherheitsmodus nach kritischen Aktionen", aus Kontexten wie Code-Review,
Production-Deployment-Gate, Datenbank-Migration mit Mensch-Freigabe. Diese
Defaults sind für PR-/Team-Workflows passend, für ein Solo-Architekt-Setup
mit autonomem Nacht-Apply aber Reibung.

**Was ich daraus mitnehme** (in mein eigenes Verhalten, ohne Verfassungs-
Änderung):
- Mensch-Mitwirkung gehört entweder vor das erste autonome Bündel (0a/0b)
  oder ans Ende (Abschluss-Review). Nicht dazwischen.
- `kritisch`-Bündel reihen sich autonom aneinander, solange die Vorsichts-
  Maßnahmen pro Bündel erfüllt sind.
- Pre-Check pro Machbarkeits-Skizze: „Wo in meiner Sequenz ist ein
  versteckter Stop?" Nicht erst nach Architekten-Review.

## Vorschlag — was sollte in Verfassung/Templates ergänzt werden

### A. Templates `_Betrieb/Templates/Dokument-Templates.md` — Machbarkeits-Vorlage

Im Machbarkeits-Body-Abschnitt einen **expliziten Pflicht-Block ergänzen**,
den jede Machbarkeit oben tragen muss:

```markdown
## Vorgehensprinzip (Pflicht-Block)

**Anti-Stop-Pflicht (Verfassung PLAT-025):**
- Alle Mensch-Mitwirkungs-Schritte stehen als Bündel 0a / 0b / 0c vor dem
  ersten autonomen Bündel. Sie sind NIE zwischen autonome Bündel eingestreut.
- Ausnahme: ein abschließendes Mensch-Review-Bündel am Ende ist erlaubt
  (Phase 8 Vorlage), aber explizit als solches markiert.
- `kritisch` ist KEIN Stop-Auslöser. Kritische Bündel bekommen Vorsichts-
  Maßnahmen (Backup vorher, Health-Check nachher, Rollback-Pfad bereit),
  laufen aber autonom hintereinander.

**Echte Stop-Auslöser (nur diese):**
- die Spec-spezifischen Stop-Auslöser (in der Spec aufgelistet)
- Fall C (Anomalie/Unsicherheit) gemäß Verfassung 00
- `sicherheitskritisch-akut`-Klassifikation
- explizite Architekten-Direktive während der Ausführung

**Mensch-Schritte-Tabelle (Pflicht — am Ende des Vorgehensprinzip-Blocks):**
| # | Mensch-Schritt | Warum nicht autonom möglich |
|---|---|---|
| 0a | … | … (z. B. „sudo-Recht auf /etc/, claude-deploy hat keinen Schreibzugriff") |

Wenn die Tabelle leer bleibt: die Sequenz läuft komplett autonom.
```

### B. Verfassung `_Betrieb/Verfassung/00_Iterationszyklus.md`

Im Abschnitt zur Phase 5 (Machbarkeit) einen Hinweis ergänzen:

> **Machbarkeit-Pflichten (zusätzlich zu Bündel-Liste):**
> - Vorgehensprinzip-Block oben (siehe Templates) — enthält explizit die
>   Anti-Stop-Pflicht und die Mensch-Schritte-Tabelle.
> - Wenn versteckte Stop-Punkte erkannt werden (Mensch-Bündel mitten in
>   autonomer Sequenz), Architekt weist die Machbarkeit zur Überarbeitung
>   zurück, ohne sie inhaltlich freizugeben.

### C. Optional — Pre-Commit-Hook auf Machbarkeits-Dateien

Pre-Commit-Hook könnte beim Commit einer `*_MACHBARKEIT.md` prüfen:
- enthält Frontmatter `report_art: machbarkeit`?
- enthält Body den String „## Vorgehensprinzip" (Pflicht-Block)?
- (heuristisch) enthält Body in der Bündel-Tabelle einen Eintrag mit „Mensch"
  oder „Architekt" als Akteur, der NICHT 0a/0b/0c oder das letzte Bündel ist?
  → Warnung, kein Block (heuristisch fehlbar, soll nur Augen schärfen).

Ich bin mir bei (C) nicht sicher, ob der heuristische Check sich lohnt —
falsch-positive würden nerven, und Architekten-Review fängt das ohnehin
zuverlässig ab. Pre-Commit-Hook ist Defense-in-Depth, hier vielleicht
overkill. Würde es ohne (C) probieren und nur ergänzen wenn der Anti-Pattern
trotz Templates und Verfassungs-Hinweis wieder auftaucht.

## Empfohlene Stufe

**Schritt.** Reine Verfassungs-/Template-Ergänzung, kein Code, kein Risiko.
- Bündel 1: Templates ergänzen (Vorgehensprinzip-Block-Vorlage).
- Bündel 2: Verfassung 00 ergänzen (Hinweis auf Machbarkeit-Pflichten).
- Bündel 3 (optional): Pre-Commit-Hook für Machbarkeits-Heuristik (siehe (C)).
- Bündel 4: Bestehende offene Machbarkeiten (heute nur PLAT-026) auf das
  neue Format heben — bei PLAT-026 nachtragen, dass der Vorgehensprinzip-Block
  schon da ist (nur Frontmatter-Marker setzen).

## Trigger

Nach PLAT-026-Abschluss. Dort ist die Beobachtung entstanden, und das neue
Format greift dann auch rückwirkend in PLAT-026 (Bündel 4).

## Verweise

- PLAT-026 Spec + Machbarkeit: `Plattform/Arbeitsgedaechtnis/PLAT-026_*`
- Verfassung 00, Abschnitt PLAT-025: kritisch ≠ stop
- Architekten-Gespräch 2026-05-27 (Chat-Session), in der die Beobachtung
  ausgelöst wurde durch die Direktive „prüfe mal in diesem realen Projekt
  ob du das so gemacht hättest oder ob du jetzt schon wieder damit gerechnet
  hast nach jedem der Punkte zu stoppen. Evtl. muss ich die Verfassung
  schärfen."
