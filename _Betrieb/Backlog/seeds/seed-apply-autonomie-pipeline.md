---
typ: backlog_seed
titel: "Apply-Autonomie-Politik: Custom-Build-Patch + Renovate-Merge-Rebuild nutzen die PLAT-002-Wand"
geltungsbereich: plattform
risikoklasse: kritisch
status: offen
klasse: prozess
zugkraft: bald
stufe: spur
beruehrt: ["~/.claude-nightly/settings.json (über PLAT-002 nach /etc/claude/)", "_Betrieb/Skripte/renovate/", "_Betrieb/Skripte/nightly/check-base-image-drift.sh", "Gitea-Webhooks"]
stand: 2026-05-26
erzeugt_durch: "PLAT-002 Phase 3 — Apply-Autonomie-Politik bewusst aus PLAT-002 ausgelagert (Scope-Trennung Mechanik vs. Politik)"
abhaengig_von:
  - "[[Plattform/Arbeitsgedaechtnis/PLAT-002_SPEC]]"
---

## Anlass

PLAT-021 hat die Sichtbarkeits-Pipeline gebaut (Nightly meldet Drift,
Renovate öffnet PRs, AKTUALITAET.md zeigt alles). Der Apply-Pfad bleibt
aber heute betreut: Architekt klickt manuell oder beauftragt Claude
Code für `build_image.sh + promote_image.sh`, Renovate-PRs werden
händisch gemergt + Container muss neu gebaut werden.

PLAT-002 baut die **Wand** (PreToolUse-Hook als echte Bash-Allowlist),
die strukturelle Voraussetzung für autonomen Apply ist — definiert
auch die **Allowlist-Stellen** für Custom-Build-Patch + Renovate-
Merge-Rebuild, schaltet sie aber bewusst nicht scharf. Diese Spec
schließt die letzte Lücke: macht Punkt 3 (Custom-Build-Patch-Apply)
und Punkt 4 (Renovate-Merge → Container-Rebuild) autonom.

## Soll-Zustand (grob, wird in der Spec präzisiert)

- Custom-Build-Patch-Apply läuft nach Drift-Erkennung autonom (für
  unkritische Container) oder mit Notification (für sensible) —
  Klassen-Staffelung pro Service.
- Renovate-Merge-Rebuild ist automatisch — entweder Auto-Merge in
  Renovate selbst (PLAT-021 hatte `automerge: false`) ODER
  Auto-Rebuild nach menschlichem Merge.
- n8n-Patch bleibt betreut wegen Workflow-DB-Migrations-Risiko.

## Architekten-Vorgaben (eingebracht am 2026-05-27, Spec hat sich daran zu halten)

- **Rebuilds laufen nachts, nicht tagsüber.** „07:31" als Rebuild-Trigger
  ist explizit abgelehnt — kollidiert mit Geschäftszeit-Erreichbarkeit.
  Lösungsraum für Q2:
  - Renovate-Cron vorziehen auf z. B. **02:00** (vor allen Drift-Schreibern,
    damit Auto-Merge bis 02:30 durch und 04:15-Drift-Skripte den neuen Stand
    schon sehen). Webhook-Pfad entfällt dann.
  - ODER Webhook-Pfad bleibt, schreibt aber nur einen **Apply-Marker** in
    `~/.cache/pending-rebuilds/`, ein Nacht-Cron (z. B. **02:30**) arbeitet
    die Marker ab.
  - Geschäftszeit-Fenster `08:00–20:00` (oder konkreter, Architekt festlegen)
    ist Apply-tabu — auch für Klasse-B-Custom-Build-Patch.
- **Auto-Seed-Mechanik existiert bereits** (Schritt 2026-05-27,
  `_Betrieb/Skripte/backlog/raise-seed.py`). Drift-Crons für n8n + Renovate-
  Major-PRs lösen Seeds aus. **PLAT-026 schaltet zusätzlich:**
  - nightly-LLM darf `raise-seed.py` aufrufen (Allowlist-Erweiterung) — für
    OS-Klasse-1a-Befund + jeden Klasse-C-Befund den der LLM identifiziert.
  - Steuerdatei `nacht-aufgaben.md` Pflicht-Hinweis pro Aufgabe: bei
    Klasse-C-/Major-Befund **immer** zusätzlich `raise-seed.py` aufrufen,
    nicht nur reporten.
- **Reboot-Autonomie** ist NICHT Teil von PLAT-026 — eigener Folge-Seed
  (Quiet-Hours-Disziplin, Pre-Check-Erweiterung um „kein Apply-Bündel
  in den letzten 24h", Post-Reboot-Verify ist schon da).

## Offene Fragen (PLAT-026 Spec-Diskussion)

### Q1 — Renovate-Auto-Merge-Politik

Drei Optionen aus PLAT-002 Faktensammlung:

- **(a)** Renovate-Auto-Merge für Security + Patch + Minor.
  PLAT-021-Architekten-Entscheidung wird umgekehrt.
- **(b)** Renovate öffnet PRs wie heute, Mensch mergt, danach
  Auto-Rebuild ohne weiteres Eingreifen. PLAT-021-Stand bleibt.
- **(c)** Hybrid: Auto-Merge nur für Security + Patch, Minor + Major
  bleiben menschlich.

### Q2 — Rebuild-Trigger-Mechanik

- **Webhook-Pfad:** Gitea sendet bei PR-Merge an
  `/usr/local/sbin/renovate-merge-handler` (root-owned, im PLAT-002-
  Hook erlaubt). Sofortige Reaktion. Setup braucht Gitea-Admin-Klick.
- **Polling-Pfad:** neuer Cron pollt täglich Gitea-API nach gemergten
  Renovate-PRs seit letztem Lauf. Maximal 24h Latenz. Kein
  Gitea-Setup.

### Q3 — Custom-Build-Klassen-Staffelung

PLAT-021 Drift-Skript meldet Patch-Drift. Autonomer Apply pro Service
freischalten?

- **Klasse A — autonom (sicher, isoliert):** `cf-alarm` (HMAC-Webhook-
  Empfänger, klein), `ollama-proxy` (Squid-Wrapper, fast leer).
- **Klasse B — autonom mit Notification (mittel-sensibel):**
  `langgraph-{analytics,content,helpdesk,interview,redaktionsplan}`,
  `admin-web`, `pwa-api`, `pwa-web`. Patches sollten autonom rein,
  Architekt sieht's im Morgen-Report.
- **Klasse C — immer betreut:** `n8n` (Workflow-DB-Schema-Risiko
  zwischen Minor-Versionen, Custom-Nodes-Kompatibilität — eigener
  Migrations-Zyklus `seed-n8n-migration-2-21`).

### Q4 — Major-Bumps

PLAT-021 hatte verbindlich: niemals autonom. Bleibt so (Bestätigung).

### Q5 — Rollback-Pfad pro Service

Klasse-A/B-Auto-Apply braucht definierten Rollback bei Health-Check-rot:
- Build_image.sh setzt schon `:prev-<timestamp>`-Backup-Tag (existiert).
- Promote_image.sh + Recreate setzt aktuellen `:latest` neu.
- Auto-Rollback bei `docker ps`-unhealthy oder definiertem Service-
  Health-Endpoint-Failure: `docker tag prev → latest + force-recreate`.
- Definition pro Service: welcher Health-Endpoint, welche Toleranz-
  Frist.

## Voraussetzungen

- **PLAT-002 abgeschlossen.** Wand steht, Allowlist-Stellen sind
  technisch verfügbar.
- **n8n-Migration-Pfad geklärt** (`seed-n8n-migration-2-21`) — wenn
  Major-Sprung läuft, ist klar, dass Apply-Autonomie n8n NICHT
  einschließt.

## Akzeptanz-Pflicht: Syntax-/Lesbarkeits-Check der Allowlist vor Aktivierung

PLAT-002 hat den Hook fail-closed gemacht: wenn
`/etc/claude/nightly-allowlist.conf` nicht lesbar ist oder leer
ankommt, blockiert der Hook **alle** Bash-Calls (Logbuch E38
Nachzug). Das ist die richtige Failure-Richtung — aber heißt für
PLAT-026: ein fehlerhafter Edit an dieser Datei (Aktivierung einer
auskommentierten Allowlist-Stelle mit Tippfehler) kann den ganzen
Nachtlauf hart blockieren.

**Akzeptanzkriterium der PLAT-026-Spec (vorgemerkt):** Pro Aktivierung
einer Allowlist-Stelle Pflicht-Verifikation
- Allowlist-Datei syntaktisch lesbar (Skript prüft Zeilen-Format,
  keine unbalancierten Quotes etc.)
- Test-Trockenlauf via direktem Hook-Pfad
  (`echo '{"tool_name":"Bash","tool_input":{"command":"<aktivierte
  Form>"}}' | /usr/local/sbin/claude-allowlist-hook; echo "exit=$?"`)
  → ALLOW erwartet, exit 0.
- Erst nach grüner Verifikation Re-Install + Logbuch-Eintrag.

## Stufe / Risiko

**Spur** (kritisch, weil Auth-/Container-Topologie-Wirkung, plus
Auto-Apply-Politik mit Außenwirkung).

## Trigger

**Bald.** Sobald PLAT-002 die Wand hochgezogen hat und die ersten
PLAT-026-Allowlist-Erweiterungen testbar sind. Architekten-Frage „3 +
4 müssen auch automatisch laufen" (2026-05-26) ist der direkte
Auslöser.

## Verweise

- Vorgänger-Spur: `Plattform/Archiv/PLAT-021/PLAT-021_ABSCHLUSS.md`
- Spec-Vorgänger: `Plattform/Arbeitsgedaechtnis/PLAT-002_SPEC.md`
  (definiert die Allowlist-Stellen, die diese Spec scharf schaltet)
- n8n-Sonderfall: `_Betrieb/Backlog/seeds/seed-n8n-migration-2-21.md`
