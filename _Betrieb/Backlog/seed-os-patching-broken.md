---
typ: backlog_seed
titel: "OS-Patching greift nicht — Diagnose + Fix (nächster Zyklus nach BOOT-001)"
geltungsbereich: plattform
risikoklasse: sicherheitskritisch-akut
status: offen
prioritaet: HOCH (nächster Zyklus, vor der Website-Anpassung)
stand: 2026-05-24
erzeugt_durch: BOOT-001 Bündel E Mini-Test
---

## Anlass — falsche Annahme aufgedeckt

Während des Mini-Echttests des nightly-Tool-Sets in BOOT-001 Bündel E zeigte
`apt list --upgradable` **36 offene Updates**, darunter:

- `systemd` + zugehörige Bibliotheken 255.4-1ubuntu8.14 → 8.15 (10 Pakete)
- `docker-ce` / `docker-cli` / `containerd.io` (mehrere Sprünge)
- `apparmor` 4.0.1...5 → ...6
- `linux-firmware`, `netplan`, `iproute2`, `rsyslog`, `snapd`, `tailscale`
- `coreutils`, `thermald`, `ubuntu-pro-client`
- `open-vm-tools` 12.5.0 → 13.0.0 (Major, separat)

Davon SICHERHEITSRELEVANT: systemd, apparmor, linux-firmware, ubuntu-pro-client.

`journalctl -u unattended-upgrades --since="7 days ago"` lieferte **keine
Einträge** (Caveat: claude-deploy hat keine Gruppe `systemd-journal`, also nicht
abschließend belastbar — separater Fix BOOT-001 E).

Damit ist die bisherige Annahme „OS-Patching läuft systemweit über unattended-
upgrades, daran nichts antasten" (Klärungspunkt 3 BOOT-001) **falsch**. Der
Mechanismus, auf den wir uns verlassen haben, greift offenbar nicht.

Nach dem Next.js-CVE-Hack vom 2026-05-23 ist das die unmittelbar gefährlichste
offene Sicherheits-Lücke. Daher: **dieser Zyklus läuft als Nächster nach
BOOT-001-Abschluss, VOR der Website-Anpassung**.

## Konkrete Diagnose-Fragen (Spec-Material)

1. **Wann lief unattended-upgrades zuletzt erfolgreich?** Lesen unter root
   (`sudo journalctl -u unattended-upgrades` ohne Zeitfenster, dazu
   `/var/log/unattended-upgrades/*.log`).
2. **Welche Origins sind in `/etc/apt/apt.conf.d/50unattended-upgrades`
   erlaubt?** Standardmäßig nur `${distro_id}:${distro_codename}-security`.
   - Erwartet: deckt systemd / apparmor / linux-firmware (kommen aus
     `noble-security` und `noble-updates`).
   - Hypothese: `-updates` ist nicht aktiviert → erklärt warum
     systemd 8.14 → 8.15 (das ist `-updates`, kein direkter `-security`-Update)
     nicht greift.
   - Hypothese: Docker-CE ist Third-Party-Repo (`download.docker.com`) und
     muss separat freigegeben werden.
3. **Welche Pakete sind in `Unattended-Upgrade::Package-Blacklist`?**
4. **Läuft der Timer überhaupt?**
   `systemctl status apt-daily.timer apt-daily-upgrade.timer unattended-upgrades.service`
5. **Sind in `/etc/apt/apt.conf.d/20auto-upgrades` die richtigen Flags
   gesetzt?** (`Update-Package-Lists "1"; Unattended-Upgrade "1";`)
6. **Hängt etwas am Reboot-Erfordernis?** (`/var/run/reboot-required`) —
   Kernel-/glibc-Updates brauchen Reboot, der wird nicht automatisch gemacht
   (außer `Automatic-Reboot "true"` mit Uhrzeit ist gesetzt).

## Erwartetes Soll-Zustand (für die spätere Spec)

- unattended-upgrades läuft täglich, Logs zeigen erfolgreiche Updates.
- Origins decken `noble-security` UND `noble-updates` ab. Third-Party (Docker)
  erwogen (Risiko vs. Aktualität).
- Reboot-Required wird mindestens gemonitort und im Morgen-Report durch den
  nightly gemeldet.
- Die 36 offenen Pakete sind eingespielt.

## Verknüpfung zum nightly

Sobald dieser Zyklus durch ist, ist Aufgabe 3 in `nacht-aufgaben.md`
(OS-Update-Verifikation) ernstgemeint — vorher liefert sie systematisch
„nichts kam in 7 Tagen", was korrekt, aber durch das Sichtbarkeits-Loch
zweideutig wäre.

## Risiko-Hinweis

Risikoklasse **sicherheitskritisch-akut**: aktiv ausgenutzte Lücke ist
gegenwärtig nicht garantiert, aber durch fehlendes Patchen entstehen täglich
neue offene CVE-Fenster. Reboot nach systemd-Update bringt 1–2 min Downtime —
das ist die Kosten-Seite, die im Zyklus eingeplant werden muss.
