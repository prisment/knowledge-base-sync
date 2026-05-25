---
typ: backlog_seed
titel: "Foundation-Stack als zweites Git-Repo (Versionierung der Traefik/Foundation-Configs)"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: prozess
zugkraft: irgendwann
stufe: schritt
beruehrt: ["/opt/infrastructure/foundation/"]
stand: 2026-05-25
erzeugt_durch: ROADMAP_SMA-Zerlegung („Späterer Punkt — Repo Phase 3C: Foundation-Stack als zweites Repo")
---

## Anlass

`/opt/infrastructure/foundation/` (Traefik + Foundation-Services) ist nicht
versioniert. Read-only für claude-deploy, root-owned. Drei lose
Konsequenzen:

- Komplettheit: alle Stack-Configs versioniert, restic ist nicht mehr die
  einzige Sicherung
- Audit-Trail bei Traefik-Config-Änderungen (selten, aber kritisch)
- Disaster-Recovery wird einfacher: Foundation-Stack ist mit
  `git clone` + `docker compose up` nachstellbar

## Ziel (Soll-Zustand, grob)

- Neues Gitea-Repo `admin/prisment-foundation` (privat)
- `sudo git init` in `/opt/infrastructure/foundation/` (root, weil dir
  root-owned ist)
- `.gitignore` für `data/`, Logs, Zertifikate (Let's-Encrypt-Live wird
  täglich rotiert — gehört nicht ins Repo)
- Initial-Commit + Push an Gitea
- claude-deploy bleibt read-only — Pflege manuell durch Korbinian, kein
  Workflow-Bruch

## Stufe / Risiko

**Schritt.** Reine Repo-Initialisierung + .gitignore, eine Stunde
Aufwand, reversibel (`rm -rf .git`). Risikoklasse `sicher` (keine
Config-Änderung, nur Versionierung). Pflicht-Vorsicht bei .gitignore
wegen Live-Zertifikaten — ein einziges versehentlich committetes Cert
zerstört den Wert.

## Trigger

Irgendwann — wenn Foundation-Config häufiger geändert wird (heute fast
nie) oder ein Disaster-Recovery-Audit eine vollständige Versionierung
fordert.

## Hinweis

Das ist ein typischer **Stufe-Schritt-Kandidat** (trivial, reversibel,
isoliert) — kein Schirm-Seed nötig. Bleibt aber im Backlog als
auffindbare Erinnerung, weil die Wert-Begründung sonst verloren geht.
