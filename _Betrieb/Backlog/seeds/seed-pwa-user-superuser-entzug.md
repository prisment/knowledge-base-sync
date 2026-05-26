---
typ: backlog_seed
titel: "pwa_user-Restschuld: SUPERUSER entziehen (impliziert noch BYPASSRLS)"
geltungsbereich: plattform
risikoklasse: kritisch
status: offen
klasse: qualitaet
zugkraft: bald
stufe: schritt
beruehrt: ["customer_postgres/agent_data Rolle pwa_user"]
stand: 2026-05-26
erzeugt_durch: "PRIS-018 Bündel 1 Faktencheck (PRIS-017 Bündel 12 Restschuld)"
---

## Anlass

Beim Phase-2-Faktencheck für PRIS-018 (PWA-Wiederanlauf) fiel auf:
`pwa_user` hat in `customer_postgres/agent_data` noch
`rolsuper = true`. PRIS-017 Bündel 12 hat zwar explizit `BYPASSRLS`
entzogen — SUPERUSER impliziert aber implizit BYPASSRLS (plus alles
andere). Damit ist der Bündel-12-Schlussriegel formal-korrekt, aber
in der Praxis weiterhin aushebelbar, sobald irgendein Pfad doch
nochmal als `pwa_user` verbindet.

## Ist-Zustand

```
 rolname  | rolcanlogin | rolsuper | rolbypassrls
----------+-------------+----------+--------------
 pwa_user | t           | t        | f
```

Aktuell verbindet nach PRIS-017 produktiv niemand mehr als `pwa_user`:
- pwa-api → `tenant_app_user` / `admin_user`
- alle 5 LangGraph-Agents → `tenant_app_user` + `SET LOCAL app.current_tenant`
- pwa-web (ab PRIS-018) → `nextauth_user`

Heißt: Risiko ist latent, nicht akut. Aber genau deshalb ist jetzt
das schmerzfreie Fenster für den Entzug.

## Ziel (Soll-Zustand)

`ALTER ROLE pwa_user NOSUPERUSER;` — Rolle bleibt als Owner aller
Tabellen bestehen (das ist OK, Owner ≠ Login-Risiko, solange nicht
verbunden), kann sich nicht mehr als Superuser anmelden.

## Vorgehen (Schritt-Stufe)

1. Vorab: `docker exec customer_postgres psql -U pwa_user -d agent_data -c "SELECT rolname FROM pg_authid WHERE rolsuper=true;"` — bestätigen, dass mindestens ein anderer Superuser für Wartung da ist (der Container-Init-User).
2. Backup `pg_dumpall --globals-only > /tmp/globals.sql` (Rollen-Definitionen).
3. `ALTER ROLE pwa_user NOSUPERUSER;` — als anderer Superuser.
4. Verifikation: `\du pwa_user` zeigt keine Attribute mehr.
5. Smoke-Test: pwa-api healthcheck + ein Agent-Run laufen weiter (sollte tun, weil keiner als pwa_user verbindet).
6. Logbuch-Eintrag.

## Rollback

`ALTER ROLE pwa_user SUPERUSER;` — sofort reversibel.

## Nicht Teil

- Owner-Wechsel der Tabellen weg von `pwa_user` (das wäre der nächste
  Schritt für „pwa_user komplett tot"). Hier nur das Privilegien-Bit.
- Login-Entzug (`NOLOGIN`) — kommt erst wenn 100% sicher, dass
  niemand mehr drauf zugreift.
