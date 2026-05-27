---
typ: backlog_seed
titel: "LLM-Kosten-Tracking + PII-Anonymisierungs-Layer in Agent-Calls"
geltungsbereich: prisment
risikoklasse: sicher
status: offen
klasse: qualitaet
zugkraft: bald
mission:         live-gang
stufe: sprung
beruehrt: ["langgraph/", "admin/admin_web/", "pwa/pwa-api/"]
stand: 2026-05-25
erzeugt_durch: ROADMAP_SMA-Zerlegung (Schritt 30.1–30.5)
---

## Anlass

Zwei verbundene Lücken vor produktivem Multi-Tenant-Betrieb:

1. **Klarnamen gehen heute in jeden Anthropic-Call.** Inhaber-Namen,
   Kunden-Adressen, Telefonnummern, konkrete Lieferanten-/Winzernamen
   landen unmaskiert im Prompt. Compliance-Risk + Tenant-Vertraulichkeit
   schwach.
2. **Keine Kostenkontrolle pro LLM-Call.** Architekt sieht nicht, wo das
   Anthropic-Budget hinfließt — pro Tenant, pro Agent, pro Modell. Bei
   Tenant #5+ wird das blind.

Die zwei Themen werden technisch **am selben Wrapper** behandelt
(`_llm_invoke_with_retry()` in allen 4 Agents) — deshalb gehören sie in
einen Zyklus.

## Ziel (Soll-Zustand, grob)

1. **PII-Anonymisierungs-Layer.** Vor jedem Anthropic-Call werden
   Tenant-PII durch Tokens ersetzt:
   - Personennamen (Inhaber + Persona-Namen) → `[INHABER]`, `[PERSON_A]`
   - Adresse/Stadt/Land aus mandant_config → `[ORT]`
   - Telefonnummern/Email-Adressen → `[KONTAKT]`
   - Konkrete Lieferanten/Partner → `[PARTNER_1]`, `[PARTNER_2]`
     (deterministisch per Hash)
   - Nach LLM-Response: De-Tokenisierung mit gleicher Map zurück
   - Map pro Call frisch gebaut (kein Persistieren, kein Cache-Leak)
   - Edge-Case: Token nicht im Output → kein Schaden, abstrakter Text
   - Nur Anthropic-Calls (Whisper bleibt clear-text, OpenWebUI ist
     ohnehin raus aus dem Hot-Path)
2. **`llm_usage`-Tabelle** in customer_postgres.agent_data mit
   tenant_id, agent, model, input_tokens, output_tokens, cost_eur,
   purpose, erfasst_am, erfasst_datum + Index (tenant_id, erfasst_datum).
3. **Anthropic-Pricing-Konstanten** als Dict in shared utils
   (HAIKU_INPUT_PER_MTOK, SONNET_INPUT_PER_MTOK etc.). Cost-Berechnung
   `(input × price + output × price) / 1_000_000` in EUR pro Call.
4. **Admin-Dashboard `/admin/kosten`** (oder Tab in `/admin/system`):
   - Heute: total + Top-3-Tenants
   - Aktueller Monat: Diagramm (Tagesverbrauch), Aggregat pro Tenant
   - Gesamt seit Start: Tabelle pro Tenant
   - Drill-Down: Klick auf Tenant → Aufschlüsselung pro Agent + Modell
5. **Budget-Alerts (optional).** Konfigurierbares Monatsbudget pro
   Tenant. Cron alle 6h: > 80% → Email an Architekt, 100% →
   automatische Tenant-Deaktivierung.

## Verzahnung mit DSGVO-Seed

Der PII-Layer überlappt teilweise mit dem LangSmith-Pseudonymisierungs-
Befund (19.1) im DSGVO-Sammel-Seed — beide Mechanismen können auf
demselben Helper aufsetzen. Reihenfolge: DSGVO-Seed liefert die
Mapping-Tabelle und das Persistenz-Modell, dieser Seed erweitert es
auf alle LLM-Calls (nicht nur LangSmith-Tracing) und auf das
Kosten-Tracking.

## Offene Fragen für die spätere Spec

- **De-Tokenisierungs-Edge-Cases:** wenn LLM den Token modifiziert
  („[INHABER] genannt") oder ausspart → wie robust ist die
  Re-Substitution?
- **Anthropic-Pricing-Pflege:** statisch in Code, oder als
  Config-Tabelle in DB (für Modell-Updates ohne Redeploy)?
- **Budget-Alert-Schwellen:** sinnvolle Default-Werte (z.B. 80% / 100%)
  oder pro Tenant konfigurierbar? Email-Adresse Konfiguration?
- **Auto-Deaktivierung bei 100%:** Soft-Disable (Read-Only) vs. Hard-
  Disable (active=false in tenants)?

## Stufe / Risiko

**Sprung.** Ein Wrapper-Refactor + neue Tabelle + Admin-UI-Erweiterung,
klar abgegrenzt. Risikoklasse `sicher` (PLAT-015-Schärfung 2026-05-26):
PII-Layer ist konzeptionell additiv vor jedem Anthropic-Call; ein Layer-Bug
ist behebbar, Anthropic-Logs sind kein dauerhafter unwiederbringlicher
Schaden. Architektur bleibt unverändert (nur Schicht dazwischen).

## Trigger

Bald — vor zweitem Mandanten. PII-Schutz ist DSGVO-Pflicht, Kosten-
Tracking wird ab Tenant #3 dringend.
