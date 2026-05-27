---
typ: backlog_seed
titel: "Apply außerhalb des Nachtfensters versucht"
geltungsbereich: plattform
risikoklasse: kritisch
status: offen
klasse: prozess
zugkraft: bald
stufe: spur
beruehrt: []
stand: 2026-05-27
erzeugt_durch: "auto-seed via raise-seed.py"
trigger: apply-out-of-window
gesehen_am: [2026-05-27]
---

## Anlass

Automatisch angelegt nach Befund in der Update-Routine
(C-Klasse). Architekt hat in `05_Update_Routine.md`
festgelegt: Klasse-C- und Major-Drift erzeugt automatisch einen Seed,
weil Reports/Mails passive Pull-Kanäle sind und im Backlog der aktive
Push-Pfad in die Planung läuft.

**Quelle:** apply-pending-rebuilds @ Ubuntu-2404-noble-amd64-base 2026-05-27T13:41:17+02:00

**Trigger-Key (Dedup-Anker):** `apply-out-of-window` — solange dieser Seed
`status: offen` trägt, bumpt jeder neue Befund nur `gesehen_am` und
`## Letzter Befund`, kein zweiter Seed wird angelegt.

## Letzter Befund
_(2026-05-27)_ apply-base-image-drift wurde um 13:41 gestartet. Erlaubtes Fenster: 00:00–04:55.

## 