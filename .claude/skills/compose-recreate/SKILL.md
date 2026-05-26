---
name: compose-recreate
description: Pflicht-Recreate nach jedem Compose-Topologie-Edit + Drift-Check. Trigger wenn der Nutzer docker-compose.yml geändert hat oder Netzwerk-/Volume-/depends_on-/Labels-/ports-Drift vermutet (Stichworte "docker-compose.yml geändert", "compose edit", "networks geändert", "volume hinzugefügt", "depends_on", "force-recreate", "compose drift", "container nimmt config nicht an", "compose-drift-check", "networks-drift", "latent kippt", "PLAT-001", "E19"). Verhindert latente Lifecycle-Drift.
---

# Skill: Pflicht-Recreate nach Compose-Topologie-Edit

## Wann recreaten

Nach jedem Edit in `/opt/infrastructure/environment_a/docker-compose.yml`,
der **Container-Topologie** ändert:

- Networks
- Volumes
- `depends_on`
- Labels
- Ports

→ **Sofort** recreate, nicht auf nächsten Daemon-Restart warten — sonst
lebt der alte Lifecycle-Zustand im Container weiter und kippt latent
(PLAT-001 Bündel 3.1 / Logbuch E19).

```bash
docker compose -p environment_a up -d --force-recreate <svc>
```

Reine **Image-Tag-Wechsel** decken bereits `promote_image.sh` ab — kein
extra Recreate nötig.

## Drift-Check auf Abruf

```bash
/opt/infrastructure/environment_a/scripts/compose-drift-check.sh
```

- Exit 0: sauber.
- Exit 1: Network-Drift zwischen laufendem Container und Compose-Datei,
  mit Fix-Befehl pro Service in der Ausgabe.

## Wann dieser Skill greift

- Eigenes oder fremdes Compose-Edit ist gerade passiert.
- Symptom „Container ignoriert neue Network-/Volume-Config".
- Periodischer Sanity-Check, ob Live-Topologie und Compose-Datei
  übereinstimmen.
