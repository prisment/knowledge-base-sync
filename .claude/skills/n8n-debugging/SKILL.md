---
name: n8n-debugging
description: Vier n8n-Debugging-Patterns — Execution-Daten per API inspizieren, JSON-Array-Splitting im Code-Node, Workflow-Patches per Script, Browser-Tab-Cache-Problem. Trigger wenn der Nutzer einen n8n-Bug oder unerwartetes Workflow-Verhalten diagnostiziert (Stichworte "n8n debug", "execution inspizieren", "execution daten", "Code Node liefert nur ein Item", "JSON Array splitting", "workflow patch script", "browser cached alten workflow", "Workflow nach Patch verloren", "n8n execution API", "HTTP Request Node Array"). Sammelt die vier wiederkehrenden Stolpersteine an einem Ort.
---

# Skill: n8n Debugging Patterns

Vier wiederkehrende Stolpersteine. Pattern erkennen → direkt das
passende Verfahren.

## 1. Execution-Daten per API inspizieren

Wenn ein Node unerwartete Werte liefert: erst die **echten Laufzeit-
Daten** holen, nicht aus dem UI raten.

```bash
TOKEN=$(docker exec internal_postgres psql -U n8n_user -d n8n -t -c \
  "SELECT \"apiKey\" FROM user_api_keys WHERE label='Internal N8n' LIMIT 1;" \
  | tr -d ' ')

curl -s -H "X-N8N-API-KEY: $TOKEN" \
  "http://172.20.0.8:5678/api/v1/executions/{id}?includeData=true" \
  | python3 -m json.tool | less
```

Execution-ID: steht in der n8n-UI in der Execution-Liste.
`includeData=true` zeigt die tatsächlichen Werte jedes Nodes.

## 2. JSON-Array-Splitting in HTTP Request Nodes

HTTP Request Nodes mit JSON-Array-Response splitten das Array in
**separate Items** (ein Item pro Element). Im nachfolgenden Code-Node
nicht das erste Item nehmen, sondern alle:

```js
// Falsch — liefert nur ein einzelnes Objekt:
$('Node').first().json

// Richtig — rekonstruiert das Array:
$('Node').all().map(item => item.json)
```

Gilt **immer**, wenn Gitea-API oder Agent-API eine Liste zurückgibt.

## 3. Workflow-Patches per Python-Script

Workflow-Änderungen, die **strukturelle Nodes** betreffen (neue Nodes,
veränderte Connections) **nicht** direkt im JSON editieren. Stattdessen:

1. Python-Script unter `/home/claude-deploy/patch_*.py` schreiben.
2. Patch per API einspielen.
3. Workflow per API exportieren und in
   `05_Entwicklung/n8n-workflows/` (bzw. der aktuellen
   Workflow-Versionierung) committen.

## 4. Browser-Tab-Cache-Problem nach API-Patches

Nach API-Patches den Workflow **nicht** im Browser öffnen und speichern
— der Browser cached den alten Zustand und überschreibt die API-
Änderungen beim nächsten Save.

Vorgehen:
1. API-Patch einspielen.
2. **Erst nach Browser-Refresh** den Workflow öffnen.
3. Nicht speichern, bis der Zustand bekannt + bestätigt ist.

## Wann dieser Skill greift

- Workflow zeigt unerwartetes Verhalten an einem Node.
- Diskrepanz zwischen UI-Anzeige und Laufzeit-Daten.
- Strukturelle Änderung an Workflow ansteht.
- Workflow „verliert" nach Save plötzlich Änderungen.
