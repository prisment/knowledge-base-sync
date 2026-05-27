---
typ: backlog_seed
titel: "Admin-UI: Configs-Editor + Archetyp-Detail + Versions-History"
geltungsbereich: prisment
risikoklasse: sicher
status: offen
klasse: feature
zugkraft: bald
mission:         live-gang
stufe: sprung
beruehrt: ["admin/admin_web/", "pwa/pwa-api/"]
stand: 2026-05-25
erzeugt_durch: ROADMAP_SMA-Zerlegung (Schritt 27, Block 2.3+2.5-Audit 2026-05-11)
---

## Anlass

Aus Audit 2026-05-11 sind im Admin-UI zwei Lücken offen, die ohne Workaround
nur per direktem DB-Zugriff oder Re-Onboarding behebbar sind:

1. **`tenant_configs.config` (17 JSONB-Keys) ist nirgendwo im UI editierbar.**
   `/configs`-Tab zeigt nur Liste mit `keys_count` und Verlinkung auf
   `/tenants/{id}` — die Detail-Seite enthält Stammdaten + Personas + VoiceDB,
   aber keinen Configs-Editor. Architekt muss heute jede Config-Änderung per
   direkter DB-Query machen.
2. **Archetyp-Detail-View fehlt.** `/archetypes` zeigt nur Name + ID +
   Ton-Leitlinie. Die strategisch relevanten Felder (`beschreibung`,
   `kaufmotiv`, `monat_gesamt`, `branchen_beispiele`, `breaking_news`,
   `inhaber_liefert_pro_woche`) sind UI-unsichtbar.

Zusätzlich: keine Versions-History auf `tenant_configs` — Edits sind
unwiderruflich.

## Ziel (Soll-Zustand, grob)

1. **Configs-Editor im Tenant-Detail.** Read-Only JSON-Viewer für alle Keys
   (pretty-printed, kollapsibel) als MVP. Einzelne Sub-Keys
   (`hashtag_sets`, `posting_zeiten`, `cta_profil`) als strukturierte
   Forms; Rest als JSON-Textarea-Fallback. `PATCH /admin/tenants/{id}/config`
   schreibt via `write_mandant_config()`. Audit-Log-Eintrag pro Edit
   (`admin_audit` → action: `tenant_config.update`).
2. **Archetyp-Detail-View.** `/archetypes/[id]/page.tsx` zeigt alle Felder
   eines Archetyps. Edit-Modus optional (Archetypen sind global, selten
   geändert — Read-First ist OK).
3. **Versions-History.** Vor jedem Config-Edit Snapshot in eigene Tabelle
   (analog `voicedb_history`). Admin-UI zeigt letzte N Versionen mit
   Rollback-Action.

## Offene Fragen für die spätere Spec

- **Strukturierte Forms vs. JSON-Textarea:** welche Sub-Keys lohnen den
  Form-Aufwand? (`hashtag_sets`, `personas`, `posting_zeiten`,
  `cta_profil` — Rest JSON.)
- **Versions-History-Schema:** eigene Tabelle pro Config-Typ, oder
  generisches `audit_snapshots(table_name, row_pk, snapshot_jsonb)`?
- **Rollback-Logik:** Rollback ist nur DB-Restore oder muss er
  downstream-Effekte (Cache-Invalidierung etc.) auslösen?

## Stufe / Risiko

**Sprung.** Klar abgegrenzte Admin-UI-Erweiterung, drei Bausteine, kein
Architektur-Eingriff. Risikoklasse `sicher` (Read-Heavy, Write-Pfad
existiert über `write_mandant_config()`, Audit-Log greift).

## Trigger

Bald — sobald häufiger Config-Edits ohne DB-Query gebraucht werden. Bis
dahin reicht der direkte DB-Pfad für den Architekten.
