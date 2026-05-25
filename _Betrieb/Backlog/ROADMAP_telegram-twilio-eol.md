---
typ: roadmap
titel: "Twilio + Telegram End-of-Life (Code + Doku + n8n + compose)"
stufe: spur
geltungsbereich: plattform
risikoklasse: sicherheitskritisch-akut
status: abgeschlossen
stand: 2026-05-25
abschluss: "Plattform/Archiv/PLAT-009/PLAT-009_Abschluss.md"
spec_id_geplant: PLAT-009
einschub_in: "ROADMAP_lebende-bereichs-doku.md (PLAT-008 pausiert)"
---

# Roadmap — Twilio + Telegram End-of-Life

**Ziel:** Twilio (WhatsApp-Webhook) und Telegram-Bot sind nach Abschluss
dieses Zyklus weder im laufenden System (Container, Workflows, Secrets,
Code) noch in der Doku noch beim externen Provider noch als aktiv geführt.
Beides ist tot — System und Dokumentation sagen dasselbe.

**Stand:** 2026-05-24 — Initiative angelegt als Einschub während PLAT-008.
Phase 1 (Auslöser/Stufen-Vorschlag/Plan für Faktensammlung) steht zur
Architekten-Freigabe.

## Stufeneinstufung — Spur

**Beratend vorgeschlagen, weil:**
- Code-Eingriffe in 5 langgraph-Codebases (content/redaktionsplan/analytics).
- `docker-compose.yml`-Änderung (systemkritisch laut globaler CLAUDE.md,
  Einzel-Freigabe pflicht).
- Aktiver n8n-Workflow stoppen.
- Container-Rebuild für betroffene langgraph-Container.
- Cross-Bereich (Plattform + Prisment).
- Risikoklasse `sicherheitskritisch-akut` (oder `kritisch`, je nach Befund
  Phase 2): Codepfade in Produktion, Webhook-Secrets, externer Provider-
  State.

Kein Sprung — zu viele bewegte Teile, mehrere Freigabe-Tore nötig.

## Schritte (Skizze, wird in der Spec geschärft)

1. ⬜ **Phase 2 — Faktensammlung.** Vollständige Inventur aller Referenzen:
   - Doku (welche Erwähnung lebende Aussage vs. historische Beschreibung)
   - n8n (welche Workflows aktiv, welche disabled, welche Webhook-URLs
     registriert)
   - Langgraph-Code (welche Funktion ruft Telegram, welche Aufrufpfade)
   - PWA-Code (sind ChatStream/TypingIndicator echte Integration oder nur
     visuell)
   - docker-compose.yml (Secrets + env vars)
   - Secret-Dateien im Filesystem
   - Externe Provider-Konsolen (Twilio + Telegram-Bot bei BotFather)
     → kann nur der Mensch prüfen
2. ⬜ **Phase 3 — Spec.** Reihenfolge: Workflow stoppen → Code anpassen →
   Container rebuild → compose säubern → Doku-Bereinigung → Provider
   abklemmen. Akzeptanzkriterien: kein laufender Workflow mehr,
   `grep -r "telegram\|twilio"` in Code-Repos liefert nur „intentionally
   removed"-Spuren, Doku konsistent.
3. ⬜ **Phase 5 — Machbarkeit.** Bündelung mit Rollback je Bündel.
4. ⬜ **Phase 6 — Ausführung in Bündeln**, jedes Bündel mit Einzel-Freigabe.
5. ⬜ **Phase 7+9 — Abschluss + Wissens-Einarbeitung.** Logbuch-Eintrag
   (Warum-Begründung für EOL), Archivierung, dann **Re-Sync PLAT-008**.

## Aktueller Schritt — Detail

**Phase 1:** Stufen-Einstufung vom Mensch zu bestätigen (Spur, Risikoklasse
sicherheitskritisch-akut), dann Phase-2-Plan freigeben, dann Faktensammlung.

## Vorsichtsregeln

- Kein Schreibzugriff auf `docker-compose.yml` oder Container-Rebuild ohne
  Einzel-Freigabe pro Aktion (globale CLAUDE.md).
- Aktive n8n-Workflows nicht stillschweigend deaktivieren — User benachrichtigen
  vor dem Stopp, falls noch eine produktive Funktion daran hängt.
- Externer Provider-State (Twilio-Konsole, Telegram-BotFather) wird nicht
  von Claude Code geprüft/geändert — nur Doku-Erwähnung des Soll-Zustands.

## Verbindung zu PLAT-008

PLAT-008 ist pausiert (Snapshot: `Plattform/Arbeitsgedaechtnis/PLAT-008_PAUSE.md`).
Nach PLAT-009-Abschluss:
- Re-Sync-Prüfung gegen aktuellen Stand
- PLAT-008-Bündel 1 lädt mit aktualisierter Aufräum-Liste (Twillo.md +
  n8n_docker_changes wahrscheinlich schon erledigt durch PLAT-009)
- PLAT-008 läuft normal weiter durch Bündel 2–6
