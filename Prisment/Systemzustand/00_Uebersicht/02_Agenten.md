---
typ: uebersicht
geltungsbereich: prisment
thema: agenten
stand: 2026-05-25
zweck: "Topologie + Pipeline-Loop der 5 langgraph-Agents. Sub-Übersicht zu 00_Bereich.md."
---

# Agenten — Prisment

> **Sub-Übersicht.** Detaillierter Einstieg siehe `00_Bereich.md`.

## In einem Absatz

Prisment hat **fünf langgraph-Agents**, die alle als FastAPI-Container
im `net_ai_pipeline`-Netz laufen und über `X-Agent-Secret` authentifiziert
werden. Vier bilden den **Pipeline-Loop** der Social-Media-Automation:
**Interview** erfasst Material vom Inhaber (Voice + Text, schreibt in
`sessions` + `voicedb_entries`), **Content** generiert daraus 3 Posts
pro Run in 8 Formaten (schreibt `posts`), **Analytics** misst nach
48-72h Boost-Würdigkeit und am Monatsende einen aggregierten Report,
und der **Lernkreislauf** schließt sich: Analytics liefert
`plan_adjustment_hints` an **Redaktionsplan**, der die monatlichen
Topics für Content festlegt. Der fünfte Agent **Helpdesk** ist
Standalone (Q&A-Pfad mit eigener RAG-DB für Tool-Dispatch und
Beratungsfragen, kein Teil der Posting-Pipeline). Mandanten triggern
Agents nicht direkt, sondern über die **PWA → pwa-api**, die als
zentraler Backend-Hub die HTTP-Calls macht und Cron-Trigger via
APScheduler steuert. Nach Abschluss jeder Agent-Aktion: `pwa-api
/internal/notify` mit einem typisierten Payload → `notifications`-Tabelle
→ PWA-Push + Email-Fallback. Mandantendaten landen in
**`customer_postgres`** (pgvector, DB `agent_data` mit `auth.*` +
`public.*` Schemas).

## Topologie als Bild (lokal)

![Agenten](02_Agenten.svg)

> SVG nur lokal. Substanz steht oben im Absatz + folgenden Tabellen.

## Die 5 Agents

| Agent | Container | LLM | Endpunkte | Rolle |
|---|---|---|---|---|
| **Interview** | `langgraph_interview` | Sonnet 4.5 | `/run /answer /format_choice` | Modi `weekly` + `breaking_news`. Material vom Inhaber sammeln, VoiceDB anreichern |
| **Content** | `langgraph_content` | Sonnet 4.5 | `/run /revise` + Demo/Onboard-Endpoints | 3 Post-Drafts pro Run, 8 Social-Formate, voice-check + CTA-aware |
| **Redaktionsplan** | `langgraph_redaktionsplan` | Sonnet 4.5 | `/run /revise /confirm` | Monatliche Themenplanung, nimmt `plan_adjustment_hints` von Analytics auf |
| **Analytics** | `langgraph_analytics` | Haiku 4.5 | `/run` (`mode=boost_check` / `monthly_report`) | Boost-Würdigkeit nach 48-72h, Monatsreport, `plan_adjustment_hints` |
| **Helpdesk** | `langgraph_helpdesk` | Haiku 4.5 | `/classify /tools/{name} /ingest /healthz` | Standalone Q&A-Pfad, Intent + RAG + Tool-Dispatch |

Alle Agents im Netz `net_ai_pipeline`. Auth durchweg `X-Agent-Secret`.

## Pipeline-Loop (Lernkreislauf)

```
PWA → pwa-api (APScheduler oder User-Aktion)
        │
        ▼ /run
Interview (Material erfassen)
        │
        ▼ Material in sessions + voicedb_entries
Content (3 Posts generieren)
        │
        ▼ Posts → Veröffentlichung durch Inhaber
Analytics (nach 48-72h: boost_check; monatlich: monthly_report)
        │
        ▼ plan_adjustment_hints
Redaktionsplan (passt Themen-Mix an für nächsten Monat)
        │
        ▼ Topics
Content (neuer Run)
```

Alle Schritte schreiben in `customer_postgres` und triggern PWA-Notify
nach Abschluss (Notification-Types siehe unten + `04_PWA.md`).

## Helpdesk (Standalone)

Nicht Teil der Posting-Pipeline. Beantwortet Beratungsfragen via RAG
über eigene Knowledge-Base + dispatcht Action-Tools nach
User-Bestätigung.

- `/classify` → Intent + Konfidenz + RAG-Antwort / Action-Vorschlag
- `/tools/{name}` → Action-Tool ausführen
- `/ingest` → Knowledge-Base neu indizieren

Eigene Postgres-Tabellen für RAG-Chunks + Audit. Detail-Doku
rudimentär in PRIS-016 Bündel 6.

## Notify-Rückweg

Jeder Agent ruft nach Abschluss `pwa-api /internal/notify` mit einem
`notification_type`:

- `post_pending` — neuer Post-Draft (von Content nach `save_post_draft`)
- `post_revised` — Revision fertig (von Content nach `/revise`)
- `boost_recommended` — Boost-Flag gesetzt (von Analytics)
- `monthly_report_ready` — Monatsbericht generiert (von Analytics)
- `interview_question` — nächste Frage (von Interview)
- `interview_follow_up` — Follow-up-Frage (gebündelt, von Interview)
- `legal_blocked` — Compliance-BLOCK (von Content)

`pwa-api` schreibt in `notifications`-Tabelle, sendet PWA-Push und
fällt nach `BUNDLE_WINDOW_SECONDS=60` auf Email zurück (für
`EMAIL_FALLBACK_TYPES`, falls keine Push-Bestätigung kam). Detail in
`04_PWA.md`.

## Detail-Dokus pro Agent

| Agent | Detail-Doku |
|---|---|
| Content | [`../Architektur/agents/langgraph-content.md`](../Architektur/agents/langgraph-content.md) |
| Interview | [`../Architektur/agents/langgraph-interview.md`](../Architektur/agents/langgraph-interview.md) |
| Analytics | [`../Architektur/agents/langgraph-analytics.md`](../Architektur/agents/langgraph-analytics.md) |
| Redaktionsplan | **fehlt** — eigene Detail-Doku steht aus (Backlog-Punkt) |
| Helpdesk | `langgraph-helpdesk.md` — rudimentär in PRIS-016 Bündel 6 |
| Content (Ablauf) | [`../Architektur/agents/CONTENT_BOT_ABLAUF.md`](../Architektur/agents/CONTENT_BOT_ABLAUF.md) |
| Content (Playbook) | [`../Architektur/agents/CONTENT_BOT_PLAYBOOK_INTEGRATION.md`](../Architektur/agents/CONTENT_BOT_PLAYBOOK_INTEGRATION.md) |
| Interview (V6 Spec) | [`../Architektur/agents/INTERVIEW_AGENT_V6_SPEC.md`](../Architektur/agents/INTERVIEW_AGENT_V6_SPEC.md) |
| Interview (V6 Runbook) | [`../Architektur/agents/INTERVIEW_V6_RUNBOOK.md`](../Architektur/agents/INTERVIEW_V6_RUNBOOK.md) |

## AI-Pipeline (Plattform)

Alle Agents nutzen die Plattform-AI-Pipeline:
- **Anthropic API** für LLMs (Sonnet für Pipeline-Arbeit, Haiku für
  Klassifikation/Reports)
- **OpenWebUI** für RAG-Indexierung (Voice-DB-Suche, Helpdesk-Knowledge)
- **internal_ollama** für lokale Embeddings (`mxbai-embed-large`)

Detail: [`../../../Plattform/Systemzustand/00_Uebersicht/03_Geteilte-Dienste.md`](../../../Plattform/Systemzustand/00_Uebersicht/03_Geteilte-Dienste.md) (Cluster B AI-Pipeline).
