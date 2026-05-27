---
typ: backlog_seed
titel: "Webshop-Anbindung Prestashop → Meta Custom Audiences (Lookalike + Cold-Audience)"
geltungsbereich: prisment
risikoklasse: kritisch
status: offen
klasse: feature
zugkraft: irgendwann
mission: ""
stufe: spur
beruehrt: ["langgraph/", "n8n/", "pwa/pwa-api/"]
stand: 2026-05-25
erzeugt_durch: ROADMAP_SMA-Zerlegung (Schritt 16)
abhaengig_von:
  - "[[_Betrieb/Backlog/seeds/seed-sma-meta-api-integration]]"
---

## Anlass

Eigenständiger Vermarktungs-Hebel: Mandanten mit Prestashop-Shop können
ihre Käuferdaten DSGVO-konform zu Meta synchronisieren — Custom Audiences
für Retargeting, Lookalikes für Cold-Acquisition, Exclusion-Logik gegen
Mehrfach-Ansprache. Nächtliche Delta-Syncs halten das Bild aktuell.

Das war Schritt 16 in der alten Roadmap und ist faktisch ein eigener
Produktbaustein, nicht Teil der Content-/Engagement-Pipeline. Hängt aber
auf Meta-API-Setup auf — kann erst sinnvoll starten, wenn der
Meta-API-Seed durch ist.

## Ziel (Soll-Zustand, grob)

1. **Meta Business Setup** (manuell durch Architekt): Marketing-API-
   Zugang, System-User-Token, Ad-Account-ID
2. **Prestashop-Daten-Extraktion:** alle Käufer + Top-Käufer +
   Warenkorbabbrecher als segmentierte Listen
3. **Custom-Audience-Aufbau:** SHA-256-Hashing der PII (Mail, Telefon),
   Lookalike 1%/5%, Cold-Audience-Liste
4. **Exclusion-Logik:** bestehende Käufer bei Lookalike/Cold excludieren
5. **Nightly Sync (Cron 02:00):** Delta-Sync, Sync-Log in DB, pwa-api
   `/internal/notify` type=admin_alert bei Fehler
6. **Onboarding-Integration:** `webshop_typ` in mandant_config; bei
   Aktivierung Workflow klonen (per Tenant)

## Offene Fragen für die spätere Spec

- **DSGVO-Bewertung:** Hashing reicht oder braucht es explizite
  Einwilligungs-Strecke pro Endkunde? Gehört in den DSGVO-Sammel-Seed
  mit (Meta als zusätzlicher Sub-Processor für PII-Hashes).
- **Prestashop-API-Anbindung:** existierende Connectoren nutzen
  (n8n-Nodes, eigener Worker) oder neuen Service?
- **Multi-Shop-Strategie:** ein Mandant kann mehrere Shops haben —
  Schema im mandant_config vorsehen?
- **Andere Shop-Systeme:** Shopify/WooCommerce nach gleichem Muster
  (eigener Seed pro Shop-System) oder einer abstrahierter Worker?
  Vermutlich erst bei zweitem Shop-System überlegen.

## Stufe / Risiko

**Spur.** Externer Service-Anbindung + DSGVO-Pflicht-Tor + 6 Substeps
mit klarer Reihenfolge. Risikoklasse `kritisch` (PII-Hashes verlassen
das System Richtung Meta — Listen-Treffer Sicherheits-Schicht).

## Trigger

Irgendwann — **weit hinten**. Voraussetzung: Meta-API-Seed durch +
DSGVO-Sammel-Seed durch + erster Prestashop-Mandant in Sicht.
