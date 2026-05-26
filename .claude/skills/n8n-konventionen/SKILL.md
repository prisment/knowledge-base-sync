---
name: n8n-konventionen
description: Pflicht-Bausteine jedes neuen n8n-Workflows — Workflow-Info-Header, Error-Trigger, Webhook-Authentication. Trigger wenn der Nutzer einen neuen Workflow baut oder einen bestehenden review't (Stichworte "neuer workflow", "workflow anlegen", "workflow review", "Error-Trigger", "Webhook ohne Auth", "Workflow Info Node", "Header-Node", "n8n best practice"). Verhindert ungeschützte Webhooks und stille Error-Schluck-Workflows.
---

# Skill: n8n Workflow-Konventionen

Pflicht-Bausteine für jeden Workflow:

## 1. Header-Node "Workflow Info"

Jeder Workflow startet (oder enthält an prominenter Stelle) einen
Node namens **"Workflow Info"** mit Name + kurzer Beschreibung des
Workflow-Zwecks. Read-only für menschliche Lesbarkeit beim Review.

## 2. Error-Handling

Jeder Workflow hat einen **Error-Trigger-Node**. Keine Ausnahmen —
sonst verschwinden Fehler stillschweigend.

## 3. Webhook-Authentication

Webhook-Nodes haben **immer** Authentication aktiviert:

- Verfahren: **Header Auth**.
- Secret aus **ENV-Variable**, nie hardgecodet im Workflow.

Webhooks ohne Auth sind nicht erlaubt — auch nicht „temporär zum Testen".

## Wann dieser Skill greift

- Neuer Workflow wird gerade gebaut.
- Code-Review eines bestehenden Workflows.
- Ein Webhook-Node ohne Authentication wird vorgeschlagen → Stopp.
