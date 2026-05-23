---
typ: verfassung
titel: "Sicherheits-Prinzipien & nächtliche Autonomie"
stand: 2026-05-23
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

## Nächtliche Routine — Ablauf

Cron startet als `claude-deploy` zur definierten Zeit einen **headless, nicht-interaktiven** Lauf (eigene Instanz, NICHT die menschliche tmux-Session). Einstiegsprompt konstant: „Lies `_Betrieb/Verfassung/04_Sicherheits-Prinzipien.md` und `Plattform/Systemzustand/Sicherheit/nacht-aufgaben.md`. Arbeite die Aufgaben nach den dort definierten Regeln ab. Schreib den Morgen-Report."

**Ort der Steuer-Datei:** `Plattform/Systemzustand/Sicherheit/nacht-aufgaben.md` — dauerhafte operative Sicherheits-Konfiguration (gespiegelt, integritätsgeprüft, langlebig). Nur der jeweilige Morgen-Report ist flüchtig und liegt in `Plattform/Arbeitsgedaechtnis/`.

**Repo definiert AUSWAHL, nicht FÄHIGKEIT.** Die `nacht-aufgaben.md` enthält NIE frei interpretierbare Befehle. Erlaubte Aktionen sind als feste, versionierte, eng umrissene Skripte auf OS-Ebene verankert (Allowlist). Die Aufgabe wählt per `routine:`-Feld nur DARAUS aus und parametrisiert. Eine eingeschmuggelte Aufgabe ohne passende Allowlist-Routine läuft ins Leere.

**Abarbeitungs-Schleife pro Aufgabe:**
1. Integrität von `nacht-aufgaben.md` prüfen (Hash/signierter Commit gegen letzte Freigabe). Unerwartet verändert? → Abbruch + Alarm.
2. `routine` zeigt auf eine erlaubte Allowlist-Routine? Wenn nein → überspringen, im Report markieren.
3. Risikoklasse `kritisch`? → NICHT ausführen, nur reporten.
4. Erfolgskriterium + Rollback-Pfad vorhanden? Wenn nein → überspringen, markieren.
5. Snapshot/Sicherung anlegen.
6. Ausführen (nur die parametrisierte Allowlist-Routine).
7. Health-Check als Beweis.
8. Rot → Auto-Rollback → im Report „brauchte Rollback".
9. Grün → weiter.
10. Am Ende: Morgen-Report als Datei `Plattform/Arbeitsgedaechtnis/nacht-report-<datum>.md`.

## Cloudflare-Anomalie-Trigger (alarmieren, nicht eingreifen)

- Auslöser: Cloudflare-Webhook (von Claude Code via Cloudflare-API anzulegen; Plan: Pro) auf Security-/Anomalie-Events. Ergänzend kann der bestehende API-Token für aktives Prüfen genutzt werden.
- **Erlaubte Reaktion: NUR alarmieren.** Eine E-Mail an den Menschen, die den Vorfall beschreibt UND eine konkrete Maßnahme zur Bestätigung anbietet („Soll ich Dienst X / Y sofort stoppen? Antworte zur Freigabe."). Kein autonomes Eingreifen.
- **Verboten ohne Freigabe:** Dienste stoppen, Container isolieren, Topologie/Auth ändern. (Autonomes Eingreifen ist bewusst in ein separates späteres Projekt ausgelagert — eine automatische Stopp-Befugnis ist selbst ein Risiko: ein provozierter Fehlalarm würde sonst zum Denial-of-Service über die eigene Automatik.)

## Kontextbindung

Diese Abwägungen gelten für den aktuellen Kontext (Solo-Gründer, Zeit als Engpass, keine/wenige zahlende Produktivkunden). Sobald Prisment zahlende Kunden mit SLA hat, sind nächtliche Autonomie und Alarm-/Eingriffsschwellen NEU zu bewerten.
