---
typ: uebersicht
titel: "Update-Routine — wer hält was wie aktuell"
geltungsbereich: plattform
stand: 2026-05-27
related:
  - "[[Plattform/Systemzustand/Sicherheit/nightly-runtime.md]]"
  - "[[Plattform/Systemzustand/Sicherheit/nacht-aufgaben.md]]"
  - "[[Plattform/Systemzustand/Sicherheit/pretooluse-hook.md]]"
  - "[[Plattform/Systemzustand/Sicherheit/renovate.md]]"
  - "[[Plattform/Systemzustand/00_Uebersicht/AKTUALITAET.md]]"
---

# Update-Routine

Eine Seite. Beantwortet drei Fragen:

1. **Was wird wie aktuell gehalten** (Container, App-Deps, OS, Zertifikate, Reboot)?
2. **Wer macht was und wann** (cron, nightly-LLM, Renovate, unattended-upgrades, Architekt)?
3. **Welche Updates laufen automatisch, welche manuell** — und wann ändert sich das?

**Leitlinie:** alles Patch/Minor läuft automatisch außerhalb Geschäftszeiten.
Major-Sprünge und strukturelle Eingriffe bleiben menschlich.

![Flow](05_Update_Routine.svg)

---

## 1. Achsen — was wird überwacht

| Achse | Beispiele | Erkenner | Quelle |
|---|---|---|---|
| **Container-Tags** (offizielle Images) | postgres, redis, traefik, n8n | nightly-LLM Aufgabe 1 | Docker-Hub-API |
| **Custom-Build-Basis-Images** | python:3.12.13-slim, node:20.x-alpine in unseren Dockerfiles | `check-base-image-drift.sh` (Cron 04:15) | Docker-Hub-API, Dockerfile-Scan |
| **App-Dependencies** | npm-Pakete (pwa-web, admin-web), pip (langgraph-*, pwa-api) | Renovate (Cron 07:30) + nightly-LLM Aufgabe 2 | Renovate-Bot, NVD, GitHub Advisories |
| **n8n-Upstream** (Workflow-Engine) | n8n 2.13.3 → 2.21.x | `check-upstream-n8n.sh` (Cron 04:20) | GitHub-Releases-API |
| **OS — Security** | beliebig in `noble-security` | `unattended-upgrades` (System-Cron) | Ubuntu-Repos |
| **OS — Updates** (Klasse 1a/1b) | Bibliotheken, Tools in `noble-updates` | nightly-LLM Aufgabe 3 | `apt list --upgradable` |
| **Zertifikate** | direkt-erreichbare HTTPS-Endpunkte | nightly-LLM Aufgabe 4 (+ Cloudflare-Mail für CF-fronted) | `openssl s_client` |
| **Reboot-Bedarf** | nach Kernel/libc-Update | nightly-LLM Aufgabe 4.5 via `nightly-system-status` | `/var/run/reboot-required` |
| **Anomalien** | 5xx-Spikes, Auth-Cluster, OOM | nightly-LLM Aufgabe 5 | `journalctl`, `docker logs`, cf-alarm |

**SSOT-Aggregat:** [`AKTUALITAET.md`](AKTUALITAET.md) zieht aus den Drift-State-Files (kein Doppel-Messen)
und committet täglich nur bei Substanz-Diff — keine Lärm-Commits.

---

## 2. Wer macht was — Akteure

| Akteur | Was | Wann | Wo dokumentiert |
|---|---|---|---|
| `unattended-upgrades` (System-Cron, root) | OS-`noble-security` autonom einspielen | täglich, system-getrieben | Ubuntu-Default, Konfig in `/etc/apt/apt.conf.d/` |
| `run_nightly.sh` (Cron 04:00, claude-deploy) | Headless Claude Code als nightly-LLM | 04:00 | `nightly-runtime.md` |
| Drift-Skripte (Cron 04:15/20/30/35) | Base-Image / n8n / Renovate-PR-Stand / AKTUALITAET aggregieren | 04:15–04:35 | `nightly-runtime.md` § „Aufgaben-Erweiterungen" |
| `nightly_liveness_watchdog.sh` (Cron 06:30) | Heartbeat prüfen; bei Stille → NIGHTLY-AUSFALL-Datei + Mail | 06:30 | `nightly-runtime.md` § „Liveness-Watchdog" |
| `run-renovate.sh` (Cron 07:30) | Renovate-CLI, scannt npm/pip/Dockerfiles, öffnet PRs in Gitea | 07:30 | `renovate.md` |
| `renovate_liveness_watchdog.sh` (Cron 08:30) | Heartbeat-Watchdog für Renovate | 08:30 | `renovate.md` |
| **PreToolUse-Hook** (root-owned) | Echte Bash-Allowlist beim nightly-LLM, fail-closed | bei jedem Bash-Call im Nachtlauf | `pretooluse-hook.md` |
| **Architekt** | Liest Morgen-Report + AKTUALITAET; merged Major-PRs; gibt Klasse-C-Bündel frei; entscheidet Wartungsfenster | morgens + adhoc | hier |

---

## 3. Wann läuft was — Tageszeitplan

```
00:15  unattended-upgrades       (OS-Security, system-getrieben)
04:00  run_nightly.sh            (Headless Claude Code)
04:15  check-base-image-drift    (Sektion 7 an Report anhängen)
04:20  check-upstream-n8n        (Sektion 8 an Report anhängen)
04:30  render-renovate-status    (Sektion 6 an Report anhängen)
04:35  aktualitaet-cron          (AKTUALITAET.md regenerieren, Substanz-Diff-Commit)
06:30  nightly-watchdog          (Stille-Prüfung)
07:30  run-renovate              (Renovate-CLI, PR-Erstellung, γ-Mail)
08:30  renovate-watchdog         (Stille-Prüfung)
```

Wartungs-/Geschäftszeiten-Logik: 04:00–08:30 ist Sicherheits-Wartungs-Fenster,
nichts davon trifft Nutzer.

---

## 4. Klassen-Staffelung — was läuft autonom, was nicht

| Klasse | Inhalt | Apply-Pfad heute | Zielzustand |
|---|---|---|---|
| **A — autonom** | OS-`noble-security`; künftig: Renovate Auto-Merge Patch+Minor; nightly OS-`noble-updates` 1b | `unattended-upgrades` läuft. Renovate Auto-Merge **aus**. nightly OS-1b **betreut**. | PLAT-026 schaltet Renovate Auto-Merge + nightly OS-1b scharf. |
| **B — autonom + Report** | Custom-Build-Patch (langgraph-*, admin-web, pwa-*, cf-alarm, ollama-proxy) | heute **betreut**: Architekt löst `build_image.sh + promote_image.sh` aus | PLAT-026 schaltet autonomes Build-+-Promote auf Patch-Drift. |
| **C — immer betreut** | n8n (DB-Migration), OS-Klasse 1a (systemd, libc, apt, dpkg, kernel, apparmor, dbus), Drittquellen (Docker, Tailscale), foundation-Stack, Auth-Layer (authentik) | manuell in Bündeln, Architekten-Freigabe pro Bündel | bleibt manuell (Risiko-Klasse, Migration). |
| **Major** | jeder X→X+1-Sprung quer durch alle Achsen | manuell, eigener Zyklus pro Sprung | bleibt manuell (Verfassung) — Renovate gruppiert sie als eigene PRs mit Label `major-bump`. |

**Heutige Lücke zwischen Ist und Ziel:** Patch-Drift in Custom-Builds + OS-`-updates`-Klasse-1b
läuft betreut. PLAT-002 hat die Sicherheits-Wand gebaut (PreToolUse-Hook als echte Bash-Allowlist),
**PLAT-026** schaltet die Allowlist-Stellen für Auto-Apply scharf — Spec dafür ist offen,
Voraussetzung erfüllt.

---

## 5. Sicherheits-Wand — warum Auto-Apply okay ist

Der nightly-LLM darf frei beurteilen (Versionen prüfen, Changelogs lesen, Logs triagieren), aber nur
eng erlaubte Operationen ausführen. Drei Schichten halten dicht:

1. **PreToolUse-Hook** `/usr/local/sbin/claude-allowlist-hook` (root-owned, claude-deploy nicht beschreibbar).
   Echte Bash-Allowlist gegen `/etc/claude/nightly-allowlist.conf`. Fail-closed bei Allowlist-weg,
   Parse-Unsicherheit (Subshell, Process-Substitution), Befehls-Verkettung (`;`, `&&`, `||`, `&` am Zeilenende).
2. **settings-deny** als zweite Linie für was der Hook defert.
3. **OS-Rechte** (sudoers NOPASSWD nur byte-genau, Write-Tool-Pfad-Grenze) als dritte Schicht.

Detail: [`pretooluse-hook.md`](../Sicherheit/pretooluse-hook.md).

---

## 6. Sichtbarkeit — drei Schichten gegen Stille

| Schicht | Mechanik | Wann sichtbar |
|---|---|---|
| **γ — Mail (msmtp)** | Direkt-Versand aus nightly + Renovate bei **neuen** Befunden | wenn Nachtlauf Fehler hat ODER Renovate neue PRs öffnet ODER neue CVEs in offenen PRs |
| **β — Report-Sektion** | nightly-Report + AKTUALITAET.md werden auch ohne neue Befunde geschrieben | wenn γ stillfällt, sieht man's hier — täglich |
| **α — Heartbeat-Watchdog** | Wenn nightly oder Renovate nicht gelaufen sind → eigene Warn-Datei `NIGHTLY-AUSFALL-<datum>.md` / `RENOVATE-AUSFALL-<datum>.md` | wenn Lauf gar nicht stattfand (doppelte Stille) |

**Keine Mail = okay, wenn nichts Neues passiert ist.** Das ist Designziel, nicht Bug:
Renovate-Stille = keine neuen CVEs; nightly-Stille = nichts Auffälliges. **Stille ist Information.**

---

## 7. Reboot — wie es heute funktioniert, wie es werden soll

**Heute:**
- `unattended-upgrades` setzt `/var/run/reboot-required` wenn Pakete einen Reboot wollen.
- nightly-LLM Aufgabe 4.5 ruft `/usr/local/sbin/nightly-system-status` (Helfer-Skript) und parst die
  Zeile `REBOOT_REQUIRED=yes|no`. Reportet im Morgen-Report.
- **Reboot selbst läuft manuell:** Architekt führt `sudo systemctl reboot` im selbstgewählten
  Wartungsfenster aus (NOPASSWD-Form gesetzt).
- **Post-Reboot:** `post-reboot-verify.service` (root-owned oneshot) feuert beim Boot, schreibt
  `nacht-report-<datum>-post-reboot.md`, schickt bei rotem Befund Alarm-Mail.

**Zielzustand (Backlog, nicht Bestandteil PLAT-026):**
- nightly soll Reboot selbst auslösen, wenn alle Pre-Check-Bedingungen grün sind und es außerhalb
  der gesetzten Geschäftszeiten ist.
- Pre-Check: kein offener eigener Rollback, kein laufendes Apply-Bündel, kein Reboot in den letzten N Tagen.
- Cool-Down + Quiet-Hours-Fenster konfigurierbar (z. B. 03:00–04:30, vor dem nightly-LLM-Lauf).
- Sicherheits-Wand: dedizierter Hook-Allowlist-Eintrag `sudo systemctl reboot` als eigene Stufe;
  würde nach PLAT-026 (Apply-Autonomie scharf) in einem eigenen kleinen Seed nachgeschoben.

**Begründung der heutigen Manualität:** der nightly läuft 04:00; Reboot direkt danach würde
laufende Schreiber (Drift-Crons 04:15–04:35) abschießen. Saubere Lösung braucht Quiet-Hours-
Disziplin — eigener Schritt nach PLAT-026.

---

## 8. Frage „warum keine Mail heute morgen" — der Pfad

Beispiel: 2026-05-27 lief der nightly sauber durch, AKTUALITAET wurde regeneriert (Substanz-Diff
gegenüber Vortag → Commit + Push), Renovate-Schedule blockt Patch/Minor außer montags → keine neuen
PRs → keine γ-Mail. Watchdogs still, weil Heartbeats frisch. **Das ist der designierte
Stille-Pfad.**

Der einzige reale Befund heute war:
- Pseudo-Reboot-Befund im Report (LLM-Halluzination unter Tool-Reibung) → fix per Helfer-Skript
  `nightly-system-status` + Steuerdatei 4.5 geschärft.
- AKTUALITAET-Cron mit `git pull --rebase` gescheitert → fix per `git fetch + merge --ff-only`.
- Aufgabe-5-Selbstzensur (`journalctl` nicht versucht) → Steuerdatei 5 schärft: Block einzelner
  Verkettungs-Calls sperrt nicht die Einzelbefehle.

---

## 8a. Auto-Seed bei Klasse C / Major (seit 2026-05-27)

Der Architekt-Chat ist ein passiver Pull-Kanal — Reports und Mails werden
nicht zuverlässig gelesen. **Aktive Push-Mechanik:** jeder Klasse-C-
oder Major-Befund legt einen Seed im Backlog an, der bei der nächsten
Planung sichtbar ist.

**Helfer-Skript:** `_Betrieb/Skripte/backlog/raise-seed.py`

**Dedup:** über das `trigger:`-Feld im Seed-Frontmatter. Solange ein
Seed mit identischem Trigger `status: offen` trägt, bumpt jeder neue
Befund nur `gesehen_am` und „Letzter Befund", **kein zweiter Seed**.

**Trigger-Keys aktuell:**

| Auslöser | Trigger-Key | Wer ruft auf |
|---|---|---|
| n8n Minor/Major-Drift | `n8n-minor-drift` / `n8n-major-drift` | `check-upstream-n8n.sh` (Cron 04:20) |
| Renovate Major-PR | `renovate-major-<repo>-<nr>` | `render-renovate-status.sh` (Cron 04:30) |
| OS-Klasse-1a-Update verfügbar | _(nach PLAT-026)_ | nightly-LLM via Allowlist-Eintrag |
| Sonstige Klasse-C/Major-Befunde | freier Key | manuell oder nightly-LLM |

Seeds heißen `seed-urgent-<slug>.md`, tragen `risikoklasse: kritisch`
und `zugkraft: bald` (bzw. `dringend` bei Major). Der Pre-Commit-Hook
(10_Kunden/-Wand, sha256-Konsistenz für Steuerdatei) greift wie für
alle Repo-Commits.

## 9. Was offen ist (Stand 2026-05-27 nach PLAT-026)

- **n8n-Migration 2.13.3 → 2.21.x** — eigener Seed `seed-n8n-migration-2-21`.
- **Reboot-Autonomie nach Quiet-Hours** — eigener Folge-Seed nach PLAT-026.
- **PWA/Agents-Session-Preservation** — Folge-Seed `seed-zero-downtime-pwa-agents`
  (Klasse C, nicht dringend, langgraph-Checkpointer macht State persistent).
- **promote_image.sh service-name-Bug** für n8n + ollama-proxy — apply-pending-rebuilds.sh
  umgeht ihn heute via manuellem `docker tag` + `compose up --force-recreate`.
  Folge-Schritt: promote_image.sh erweitern um compose_service als optionalen 2. Arg.
- **sign_nacht_aufgaben.sh worktree-relativ machen** — heute hardcoded auf
  `/home/claude-deploy/git/knowledge-base/`. Im wt-Worktree muss manuell signiert
  werden. Folge-Schritt.
