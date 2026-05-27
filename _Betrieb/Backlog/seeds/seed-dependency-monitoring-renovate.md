---
typ: backlog_seed
titel: "Dependency-Monitoring (Dependabot/Renovate) für alle Gitea-Repos"
geltungsbereich: plattform
risikoklasse: kritisch
status: offen
klasse: security
zugkraft: bald
mission:         security-haertung-plattform
stufe: sprung
beruehrt: ["alle Gitea-Repos", "Renovate-Config in admin/prisment-platform"]
stand: 2026-05-26
erzeugt_durch: "Phase-7-Atomisierung HAERTUNGS_SPEC 7.1 + ROADMAP_cf_migration Phase-7-Backlog"
---

## Anlass

CVE-2025-55182 (npm-Paket-CVE in `pwa_web`) ist 5,5 Monate unentdeckt
geblieben, weil keine automatische CVE-Beobachtung lief. Heute existiert
das Risiko fort: keinerlei automatische Alarmierung für veraltete oder
verwundbare Dependencies in Gitea-Repos. Diun beobachtet nur Container-
Images, nicht App-Dependencies.

## Ziel (Soll-Zustand, grob)

- Renovate (oder Dependabot, falls in Gitea sauber integrierbar) als
  Bot in allen aktiven Gitea-Repos: `prisment-platform`,
  `knowledge-base`, optional LangGraph-Worktree-Repos.
- Default-Konfiguration: Security-Alerts sofort als PR; reguläre
  Patch/Minor-Updates wöchentlich gesammelt.
- Major-Versionssprünge nur als Hinweis-Issue, nie als Auto-PR
  (Breaking-Changes-Risiko).
- Ausgabekanal für Alerts: Mattermost gibt es nicht mehr; entweder
  Email an `korbinian.schnall@prisment.de` oder Notifier-Eintrag im
  bestehenden nightly-Report.

## Stufe / Risiko

**Sprung**. Bot-Setup + Config + Test-PR pro Repo. Tagesaufwand.
Reversibel (Bot deaktivieren). Risikoklasse `kritisch` weil ohne
diesen Punkt ist die Lehre aus dem Incident strukturell nicht
gezogen — neue CVEs würden wieder genauso lange unentdeckt bleiben.

## Trigger

Bald — vor Onboarding Kunde #2. Phase-5-Abschluss kann noch dazwischen.
