---
typ: uebersicht
geltungsbereich: prisment
stand: 2026-05-25
zweck: "Schneller Einstieg in den Prisment-Bereich für Mensch und Chat-Architekt. Verdichtet den Detail-Stand, ersetzt ihn nicht."
---

# Prisment — Bereichs-Übersicht

**Abschalt-Test:** Was unter Prisment liegt, ist das Produkt selbst —
Mandanten-PWA, langgraph-Agent-Pipeline, Mandantendaten. Würde Prisment
morgen wegfallen, würden diese Komponenten mit verschwinden (die
Plattform-Schicht darunter bliebe).

## Architektur in einem Absatz (für Lesende ohne SVG-Sicht)

Prisment ist eine vollautomatische Social-Media-Pipeline für Mandanten,
gebaut auf der gemeinsamen Plattform-Infrastruktur. Mandanten interagieren
ausschließlich über die **PWA** (Next.js Frontend, derzeit offline via
HARDENING-Profile-Lock — siehe `04_PWA.md` Wiederanlauf-Status). Die
**`pwa-api`** (FastAPI Backend) routet User-Aktionen an die fünf
**langgraph-Agents** (`content`, `interview`, `redaktionsplan`,
`analytics`, `helpdesk`) und schreibt Mandantendaten in den dedizierten
**`customer_postgres`** (pgvector mit `agent_data`-Schema, Trennung in
`auth.*` für PWA-Sessions und `public.*` für Tenant-Daten).
Mandanten-Kommunikation läuft über `pwa-api /internal/notify` mit
PWA-Push + Email-Fallback — keine Telegram-/Twilio-Routing-Schicht mehr
(seit PLAT-009, Logbuch E25). Cron-Trigger für wöchentliche Interviews,
Boost-Checks und Redaktionsplan-Generierung sind seit
n8n-Cron-Migration (2026-05-18) im **APScheduler in pwa-api**, nicht
mehr in n8n. **Öffentliche Touchpoints** sind `prisment.de` (Landingpage),
`assets.prisment.de`, `app.prisment.de` (PWA, derzeit offline) und der
Umami-Tracking-Endpoint. **Mandantendaten-Zone** ist isoliert: kein
Public-Routing, einziger Weg führt durch die PWA → pwa-api → Agent → DB.
**Aktuelle Mandanten-Phase:** Kunde #1 (Grubis-Weine) ist onboardet
und agiert als interner Tester — produktiver Posting-Betrieb (echte
Veröffentlichungen) noch nicht aktiv. **Voraussetzung Kunde #2:**
HARDENING Phase 5 (fail-closed RLS in `customer_postgres`).

## Architektur als Bild (nur lokal)

![Architektur](01_Architektur.svg)

> SVG nur lokal verfügbar. Substanz steht im obigen Absatz +
> Sub-Übersichten.

## Drei Zonen, drei Vertrauensstufen

- **🟦 Public-Zone:** Landingpage, PWA (offline), Assets, Umami-Tracking,
  cf-alarm — anonymer Traffic erlaubt.
- **🟪 Cloudflare-Access-Zone:** 8 Apps hinter Identity-Login
  (`korbinian.schnall@prisment.de`) — WAF deaktiviert (Logbuch E26).
  Details siehe Plattform-Übersicht.
- **🟥 Mandantendaten-Zone:** `pwa_api`, 5 langgraph-Agents,
  `customer_postgres`. **Kein Public-Routing.** Schichten:
  Cloudflare Tunnel → NextAuth → internes Docker-Netz → DB-Auth →
  (geplant: RLS Phase 5).

Plus parallel: **Tailscale** für Admin-SSH (Plattform-Detail, siehe
[`Plattform/.../tailscale.md`](../../../Plattform/Systemzustand/Geteilte-Dienste/tailscale.md)).

## Auth in einem Satz

Mandanten authentifizieren sich per **NextAuth** in der PWA; die PWA
ruft `pwa-api` mit der User-Session, `pwa-api` ruft langgraph-Agents
mit `X-Agent-Secret`. Plattform-Auth (Cloudflare Access für
Admin-Apps) ist in `Plattform/.../00_Uebersicht/` dokumentiert.

## Sub-Übersichten

- [`02_Agenten.md`](02_Agenten.md) (+ `02_Agenten.svg`) — Die 5
  langgraph-Agents, Pipeline-Loop, LLM-Stack, Notify-Rückweg.
- [`03_Onboarding-Pipeline.md`](03_Onboarding-Pipeline.md)
  (+ `03_Onboarding-Pipeline.svg`) — Vom Audio-Upload zum aktiven
  Mandanten.
- [`04_PWA.md`](04_PWA.md) — PWA-Stack (web/api/admin),
  DB-Topologie, Notification-Architektur, APScheduler,
  Test-vs-Live-Trennung, **pwa-web Wiederanlauf-Status**.
- [`05_Strategie.md`](05_Strategie.md) — Vision, Geschäftsmodell,
  Tier-/Preismodell, aktuelle Phase.

## Detail-Dokus (Quelle der Wahrheit)

Diese Übersicht ist **abgeleitete Ansicht** (Konzept SSOT,
Verfassung 03), nicht zweite Quelle. Maßgeblich:

### Architektur

| Datei | Inhalt |
|---|---|
| [`../Architektur/Social Media Automation – Technische Dokumentation.md`](../Architektur/Social%20Media%20Automation%20%E2%80%93%20Technische%20Dokumentation.md) | Haupt-Tech-Doku (2812 Z., Pipeline + Daten-Modell + Code-Beispiele) |
| [`../Architektur/SMA_Operative_Doku.md`](../Architektur/SMA_Operative_Doku.md) | Dev-Workflow + Test-Stack + Promote-Strategie |

### Agenten (Detail)

| Datei | Agent |
|---|---|
| [`../Architektur/agents/langgraph-content.md`](../Architektur/agents/langgraph-content.md) | Content (3 Posts pro Run, 8 Formate) |
| [`../Architektur/agents/langgraph-interview.md`](../Architektur/agents/langgraph-interview.md) | Interview (Voice-DB-Aufbau) |
| [`../Architektur/agents/langgraph-analytics.md`](../Architektur/agents/langgraph-analytics.md) | Analytics (Boost-Check + Monthly Report) |
| `../Architektur/agents/langgraph-helpdesk.md` | Helpdesk (rudimentäre Doku — kommt in PRIS-016 Bündel 6) |
| `../Architektur/agents/langgraph-redaktionsplan.md` | Redaktionsplan (**Detail-Doku-Lücke** — kommt später) |
| [`../Architektur/agents/CONTENT_BOT_ABLAUF.md`](../Architektur/agents/CONTENT_BOT_ABLAUF.md) | Content-Bot-Ablauf-Detail |
| [`../Architektur/agents/CONTENT_BOT_PLAYBOOK_INTEGRATION.md`](../Architektur/agents/CONTENT_BOT_PLAYBOOK_INTEGRATION.md) | Playbook-Integration |
| [`../Architektur/agents/INTERVIEW_AGENT_V6_SPEC.md`](../Architektur/agents/INTERVIEW_AGENT_V6_SPEC.md) | Interview-V6-Spec |
| [`../Architektur/agents/INTERVIEW_V6_RUNBOOK.md`](../Architektur/agents/INTERVIEW_V6_RUNBOOK.md) | Interview-V6-Runbook |

### Produkt

| Datei | Inhalt |
|---|---|
| [`../Produkt/PWA_KUNDEN_APP.md`](../Produkt/PWA_KUNDEN_APP.md) | PWA-Spec (vollständig) |
| [`../Produkt/PWA_DESIGN.md`](../Produkt/PWA_DESIGN.md) | UX-Spec für PWA-Frictionless-Überarbeitung |
| [`../Produkt/onboarding.md`](../Produkt/onboarding.md) | Onboarding-Ablauf |
| [`../Produkt/onboarding_pflichtfelder.md`](../Produkt/onboarding_pflichtfelder.md) | Pflichtfelder-Inventar |
| [`../Produkt/Product & Execution.md`](../Produkt/Product%20%26%20Execution.md) | Produkt-Sicht |
| [`../Produkt/CONTENT_MIX_KONZEPT.md`](../Produkt/CONTENT_MIX_KONZEPT.md) | Content-Mix-Konzept |
| [`../Produkt/content_mix_crossposting_archetypen.md`](../Produkt/content_mix_crossposting_archetypen.md) | Crossposting-Archetypen |

### Marketing

| Datei | Inhalt |
|---|---|
| [`../Marketing/prisment_brand_design_system.md`](../Marketing/prisment_brand_design_system.md) | Brand & Design System (verbindlich für alle UI) |
| [`../Marketing/Landingpage Content-Architektur.md`](../Marketing/Landingpage%20Content-Architektur.md) | Landingpage-Konzept |
| [`../Marketing/Social Media Automation – Marketing-Architektur & Strategie.md`](../Marketing/Social%20Media%20Automation%20%E2%80%93%20Marketing-Architektur%20%26%20Strategie.md) | Marketing-Strategie |

### Strategie

| Datei | Inhalt |
|---|---|
| [`../Strategie/Business Strategy.md`](../Strategie/Business%20Strategy.md) | Geschäftsstrategie |
| [`../Strategie/prisment_roadmap_businessplan.md`](../Strategie/prisment_roadmap_businessplan.md) | Roadmap + Businessplan |
| [`../Strategie/Compliance & Moat.md`](../Strategie/Compliance%20%26%20Moat.md) | Compliance + Wettbewerbsvorteil |
| [`../Strategie/legal_guardrails_entwurf.md`](../Strategie/legal_guardrails_entwurf.md) | Legal Guardrails |
| [`../Strategie/Datenschutz/`](../Strategie/Datenschutz/) | Datenschutz-Quellen |

## Mandanten-Phase (Stand 2026-05-25)

Kunde #1 (**Grubis-Weine**) ist onboardet, agiert als interner Tester.
Echte Posting-Veröffentlichungen über die Mandanten-PWA stehen noch
aus (pwa_web ist via HARDENING-Profile-Lock offline). Kunde #2 setzt
HARDENING Phase 5 (RLS in `customer_postgres`) voraus.

Offene SMA-Arbeit lebt im zentralen Backlog ([`../../../_Betrieb/Backlog/00_UEBERSICHT.md`](../../../_Betrieb/Backlog/00_UEBERSICHT.md), Seeds mit `sma`-Präfix). Die ehemalige `ROADMAP_SMA.md` wurde am 2026-05-25 in Einzel-Seeds zerlegt (PLAT-022 Folge-Bereinigung).

## Was sich gegenüber pre-Telegram-EOL geändert hat

- **Telegram + Twilio aus Produktbetrieb** (PLAT-009, Logbuch E25):
  n8n-Telegram-Router-Workflow weg, Code-Pfade in 3 langgraph-Agents
  bereinigt, compose-env-Vars raus, Mandanten-Kommunikation komplett
  über PWA-Push + Email-Fallback.
- **n8n-Cron-Migration** (Feature-Log 2026-05-18): alle Cron-Trigger
  von n8n in APScheduler von pwa-api.
- **Authentik abgebaut** (CF-MIG Mai 2026): Plattform-Auth läuft über
  Cloudflare Access.

## Pflege-Hinweis

Diese Übersicht ist abgeleitete Ansicht (Konzept SSOT). Bei einer
Systemzustands-Änderung im Prisment-Bereich soll diese Übersicht
(MD + SVG) im selben Zyklus mit aktualisiert werden — sonst entsteht
stille Doppel-Wahrheit. Pflicht-Tor in Verfassung 00 (Phase 9 „Doku-
Synchronität") verankert.
