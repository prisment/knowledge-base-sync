---
typ: backlog_seed
titel: "Logging/Forensik-Grundlage: CF-Access-Logs Logpush + Postgres log_connections + Traefik accessLog"
geltungsbereich: plattform
risikoklasse: kritisch
status: offen
klasse: security
zugkraft: bald
stufe: spur
beruehrt: ["Cloudflare Zero Trust (Logpush)", "/opt/infrastructure/foundation/traefik/", "internal_postgres", "customer_postgres", "Log-Speicher (S3/lokal?)"]
stand: 2026-05-26
erzeugt_durch: "Phase-7-Atomisierung HAERTUNGS_SPEC 7.2 + ROADMAP_cf_migration HTTP-Access-Logs"
---

## Anlass

CVE-2025-55182-Incident war 5,5 Monate unsichtbar. Kein zentraler
Audit-Trail über Login-Versuche, Tool-Zugriffe oder DB-Connections.
Nach Phase 4 (CF-Migration) loggt Cloudflare zwar Access-Versuche
intern, aber retentionarm — Logpush in eigenen Speicher fehlt.
Postgres läuft mit Default-Logging (kein `log_connections`,
`log_disconnections`, `log_statement`). Traefik schreibt keinen
`accessLog` (foundation/-readonly, müsste Architekt setzen).

## Ziel (Soll-Zustand, grob)

Drei Logging-Schichten gleichzeitig:

1. **Cloudflare Access Logpush** in einen S3-kompatiblen Speicher
   (Hetzner Storage Box oder Cloudflare R2). Mindestens 90 Tage
   Retention. Erfasst alle Access-Authentifizierungen pro Tool.
2. **Postgres `log_connections=on` + `log_disconnections=on`** auf
   `internal_postgres` UND `customer_postgres`. Plus `log_statement
   = 'ddl'` (mindestens). Logs in Container, restic-Backup zieht's
   mit.
3. **Traefik `accessLog`** im foundation-Stack aktivieren (Architekt-
   Edit, foundation/ ist read-only). JSON-Format, rotation, retention
   30 Tage.

## Stufe / Risiko

**Spur**. Drei eigenständige Bauteile, jeweils mit Container-Restart
und Speicher-Setup. Postgres-Restart ist kritisch (Live-DB) →
Architekt-Hand zwingend. Aufwand: 2-3 Tagessitzungen. Reversibel
(Config-Rollback je Komponente).

## Trigger

Bald — vor Onboarding Kunde #2, weil Audit-Anforderungen kommen
können. Davor wartet auf Phase-5-Abschluss (Test-Familie braucht
nicht reingestört zu werden).

## Hinweis

Cloudflare Logpush ist Pro-Plan-Feature (haben wir). DSGVO-Bewertung
des Log-Speicherorts macht es zur Spur statt Sprung — Daten-Routing
muss klar dokumentiert sein.
