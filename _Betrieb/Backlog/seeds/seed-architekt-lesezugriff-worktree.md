---
typ: backlog_seed
titel: "Architekten-Lesezugriff auf Worktree-Branche (Sync-Werkzeug, nicht git-Topologie biegen)"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: prozess
zugkraft: bald
stufe: sprung
beruehrt: ["scripts/", "Architekten-Setup (Obsidian Git Plugin, Gitea-Web-UI)"]
stand: 2026-05-26
erzeugt_durch: PLAT-019 Phase 3 (Architekt konnte PLAT-019_SPEC im Worktree-Branch nicht sehen)
ehemaliger_titel: "Worktree-Branches: auto-ff-merge nach jedem Phase-Commit"
linie_pivotiert: 2026-05-26 (Sync-Werkzeug statt git-Topologie-Biegung — Begründung siehe „Verworfene Alternative" unten)
---

## Anlass

PLAT-015 hat die Worktree-Mechanik etabliert: jeder Spec-Auftrag mit
`spec_id` läuft in einem eigenen `wt/<short>`-Branch, der erst in
Phase 9 nach `main` gemergt wird. **Annahme war**, dass der Architekt
während der laufenden Spur auf dem `wt/*`-Branch reviewen kann.

**Realität (aufgedeckt 2026-05-26 in PLAT-019):** der Architekt synct
seinen Windows-Obsidian via **Obsidian Git Plugin** — und dieses Plugin
zieht nur den aktuellen Branch (Default `main`). Mittel-Zyklus-
Artefakte (Spec-Entwurf, Machbarkeit, Fakten) auf `wt/*`-Branches sind
für ihn in Obsidian **nicht sichtbar**. In PLAT-019 musste die Spec
manuell per ff-Merge nach `main` gezogen werden, damit der Architekt
sie liest. Das ist nicht skalierbar.

## Soll-Zustand — drei Lese-Pfade, kein Eingriff in git-Topologie

Der Architekt bekommt Lesezugriff auf `wt/*`-Branches über
**Standard-Werkzeuge**, ohne dass Claude Bündel-Stände auf `main`
publiziert. Welcher der drei Pfade primär gewählt wird, klärt die Spec
am echten Setup:

### Pfad (A) — Gitea-Web-UI ✅ DEFAULT (festgelegt 2026-05-26 nach Praxis-Test)

`git.prisment.de/admin/knowledge-base/branches` → Branch-Dropdown auf
`wt/<short>` → Markdown wird gerendert. Funktioniert sofort, kein
Tooling-Aufwand. **Praxis-Test 2026-05-26 bestätigt:** der Branch-
Dropdown auf `branches`-Page zeigt direkt „commits ahead/behind" pro
Branch, du erkennst sofort welche Spur Aktivität hat.

**Bookmark-Empfehlung:** `https://git.prisment.de/admin/knowledge-base/branches`
als Startpunkt — von dort ein Klick auf den Branch-Namen → Code-View
mit gerendertem Markdown.

**Direkt-URL-Schema** für oft-aufgerufene Files (in Bookmark mit
variabler `<branch>`-Komponente):
```
https://git.prisment.de/admin/knowledge-base/src/branch/wt/<short>/<pfad>
```

### Pfad (B) — Obsidian Git Plugin Branch-Switch ❌ NICHT PRAKTIKABEL

**Verworfen am 2026-05-26 nach Praxis-Test.** Das Obsidian Git Plugin
(Standard-Build) zeigt im „Switch branch"-Command **nur lokale
Branches**, nicht die per `auto-fetch` geholten Remote-Tracking-
Refs (`refs/remotes/origin/wt/*`). Damit wären alle `wt/<short>`-
Branches unsichtbar, solange sie nicht manuell als lokale Tracking-
Branche angelegt sind (`git branch wt/<short> origin/wt/<short>`).

Das ist Plugin-Designed-As-Backup-Sync, nicht Plugin-Bug — das Plugin
ist nie als Multi-Branch-Reader gebaut. Auch Plugin-Forks und
Sub-Plugins („Source Control", „Branch View") sind heute nicht
ausreichend zuverlässig im Standard-Setup.

**Wer trotzdem Obsidian-internal lesen will:** kann lokal mit CLI
(`git fetch --all && git branch wt/<short> origin/wt/<short>`) den
Tracking-Branch anlegen, danach ist „Switch branch" verfügbar.
Aber: pro neuer wt/-Spur einmal CLI = nicht praktikabel.

### Pfad (C) — `wt-show`-Helfer am Server

Bash-Skript am Server, das `git show wt/<branch>:<pfad>` als stdout
gibt — vom Architekten-Terminal aus aufgerufen. Beispiel:
```bash
wt-show plat-019              # → listet alle Dateien im wt/plat-019-Branch
wt-show plat-019 spec         # → zeigt direkt PLAT-019_SPEC.md
wt-show plat-019 fakten       # → zeigt PLAT-019_FAKTEN.md falls vorhanden
```
**Voraussetzung:** Architekt hat Server-Terminal-Zugriff (was er hat).
Nützlich, wenn Pfade nicht im Obsidian-Vault gemapped sind oder
schneller Konsolen-Lookup gewünscht ist. Sekundärer Pfad — Pfad (A)
deckt 95% ab.

## Empfehlung für die Spec

**Pfad (A) als DEFAULT (festgelegt nach Praxis-Test 2026-05-26).** Pfad
(C) nur, wenn der Architekt eh in einer Server-Session ist und schneller
Konsolen-Lookup gewünscht. Pfad (B) ausdrücklich **nicht empfohlen** —
Plugin-Setup-Aufwand pro Branch übersteigt den Nutzen.

**Damit kann der Seed-Inhalt am Spec-Start sofort in die Praxis
gebracht werden:** Bookmark setzen, fertig. Eine Spec ist
möglicherweise gar nicht mehr nötig — nur ein Schritt für
`wt-show`-Skript, falls jemand das später als Komfort haben will.

Begründung Default (A):
- Null-Setup: läuft sofort.
- Kein Plugin-Eingriff, kein Skript-Pflege.
- Markdown-Rendering kommt eingebaut.
- Architekten-Korridor-Disziplin bleibt strikt: Lesezugriff ist explizit
  ad-hoc, nicht push-getrieben.

## Verworfene Alternative — Auto-FF-Merge nach jedem Worktree-Commit

**Ursprünglicher Seed-Titel war:** „Worktree-Branches: auto-ff-merge
nach jedem Phase-Commit". Mechanik: nach jedem Commit auf `wt/*` läuft
ein Hook oder eine CLAUDE.md-Anweisung, die den Branch per ff-Merge
nach `main` zieht. Damit sähe der Architekt jeden Commit sofort auf
seinem `main`-Sync — kein Branch-Wissen nötig.

**Verworfen am 2026-05-26 nach Bewertung in der Spec-Diskussion,
vier Gründe:**

1. **Untergräbt den Entscheidungs-Protokoll-Anker (Verfassung 00).**
   Korridor-Geist: „Revision erfolgt geballt im Nachhinein (auf Branch
   billig), statt vorab häppchenweise freizugeben." Auto-FF-Merge nach
   jedem Commit kehrt das um — jeder Mid-Bündel-Commit landet sofort
   sichtbar auf `main`, was den Architekten verleitet, mid-Bündel zu
   intervenieren statt am Bündel-Ende geballt zu reviewen. Das ist
   genau die Korridor-Auflösung, gegen die das Modell gebaut wurde.

2. **Echte Ursache ist Sync-Werkzeug, nicht git-Topologie.** Obsidian
   Git Plugin pulled nur `main` — das ist Plugin-Setup, nicht
   git-Topologie-Defekt. Die git-Branches zu verbiegen, um eine
   Plugin-Begrenzung zu umgehen, ist Anti-Pattern. Sauberer ist es,
   das Plugin zu fixen (Pfad B) oder eine Standard-Web-UI zu nutzen
   (Pfad A).

3. **Race-Komplexität wird unterschätzt.** Bei N parallelen Worktree-
   Spuren mit Auto-FF-Merge nach jedem Commit hat man N×Race-Punkte
   statt 1 in Phase 9. Live-Lock-Risiko bei mehreren simultanen
   Sessions, die alle „kleine" Commits machen. Rebase+Retry mildert
   das nicht zuverlässig.

4. **PLAT-015 Phase-9-Lösung würde entwertet.** Inline-Konflikt-
   Klassifikator (`phase9-merge-classify.sh`) wurde gebaut für genau
   einen Konflikt-Punkt pro Spur (Phase 9). Bei Auto-FF-Merge
   verschieben sich die Konflikte auf jeden Commit → Klassifikator
   muss hochfrequent laufen statt punktuell. Mehr Auflauf, gleiche
   Klassifikation, höhere Disziplin-Pflicht für Claude.

## Korrektur faktischer Beleg-Anker (gegenüber alter Seed-Version)

Der ursprüngliche Seed nannte als Beleg-Anker eine
`PLAT-019_FAKTEN.md`, die per ff-Merge auf `main` gezogen worden sei.
**Korrektur:** Stufe Sprung hat keine separate Fakten-Datei — die
Fakten leben inline in der Spec (`PLAT-019_SPEC.md`). Der tatsächliche
manuelle ff-Merge betraf die Spec selbst, nicht eine Fakten-Datei. Der
Auslöser-Mechanismus bleibt derselbe (Architekt sah Worktree-Branch-
Artefakt nicht), nur der Datei-Name war ungenau.

## Akzeptanzkriterien

- [ ] Architekt erreicht `wt/<short>`-Branch-Inhalt lese-fähig **ohne
      manuellen ff-Merge** durch Claude.
- [ ] Markdown-Dateien werden gerendert dargestellt (nicht roh).
- [ ] Korridor-Disziplin bleibt strikt: kein Mid-Bündel-Push nach
      `main`, kein automatischer ff-Merge.
- [ ] Falls Pfad (B) gewählt: Plugin-Setting dokumentiert in Architekten-
      Setup-Notizen.
- [ ] Falls Pfad (C) gewählt: Skript ausführbar in Architekten-PATH
      verfügbar + Doku-Verweis (Architekten-CLI-Helfer-Verzeichnis).

## Offene Fragen für die spätere Spec

- **Pfad-Wahl (A/B/C) für den Default:** Architekt-Entscheidung am
  echten Web-UI-/Plugin-Setup. Vermutung: (A) reicht, weil
  Cloudflare-Tunnel-Latenz für Lesezugriff unkritisch.
- **Markdown-Rendering in Gitea:** sollten die `wt/*`-Branches in
  Gitea ohne Authentik-Tunnel-Friction erreichbar bleiben? Heute
  Cloudflare-Access — der erste Klick verlangt SSO-Login.
- **Branch-Sichtbarkeit-Cleanup:** geschlossene Spuren haben ihren
  `wt/*`-Branch im `wt rm`-Schritt gelöscht (PLAT-015). Gut so —
  alte Branches lärmen Gitea-Branch-Dropdown nicht zu.

## Stufe / Risiko

**Sprung.** Klar abgegrenzt — entweder Plugin-Setting + Doku (Pfad B),
oder Skript + Doku (Pfad C), oder Null-Setup-Bestätigung (Pfad A).
Risikoklasse `sicher` — kein produktiver Datenpfad berührt,
Lese-Werkzeug ohne Schreibwirkung. Bei Pfad-A-Default möglicherweise
auch **Schritt** (reine Doku-Anpassung „nutze Gitea-Web").

## Trigger

`bald` — vor dem nächsten echten Mehrfach-Parallel-Lauf, in dem der
Architekt Spec-Drafts in mehreren `wt/*`-Branches simultan sehen will.
Nicht akut blockierend, weil Gitea-Web bereits funktional erreichbar
ist (Pfad A heute schon nutzbar als Workaround).
