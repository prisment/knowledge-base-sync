---
typ: verfassung
titel: "Sicherheits-Prinzipien & nächtliche Autonomie"
stand: 2026-05-28
aenderung: "nur nach oben, nur durch bewusste Freigabe"
---

# 04 — Sicherheits-Prinzipien & nächtliche Autonomie

## Grundprinzip

„Bewiesen, nicht angenommen; Mensch gibt frei." Gilt für jede Aktion am System UND für die Veränderung des Systems selbst.

## Risikoklassen (drei Stufen)

| Klasse | Beispiele | Nächtliche Autonomie? |
|---|---|---|
| **sicher** (reversibel, isoliert) | OS-Security-Patches, Container-Image-Updates mit Health-Check + Auto-Rollback, CVE-Daten ziehen+prüfen, Zertifikats-Erneuerung | JA — ausführen erlaubt |
| **sicherheitskritisch-akut** (Gefahr im Verzug, Abwehr ≠ Umbau) | Aktiv ausgenutzte CVE, kompromittiertes Secret, offener Port der sofort zu muss | TEILS — Hotfix zur Gefahrenabwehr sofort einspielen (Notbremse); die vollständige architektonische Absicherung als Report zur Architekten-Klärung, NICHT autonom umbauen |
| **kritisch** (kann Dienst/Daten brechen, gestaltet Architektur) | DB-Migrationen, Netzwerk-Topologie, alles was Kundendaten berührt, Major-Upgrades, Auth-Schicht | NEIN — nur prüfen+reporten, wartet auf Mensch |

**Leitprinzip der mittleren Klasse:** Gefahr abwenden darf autonom (die Tür schließen). Architektur gestalten nie autonom (die Tür neu konstruieren).

## Pflicht-Mechanismen für jede nächtliche Aktion

- Erfolgskriterium UND Rollback-Pfad sind VORAB definiert.
- Health-Check beweist Erfolg (nicht die Abwesenheit einer Fehlermeldung). „OK" trotz nicht startendem Dienst = Fehlschlag.
- Bei rotem Health-Check: automatischer Rollback + alarmierender Morgen-Report.
- Morgen-Report unterscheidet: „alles glatt" / „brauchte Rollback / Aufmerksamkeit" / „Notbremse-Alarm liegt bereit".

## Harte Grenze im OS

Die kritische Grenze sitzt im Betriebssystem, nicht im Prompt: `claude-deploy` hat KEIN root, KEINEN Foundation-Layer-Zugriff. Diese Rechtegrenze umgeht kein Gedankengang. Sie schützt doppelt — gegen einen Fehler des Agenten UND gegen einen gekaperten Account.

## Nächtliche Routine — Ablauf (real, Stand PLAT-151)

Cron startet 04:00 als `claude-deploy` das **deterministische Bash-Skript** `_Betrieb/Skripte/nightly/run_nightly.sh` — **kein LLM-Aufruf, keine headless-Claude-Instanz** (PLAT-151 B2.1; der frühere headless-Claude-Ablauf mit 10-Schritt-Schleife + Morgen-Report ist abgelöst). Ablauf:

1. **Selbst-Heil-Preflight:** `core.hooksPath` gegen den Sollpfad prüfen; Drift → Queue-Meldung + Heilung via `install-git-hooks.sh` (PLAT-091 B0).
2. **Integritäts-Tor:** sha256-Prüfung der Steuerdatei `Plattform/Systemzustand/Sicherheit/nacht-aufgaben.md` gegen ihre `.sha256`-Datei. Mismatch → Abbruch + Alarm-Mail, kein Lauf.
3. **Deterministischer Digest** (`nightly_digest.sh`): liest die Anomalie-Datei von `collect-anomalie-input.sh` (Cron 03:55) und emittiert Betriebs-Queue-Einträge (`NIGHTLY_QUEUE_ONLY=1`). Kein Report-Artefakt.
4. **Infrastruktur-Tail** (unconditional, je non-fatal): Archiv-Tor-Sweep Layer 2, Trace-Retention, env_a-Worktree-Sweep, Host-Verdrahtungs-Drift-Check, Effizienz-Telemetrie, quartalsweise Tor-Gesundheits-Metrik, Residuen-Commit, Docker-Builder-Prune, CC-Update.

**Repo definiert AUSWAHL, nicht FÄHIGKEIT** bleibt das tragende Prinzip — heute in der Form: nachts läuft ausschließlich, was als festes, versioniertes Skript in der Crontab verankert ist; die integritätsgeprüfte `nacht-aufgaben.md` bleibt die dauerhafte operative Sicherheits-Konfiguration (gespiegelt, langlebig), und ihr Hash-Tor gated den Lauf. Weitere nächtliche Cron-Skripte (Renovate, apply-*, Watchdogs, Richtungs-Review) folgen derselben Regel: feste Skripte, keine frei interpretierbaren Befehle aus dem Repo.

Käme je wieder ein **ausführender** unbeaufsichtigter LLM-Lauf hinzu, gilt zusätzlich zum Tool-Freeze (Verfassung 05) die frühere Ausführungs-Schleife unverändert als Pflicht: Risikoklasse `kritisch` → nicht ausführen, Erfolgskriterium + Rollback-Pfad vorab, Snapshot, Health-Check als Beweis, Rot → Auto-Rollback (s. „Pflicht-Mechanismen" oben).

## Cloudflare-Anomalie-Trigger (alarmieren, nicht eingreifen)

- Auslöser: Cloudflare-Webhook (von Claude Code via Cloudflare-API anzulegen; Plan: Pro) auf Security-/Anomalie-Events. Ergänzend kann der bestehende API-Token für aktives Prüfen genutzt werden.
- **Erlaubte Reaktion: NUR alarmieren.** Eine E-Mail an den Menschen, die den Vorfall beschreibt UND eine konkrete Maßnahme zur Bestätigung anbietet („Soll ich Dienst X / Y sofort stoppen? Antworte zur Freigabe."). Kein autonomes Eingreifen.
- **Verboten ohne Freigabe:** Dienste stoppen, Container isolieren, Topologie/Auth ändern. (Autonomes Eingreifen ist bewusst in ein separates späteres Projekt ausgelagert — eine automatische Stopp-Befugnis ist selbst ein Risiko: ein provozierter Fehlalarm würde sonst zum Denial-of-Service über die eigene Automatik.)

## Echtdaten-Risikoklasse-Kopplung — Test-/Dev-/Staging-Bühnen (PLAT-046, 2026-05-28)

**Datencharakter der Umgebung ist Faktum, nicht Wahl.** Welcher Datencharakter eine Bühne trägt — synthetisch / pseudonymisiert / Echtdaten-Klon / Live-direkt — gehört in Akt 1 (Sondierung) erhoben und in den Ist-Zustand der Spec aufgenommen. Nicht in Akt 2 (Spec-Verfassen) als Schreibtisch-Wahl gesetzt.

**Echtdaten-Klon hebt die Risikoklasse.** Trägt eine Bühne Echtdaten-Klone — auch als Snapshot, auch nach Migration aus Live, auch hinter einem Auth-Tor — ist die Spec-Risikoklasse **mindestens `sicherheitskritisch-akut`**, bis Pseudonymisierung als Pflicht-Tor abgeschlossen ist. Kein Spielraum nach unten.

**Erreichbarkeits-Ausweitung ist verboten vor abgeschlossenem Pseudonymisierungs-Pass.** Auth-Tor lockern, neue öffentliche Endpoints, zusätzliche Hostnamen — solange un-pseudonymisierte Echtdaten in der Bühne liegen, bleibt jede solche Änderung gesperrt. Eine Pseudonymisierung selbst hebt die Sperre nur unter Restrisiko-Bewertung (siehe Skill, Pattern (g)) — Branchen-Vokabular und strukturell re-identifizierbare Restdaten können die Erreichbarkeits-Ausweitung weiter blockieren.

**Fail-closed-Marker im Snapshot.** Pseudonymisierte Snapshots tragen `-- PSEUDONYMIZED <YYYY-MM-DD>` als erste oder zweite Header-Zeile. Das **Hochfahr-Skript der Bühne** (z.B. `dev-start.sh`, `build_voicedb_snapshot.py`) prüft den Marker hart und **verweigert den Container-Start ohne Marker**. PreCheck-Wand früher als der Container-Lauf — nicht erst beim Selbsttest.

**Test-/Dev-Bühnen nie als öffentlicher Traefik-Router ohne Auth-Tor (PLAT-043).** Eine Test-Umgebung, die öffentlich geroutet wird, trägt immer ein vorgeschaltetes Auth-Tor (heute: dev hinter NextAuth); neue öffentliche Test-Router ohne Auth-Tor sind verboten.

**Werkzeug und Verfahren.** Skill `test-data-pseudonymize` (`.claude/skills/test-data-pseudonymize/SKILL.md`) trägt das How: vier-Schritte-Verfahren, sieben Pattern-Lehren aus PLAT-043 B-PSEUDO, Referenzen auf die Pattern-Skripte in `prisment-platform/scripts/fixtures/`. Mapping-Tabelle ist Architekten-Hand-Schlüssel und lebt außerhalb des Repos (auch außerhalb des knowledge-base-Vaults).

## Betriebs-Queue (Monitoring-Befunde sind keine Seeds)

Befunde aus Monitoring, Nightly-Läufen und CI (Image-Update verfügbar, Build rot, Patch ausstehend, Workflow-Fehler) werden in `_Betrieb/Betriebs-Queue/QUEUE.md` geschrieben — **nie als Seed-Dateien materialisiert**. Eintragsformat: Datum, Quelle, Objekt, Befund, Root-Cause-Gruppe, Status (`offen`/`erledigt`).

**Dedupe-Pflicht** über Fingerprint (Quelle + Objekt): wiederholte Befunde aktualisieren den bestehenden Eintrag (Zähler + letztes Datum), statt neue Einträge zu erzeugen. Default-Einstufung von Queue-Arbeit: `sicher`/Routine — die Allowlist-Routinen aus diesem Modul bleiben der Ausführungsweg.

**Eskalation Queue → Seed** nur durch Mensch oder CC-Vorschlag, wenn echte gestaltende Arbeit dahintersteht (dann ein Sammel-Seed pro Root-Cause, nicht ein Seed pro Symptom). Der Nightly-Lauf schreibt ausschließlich in die Queue + Morgen-Report — legt nie selbst Seeds an.

## Kontextbindung

Diese Abwägungen gelten für den aktuellen Kontext (Solo-Gründer, Zeit als Engpass, keine/wenige zahlende Produktivkunden). Sobald Prisment zahlende Kunden mit SLA hat, sind nächtliche Autonomie und Alarm-/Eingriffsschwellen NEU zu bewerten.
