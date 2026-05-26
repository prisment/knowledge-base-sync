---
name: worktree-merge-konflikt
description: Phase-9-Merge-Konflikt-Klassifikation (technisch vs. inhaltlich) und Auflösungs-Verfahren beim Rebase eines wt/-Branches nach main. Trigger wenn beim Phase-9-Merge ein Konflikt auftritt (Stichworte "merge konflikt", "rebase konflikt", "phase 9 merge", "wt branch konflikt", "phase9-merge-classify", "konflikt klassifizieren", "konflikt technisch oder inhaltlich", "auto-regenerate konflikt", "schritt-log konflikt"). Die Trigger-Regel (wann Phase-9-Merge läuft) bleibt in der globalen CLAUDE.md; dieser Skill trägt nur das How der Konflikt-Behandlung.
---

# Skill: Worktree Phase-9 Merge-Konflikt-Auflösung

Greift **innerhalb** eines Phase-9-Merges, wenn `git rebase origin/main`
im Worktree einen Konflikt meldet. (Der Auslöser „wann Phase-9-Merge
läuft" steht in der globalen CLAUDE.md / Verfassung 00.)

## Schritt 1 — Klassifizieren

```bash
phase9-merge-classify.sh
```

Helfer-Skript aus PLAT-015 Bündel 4. Liefert eine Einordnung pro
Konflikt-Datei:

- **`technisch`** — Whitespace, Auto-Regenerate-Ausgabe, E-Nummern-
  Kollision, getrennte Sections in derselben Datei, Schritt-Log-
  Append. Beide Seiten haben „die gleiche Wahrheit gesagt", nur
  formal kollidiert.
- **`inhaltlich`** — gleicher Code-Block anders geändert, oder
  Akzeptanzkriterium widerspricht main-Stand. Hier kollidieren
  Wahrheiten.

## Schritt 2 — Behandlung

### Technisch

Claude Code löst inline:
- Reihenfolge anpassen.
- Sections konkatenieren.
- Auto-Regenerate neu erzeugen (Schritt-Log, Backlog-Übersicht etc.).
- Commit.

Auflösung im Entscheidungs-Protokoll des Zyklus dokumentieren:

```
Konflikt: <Datei>
Klassifikation: technisch
Auflösung: <Halbsatz>
```

### Inhaltlich

**Stopp, Fall C.** Beide-Seiten-Diff aufbereiten und an den Architekten
zurück, mit:
- welcher Block kollidiert,
- was main sagt,
- was der wt-Branch sagt,
- welche Variante aus Spec-Sicht plausibler ist.

Nicht selbst entscheiden — Architekt wählt.

## Wann dieser Skill greift

- Phase-9-Merge läuft, `git rebase` meldet Konflikt.
- Sanity-Check vor dem Merge, ob Konflikte zu erwarten sind
  (`git fetch origin main && git diff main...HEAD`).
- Wiederkehrendes „wie war das mit technisch vs. inhaltlich nochmal"
  während der Auflösung.
