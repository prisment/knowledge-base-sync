---
typ: backlog_seed
titel: "systemd-resolved Cache: Origin-IP statt CF-Edge bei Hairpin-Aufrufen vom Server selbst"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: wartung
zugkraft: irgendwann
stufe: schritt
beruehrt: ["/etc/systemd/resolved.conf", "evtl. Container-DNS-Konfig"]
stand: 2026-05-26
erzeugt_durch: "Phase-7-Atomisierung ROADMAP_cf_migration Phase-7-Backlog"
---

## Anlass

Nach CF-Migration zeigt `dig prisment.de` von extern Cloudflare-IPs
(korrekt). Vom Server selbst (Hairpin-Aufruf) liefert systemd-resolved
manchmal die Origin-IP aus dem lokalen Cache — was Tools verwirren
kann, die auf den CF-Pfad angewiesen sind (Health-Checks, interne
HTTP-Calls von Container zu Container über Hostname).

## Ziel (Soll-Zustand, grob)

- `/etc/systemd/resolved.conf`: TTL-Verhalten und Cache-Strategie
  prüfen — möglicherweise `Cache=no-negative` reicht.
- Alternative: Container, die Public-Hostnamen aufrufen, auf interne
  Docker-Network-Hostnamen umstellen, sodass DNS gar nicht ins Spiel
  kommt.
- Verifikation: `dig prisment.de` vom Host und aus einem Container
  zeigt CF-IPs, nicht 88.99.163.227.

## Stufe / Risiko

**Schritt** — kleine Config-Änderung mit Restart von
`systemd-resolved`. Reversibel. Risikoarm.

## Trigger

Irgendwann — kein akut sichtbares Problem, aber als Saubermachung
nach CF-Migration vermerkt. Konkrete Auslöser: Health-Check schlägt
fehl, oder ein Container-Hostname-Aufruf liefert anders als erwartet.
