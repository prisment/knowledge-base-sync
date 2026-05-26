---
name: n8n-zugang
description: n8n-API-Zugang, Workflow-Versionierung und Credential-Regel. Trigger wenn der Nutzer mit n8n arbeitet (Stichworte "n8n API", "n8n workflow", "workflow exportieren", "workflow importieren", "workflow aktivieren", "n8n API key", "n8n credentials", "workflow JSON", "internal_n8n"). Klärt API-URL, Token-Pfad, Workflow-Ablage und die harte Credential-Regel.
---

# Skill: n8n Zugang

## API

- **URL:** `http://internal_n8n:5678/api/v1`
- **API Key:** `/run/secrets/n8n_api_key` (Docker Secret, im Container
  verfügbar; vom Host via `docker exec` lesen oder Postgres-Lookup,
  siehe `n8n-debugging`-Skill).

## Workflow-Versionierung

- Workflows liegen als JSON unter
  `/opt/infrastructure/environment_a/n8n/workflows/`.
- **Nach jeder Änderung:** JSON exportieren + committen.
- **Workflow-IDs** nach Import in der projekt-passenden Dokumentation
  festhalten (nicht in der globalen CLAUDE.md).

## Harte Regel für Credentials

**Credentials werden NIE per API angelegt — nur manuell in der n8n UI.**

Grund: API-Anlage umgeht die UI-Encryption-Pfade und produziert
inkonsistente Zustände. Bei jeder Versuchung „ich schreibe den Cred
schnell per Script rein": nein.

## Wann dieser Skill greift

- Workflow neu anlegen, exportieren, importieren, aktivieren.
- API-URL/Token nicht zur Hand.
- Versuchung, Credentials zu skripten — Stopp und an die UI verweisen.
