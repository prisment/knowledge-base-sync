---
typ: backlog_hotfiles
titel: "Hot-Files — strang-übergreifend, nur seriell anfassbar"
gepflegt_von: Mensch (handgepflegt, NICHT generiert)
stand: 2026-05-25
---

# Hot-Files

**Definition (Verfassung 00, Abschnitt „Backlog-Pflege"):** Pfade/Dateien, die
fast jeder Strang berührt — die typische Konflikt-Quelle. Werden NUR seriell
angefasst, NIE in zwei parallelen Worktrees gleichzeitig. Strikt handgepflegt;
das Übersichts-Skript liest sie nicht und überschreibt sie nicht.

## Aktuelle Liste

### `prisment-platform` Repo (`/opt/infrastructure/environment_a/`)
- `docker-compose.yml` — Service-Topologie, jeder Service-Strang fasst sie potenziell an.
- `pwa/pwa-web/package-lock.json` — Frontend-Lockfile.
- `pwa/pwa-api/requirements.txt` — Backend-Lockfile.
- `pwa/migrations/**` — DB-Schema, einziger Migrationspfad im Repo.

### `knowledge-base` Repo
- `_Betrieb/Verfassung/**` — jede Änderung ist Spec-relevant, niemals parallel.
- `_Betrieb/Backlog/00_UEBERSICHT.md` — vom Skript generiert, niemals händisch.
- `_Betrieb/Logbuch/ENTSCHEIDUNGS_HISTORIE_FRAMEWORK.md` — Hot wegen E-Nummern-Vergabe (paralleles Append bricht die Reihenfolge).

## Nicht in der Liste (bewusste Auslassungen)

- `langgraph/{analytics,content,helpdesk,interview,redaktionsplan}/requirements.txt` — Service-lokal, keine Cross-Strang-Konflikte erwartet.
- `admin/admin_web/` — kein committeter `package-lock.json` (eigener Drift, eigener Folge-Seed denkbar; aber kein Hot-File-Thema).
- `/opt/infrastructure/foundation/**` — NO TOUCH READ ONLY, nicht Hot, sondern Wand. Wer das berührt: synchroner Stopp per Definition.

## Pflege

- Wer einen Strang vorbereitet, der einen neuen typischen Konfliktpunkt offenlegt → Eintrag hier ergänzen, im selben Commit wie der Strang-Beginn.
- Reviews dieser Datei sind selten; sie altert langsam.
- Bei Streichung eines Pfads: kurze Begründung in der Commit-Message, warum er kein Hot-File mehr ist.
