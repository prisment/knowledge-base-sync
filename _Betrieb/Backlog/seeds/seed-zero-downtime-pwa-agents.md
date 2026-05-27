---
typ: backlog_seed
titel: "PWA + Agents: Update ohne Memory-Verlust für offene Kundensitzungen"
geltungsbereich: prisment
risikoklasse: kritisch
status: offen
klasse: prozess
zugkraft: irgendwann
mission:         ""
stufe: spur
beruehrt: ["pwa/pwa-web", "pwa/pwa-api", "langgraph/*"]
stand: 2026-05-27
erzeugt_durch: "PLAT-026 Spec-Diskussion 2026-05-27 — aus Apply-Autonomie ausgelagert"
abhaengig_von:
  - "[[Plattform/Arbeitsgedaechtnis/PLAT-026_SPEC]]"
---

## Anlass

PLAT-026 schaltet nächtliches Auto-Apply für pwa-* und langgraph-*-Container.
Architekt-Frage: was passiert mit offenen Kundensitzungen (Prisment-Chat im
Browser, laufender Agent-Stream), wenn Container restartet wird?

**Heutiger Stand (Architekt-bestätigt nicht dringend):**
- LangGraph-Checkpointer speichert Agent-State persistent in Postgres → Memory
  überlebt Restart.
- Browser hält JWT/Cookie → Auth überlebt Restart.
- ABER: aktive SSE/WebSocket-Streams brechen. Nutzer sieht halbe Antwort,
  muss Frage neu stellen oder Page reloaden.
- Wahrscheinlichkeit nachts (03:30–04:00) gering, aber nicht null.

## Soll-Zustand (grob, wird in der Spec präzisiert)

- Drain-Mechanik vor Container-Stop: aktive Streams Toleranz-Frist geben
  (z. B. 30 s warten, ob Stream endet) bevor restart.
- Streaming-Resumption-Token: Client kann nach Stream-Bruch denselben Stream
  fortsetzen (LangGraph-Checkpoint + Resumption-Header).
- Optional: Rolling-Restart hinter Reverse-Proxy (zweite Container-Instanz
  hochziehen, Traffic schwenken, alte Instanz drainen). Architektur-Eingriff,
  nicht trivial.

## Voraussetzung

PLAT-026 abgeschlossen — sonst gibt es noch keinen Auto-Apply, der die Frage
relevant macht.

## Risiko-Klasse

Klasse C heute (akzeptables Restrisiko, weil seltenes Vorkommnis + manueller
Reload als Behelf). Wenn Plattform mehr Last bekommt → eskaliert zu B.

## Verweise

- PLAT-026 Spec: `Plattform/Arbeitsgedaechtnis/PLAT-026_SPEC.md`
- Architektur-Kontext: `Prisment/Systemzustand/Architektur/SMA_Operative_Doku.md`
