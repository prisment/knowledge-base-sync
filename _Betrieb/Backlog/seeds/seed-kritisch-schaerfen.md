---
typ: backlog_seed
titel: "Risikoklasse `kritisch` schärfen — Bestand prüfen + Verfassung 01 ergänzen"
geltungsbereich: alle
risikoklasse: sicher
status: offen
klasse: prozess
zugkraft: jetzt
stufe: sprung
beruehrt: ["_Betrieb/Backlog/seeds/", "_Betrieb/Verfassung/01_Spec-Format.md"]
stand: 2026-05-25
erzeugt_durch: PLAT-014-Spec-Diskussion 2026-05-25 (Architekt: „zu viel kritisch")
---

## Anlass

Bei der PLAT-014-Spec-Diskussion fiel auf: die heutige Backlog-Population
nutzt `risikoklasse: kritisch` inflationär. Architekt nennt konkrete
Fehl-Klassifizierungen:

- **Nicht wirklich kritisch** (heute aber als `kritisch` markiert):
  - DSGVO-Sammel-Seed
  - Data-Integrity-Agents-Seed
  - LLM-Kosten + PII-Anonymisierung
  - Meta-API-Integration
  - PreToolUse-Hook-Allowlist-Seed
  - Multi-Persona-Onboarding
  - Webshop-Anbindung Prestashop
- **Tatsächlich kritisch** (Beispiele, an denen das Gefühl stimmt):
  - OS-Patching greift nicht
  - Code-Security-Standards (CVE-Anlass)
  - PLAT-014 Worktrees (Branch-Topologie + Mandantendaten-Sichtbarkeit)

Folge der Inflation: das Kritikalitäts-Flag verliert Steuer-Wirkung. Im
Korridor-Modell (PLAT-013) bedeutet `kritisch` einen synchronen Stopp +
Mensch-Pflicht-Review. Wenn jedes zweite Bündel `kritisch` ist, gibt es
faktisch keinen Korridor. Im Worktrees-Modell (PLAT-014) bedeutet
`kritisch` zusätzlich „darf nicht parallel laufen" — bei inflationärer
Markierung blockiert das die Parallelität.

## Ziel (Soll-Zustand, grob)

Zwei Bausteine:

1. **Bestand sichten.** Alle Seeds im `_Betrieb/Backlog/seeds/` (+ aktive
   Specs in den `Arbeitsgedaechtnis/`-Ordnern) gegen die geschärfte
   Definition prüfen, neu eingruppieren. Erwartung: ein Großteil rutscht
   auf `sicher` zurück; nur eine kleine Klasse bleibt `kritisch`.
2. **Verfassung 01 (`_Betrieb/Verfassung/01_Spec-Format.md`) schärfen.**
   Die heutige Definition ist die Stufen-Listen-Mechanik („Listen-Treffer
   = kritisch") plus zwei Testfragen für den Graubereich. Was fehlt:
   - **Knappe Negativ-Beispiele** („was NICHT kritisch ist, auch wenn es
     so anfühlt"), gegen die das Bauchgefühl prüfen kann.
   - **Sparsamkeits-Klausel:** „Kritisch ist die Ausnahme, nicht die
     Norm. Wenn mehr als die Hälfte des Backlogs `kritisch` ist, stimmt
     die Definition nicht — entweder die Definition schärfen oder die
     Einträge revidieren."
   - Klarere Trennung **`kritisch`-Risikoklasse** (auf Spec-Ebene) vs.
     **`kritisch`-Flag pro Bündel** (in der Machbarkeits-/Arbeitsliste).
     Sind heute begrifflich überlappend, in der Praxis aber zwei
     verschiedene Steuerhebel.

## Geschärfte Definition (Diskussionsvorschlag für die Spec)

Eine Spec / ein Seed ist `kritisch` nur, wenn mindestens einer der drei
Aspekte gleichzeitig erfüllt ist:

- **Unwiederbringlichkeit:** ein Fehler kann nicht ohne Datenverlust,
  Vertrauensverlust oder Aussperrung zurückgerollt werden (z.B.
  SSH-Lockdown ohne Notausgang, irreversible DB-Migration auf
  Live-Kundendaten, öffentlich versendete falsche Inhalte).
- **Akut bewiesener Schmerz mit Außenwirkung:** es gibt einen realen
  Incident, nicht nur ein theoretisches Risiko (CVE-Vorfall =
  ja; „könnte mal CVE geben" = nein).
- **Branch-/Tree-/Auth-Topologie:** der Zyklus ändert die Mechanik, mit
  der zukünftige Zyklen überhaupt sicher laufen können (Worktrees,
  PreToolUse-Hook **wenn er scharf geschaltet wird**, Tenant-Isolation
  mit fail-closed).

Architektur-Größe allein ist NICHT kritisch. Komplexität ist NICHT
kritisch. „Berührt sensible Daten" ist NICHT kritisch, wenn die Berührung
sauber gekapselt ist. Externe Service-Anbindung ist NICHT kritisch, wenn
sie additiv ist und ein Rollback-Pfad existiert.

## Vorgehen beim Bestand-Sichten (für die spätere Spec)

- Pro Seed: aktueller Wert + neue Bewertung gegen geschärfte Definition +
  Halbsatz Begründung
- Architekt revidiert pro Seed (nicht maschinell, weil das die
  Inflation gerade verursacht hat)
- Phase-9-Eintrag in die Verfassung übernimmt die geschärfte Definition
  als Liste-Anker (analog zum Wachstums-Mechanismus der heutigen
  Kritikalitäts-Liste)

## Stufe / Risiko

**Sprung.** Klar abgegrenzt (Seed-Bestand + eine Verfassungs-Datei),
keine Architektur-Weiche. Risikoklasse `sicher` (reine Dokumentations-/
Kategorisierungs-Arbeit) — paradoxerweise ist „Schärfen, was kritisch
heißt" selbst nicht kritisch.

## Trigger

JETZT — direkt nach PLAT-014-Spec-Freigabe. Begründung: die
Worktrees-Mechanik verlässt sich auf „nie zwei kritische Spuren parallel"
als harte Regel. Wenn die heutige Inflation steht, blockiert die Regel
fast jede Parallelität. Vorgezogen vor reguläre Backlog-Reihenfolge.

## Bezug

- **PLAT-014 Spec** als Auslöser (harte Regel „nie zwei kritisch parallel").
- **PLAT-013** Korridor-Modell — heutige Listen-Mechanik in Verfassung 01.
- **Logbuch:** der Eintrag zur PLAT-014-Gate-Übersprung-Entscheidung
  vermerkt den Auslöser dieses Seeds als Folge-Korrektur.
