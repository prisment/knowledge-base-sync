---
typ: backlog_seed
titel: "Dateisystem-Bereinigung nach Konsolidierung"
geltungsbereich: alle
risikoklasse: sicher
status: offen
prioritaet: niedrig (erst nach den abhängigen Zyklen)
klasse: wartung
zugkraft: irgendwann
stufe: sprung
beruehrt: ["./"]
stand: 2026-05-24
erzeugt_durch: BOOT-001
abhaengig_von:
  - "[[_Betrieb/Backlog/seed-code-repo-migration.md]]"
  - "[[_Betrieb/Backlog/seed-bereichs-uebersichten.md]]"
---

## Anlass

Nach BOOT-001 ist die neue Vierer-Struktur befüllt, aber an mehreren Stellen
liegt struktureller Ballast:

- `_AUSGESONDERT_CODE/` mit 73 Dateien (Inventar in `_AUSGESONDERT_CODE/Inventar.md`).
  Wandert per `seed-code-repo-migration.md` ins `prisment-platform`-Repo
  und sollte danach aus der KB verschwinden.
- Wenig befüllte Standard-Ordner der Vierer-Struktur (Logbücher und Archive
  je Bereich), die nach einigen Zyklen entweder organisch gefüllt sind oder
  zeigen, dass sie eher Theorie waren — dann kann man sie sauberer fassen.
- Eventuell `_bootstrap_input/` (Boot-Korridor) — sollte nach Phase 9
  abgearbeitet sein, aber die einmaligen Spec/Report-Dokumente leben dann im
  Archiv.

## Soll-Zustand

- `_AUSGESONDERT_CODE/` ist leer und entfernt (nachdem die Code-Repo-Migration
  durch ist).
- Standard-Ordner der Vierer-Struktur, die nach mehreren Zyklen nachweislich
  nichts aufgenommen haben, werden hinterfragt — entweder Verwendung erzwingen
  oder strukturell entfernen.
- `_bootstrap_input/` ist leer oder entfernt; alles in passenden Bereich verlegt.

## Trigger

Erst wenn:
1. `seed-code-repo-migration.md` abgeschlossen ist (Code im prisment-platform).
2. Die Bereichs-Übersichten aus `seed-bereichs-uebersichten.md` für mindestens
   einen Bereich existieren — dann sieht man im Alltag, welche Ordner Inhalt
   bekommen und welche nicht.

Erkennungsmuster für „toter Ordner": > 2 Monate nach Boot-001 keine Datei
außer `.gitkeep`, und keine konkrete Pipeline plant Belegung.

## Methode

- `find . -type d -empty` läuft regelmäßig, Trefferliste durchgehen.
- Pro toten Ordner: Verwendung erzwingen, neu schneiden, oder ersatzlos
  streichen. Verfassungs-Update wenn die Struktur sich ändert (Verfassung 02:
  „Agent ändert Leitplanken nie selbst" — Architekten-Freigabe).
- Inventar.md vom `_AUSGESONDERT_CODE/` mit dem `prisment-platform`-Commit
  abgleichen: zeigt 1:1-Übernahme oder dokumentiert bewusste Verluste.
