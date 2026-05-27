---
typ: backlog_seed
titel: "Zentraler Error-Sammler + wiederkehrende Security-Checks-Schedule"
geltungsbereich: alle
risikoklasse: sicher
status: offen
klasse: qualitaet
zugkraft: bald
mission:         security-haertung-plattform
stufe: spur
beruehrt: ["pwa/pwa-api/", "langgraph/", "n8n/", "_Betrieb/"]
stand: 2026-05-25
erzeugt_durch: ROADMAP_SMA-Zerlegung („Späterer Punkt — Error-Reporting + Security-Checks-Schedule")
---

## Anlass

Heute kein systematischer Error-Sammler. Errors stehen verstreut in
Container-Logs — niemand sieht sie aktiv. Beim Auth-/Schema-Bug am
2026-05-13 (`relation "posts" does not exist`) fiel das erst im User-Test
auf, obwohl im Container-Log dokumentiert.

Parallel: Security-Härtung-Items aus der laufenden HAERTUNGS_SPEC und
diversen Seeds (LangSmith-Tracing-Status, n8n-Retention, Secret-Rotation,
Gitea-Token-Lebensdauer, Foundation-Stack-Drift, Push-Subscription-
Cleanup, Voice-DB-Source-Tag-Audit) werden einmal angefasst und
vergessen. Keine Drift-Detection.

## Ziel (Soll-Zustand, grob)

1. **Zentraler Error-Sammler.**
   - Container-Logs (pwa-api, 5 LangGraph-Agents, n8n) in einen Stream
   - 500er + Tracebacks in ein Dashboard (Loki/Grafana ODER simpler
     Python-Sink mit Datei-Aggregation — Architektur-Weiche)
   - Push an Architekt via pwa-api `/internal/notify`
     type=admin_alert bei kritischen Errors (Email-Fallback existiert)
2. **Wiederkehrende Security-Checks-Schedule** (täglich/wöchentlich):
   - LangSmith-Tracing-Status (Pseudonymisierung aktiv?)
   - n8n Execution-Aufbewahrungs-Frist (Retention konfiguriert?)
   - Secret-Rotation-Erinnerungen (Token-Alter, Cert-Ablauf)
   - Gitea-Token-Lebensdauer
   - Foundation-Stack unverändert (Traefik darf nicht angefasst werden)
   - Push-Subscription-Tabelle bereinigen (abgelaufene VAPID-Subs)
   - Voice-DB-Source-Tag-Audit (welche Anker kommen aus welchen
     Quellen — Test-Tags rausfiltern)
3. **Daily-Report** via pwa-api `/internal/notify` type=health_report
   (mit Email-Fallback wenn Push fehlt).

## Implementierungs-Optionen

- **APScheduler in pwa-api** — analog zu bestehenden Cron-Jobs nach
  n8n-Cron-Migration. Vorteil: Python-nativ, läuft im selben Container
  wie die Notify-Schicht.
- **Claude Code im Schedule-Modus** — Claude-Agent fährt 1× täglich los,
  prüft Akzeptanzkriterien, schreibt Bericht. Vorteil: kann tatsächlich
  Code lesen + bewerten. Nachteil: API-Cost + Tooling noch nicht
  erprobt. (Berührt Frame der nachtaufgaben-Mechanik in Plattform.)
- **Python-Script + systemd-timer** — simpel, eigene Pflege.

## Verhältnis zu bestehenden Mechaniken

- **nightly-Pipeline (Plattform):** macht heute OS-/Container-Updates +
  CVE-Korrelation. Security-Checks aus diesem Seed könnten dort
  andocken statt isoliert zu laufen — eine Reihe von Checks statt zwei
  Orte für dieselbe Sache. Klärung in der Spec gegen die nightly-
  Überarbeitung.
- **Verfassung 04 Sicherheits-Prinzipien:** Drift-Checks sind das
  natürliche Verfassungs-Glied.
- **`seed-skills-evaluieren.md`:** wiederkehrende, fehleranfällige
  Security-Verfahren werden bei drittem Auftreten Skill-Kandidat.

## Offene Fragen für die spätere Spec

- **Sammler-Architektur:** Loki/Grafana (Heavy, schöne UI) vs. simpler
  Python-Sink (leichtgewichtig, an Notify-Schicht koppelbar)?
- **Daily-Report-Inhalt:** Aggregat nur, oder mit Drill-Down-Links zu
  Einzel-Errors?
- **Andockung an nightly:** beide Ebenen klar abgrenzen (Build/CI vs.
  Runtime/Drift) oder konsolidieren?
- **Wann triggert Push vs. Email:** Severity-Schema definieren.

## Stufe / Risiko

**Spur.** Cross-cutting Observability-Schicht, berührt alle Container +
Notify-Pfad. Risikoklasse `sicher` (reine Beobachtung, keine
Eingriffslogik).

## Trigger

Bald — nach erstem Multi-Mandanten-Betrieb. Bei einem Live-Tenant sieht
man Errors auch ohne Sammler; ab Tenant #3 wird die Latenz schmerzhaft.
