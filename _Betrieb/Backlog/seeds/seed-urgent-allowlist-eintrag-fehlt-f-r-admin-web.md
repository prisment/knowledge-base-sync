---
typ: backlog_seed
titel: "Allowlist-Eintrag fehlt für admin-web"
geltungsbereich: plattform
risikoklasse: kritisch
status: offen
klasse: prozess
zugkraft: bald
stufe: spur
beruehrt: []
stand: 2026-05-27
erzeugt_durch: "auto-seed via raise-seed.py"
trigger: coverage-allowlist-fehlt-admin-web
gesehen_am: [2026-05-27]
---

## Anlass

Automatisch angelegt nach Befund in der Update-Routine
(C-Klasse). Architekt hat in `05_Update_Routine.md`
festgelegt: Klasse-C- und Major-Drift erzeugt automatisch einen Seed,
weil Reports/Mails passive Pull-Kanäle sind und im Backlog der aktive
Push-Pfad in die Planung läuft.

**Quelle:** check-service-coverage @ 2026-05-27T13:44:29+02:00

**Trigger-Key (Dedup-Anker):** `coverage-allowlist-fehlt-admin-web` — solange dieser Seed
`status: offen` trägt, bumpt jeder neue Befund nur `gesehen_am` und
`## Letzter Befund`, kein zweiter Seed wird angelegt.

## Letzter Befund
_(2026-05-27)_ Service admin-web ist Klasse A/B in Registry, aber /etc/claude/nightly-allowlist.conf hat keine build-Stelle. registry-apply.sh erneut laufen lassen + sudo-Install des Allowlist-Fragments.

## Vorschlag (vom Architekten zu schärfen)

- Risiko-/Aufwands-Einschätzung
- Bündel-Liste (Stufe + Risiko pro Bündel)
- Akzeptanzkriterien

## Verweise

- Update-Routine-Übersicht: `Plattform/Systemzustand/00_Uebersicht/05_Update_Routine.md`
- Mechanik: `_Betrieb/Skripte/backlog/raise-seed.py`
