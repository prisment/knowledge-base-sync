---
typ: backlog_seed
titel: "Multi-Tenant-UX: Tenant-Auswahl-Screen nach Login + sanfter Tenant-Switch"
geltungsbereich: prisment
risikoklasse: sicher
status: offen
klasse: feature
zugkraft: bald
mission:         kunde-2
stufe: sprung
beruehrt: ["pwa/pwa-web/"]
stand: 2026-05-25
erzeugt_durch: ROADMAP_SMA-Zerlegung (Schritt 36)
---

## Anlass

User mit 2+ Tenant-Memberships landen heute automatisch im `tenant_ids[0]`
und müssen über den Header-Switcher wechseln. Risiko: versehentlich im
falschen Tenant editieren (Posts approven, Plan-Edits) — speziell für den
Architekten kritisch, der Memberships in vielen Tenants hat.

Außerdem: der heutige Tenant-Switcher setzt Cookie + `location.reload()` —
UX-Bruch (Komplett-Reload, Verlust von Scroll-State + offenen Sheets).

## Ziel (Soll-Zustand, grob)

1. **Selector-Page `/auth/tenant-select`.** Wird vom Login-Flow aufgerufen
   wenn `tenant_ids.length >= 2`. Liste aller Tenants mit Display-Name,
   Branche, Avatar/Logo (sobald Profilbild-Upload aus Meta-API-Seed da
   ist). Tap → `active_tenant`-Cookie + Redirect `/`. Optional "Tenant
   merken" → `user_settings.preferred_tenant`.
2. **Layout-Redirect.** Middleware: wenn `tenant_ids.length >= 2` und kein
   `active_tenant`-Cookie → redirect zu Selector.
3. **Sanfter Tenant-Switch ohne Reload.** Server-Action
   `setActiveTenant(tid)` + `router.refresh()` statt `location.reload()`.
4. **Architekt-Sonderfall.** Bei sehr vielen Memberships (Architekt mit
   N Tenants) eigene Übersichts-Route `/admin/tenants` statt langer
   Selector-Liste — `user_settings.role = 'architect'` als Flag.

## Offene Fragen für die spätere Spec

- **Default-Verhalten beim Single-Tenant-User:** automatisch durch, kein
  Selector (so wie heute). Bei Multi: immer Selector oder
  remembered-Pref?
- **„Tenant merken"-UX:** Toggle in Selector oder im Profil?
- **Architekt-Schwelle:** ab welcher Membership-Anzahl ist „lange Liste"?
  (Vermutlich ≥ 4, dann eigene Übersicht.)
- **State-Cleanup beim Switch:** Caches, offene Sheets, Server-Cached-
  Listen — was muss invalidieren?

## Stufe / Risiko

**Sprung.** Reine PWA-Frontend-Erweiterung + eine Middleware-Regel.
Risikoklasse `sicher` (Read-Only-Routing, kein Datenpfad-Eingriff).

## Trigger

Bald — kritisch sobald der erste Multi-Tenant-User über die Architekten-
Rolle hinaus auftaucht (Agentur-Mandant, Mandant mit 2 Firmen). Heute
unkritisch, weil 99% Single-Tenant.
