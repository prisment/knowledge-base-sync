---
typ: backlog_seed
titel: "test-mandant Aufräumung in voicedb_entries (4 Testdaten-Zeilen)"
geltungsbereich: prisment
risikoklasse: sicher
status: offen
klasse: wartung
zugkraft: irgendwann
stufe: schritt
beruehrt: ["customer_postgres/agent_data/public.voicedb_entries (+ ggf. voicedb_history Cascade)"]
stand: 2026-05-26
erzeugt_durch: "PRIS-017 Bündel 4-Befund + Architekten-Erinnerung beim Zyklus-Abschluss"
---

## Anlass

Beim Faktencheck PRIS-017 und in der Cross-Tenant-Verifikation
(Bündel 13) tauchte auf, dass `voicedb_entries` 10 Zeilen über 2
Tenants hat: 6 für Grubis (`t_01KRH8GXBXBY4YEM3HPZ9QRR3Y`) + 4 für
einen `test-mandant`. Der `test-mandant` ist altes Test-Material aus
einer früheren Entwicklungsphase, kein produktiver Tenant. Beim
PRIS-017-Bündel-4-Lauf bewusst stehen gelassen (kein Spec-Scope).

## Ziel (Soll-Zustand, grob)

- Test-Mandant-Daten in `voicedb_entries` (4 Zeilen) löschen.
- FK `voicedb_history → voicedb_entries(id) ON DELETE CASCADE` zieht
  zugehörige History-Einträge mit weg.
- Eventuelle weitere Tenant-bezogene Tabellen für `test-mandant`
  prüfen (z.B. `topics`, `chat_messages`) — kann Begleit-Aufräumung
  sein, falls vorhanden.
- Tenant-Eintrag selbst in `tenants` ggf. ebenfalls weg, falls nur
  Test-Stub.

## Vorgehen (Schritt-Stufe)

1. Bestandsaufnahme: `SELECT * FROM voicedb_entries WHERE tenant_id=
   'test-mandant'` (Inhalt prüfen, bestätigen dass es Test-Stub ist).
2. Andere Tabellen mit `tenant_id='test-mandant'` auflisten.
3. Falls leer/test: `DELETE FROM voicedb_entries WHERE tenant_id=
   'test-mandant'` (CASCADE zieht voicedb_history nach).
4. Analog für andere Tabellen.
5. Verifikation: `SELECT count(*) FROM voicedb_entries` = 6 (nur
   noch Grubis).

## Stufe / Risiko

**Schritt.** Reine DELETE-Operation auf identifiziertes Test-
Material, reversibel via Restic-Restore wenn nötig. Risikoklasse
`sicher`. Kein Code-Eingriff, kein Container-Neustart.

## Trigger

Irgendwann — kein Druck. Spätestens vor Kunde #2, damit das
Cross-Tenant-Verifikations-Pattern (admin_user sieht 10 statt 6 in
voicedb_entries) sauber aussagekräftig bleibt. Alternativ: wenn der
nächste Backup-Dump zu groß wird.

## Hinweis

Beim Aufräumen ist die rollenbewusste Policy (admin_user darf
cross-tenant) nützlich — `_admin_conn` oder direkter psql als
`pwa_user` (Superuser) reicht. Kein RLS-Hindernis.
