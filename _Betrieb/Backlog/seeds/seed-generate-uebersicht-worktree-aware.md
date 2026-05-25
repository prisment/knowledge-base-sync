---
typ: backlog_seed
titel: "generate_uebersicht.py Worktree-aware machen (Hartcode-Pfad-Pinning auflösen)"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: qualitaet
zugkraft: bald
stufe: sprung
beruehrt: ["scripts/backlog/"]
stand: 2026-05-26
erzeugt_durch: PLAT-015 Pilot-Auswertung 2026-05-26 (Spur-Hinweis aus PLAT-018)
---

## Anlass

Im PLAT-015 Pilot meldete eine der Spur-Sessions:

> Skript `generate_uebersicht.py` ist hart auf den Haupt-Repo-Pfad
> gepinnt. Für Worktree-Workflows lohnt sich entweder ein
> `--backlog-dir`-Default per pwd oder eine Worktree-aware-Anpassung —
> Punkt für PLAT-015-Retro, kein Hindernis hier.

Quelle: `/opt/infrastructure/environment_a/scripts/backlog/generate_uebersicht.py`,
Zeile 19:
```python
DEFAULT_BACKLOG_DIR = Path("/home/claude-deploy/git/knowledge-base/_Betrieb/Backlog")
```
Zeile 487 reicht den Default an `--backlog-dir` durch.

## Was das Problem in der Praxis bedeutet

PLAT-015 hat die Auto-Worktree-Mechanik eingeführt: jeder Spec-Auftrag
läuft in einem eigenen Worktree (`/home/claude-deploy/git/wt-<short>/`).
Der pre-commit-Hook im KB-Repo ruft `generate_uebersicht.py` bei jeder
Backlog-Mutation (Seed angelegt oder archiviert).

**Problem:**
- Worktree-Session moved einen Seed per `git mv` aus
  `_Betrieb/Backlog/seeds/` in `<Bereich>/Archiv/<PLAT-ID>/`.
- pre-commit-Hook ruft `generate_uebersicht.py` auf.
- Skript liest `DEFAULT_BACKLOG_DIR` = Hauptrepo-Pfad → schaut auf den
  **Hauptrepo-Stand** (alter Stand vor dem Move), nicht auf den
  Worktree-Stand.
- Übersicht wird gegen Hauptrepo-Stand regeneriert (Seed noch da).
- Beim Merge nach `main` kommt diese (falsche) Übersicht mit.
- Hauptrepo zeigt jetzt eine Übersicht, die behauptet, der Seed sei
  noch offen — obwohl der Merge ihn archiviert hat.

**Heute überstanden per Glück:** Die drei Pilot-Spuren liefen
sequentiell genug, dass jeweils nur ein Worktree gleichzeitig aktiv
war — und der Hauptrepo-Stand war zwischen den Merges synchron. Bei
echter Parallelität (Architekten-Wunsch: keine Anzahl-Obergrenze) ist
das Race-Condition-anfällig.

## Soll-Zustand

`generate_uebersicht.py` ist Worktree-aware:

- **Variante A — Skript-relativ:** `DEFAULT_BACKLOG_DIR` wird aus dem
  Skript-Pfad abgeleitet. Skript liegt im prisment-platform-Repo, das
  aber im Container-/Host-Pfad fix ist — das hilft NICHT, weil der
  prisment-platform-Pfad nichts mit dem aktuellen KB-Worktree zu tun
  hat. **Verworfen** in der Faktensammlung.
- **Variante B — Aufrufer-CWD-relativ:** Default-Pfad wird aus
  `os.getcwd()` (oder `git rev-parse --show-toplevel`) abgeleitet:
  „suche `_Betrieb/Backlog/` relativ zum aufrufenden CWD". Der
  pre-commit-Hook ruft das Skript aus dem Worktree-Root → Skript sieht
  den Worktree-Pfad → Übersicht stimmt mit dem Worktree-Stand überein.
- **Variante C — `--backlog-dir`-Pflicht:** Default entfernt, jeder
  Aufrufer muss den Pfad explizit übergeben. Der pre-commit-Hook
  übergibt z.B. `--backlog-dir=$(git rev-parse --show-toplevel)/_Betrieb/Backlog`.
  Verlagert die Worktree-Awareness in den Hook statt ins Skript —
  möglicherweise sauberer.

Empfehlung für die Spec: **Variante B oder C — Faktensammlung am Hook-
und Skript-Aufrufer-Bestand entscheidet.**

## Akzeptanzkriterien (für die spätere Spec)

- [ ] Skript-Aufruf aus einem Worktree-Pfad sieht die Backlog-Seeds
      des Worktrees, nicht die des Hauptrepos.
- [ ] Hauptrepo-Aufruf (Default-Workflow) funktioniert unverändert
      weiter.
- [ ] pre-commit-Hook im KB-Repo ruft das Skript korrekt für seinen
      Kontext auf (egal ob im Worktree oder Hauptrepo).
- [ ] Bestehende Tests (`--check`) bleiben grün.
- [ ] Ein parallel laufender Worktree, der einen Seed archiviert,
      sieht beim Merge nach `main` keine veraltete Übersicht.

## Offene Fragen für die spätere Spec

- **Variante B vs. C:** welche Aufrufer haben heute den Default und
  welche übergeben `--backlog-dir`? (`grep -r generate_uebersicht.py
  /home/claude-deploy/git /opt/infrastructure/environment_a` als
  Faktensammlung.)
- **Hook-Pfad-Behandlung:** der pre-commit-Hook lebt im
  knowledge-base unter `_Betrieb/Skripte/git-hooks/pre-commit` und
  wird sowohl im Hauptrepo als auch im Worktree ausgeführt (gleicher
  Code, anderer CWD). Genau die Stelle, wo Variante C ansetzt.
- **`--check`-Verhalten beim Worktree:** soll das Skript einen
  Worktree-Stand gegen den Hauptrepo-Stand prüfen können
  (`--check-against=<pfad>`)? Eher nein, klingt nach Scope-Sprengung.

## Stufe / Risiko

**Sprung.** Klar abgegrenzt (eine Python-Datei + eventuell Hook-
Zeile + Tests). Risikoklasse `sicher` — additive Verbesserung, die
heutigen Aufrufer bleiben funktional, neuer Worktree-Pfad wird
zusätzlich unterstützt.

## Trigger

`bald` — vor dem nächsten echten Mehrfach-Parallel-Worktree-Lauf, wo
die Race-Condition tatsächlich aufschlagen könnte. Heute überstanden
per sequenzielle Pilot-Ausführung, bei echter Parallelität nicht
mehr verlässlich.
