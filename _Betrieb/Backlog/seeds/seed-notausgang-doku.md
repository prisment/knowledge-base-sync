---
typ: backlog_seed
titel: "Notausgang-Doku: schriftliche Rescue-System- und Telefon-Tailscale-Prozedur"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: prozess
zugkraft: bald
stufe: schritt
beruehrt: ["Plattform/Systemzustand/Sicherheit/notausgang.md (neu)", "physische Notiz an sicherem Ort (außerhalb Repo)"]
stand: 2026-05-26
erzeugt_durch: "Phase-7-Atomisierung HAERTUNGS_SPEC 7.10"
---

## Anlass

Phase 0 verlangt zwei unabhängige Notausgangs-Pfade: Hetzner Rescue
und Telefon-Tailscale. Beide wurden getestet. Die schriftliche
Schritt-für-Schritt-Doku, wie sie im Ernstfall (Notebook tot,
Tailscale-Key abgelaufen, Lockdown-Bug) genutzt werden, fehlt aber.
Im Ernstfall ist Google-Suche keine Option (Stress, fehlende
Tailscale-Verbindung).

## Ziel (Soll-Zustand, grob)

- Eine kurze, schritt-für-schritt-orientierte Notausgangs-Prozedur:
  1. Hetzner Rescue (URL, Login-Pfad, Aktivierung, SSH-Login,
     Rückboot)
  2. Telefon-Tailscale (App-Pfad, Re-Auth-Möglichkeit, SSH-Client
     auf Mobil, Tailscale-Server-Key-Reset wenn nötig)
- Ablage doppelt:
  - `Plattform/Systemzustand/Sicherheit/notausgang.md` (im Repo,
    versioniert)
  - Eine physische Kopie (Ausdruck oder Offline-Notiz) an einem
    sicheren Ort — KEIN Cloud-Service, der Anmeldung am Notebook
    voraussetzt.

## Stufe / Risiko

**Schritt** — pure Doku-Aktion. Null Risiko.

## Trigger

Bald — wäre eigentlich Phase-0-Pflicht gewesen, ist nachzuholen.
Spätestens vor Onboarding Kunde #2.
