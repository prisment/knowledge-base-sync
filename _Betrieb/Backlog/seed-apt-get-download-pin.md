---
typ: backlog_seed
titel: "apt-get download muss immer mit exakter Versions-Pinnung erfolgen"
geltungsbereich: plattform
risikoklasse: niedrig
status: offen
prioritaet: niedrig
stand: 2026-05-24
erzeugt_durch: PLAT-001 Bündel 3.2 (Tailscale-Rollback-Cache-Fehlbefüllung)
---

## Anlass

In PLAT-001 Bündel 3.2 (Tailscale-Update) wurde der Rollback-Cache vor dem
Apply mit `apt-get download tailscale` befüllt — ohne `=version`-Pinnung.
`apt-get download` zog dabei die **Repo-Latest** in den Cache, nicht die
installierte Version. Hätte das Apply einen Rollback erfordert, wäre der
„Rollback" effektiv ein zweites Apply auf dieselbe neue Version gewesen.

## Soll-Zustand

- Rollback-Cache-Befüllung immer mit exakter Versions-Pinnung:
  `apt-get download pkg=$(dpkg-query -W -f='${Version}' pkg)`
- Verifikation nach Cache-Befüllung: `.deb`-Version im Cache vs. installierte
  Version → muss identisch sein, sonst Cache verwerfen und neu befüllen.

## Aktuelle Verankerung

- Als Pflicht-Block in `Plattform/Systemzustand/Sicherheit/nacht-aufgaben.md`
  (Abschnitt „Rollback-Cache-Pfad") bereits aufgenommen.
- Pattern wird im 1b-Pfad scharf, sobald PreToolUse-Hook gebaut und 1b
  autonom geschaltet ist (PLAT-002).

## Trigger / Abhängigkeit

- Niedrig — Mechanik ist dokumentiert und in der Steuerdatei verankert.
  Dieser Seed dient als Erinnerung für den späteren PLAT-002-Spec-Bau:
  das Pinning ist NICHT optional, sondern Pflicht-Pattern des Rollback-Cache.
- Optional: ein kleiner Shell-Helper (`nightly-rollback-cache-add.sh`), der
  Pinning + Verifikation kapselt und Fehlbedienung mechanisch ausschließt.

## Offene Fragen für die spätere Spec

- Helper-Script oder reines Pattern-Pflege via Doku?
- Wo lebt der Helper (root-owned `/usr/local/sbin/`, analog zu
  `verify_post_reboot.sh`)? Oder im `_Betrieb/Skripte/`-Repo-Pfad,
  vom claude-deploy aufrufbar?
