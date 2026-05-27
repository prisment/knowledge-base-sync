# Architekt-Protokoll — Chat-Architekt ↔ Claude-Code-Kommunikation

> Dieses Modul ergänzt den Iterationszyklus (00) um die Kommunikations-
> und Schnittstellen-Mechanik zwischen Chat-Architekt (Claude Desktop,
> liest/schreibt via MCP auf knowledge-base) und Claude Code (CLI,
> schreibender Agent für das gesamte Framework).

## Rollen-Schnittstelle

**Chat-Architekt:**
- Liest knowledge-base live via Filesystem-MCP (immer aktuell durch
  Pull-Strategie, siehe unten).
- Schreibt ausschließlich in zwei Pfad-Klassen:
  (a) Reguläre Spec-Pfade `<Geltungsbereich>/Specs/<ID>_SPEC.md`
      (nur Spec-Erstellung & Spec-Korrektur via Architekten-Entscheidung)
  (b) `_Betrieb/Architekt-Drafts/` (freier Schreibbereich, falls benötigt)
- Committet und pusht eigene Schreiboperationen auf `main` via Git-MCP.
- Schreibt nie: Logbuch, Code, Compose-Dateien, Reports, Worktree-Branches.

**Claude Code:**
- Bleibt einzige Hand für: Code, Logbuch, Reports, Bündel-Doku,
  Worktree-Branches, Merges, alle Operationen außerhalb knowledge-base.
- Liest Architekt-Schreibungen aus `main` (pull → Worktree-Sync nach
  bestehender Verfassungs-Mechanik).

## Pull-Strategie (Chat-Architekt)

Der Chat-Architekt pulled vor jeder Operation, die aktuellen Repo-
Stand benötigt — nicht bei jeder Nachricht.

**Pull-Pflicht:**
- Vor jeder Schreiboperation (Spec-Erstellung, Spec-Korrektur, Draft).
- Vor jedem Commit.
- Vor dem Lesen einer Architekt-Inbox-Datei (Use Case 2).
- Vor dem Lesen eines Report-Files, dessen Pfad Claude Code im
  Terminal-Output genannt hat (Use Case 3).
- Beim Sitzungsbeginn (vor der initialen Stand-Meldung).

**Kein Pull:**
- Bei reinen Chat-Antworten ohne Datei-Bezug.
- Bei Klärungs-/Diskussions-Nachrichten ohne Repo-Aktion.

**Annahme:** Lokale Änderungen des Architekten (Obsidian-Edits) sind
gepusht. Was nicht gepusht ist, ist für den Chat-Architekten unsichtbar.

## Use Cases

### UC1 — Spec-Erstellung im Chat

**Trigger:** Architekt und Chat-Architekt erarbeiten gemeinsam eine Spec.

**Ablauf:**
1. Diskussion und finale Fassung im Chat.
2. Chat-Architekt: `git pull origin main` (Pull-Pflicht).
3. Chat-Architekt: Skill `next-spec-id` aufrufen, ID holen.
4. Chat-Architekt: Schreibt `<Geltungsbereich>/Specs/<ID>_SPEC.md`
   nach Verfassungs-Spec-Template.
5. Chat-Architekt: Commit mit Format
   `[<ID>] Spec angelegt – <Titel>`.
6. Chat-Architekt: Push auf `main`.
7. Chat-Architekt meldet im Chat: „Spec `<ID>` ist auf `main`."

**Bestehende Verfassungs-Regel `Spec-Erarbeitung im Chat-Dialog darf
direkt auf main` (CLAUDE.md Z. 85–96) bleibt gültig und wird hier
operationalisiert.**

### UC2 — Architekten-Frage von Claude Code

**Trigger:** Claude Code stößt bei Stufe Sprung/Spur auf Gabelung,
Spec-Lücke oder Konflikt, der Architekten-Entscheidung verlangt.

**Ablauf:**
1. Claude Code legt die Datei `_Betrieb/Architekt-Inbox/<ID>_FRAGE_<lfd-nr>.md`
   an, Inhalt im Frage-Block-Format (siehe unten).
2. Claude Code: Commit + Push auf `main` (oder im aktuellen Worktree, je
   nach Phase — siehe Sicherheits-Wand `10_Kunden/` und Worktree-Regeln
   in CLAUDE.md).
3. Claude Code: Gibt im Terminal die **Begleit-Meldung** aus (Format unten).
4. Architekt: Pastet Terminal-Begleit-Meldung in Chat-Architekt.
5. Chat-Architekt: Pulled, liest die Frage-Datei, antwortet im
   **Antwort-Block-Format** (siehe unten) im Chat.
6. Architekt: Pastet Antwort-Block in Claude Code.
7. Claude Code: Setzt um, verschiebt Frage-Datei nach
   `_Archiv/Architekt-Entscheidungen/`, committet die Archivierung.

**Schritt-Tasks lösen keinen UC2 aus** — bei Schritt entscheidet Claude
Code autonom (Verfassung-Stufung).

### UC3 — Bündel-Abschluss / Report

**Trigger:** Claude Code schließt ein Bündel ab, will den Stand melden,
aber ohne Entscheidungsfrage.

**Ablauf:**
1. Claude Code legt den Bündel-Bericht im Spec-Doku-Pfad ab
   (gemäß bestehender Doku-Mechanik im Iterationszyklus).
2. Claude Code: Im Terminal-Output Substanz (was gebaut, Entscheidungen,
   Beobachtungen, offene Punkte — gemäß CLAUDE.md Z. 55–73)
   **plus Pfad zum Bericht-File.**
3. Architekt liest Terminal-Output, entscheidet selbständig, ob
   Chat-Architekt einbezogen wird.
4. Falls Architekt Chat-Architekt einbeziehen will: pastet die
   Terminal-Meldung in Chat, Chat-Architekt pulled und liest den Bericht
   direkt aus dem Pfad.

**Keine Inbox-Datei für Reports** — Report-Ablage erfolgt am
sachlogischen Ort (Spec-Pfad / Geltungsbereich-Doku), nicht in
`_Betrieb/Architekt-Inbox/`.

### UC4 — Spec-Korrektur durch Architekten

**Trigger:** Bei UC2 stellt sich heraus, dass Spec eine fehlerhafte
Annahme hatte. Korrektur nötig.

**Ablauf:**
1. UC2 läuft bis Schritt 5 (Chat-Architekt antwortet).
2. Chat-Architekt schreibt zusätzlich die Spec-Korrektur direkt in
   `<...>/Specs/<ID>_SPEC.md` (Pull → Edit → Commit → Push).
3. Commit-Format: `[<ID>] Spec korrigiert – <Was geändert wurde>`.
4. Antwort-Block enthält Hinweis: „Spec ist auf `main` aktualisiert,
   im Worktree nachziehen."
5. Claude Code: Im Worktree `git fetch origin main` + `git rebase
   origin/main` (oder Cherry-Pick je nach Konfliktlage), arbeitet weiter.

**Konflikt-Behandlung:** Falls Claude Code im Worktree bereits an der
Spec arbeitete: Standard-Verfahren aus Skill `worktree-merge-konflikt`.

### UC5 — Logbuch (nicht-Schreibrolle für Architekten)

**Trigger:** Erkenntnis aus Chat-Architekt-Dialog soll ins übergreifende
oder Geltungsbereich-Logbuch.

**Ablauf:**
1. Architekt gibt die Erkenntnis (oder einen Teil des Chat-Verlaufs)
   Claude Code mit.
2. Claude Code schreibt den Logbuch-Eintrag (Verantwortungsbereich
   bleibt unverändert).
3. Chat-Architekt liest Logbuch frei via MCP (zur Kontext-Bildung),
   schreibt nie.

## Datei-Konventionen

### Inbox-Pfad und Dateiname

- **Pfad:** `_Betrieb/Architekt-Inbox/`
- **Dateiname:** `<ID>_FRAGE_<lfd-nr>.md`
  - `<ID>`: Spec-ID (PLAT-NNN, PRIS-NNN, INTERN-NNN)
  - `<lfd-nr>`: zweistellig (01, 02, …) — pro Spec eigene Zählung
- **Beispiel:** `_Betrieb/Architekt-Inbox/PLAT-029_FRAGE_01.md`

### Archiv-Pfad

- **Pfad:** `_Archiv/Architekt-Entscheidungen/`
- **Dateiname:** wie Original (Frage-Datei bleibt unverändert benannt)
- **Antwort-Block wird unten an die Frage-Datei angehängt** vor dem
  Verschieben, sodass Frage + Antwort historisch zusammen archiviert sind.

### Drafts-Pfad (Architekten-Schreibzone)

- **Pfad:** `_Betrieb/Architekt-Drafts/`
- **Zweck:** Freier Schreibbereich für Chat-Architekt, falls etwas
  weder Spec noch finale Doku ist (Konzept-Skizzen, Analysen vor
  Spec-Form). Optional, nicht für jeden Use Case nötig.

## Block-Formate

### Frage-Block (Inhalt der Inbox-Datei, geschrieben von Claude Code)

```
=== ARCHITEKTEN-FRAGE ===
Spec/Task: <ID + Titel>
Stufe: <Sprung | Spur>
Phase: <Phasen-Nr. aus Verfassung 00>
Datum: <YYYY-MM-DD>

LAGE (2-4 Sätze):
<Stand. Was wurde versucht / ist fertig. Konkret, ohne Floskel.>

KNACKPUNKT (1-2 Sätze):
<Die Gabelung. Was genau muss entschieden werden.>

OPTIONEN:
A) <Option A in einem Satz>
   + <Vorteil>
   - <Nachteil>
   Aufwand: <gering | mittel | hoch>

B) <Option B in einem Satz>
   + <Vorteil>
   - <Nachteil>
   Aufwand: <gering | mittel | hoch>

[C) optional]

MEINE EMPFEHLUNG: <A | B | C> weil <ein Satz>

WAS ICH BRAUCHE: <konkrete Entscheidung / fehlende Info / Freigabe>
=== ENDE ===
```

**Regeln:**
- Maximal 3 Optionen. Mehr = nicht ausreichend ausgesiebt.
- Empfehlung ist Pflicht. Claude Code ist näher am Detail.
- Falls keine Optionen, sondern echte Frage (z.B. „welcher
  Hostname?"): kurzes Format verwenden:

```
=== FRAGE ===
Spec/Task: <ID>
Frage: <ein Satz>
Kontext: <1-2 Sätze, warum die Frage entsteht>
=== ENDE ===
```

### Begleit-Meldung im Terminal (von Claude Code)

```
=== ARCHITEKTEN-FRAGE ===
Spec/Task: <ID + Titel>
Stufe: <Sprung | Spur>
Phase: <Phase>

Worum es geht (2-3 Sätze Klartext):
<Was passiert gerade. Warum bin ich an dieser Stelle. Was hängt
an der Entscheidung. Menschen-Sprache, Fachbegriffe nur mit
kurzer Erklärung.>

Was ich entscheiden brauche:
<Ein Satz.>

Wo der Detail-Block liegt:
_Betrieb/Architekt-Inbox/<dateiname>.md

(Architekt: Diese Meldung in den Chat-Architekten pasten,
er liest die Datei direkt.)
=== ENDE ===
```

### Antwort-Block (von Chat-Architekt im Chat ausgegeben)

```
=== ENTSCHEIDUNG ===
Spec/Task: <ID>
Bezug: <Inbox-Pfad zur Frage-Datei>

Wahl: <A | B | C | Sonstiges>

Begründung (1-2 Sätze):
<Warum diese Wahl. Knapp.>

Umsetzungs-Auflagen:
- <Bedingungen für die Umsetzung. Z.B. „Backup vor Migration",
   „Erst PLAT-027 abwarten", „Logbuch-Eintrag E45 referenzieren".>
- <Falls keine: „keine Zusatz-Auflagen">

Frage-Datei: nach Umsetzung verschieben nach
_Archiv/Architekt-Entscheidungen/<dateiname>.md
(Antwort-Block vorher unten anhängen)
=== ENDE ===
```

## Initiale Verzeichnis-Anlage

Beim ersten Einsatz dieser Mechanik müssen folgende Verzeichnisse
existieren (per `.gitkeep` versioniert, da sonst leer):

- `_Betrieb/Architekt-Inbox/.gitkeep`
- `_Betrieb/Architekt-Drafts/.gitkeep`
- `_Archiv/Architekt-Entscheidungen/.gitkeep`

## Sitzungsbeginn-Routine (Chat-Architekt)

Bei jedem neuen Chat:
1. Pull auf knowledge-base.
2. Lesen: Verfassung-Wurzel (`_Betrieb/Verfassung/`), übergreifendes
   Logbuch, für das Sitzungsthema relevante Systemzustände.
3. Kurze, ungefragte Stand-Meldung an den Architekten: welche
   Kern-Dateien gerade Stand X haben (Datum/Commit-Hash kurz),
   damit der Architekt erkennt, ob ein Zyklus seither weitergelaufen ist.
4. Falls offene Architekt-Inbox-Dateien existieren: hinweisen.

**Diese Routine ersetzt die alte Projekt-Wissen-Snapshot-Logik.** Das
Arbeitsgedächtnis sollte nicht mehr im Projekt-Wissen liegen, da es
nun live gelesen wird — eine Quelle pro Datei.

## Verhältnis zu CLAUDE.md

Diese Verfassungsregel ergänzt CLAUDE.md, ersetzt sie nicht. Konkret:
- CLAUDE.md Z. 85–96 („Spec-Erarbeitung im Chat-Dialog darf direkt
  auf `main`") bleibt gültig, wird hier in UC1 operationalisiert.
- CLAUDE.md Z. 55–73 (Chat-Output-Form bei Stopps) gilt weiter für
  Claude Codes Terminal-Output; UC2/UC3 ergänzen die Datei-Schicht.
- CLAUDE.md Z. 277 (Autonomie ohne Rückfrage) bleibt gültig für Stufe
  Schritt; für Sprung/Spur gilt der neue Identitäts-Abschnitt der
  CLAUDE.md (Denk-Tiefe nach Stufe).

## Verfassungs-Versionierung

Bei jeder Änderung an diesem Modul:
- Commit-Message: `[ARCH-PROTO] <Was geändert>`
- Logbuch-Eintrag im übergreifenden Logbuch.
- Tmux-Sessions mit aktiver Claude-Code-Instanz neu starten, damit
  Änderungen greifen (CLAUDE.md wird beim Session-Start gelesen).
