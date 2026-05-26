---
name: image-build-promote
description: Image-Build und Promote für prisment-platform-Services (langgraph-content u.a.) — Test/Live-Trennung über :test und :latest Tags. Trigger wenn der Nutzer ein Service-Image neu bauen oder von Test nach Live promoten will (Stichworte "image bauen", "build image", "promote image", "langgraph build", "rebuild service", "neues image", "Container-Image aktualisieren", "scripts/build_image.sh", "scripts/promote_image.sh", "test image deployen", "live deployen"). Verhindert versehentliches Überschreiben von :latest.
---

# Skill: Image-Build/Promote (prisment-platform Services)

Test-Compose nutzt `<image>:test`, Live-Compose nutzt `<image>:latest` —
**strikt getrennt**. Direktes `docker compose build` ist für diese
Services verboten, weil es `:latest` überschreiben und die Trennung
umgehen würde.

## Bauen (Test-Tag setzen)

```bash
cd /opt/infrastructure/environment_a
scripts/build_image.sh <image> <context>
# z.B.:
scripts/build_image.sh langgraph-content langgraph/content/
```

- Schreibt `:<sha>` + `:test`, **nie** `:latest`.
- Verweigert dirty Working-Tree (Commit-Pflicht vor Build).

## Promoten (Test → Live)

```bash
cd /opt/infrastructure/environment_a
scripts/promote_image.sh <image>
```

- Fragt zur Sicherheit nach.
- Setzt vorher `:prev-<timestamp>` als Backup.
- Taggt `:test → :latest`.
- Recreated den Live-Container.

## Anti-Pattern

```bash
# NICHT TUN — würde :latest direkt überschreiben:
docker compose build <svc>
docker compose up -d --build <svc>
```

Für die 6 Services unter dem `build_image.sh`/`promote_image.sh`-
Regime gilt das ausnahmslos.

## Wann dieser Skill greift

- Code-Änderung in `langgraph/<service>/` o.ä. → neuer Image-Build nötig.
- Test-Container läuft mit neuem Image, soll nach Live.
- Verwirrung über `:test` vs. `:latest` vs. `:<sha>`.
