---
typ: backlog_seed
titel: "Voice-Check-Audit + Admin-Insights (Issues sichtbar machen statt verstecken)"
geltungsbereich: prisment
risikoklasse: sicher
status: offen
klasse: qualitaet
zugkraft: bald
stufe: sprung
beruehrt: ["langgraph/content/", "admin/admin_web/", "pwa/pwa-api/"]
stand: 2026-05-25
erzeugt_durch: ROADMAP_SMA-Zerlegung (Schritt 32)
---

## Anlass

Der Voice-Check im Content-Agent ist ein wertvolles Optimierungs-Werkzeug
(„Stil-Inkonsistenz", „Zeichenlimit überschritten", „Pflichtfeld fehlt") —
heute verschwindet die Information nach erfolgreicher Auto-Korrektur. Der
Architekt sieht die Issues nicht, kann also nicht ableiten, welche
Voice-Anchors / No-Goes / Prompts geschärft werden müssten.

Verlust einer kostenlosen Lern-Quelle pro Mandant.

## Ziel (Soll-Zustand, grob)

1. **Audit-Tabelle `voice_check_audit`** mit tenant_id, post_id, format,
   attempt, passed, severity, issues (JSONB), resolved_in_revisions,
   created_at + Index auf (tenant_id, created_at DESC).
2. **Eintragung im `node_voice_check`.** Nach jedem Check-Lauf (auch bei
   `passed=True`) eine Row schreiben. Bei finalem Pass:
   `resolved_in_revisions = revision_count`.
3. **Admin-Dashboard „Voice-Check-Insights"** (eigene Route oder Tab im
   Tenant-Detail) mit:
   - **Top-Issues** der letzten 30 Tage (häufigste Befunde pro Tenant)
   - **Avg-Revisions-bis-Pass** (Indikator wie gut der LLM den Stil beim
     1. Versuch trifft)
   - **Failure-Rate** (Anteil Posts die `human_review_required` ausgelöst
     haben)
   - Drill-Down: Klick auf Issue → Liste der Posts → Direkt-Edit der
     `voice-db/style-anchors.md` oder `no-goes.md`

## Offene Fragen für die spätere Spec

- **Retention:** wie lange behalten wir Audit-Rows? (Gehört in DSGVO-Seed
  mit — `auto_purge` nach 90 Tagen ist plausibel, da Aggregat-Insight
  schnell stagniert.)
- **Severity-Schema:** wie heute (info/warning/error) oder eigene
  Klassifizierung für Insights-Ranking?
- **PWA-Sichtbarkeit:** soll der Inhaber selbst „seine" Issues sehen, oder
  nur der Architekt? (Vermutlich nur Architekt — Inhaber soll nicht über
  Voice-Check-Internals nachdenken müssen.)

## Stufe / Risiko

**Sprung.** Eine neue Tabelle + zwei Code-Stellen (Content-Agent +
Admin-UI), klar abgegrenzt. Risikoklasse `sicher` (rein additive
Observability, keine Geschäftslogik-Änderung).

## Trigger

Bald — wertvoll ab dem zweiten Mandanten, weil Aggregat-Insights mehr
sagen als Einzel-Post-Inspektion. Vorher reicht Manuell-im-Code-Schauen.
