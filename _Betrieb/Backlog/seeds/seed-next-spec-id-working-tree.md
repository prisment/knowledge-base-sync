---
typ: backlog_seed
titel: "next-spec-id: untracked Working-Tree-Specs einbeziehen, damit IDs nicht kollidieren"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: wartung
zugkraft: bald
stufe: schritt
beruehrt: ["/opt/infrastructure/environment_a/scripts/backlog/next-spec-id.py"]
stand: 2026-05-26
erzeugt_durch: "PLAT-030 Spec-Anlage — ID-Kollision mit untracked PLAT-029_SPEC_pg_hba_haerten.md aufgedeckt"
---

## Anlass

Beim Anlegen von PLAT-030 (Image-Pinning-Spec) lieferte `next-spec-id PLAT`
„029" — obwohl bereits eine `PLAT-029_SPEC_pg_hba_haerten.md` als
untracked Datei im Arbeitsgedächtnis lag (heute erstellt, noch nicht
committet). Folge: manuelle Umnummerierung von 029 → 030 nach `git mv`.

Das passiert systemisch immer dann, wenn zwei Specs am selben Tag in
schneller Folge angelegt werden (eine vor Commit, eine danach) — wird
mit zunehmender Spec-Frequenz häufiger.

## Soll-Zustand

`next-spec-id <BEREICH>` berücksichtigt zusätzlich zu Commits/Branches
auch **untracked oder modifizierte Spec-Dateien im Working-Tree** des
knowledge-base-Repos. Konkret: alle Dateien, die `git ls-files
--others --exclude-standard` und `git diff --name-only` matchen, mit
Glob `PLAT-*_SPEC*.md` / `PRIS-*_SPEC*.md` / `INT-*_SPEC*.md` in
den ID-Pool aufnehmen.

## Stufe / Risiko

**Schritt** — Skript-Edit, nur additive Quelle, kein Verhaltens-
Rückbau. Reversibel.

## Trigger

Bald — beim nächsten Mal, wenn ein Arbeits-Bündel ohnehin in dieses
Skript greift, oder als isolierter Schritt. Kein Block für laufende
Arbeit (Workaround: bei Konflikt manuell umnummerieren wie bei
PLAT-030).

## Verweis

- Skript: `/opt/infrastructure/environment_a/scripts/backlog/next-spec-id.py`
- Wrapper: `~/bin/next-spec-id`
- Skill, der das Verfahren trägt: `next-spec-id`
