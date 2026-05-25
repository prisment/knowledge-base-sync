---
typ: backlog_seed
titel: "DSGVO-Konformität SMA — Sammel-Spec vor erstem zahlenden Mandanten"
geltungsbereich: prisment
risikoklasse: kritisch
status: offen
klasse: security
zugkraft: jetzt
stufe: spur
beruehrt: ["langgraph/", "pwa/pwa-api/", "n8n/"]
stand: 2026-05-25
erzeugt_durch: ROADMAP_SMA-Zerlegung (Schritt 19.1–19.9, DSGVO-Analyse 2026-05-09)
---

## Anlass

Aus der DSGVO-Analyse 2026-05-09: das System ist **nicht DSGVO-konform**.
Mehrere Befunde verschiedener Tiefe müssen vor Echtbetrieb mit zahlenden
Mandanten adressiert sein. Heute steht nur die Mandanten-Datenschutz-
erklärung (19.3 ✅, Korbinian 2026-05-13) — alles andere ist offen.

Die alte Roadmap führte die 9 Befunde einzeln, aber sie hängen technisch
und organisatorisch zusammen (Sub-Processor-Inventar, Pseudonymisierungs-
Layer, Retention-Konfigs) — werden hier zu **einem Spur-Zyklus**
gebündelt, damit AVV/DSFA aus einer Hand stehen.

## Befunde (gebündelt)

### Kritisch (vor Go-Live, sofortiger Handlungsbedarf)

- **19.1 LangSmith-Tracing pseudonymisieren** (Entscheidung 2026-05-13:
  LangSmith bleibt aktiv, Pseudonymisierung statt Deaktivierung). Felder
  vor LLM-Call durch Tokens ersetzen (Mandanten-Name → stabiler Hash,
  authentic_phrases sanitised, qa_pairs-Personenfelder durch Marker).
  Mapping-Tabelle pro Tenant in customer_postgres, alle 4 Agents.
- **19.2 Anthropic API DPA** klären — Standard-Terms erlauben ggf.
  Training-Nutzung. Optionen: (A) Business-DPA abschließen, (B)
  Pseudonymisierung der PD vor API-Call (überlappt mit 19.1).
  Subprozessor in AVV listen.

### Mittel (umzusetzen nach Go-Live, spätestens Monat 1)

- **19.4 n8n PostgreSQL Execution-Purging** (Retention 30 Tage,
  historische Telegram-Logs mit-bereinigen).
- **19.5 Docker-Log-Rotation** (max-size 10m, max-file 3 in compose) +
  Audit ob sensitive Inhalte aus Log-Output entfernt werden müssen.
- **19.6 Gitea Session-WIP-State Expiration** (Auto-Löschung nach
  Session-Abschluss + Bereinigung WIP > 30 Tage).
- **19.7 OpenWebUI Offboarding-Prozess** dokumentieren (Collection-IDs,
  Gitea-Repos, n8n-Workflows). Hinweis: OpenWebUI ist als RAG-Backend
  faktisch raus aus den Agents (Chat-First-Migration Variante A live),
  aber die Onboarding-Files liegen weiter im OpenWebUI-Storage — der
  Lösch-Prozess beim Offboarding fehlt trotzdem.

### Architekturell (langfristig, optional je nach Modell)

- **19.8 DPIA (Art. 35 DSGVO)** — Entscheidung treffen ob Profiling-Daten
  ausreichend hochrisikoreich sind. Formales Dokument (Zweck, Risiken,
  Mitigationen).
- **19.9 Prompt Redaction** — entfällt voraussichtlich wenn 19.2 via DPA
  gelöst ist. Sonst: `display_name` aus Prompts neutral formulieren, PD
  nur im Output-Rendering ergänzen.

## Verzahnung mit Meta-API-Seed

Sobald `seed-sma-meta-api-integration` aktiviert wird, kommt **Meta als
weiterer Sub-Processor** dazu. AVV/DSFA müssen ihn dann mitführen
(Mandanten-Daten via Graph API). Reihenfolge: DSGVO-Sammel **vor**
Meta-API-Aktivierung, oder Meta-Block in dieser Sammel-Spec
mit-bedienen.

## Offene Fragen für die spätere Spec

- **Pseudonymisierungs-Helper:** shared module für alle 4 Agents (DRY,
  aber zusätzliche Dependency-Schicht) vs. duplizierte Helper pro Agent
  (Pattern wie heute bei `db_sync.py`)?
- **Mapping-Tabelle-Persistenz:** customer_postgres pro Tenant, oder
  ephemer pro Call (kein Cache-Leak zwischen Tenants, aber LLM-Output-
  Re-Tokenisierung wird harder)?
- **DPIA-Verpflichtung:** vor erstem zahlenden Mandanten zwingend, oder
  beim ersten Risiko-Indikator (z.B. höhere Tenant-Anzahl, neue
  PD-Kategorie)? Juristische Bewertung holen.
- **Reihenfolge Pseudonymisierung vs. DPA:** DPA macht
  Pseudonymisierung in Teilen optional — aber Pseudonymisierung ist
  Defense-in-Depth und sollte trotzdem kommen.

## Stufe / Risiko

**Spur, zwingend.** Compliance-/Sicherheits-architektur-gestaltend,
berührt alle 4 Agents + Logging-Pfad + Sub-Processor-Inventar. Risiko-
klasse `kritisch` (Sicherheit & Zugang + Daten-Integrität — Listen-
Treffer in Verfassung 01) → erzwingt Spur. Compliance-Pflicht-Tor.

## Trigger

JETZT — Voraussetzung für ersten zahlenden Mandanten. Ohne dieses
Bündel ist jeder bezahlte Onboarding-Schritt ein DSGVO-Risiko.
