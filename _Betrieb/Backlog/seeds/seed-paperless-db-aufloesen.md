---
typ: backlog_seed
titel: "Verwaiste paperless-DB im PostgreSQL-Hub auflösen"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: wartung
zugkraft: irgendwann
stufe: sprung
beruehrt: ["postgres/"]
stand: 2026-05-25
erzeugt_durch: PLAT-008 Bündel 4
---

## Anlass

Bei der Bestandsaufnahme in PLAT-008/PLAT-009 fiel auf: Im
PostgreSQL-Hub (`internal_postgres`) existieren eine Datenbank
`paperless` (Owner `paperless_user`) und ein zugehöriger Service-User —
aber **kein paperless-Container läuft** in `docker ps`, und keine
Spur von paperless in `docker-compose.yml`.

Quelle: `\l` im PG-Hub als `n8n_user` (PLAT-009 Faktensammlung,
Nacherhebung Phase 5).

## Soll-Zustand

Klärung in zwei Schritten:

1. **Sind die Daten in der DB noch wertvoll?**
   - DB anschauen: welche Tables, welche Row-Counts, letzter
     Aktivitäts-Timestamp.
   - Falls leer oder uralt-leer: löschen ohne Sicherung.
   - Falls Inhalt: Dump nach `secrets/`-getrennten Backup-Pfad, dann
     ins WORM-S3-Restic, dann DB + User löschen.
2. **DB + User aus PG-Hub entfernen:**
   ```sql
   DROP DATABASE paperless;
   DROP USER paperless_user;
   ```
3. **PostgreSQL-Hub.md aktualisieren** — paperless-Zeile aus
   DB-Inventar-Tabelle + User-Liste entfernen.

## Auslöser für die Mini-Spec

Niedrige Priorität — verwaiste DB richtet keinen aktiven Schaden an,
verbraucht aber ~7.5 MB und ist eine Quelle für „warum gibt's das"-Verwirrung.
Kann jederzeit eingeschoben werden, blockiert nichts.

## Form

Stufe **Schritt** (trivial, reversibel via Backup, isoliert). Eine
Schritt-Log-Zeile in `Plattform/Schritt-Log.md` als Abschluss, kein
voller Zyklus.

## Verweis

Erwähnt in `Plattform/Systemzustand/Geteilte-Dienste/PostgreSQL-Hub.md`
als „verwaist" markiert.
