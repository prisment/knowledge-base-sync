---
typ: backlog_seed
titel: "RAG-Endzustand: pgvector-Hybrid (Variante C) — wenn Voice-Material zu groß für direkten Prompt"
geltungsbereich: prisment
risikoklasse: sicher
status: offen
klasse: qualitaet
zugkraft: irgendwann
mission: ""
stufe: spur
beruehrt: ["langgraph/content/", "langgraph/", "Plattform/Systemzustand/"]
stand: 2026-05-25
erzeugt_durch: ROADMAP_SMA-Zerlegung („Späterer Punkt — RAG-Endzustand: pgvector-Hybrid")
---

## Anlass

Heutiger Stand (nach Chat-First-Migration Schicht 4): OpenWebUI ist aus
allen 4 Agents raus (Variante A live). Voice-DB-Files
(mensch/sprache-ton/style-anchors/no-goes/personas) laufen komplett im
direkten Sonnet-Prompt mit. Solange Post-Archiv pro Tenant unter 100
Posts bleibt und die statische Voice-DB klein ist, ist das optimal.

**Trigger für diesen Zyklus:** Wenn ein Tenant >300 Posts mit
authentischen Phrasen hat UND der Token-Overhead in der regie_brief-
Generierung spürbar wird (Anthropic-Kosten oder Latenz). Heute nicht
absehbar, aber als Architektur-Endzustand vorgehalten, damit niemand
in Panik OpenWebUI wieder einführt.

## Ziel-Architektur (Variante C — Hybrid)

1. **Statische Voice-DB bleibt komplett im Prompt** (mensch,
   sprache-ton, style-anchors, no-goes, personas) — immer relevant,
   klein, ändert sich selten. Kein RAG nötig.
2. **Post-Archiv-Phrasen → pgvector-Index** in derselben
   `agent_data`-DB:
   - Extension: `CREATE EXTENSION vector;`
   - Spalte `embedding vector(384)` auf neue Tabelle `voice_phrases`
     oder direkt auf `posts.regie_brief.authentic_phrases`
   - Embedding-Modell: **lokales**
     `intfloat/multilingual-e5-small` (384-dim, deutsch, ~20ms CPU)
   - Embedding-Container: kleiner FastAPI-Service oder als Lib im
     langgraph_content
3. **Query in `node_generate_regie_brief`:**
   ```sql
   SELECT phrase FROM voice_phrases
   WHERE tenant_id=$1
   ORDER BY embedding <=> $2::vector
   LIMIT 10
   ```
4. **Embedding-Pflege:** beim INSERT eines neuen Posts →
   Background-Task schreibt Embedding mit. Kein separater Sync-Job.

## Was es eliminiert (vs. heute + vs. OpenWebUI früher)

- Keine externe Knowledge-Base mehr (kein OpenWebUI als RAG, keine
  Collection-IDs zu pflegen)
- Keine Ollama-Inferenz im RAG-Pfad
- Keine extra Datenbank — nutzt unser existierendes Postgres
- Embeddings sind synchron mit Posts (referentielle Integrität)

## Offene Fragen für die spätere Spec

- **Token-Schwelle als Trigger-Kriterium:** ab welchem
  Prompt-Token-Overhead aktiviert dieser Zyklus tatsächlich? (Aktuell:
  „spürbar in Kosten/Latenz" — präzisieren mit echten Messwerten
  sobald ein Tenant Richtung 300 Posts geht.)
- **Embedding-Container vs. Lib:** kleiner separater Service (bessere
  Isolation, leichter ersetzbar) vs. eingebaut in langgraph_content
  (weniger Container-Overhead).
- **Backfill-Strategie:** bestehende Posts via Background-Worker
  embedden — Reihenfolge nach Tenant oder global?
- **Index-Tuning:** `ivfflat` vs. `hnsw`, lists/m-Parameter?
- **Voice-Phrase-Granularität:** ganzer Post als Embedding, oder
  einzelne `authentic_phrases`-Items? Letzteres ist feiner, aber mehr
  Rows.

## Stufe / Risiko

**Spur.** Architektur-gestaltend (neuer DB-Mechanismus, neue
Background-Pflege, neuer Query-Pfad im kritischsten Agent). Risiko-
klasse `sicher` (additiv zur heutigen direkten Voice-DB — Fallback
bleibt der direkte Prompt-Modus).

## Trigger

Irgendwann — **nicht jetzt**. Aktivierung erst wenn die Token-Schwelle
in der Realität gerissen wird. Bis dahin: der Seed ist die schriftliche
Sicherung der Architektur-Entscheidung, damit niemand stattdessen
wieder OpenWebUI verbaut.

## Aufwand (Schätzung)

- pgvector-Setup + Schema-Migration: 1 h
- Embedding-Container oder lokale Lib: 2–3 h
- Backfill-Script für bestehende Posts: 1 h
- Integration in `node_generate_regie_brief`: 1–2 h

**Total: ~1 Tag**, plus Test-Phase.
