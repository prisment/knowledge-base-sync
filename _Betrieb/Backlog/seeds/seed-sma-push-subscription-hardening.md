---
typ: backlog_seed
titel: "Push-Subscription-Hardening: Silent Re-Subscribe + Zero-Friction-Onboarding E2E"
geltungsbereich: prisment
risikoklasse: sicher
status: offen
klasse: qualitaet
zugkraft: bald
stufe: sprung
beruehrt: ["pwa/pwa-web/", "pwa/pwa-api/"]
stand: 2026-05-25
erzeugt_durch: ROADMAP_SMA-Zerlegung (Schritt 29.3 + 29.4)
---

## Anlass

Schritt 29.1+29.2 (Registrierungs-Email, Onboarding-Wizard mit A2HS + Push-
Permission, Onboarding-Gate) sind live. Zwei Lücken bleiben:

1. **Push-Subscriptions invalidieren ohne sichtbaren Effekt.** Nach
   Service-Worker-Updates (v1→v2) verlieren User ihre Subscription
   stillschweigend — sie müssen manuell im Profil den Push-Toggle aus/an
   schalten. Symptom bereits aufgetreten (Korbinian nach SW-Major-Update).
2. **End-to-End-Test mit echtem Test-User ist nie sauber durchgespielt
   worden.** Mail-Flow (`Admin-UI Welcome-Mail → iPhone öffnen → A2HS →
   Push-Permission → Push kommt durch ohne weitere User-Aktion`) ist
   noch nicht verifiziert.

## Ziel (Soll-Zustand, grob)

1. **Silent Re-Subscribe im ServiceWorkerReg.**
   `ServiceWorkerReg.tsx` prüft nach SW-Registration:
   - `pushManager.getSubscription()` existiert?
   - Gültig für aktuellen VAPID-Key?
   - Falls nein → silent neu subscriben (Permission ist persistent, kein
     neuer Prompt). POST `/push/subscribe` mit neuer Subscription.
   Schützt vor Subscription-Verlust nach SW-Updates und FCM-410-Cleanups.
2. **E2E-Test sauber durchspielen.** Zwei Geräte (iPhone + Android), je
   ein echter Test-User. Admin-UI „Kunde einladen" → Mail kommt →
   Auto-Install (A2HS) → Auto-Permission (Pre-Permission-Card →
   Notification.requestPermission) → Test-Push aus Admin-UI → User sieht
   Push ohne irgendwo Toggle bedient zu haben. iOS und Android getrennt
   dokumentieren.

## Offene Fragen für die spätere Spec

- **Re-Subscribe-Trigger:** nur bei SW-Update oder bei jedem PWA-Start?
  (Jedes-Start ist defensiv, kostet aber bei jedem Visit einen
  `getSubscription()`-Call.)
- **VAPID-Key-Rotation:** wie wird ein zukünftiger VAPID-Wechsel
  orchestriert? Re-Subscribe-Logic muss alten Key erkennen und
  resubscriben.
- **FCM-410-Detection im Backend:** invalidierte Subscriptions im
  Backend aktiv markieren (`push_subscriptions.invalid_at`), damit
  Re-Subscribe ohne SW-Update-Trigger laufen kann?

## Stufe / Risiko

**Sprung.** Drei eng zusammenhängende Bausteine (SW-Code, Backend-
Detection optional, E2E-Test-Dokumentation). Risikoklasse `sicher` —
defensive Hardening, keine neue Funktionalität.

## Trigger

Bald — sollte vor Multi-Mandanten-Rollout sauber sein, damit Push-
Ausfälle nicht zur Support-Last werden. Quick-Win-Charakter.
