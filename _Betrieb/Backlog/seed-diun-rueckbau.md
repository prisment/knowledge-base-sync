---
typ: backlog_seed
titel: "Diun zurückbauen, sobald nightly Stabilität bewiesen hat"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
stand: 2026-05-24
erzeugt_durch: BOOT-001
---

## Anlass

Diun (Docker Image Update Notifier, läuft als Container in `environment_a` und
schickt Mails über die SMTP-Secrets) wurde als passive Image-Update-Notification
eingerichtet. Seit BOOT-001 Bündel E übernimmt der nächtliche Lauf
(`run_nightly.sh` + Steuerdatei + Tool-Set) diese Aufgabe deutlich reicher:

- Unterscheidet Patch / Minor / Major (Diun: nicht).
- Korreliert mit CVE-Quellen (Diun: nicht).
- Spielt Updates ein mit Rollback (Diun: rein Notification).
- Macht Funktions-Health-Check (Diun: nichts).

Diun ist damit semantisch redundant. Aber: solange der nightly noch nicht
bewiesen hat, dass er stabil läuft (keine Fehlalarme, keine vergessenen
Updates, kein Tool-Permission-Loch), bleibt Diun als zweite Linie.

## Soll-Zustand

- Nightly-Lauf läuft 2–3 Wochen sauber durch: Morgen-Reports liegen vor, keine
  Tool-Block-Fehler, keine offenen Rollbacks.
- Diun-Container wird aus `docker-compose.yml` entfernt.
- Zugehörige SMTP-Notif-Config in Diun bereinigt.
- Etwaige Diun-spezifische Doku in `Plattform/Systemzustand/Sicherheit/diun-setup.md`
  archiviert (`Plattform/Archiv/`) mit Verweis auf den nightly als Nachfolger.

## Auslöser für die Mini-Spec

Wenn der nightly nach ca. 3 Wochen ohne menschliches Eingreifen läuft, eröffnen
wir den Rückbau-Zyklus. Falls vorher Probleme auftreten: nightly debuggen,
Diun bleibt parallel.

## Offene Fragen

- Welche Diun-Notifications waren in den letzten Monaten nützlich, die der
  nightly NICHT abdeckt? (Wenn ja, in den nightly einbauen, bevor Diun raus.)
- Soll der Diun-Mail-Kanal als „audit notification" trotzdem bleiben (einmalige
  Image-Up-to-Date-Bestätigung), oder komplett raus?
