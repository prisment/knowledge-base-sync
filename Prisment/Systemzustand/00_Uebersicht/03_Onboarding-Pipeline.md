---
typ: uebersicht
geltungsbereich: prisment
thema: onboarding-pipeline
stand: 2026-05-25
zweck: "Vom ersten Kontakt zum aktiven Mandanten in vier Phasen. Sub-Übersicht zu 00_Bereich.md."
---

# Onboarding-Pipeline — Prisment

> **Sub-Übersicht.** Detaillierter Einstieg siehe `00_Bereich.md`.

## In einem Absatz

Mandanten werden derzeit **manuell durch Korbinian** onboardet — PWA-
Self-Onboarding ist explizit Phase 3 der PWA-Roadmap (noch nicht aktiv).
Der Ablauf hat vier Phasen: **Phase 1 (Technisch)** legt das
Tenant-Profil in `customer_postgres` an (`tenants` mit `active=false` +
leere `voicedb_entries`-Strukturen) — Skript `~/tenant_onboarding.py`.
**Phase 2 (Material)** ist der eigentliche Inhalt-Aufbau: Korbinian
führt 1-2h Onboarding-Interview mit dem Mandanten (Audio-Aufnahme),
lädt die Audio-Datei via `curl POST` an den n8n-Webhook
`/webhook/onboarding-upload` hoch (der Workflow `T: Onboarding: Audio
Upload & Transkription` speichert in Gitea), dann läuft
`~/onboarding_import.py` mit Whisper-Transkription + Claude-Extraktion
der VoiceDB-Blöcke (stories · style-anchors · sprache-ton · mensch ·
personas) → UPSERT in `voicedb_entries`. **Phase 3 (Prüfen +
Aktivieren)** ist die Qualitätskontrolle: Korbinian liest die
extrahierten Blöcke und korrigiert wo nötig, dann wird die Konzept-
Präsentation generiert (n8n „T: Präsentation: Entwurf erstellen" →
Content-Agent `/demo/generate_post` für 3 Muster-Posts → HTML-
Präsentation auf `konzept.prisment.de`), und der Tenant wird aktiviert
(`UPDATE tenants SET active=true`). **Phase 4 (Abschluss)** schickt
die Präsentation an den Mandanten (n8n „T: Präsentation: Versenden",
Link + Passwort), Mandant gibt Feedback (Anpassungen direkt in DB oder
admin-UI), und sobald freigegeben: APScheduler in pwa-api triggert den
ersten Pipeline-Lauf (wöchentliches Interview, monatlicher
Redaktionsplan).

## Flow als Bild (lokal)

![Onboarding-Pipeline](03_Onboarding-Pipeline.svg)

> SVG nur lokal. Substanz steht oben im Absatz + folgender Tabelle.

## Phasen-Übersicht

| Phase                       | Was passiert                                          | Mensch                                                           | Code                                                                                                            |
| --------------------------- | ----------------------------------------------------- | ---------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| **1 — Technisch**           | Tenant-Zeile in DB + leere VoiceDB-Strukturen         | Korbinian: Tenant-ID + Display-Name + content_archetyp festlegen | `~/tenant_onboarding.py`                                                                                        |
| **2 — Material**            | Audio-Aufnahme → Transkription → VoiceDB-Anreicherung | Korbinian: 1-2h Interview mit Mandant, Audio-Upload via curl     | n8n „T: Onboarding: Audio Upload" → Whisper → `~/onboarding_import.py` → `voicedb_entries`                      |
| **3 — Prüfen + Aktivieren** | Qualitätskontrolle + Konzept-Präsentation             | Korbinian: VoiceDB-Blöcke prüfen + korrigieren                   | n8n „T: Präsentation: Entwurf erstellen" → Content-Agent `/demo/generate_post` → HTML auf `konzept.prisment.de` |
| **4 — Abschluss**           | Präsentation versenden + erster Pipeline-Lauf         | Korbinian: Link + Passwort an Mandant senden                     | n8n „T: Präsentation: Versenden" · `UPDATE tenants SET active=true` · APScheduler startet Cron-Trigger          |

## Beteiligte Skripte

Alle auf dem Host unter `/home/claude-deploy/`:

| Skript | Zweck |
|---|---|
| `tenant_onboarding.py` | Tenant-Profil in DB anlegen + initiale VoiceDB-Strukturen + temporärer admin-Token |
| `onboarding_import.py` | Audio aus Gitea → Whisper-Transkription → Claude-Extraktion → VoiceDB-UPSERT |
| `patch_onboarding_workflow.py` | n8n-Workflow-Patching für Onboarding-Audio-Upload (interne Wartung) |
| `migrate_gitea_to_postgres.py` | Migration alter Gitea-Tenant-Daten in `customer_postgres` (historisch, Schritt 23) |

## Beteiligte n8n-Workflows

| Workflow | n8n-Status | Funktion |
|---|---|---|
| `T: Onboarding: Audio Upload & Transkription` | ✅ aktiv | Webhook-Endpoint für Audio-Upload, speichert in Gitea |
| `T: Präsentation: Entwurf erstellen` | ⏯️ manuell | Content-Agent für Konzept-Präsentation triggern |
| `T: Präsentation: Versenden` | ⏯️ manuell | Präsentations-Link + Passwort an Mandant |

## VoiceDB-Blöcke (was extrahiert wird)

Aus dem transkribierten Audio extrahiert Claude folgende Blöcke (Tabellen
in `voicedb_entries`, Spalte `entry_type`):

- **mensch** — Inhaber-Profil (Name, Background, Persönlichkeit)
- **sprache-ton** — Wie der Inhaber redet (Tonalität, Eigenheiten)
- **style-anchors** — Charakteristische Formulierungsmuster, Phrasen
- **stories** — Erzählte Geschichten + Anekdoten (APPEND-only)
- **product-knowledge** — Fachwissen, Insider-Meinungen
- **no-goes** — Tabus, Ausschlüsse, was nicht gesagt werden darf
- **persona_1…N** — Zielgruppen-Personas (typische Kunden)

Details: [`../Produkt/onboarding_pflichtfelder.md`](../Produkt/onboarding_pflichtfelder.md).

## Aktuelle Mandanten-Phase

Kunde #1 (**Grubis-Weine**) ist durch alle 4 Phasen — Mandant ist
aktiv (active=true), VoiceDB ist befüllt. Aktuell aber Tester:
echte Posting-Veröffentlichungen über die PWA stehen noch aus, weil
pwa-web via HARDENING-Profile-Lock offline ist (siehe `04_PWA.md`
Wiederanlauf-Status).

Kunde #2 ist noch nicht onboardet — Voraussetzung: HARDENING Phase 5
(RLS in `customer_postgres`).

## Detail-Quellen

| Datei | Inhalt |
|---|---|
| [`../Produkt/onboarding.md`](../Produkt/onboarding.md) | Onboarding-Ablauf (vollständig, mit Befehlen) |
| [`../Produkt/onboarding_pflichtfelder.md`](../Produkt/onboarding_pflichtfelder.md) | Pflichtfelder-Inventar pro Tabelle |
| [`../Architektur/SMA_Operative_Doku.md`](../Architektur/SMA_Operative_Doku.md) | Dev-Workflow + Promote-Strategie (für Skript-Änderungen) |
| `~/tenant_onboarding.py` + `~/onboarding_import.py` | Quellcode der Skripte |
