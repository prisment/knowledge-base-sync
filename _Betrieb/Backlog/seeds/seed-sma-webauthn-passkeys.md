---
typ: backlog_seed
titel: "WebAuthn / Passkeys als einheitlicher Login (Hybrid-Flow ablösen)"
geltungsbereich: prisment
risikoklasse: sicher
status: offen
klasse: feature
zugkraft: irgendwann
mission: ""
stufe: spur
beruehrt: ["pwa/pwa-web/", "pwa/pwa-api/"]
stand: 2026-05-25
erzeugt_durch: ROADMAP_SMA-Zerlegung (Schritt 38, Diskussion 2026-05-12)
---

## Anlass

Der heutige Hybrid-Login (Magic-Link für Android/Desktop, 6-stelliger Code
für iOS) wurde unter Zeitdruck zum Grubi-Termin 2026-05-12 eingeführt und
ist eine pragmatische Lösung für das iOS-Cookie-Store-Problem (Safari ↔
PWA-Container haben getrennte Cookie-Stores). UX wirkt zweigleisig — Mail
enthält bewusst beide Wege, was bei einzelnen Usern Rückfragen produziert.

Passkeys (WebAuthn) würden das eliminieren: hardware-gebundene Auth, kein
Browser-↔-PWA-Cookie-Roundtrip, kein Plattform-Unterschied.

## Ziel (Soll-Zustand, grob)

1. **Erst-Login bleibt unverändert** (Welcome-Mail mit Code/Magic-Link für
   Onboarding-Erstkontakt).
2. **Im Onboarding-Wizard neuer Schritt „Login einfacher machen".**
   Passkey registrieren via Face-ID / Touch-ID / Fingerprint /
   Windows-Hello. Skip-Option vorhanden.
3. **Ab dann: Login = ein Wisch über den Sensor.** Keine Mail, kein Code,
   kein Plattform-Unterschied.
4. **Fallback-Pfad bleibt erhalten** für Geräte ohne Passkey-Support oder
   wenn der User seinen Passkey verliert (Magic-Link/Code als Recovery).

## Technische Bausteine (grob)

- Lib: `@simplewebauthn/server` (FastAPI) + `@simplewebauthn/browser` (PWA)
- DB: neue Tabelle `webauthn_credentials` (credential_id, public_key,
  counter, user_id, created_at, last_used_at)
- API: 4 Endpoints (`/register/options`, `/register/verify`,
  `/login/options`, `/login/verify`)
- NextAuth Custom-Provider gegen WebAuthn-Verify-Endpoint

## Offene Fragen für die spätere Spec

- **Recovery-Pfad detailliert:** verlorener Passkey → Magic-Link reicht?
  Oder zusätzlicher Backup-Code beim Registrieren ausgeben?
- **Passkey-Roaming:** iCloud Keychain / Google Password Manager
  vorausgesetzt — was passiert bei Geräte-Wechsel ohne Sync?
- **Onboarding-Step-Reihenfolge:** vor oder nach Push-Permission?
- **Browser-Support-Detection:** Feature-Detection in der PWA, Fallback
  zu Hybrid wenn nicht verfügbar.

## Stufe / Risiko

**Spur.** Auth-naher Bereich (kritischer Pfad), neuer Sub-Processor
(WebAuthn-Lib), Schema-Erweiterung. Risikoklasse `kritisch` (Auth →
Listen-Treffer in Verfassung 01) — erzwingt Spur ohnehin.

## Trigger

Irgendwann — sobald >5–10 echte User aktiv sind und der Code-Pfad sich
abnutzt. Besonders relevant wenn iOS-User dominieren (dort ist der
Gewinn am größten). Spätestens vor öffentlichem Self-Service-Onboarding.

## Alternative weiterhin auf dem Tisch

- Code-only für alle (Verzicht auf Magic-Link) → konsistent, aber
  1 Extra-Schritt für 90% der User.
- Native iOS/Android-Apps mit Universal Links → Stripe-würdige UX, aber
  2 Apps + Store-Submissions = mehrere Wochen Aufwand.
