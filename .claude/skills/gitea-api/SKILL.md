---
name: gitea-api
description: Gitea-API vom Host nutzen — Token, Container-IP, API-Basis, Repo-Operationen, optionale Repo-Erstellung. Trigger wenn der Nutzer mit der internen Gitea-Instanz arbeiten will (Stichworte "gitea", "Gitea-API", "internal_gitea", "Repo lesen/schreiben", "git.prisment.de API", "Datei via API in Gitea", "git via curl", "Gitea-Token", "neuen Token generieren", "API-Aufruf gegen Gitea"). Klärt Token-Pfad, IP-Resolve und Repo-Erstellungs-Verfahren.
---

# Skill: Gitea API Zugang (vom Host)

## Token

- Pfad: `/opt/infrastructure/environment_a/secrets/gitea_token`
- Scope: `write:repository` — kann Dateien lesen/schreiben, **keine Repos erstellen**.

```bash
TOKEN=$(cat /opt/infrastructure/environment_a/secrets/gitea_token)
```

## Container-IP ermitteln (Hostname intern-only)

`internal_gitea` ist nur Docker-intern auflösbar. Vom Host die IP holen:

```bash
GITEA_IP=$(docker inspect internal_gitea \
  --format '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' \
  | awk '{print $1}')
```

Gitea läuft auf Port **3000**.

## API-Basis

```
http://{container_ip}:3000/api/v1
```

Beispiel (Repo-Inhalt lesen):

```bash
curl -s -H "Authorization: token $TOKEN" \
  "http://$GITEA_IP:3000/api/v1/repos/admin/knowledge-base/contents/CLAUDE.md"
```

## Repo-Erstellung (nur wenn Task Brief explizit)

Der reguläre Token kann **keine** Repos erstellen. Temporären Admin-Token
generieren:

```bash
docker exec -u git internal_gitea gitea admin user generate-access-token \
  --username admin --token-name <name> --raw \
  --scopes "write:repository,write:organization,write:user"
```

Nach Repo-Anlage Token **sofort** im Gitea-UI löschen:
`git.prisment.de → Settings → Applications`.

## Wann dieser Skill greift

- API-basierte Datei-Operationen gegen ein internes Repo.
- Repo-Erstellung (mit explizitem Auftrag).
- Diagnose, warum `git push` über SSH (Port 2222) nicht der richtige Weg ist
  (für Datei-Edits → API; für Code-Commits → SSH).
