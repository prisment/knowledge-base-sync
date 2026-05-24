---
typ: backlog_seed
titel: "PreToolUse-Hook als echte Bash-Tool-Allowlist (E13 strukturell realisieren)"
geltungsbereich: plattform
risikoklasse: kritisch
status: offen
prioritaet: HOCH (Voraussetzung für nächtlich-autonomen -updates-Pfad aus PLAT-001)
stand: 2026-05-24
erzeugt_durch: PLAT-001 Q2
abhaengig_von:
  - "[[Plattform/Arbeitsgedaechtnis/PLAT-001_SPEC_v2.md]]"
---

## Anlass — falsche Annahme aufgedeckt (PLAT-001 Q2)

Beim Schärfen des nächtlichen Tool-Sets für den `-updates`-Auto-Pfad (PLAT-001
Block 1b) zeigte ein 4-Tests-Echtlauf: **`settings.json` ist im
`acceptEdits`-Modus für Bash strukturell eine Deny-Blacklist, keine Allowlist.**
Die `allow`-Liste hat für Bash-Kommandos keinen blockierenden Effekt — nur
`deny` greift. Eine breitere `deny`-Regel (`Bash(sudo:*)`) schlägt spezifische
`allow`-Einträge.

Damit ist E13 („Repo definiert AUSWAHL aus einer OS-Allowlist, nie FÄHIGKEIT")
auf der Tool-Set-Ebene bisher **nur aspirational, nicht realisiert**. Was als
Allowlist gedacht war, ist mechanisch eine Blacklist. Eine Blacklist gegen
Shell-`sudo` ist durch Form-Variation schlagbar (`sudo bash -c`, `sudo sh -c`,
`sudo find -exec`, `sudo install`, lokales `.deb` mit Maintainer-Skript, neuer
ungelisteter Aufruf) — exakt das E16-Antimuster, das für den nachts-
unbeaufsichtigten, injection-exponierten Lauf bewusst verworfen wurde.

Konsequenz in PLAT-001: der nächtlich-autonome `-updates`-Pfad (Block 1b) wurde
NICHT scharf geschaltet. Block 1b läuft vorerst betreut (wie 1a), `sudo apt-get
install` ist NICHT im nächtlichen Tool-Set. Die Mechanik ist gebaut und unter
Aufsicht bewiesen; nur die Autonomie-Schaltung wartet auf diesen Hook.

## Soll-Zustand (grob, wird in der Spec präzisiert)

- Ein `PreToolUse`-Hook (Claude-Code-Hook-API) bekommt den Bash-Command-String,
  matcht ihn gegen eine **echte Allowlist**, gibt bei Mismatch `exit 2` (Block).
  Damit ist „nur explizit Erlaubtes läuft" strukturell erzwungen, nicht durch
  Pattern-Akrobatik.
- **OS-Rechtegrenze auf den Hook angewandt (E12/E13, analog post-reboot-verify):**
  das Hook-Skript liegt root-owned (`/usr/local/sbin/`), außerhalb des
  GitHub-Sync, für claude-deploy NICHT beschreibbar. Sonst hebelt sich ein
  gekaperter claude-deploy-Account mit einem Hook-Edit selbst aus — der Hook
  wäre dann eine neue Fernsteuerungs-Stelle statt einer Grenze.
- Die Allowlist selbst (die erlaubten Kommando-Formen) ist ebenfalls
  integritätsgeschützt (signiert/root-owned), nicht frei aus dem Repo editierbar.
- Integration in `run_nightly.sh` / `settings.json` so, dass der Hook im
  unbeaufsichtigten Lauf zwingend aktiv ist (Tool-Freeze, E13).

## Trigger / Abhängigkeit

- Voraussetzung für die Scharfschaltung von PLAT-001 Block 1b (nächtlich-
  autonomes `-updates`-Patchen). Bis dahin: `-updates` betreut, `-security`
  weiter autonom über unattended.
- HOCH priorisiert, weil ohne diesen Hook der gebaute 1b-Pfad ungenutzt bleibt
  und `-updates`-Pflege an menschlicher Disziplin hängt (das Risiko aus E12).

## Offene Fragen für die spätere Spec

- Hook-API-Verhalten exakt verifizieren (exit-Codes, stdin-Format des
  Command-Strings, Verhalten bei verketteten Kommandos `a && b`, Pipes,
  Subshells) — bewiesen, nicht angenommen, vor dem Bau.
- Allowlist-Format: Liste exakter Kommando-Präfixe? Reguläre Ausdrücke (riskanter)?
  Wie werden Argumente begrenzt (z. B. `apt-get install` nur Repo-Pakete, kein
  lokaler `.deb`-Pfad)?
- Greift der Hook auch für verkettete/escapte Formen, oder muss `run_nightly.sh`
  zusätzlich die Shell-Verkettung einschränken?
- Verhältnis settings-`deny` ↔ Hook: bleibt `Bash(sudo:*)` in deny als zweite
  Linie, oder ersetzt der Hook sie? (Tendenz: deny als grobe erste Linie behalten,
  Hook als feine Allowlist darüber — Defense in Depth.)
- Caveat für künftige Pflege (Logbuch): nach diesem Bau ist die Hook-Allowlist
  die maßgebliche Tool-Grenze — jede Erweiterung sicherheitskritisch.

## Verhältnis zu bestehenden Logbuch-Einträgen

- Realisiert E13 strukturell (war bisher auf Tool-Set-Ebene nur Absicht).
- Ergänzt E16 (Write-Tool-Deny + acceptEdits-Caveat): E16 löste die
  Schreib-Grenze strukturell, ließ aber die Bash-Ausführungs-Grenze als
  Blacklist offen. Dieser Hook schließt genau diese Lücke.
- Sobald gebaut: Logbuch-Eintrag „Warum PreToolUse-Hook statt settings-Allowlist"
  (settings-Allowlist existiert für Bash mechanisch nicht — die 4 Tests aus
  PLAT-001 Q2 als Beweis-Anker).
