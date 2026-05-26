---
name: backup-status-check
description: Status des nächtlichen restic-Backups prüfen (Snapshots listen, letzter Lauf, Größe, Repo-Integrität). Trigger wenn der Nutzer wissen will, ob/wann das Backup zuletzt gelaufen ist (Stichworte "backup status", "läuft das backup", "letzter snapshot", "restic snapshots", "backup heute", "backup nacht", "restic stats", "backup-log", "ist das backup intakt", "backup-größe"). NUR Status-Seite — für Restore das Runbook RESTORE.md, nicht dieser Skill.
---

# Skill: Backup-Status prüfen

Nächtliches restic-Backup läuft per Cron `0 3 * * *` über `backup.sh`,
Ziel Hetzner-S3, Log unter `/var/log/restic_backup.log`. Dieser Skill
zeigt nur den **Status**; für **Restore** gilt das Runbook
`Plattform/Systemzustand/Sicherheit/RESTORE.md`.

## Umgebung laden

Restic-Credentials + Repo-URL liegen in `/root/.restic_env` (S3-Keys,
Master-Password). Vom Host als root:

```bash
sudo bash -c 'source /root/.restic_env && restic snapshots'
```

Wenn `sudo` für restic ohne Argument-Pattern nicht freigegeben ist:
Architekt manuell aufrufen.

## Standard-Checks

```bash
# Letzte Snapshots (chronologisch absteigend)
sudo bash -c 'source /root/.restic_env && restic snapshots --last 10'

# Repo-Statistik (Größe, Dedup-Verhältnis)
sudo bash -c 'source /root/.restic_env && restic stats latest'

# Integrität: schnell (nur Metadaten)
sudo bash -c 'source /root/.restic_env && restic check'

# Lief der nächtliche Run heute?
sudo tail -50 /var/log/restic_backup.log
```

## Pattern: „lief das Backup heute durch?"

1. `sudo tail -50 /var/log/restic_backup.log` → letzter Lauf-Stempel.
2. `sudo bash -c 'source /root/.restic_env && restic snapshots --last 1'`
   → bestätigt, dass der Snapshot im Repo angekommen ist.
3. Stand ≤ 24 h alt = OK. Älter = Eskalation an Architekten.

## Wann dieser Skill greift

- Nutzer fragt „läuft das Backup".
- Routine-Check nach einem Systemausfall („haben wir noch einen
  intakten Snapshot von gestern?").
- Vor einer kritischen Aktion: „ist der jüngste Snapshot frisch genug
  als Sicherheitsnetz?"

## Nicht hierher

- **Restore** → `Plattform/Systemzustand/Sicherheit/RESTORE.md`
  (Disaster-Recovery-Runbook, Schritt-für-Schritt).
- **Backup-Architektur, Zero-Trust-Keys, WORM-Lock** →
  `Plattform/Systemzustand/Geteilte-Dienste/backup-restore.md`.
