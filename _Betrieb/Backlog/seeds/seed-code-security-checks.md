---
typ: backlog_seed
titel: "Code-Security-Standards + automatisierte Checks für Prisment-Produkt-Code (Next.js + LangGraph)"
geltungsbereich: plattform
risikoklasse: kritisch
status: offen
prioritaet: HOCH
klasse: security
zugkraft: jetzt
stufe: spur
beruehrt: ["pwa/", "langgraph/", "admin/admin_web/"]
stand: 2026-05-25
erzeugt_durch: Chat-Architekt (nach Next.js-Sicherheitsvorfall, Security/Integrity-Woche)
---

## Anlass — Sicherheitsvorfall (Post-Mortem-Verweis, weil nicht sauber dokumentiert)

In der Woche vor dem 2026-05-25 wurde der Mensch Opfer eines Angriffs, der eine
**veraltete Next.js-Version** ausnutzte. Die Netzwerksicherheit (Cloudflare-
Schicht) bewahrte vor Schlimmerem — der Angriff kam nicht voll durch, aber die
Lücke war real und ausgenutzt.

**Wichtig — Dokumentations-Lücke:** Dieser Vorfall passierte VOR dem neuen
Framework und wurde NIE sauber als Post-Mortem festgehalten. Das WARUM und der
genaue Hergang existieren nur noch im Kopf des Menschen. Erste Pflicht-Aufgabe
der späteren Spec (oder schon der Faktensammlung) ist deshalb, das Post-Mortem
zu rekonstruieren, solange erinnerbar:
- Welche Next.js-Version war installiert, welche CVE wurde ausgenutzt?
  (Vermutlich CVE-2025-55182-Umfeld — derselbe CVE, der laut Logbuch-Kontext
  das gesamte Framework auslöste; verifizieren, nicht annehmen.)
- Was genau hat der Angreifer erreicht / versucht?
- Was hat Cloudflare konkret abgefangen (welche Regel/Schicht)?
- Warum war die Version veraltet — fehlender Update-Pfad für Produkt-Code
  (vgl. der n8n-Drift-Befund in `seed-diun-rueckbau.md` Nachtrag: Custom-Builds
  fallen aus der Update-Pipeline)?

Ohne dieses Post-Mortem fehlt der Beweis-Anker für die ganze Security-Arbeit.

## Einordnung in die bereits laufende Härtung

Security wird auf mehreren Ebenen gehärtet. Bereits unternommen / laufend:
- **Netz/Edge:** Cloudflare-Migration, WAF-Policy je Host (Logbuch E26),
  Brand Protection (`seed-brand-protection-saved-queries.md`).
- **Daten:** Tenant-Isolation + RLS (Security-Refactor, der das Framework
  mitauslöste).
- **Plattform/OS:** PLAT-008/-009, nightly-Update-Pipeline (in Überarbeitung).

**Diese Lücke:** der **Produkt-Code selbst** (Next.js-PWA + LangGraph-Python).
Die Edge schützt, aber der Code dahinter hatte eine ausnutzbare Altlast. Genau
diese Ebene deckt dieser Zyklus ab.

## Scope-Abgrenzung (bewusst)

- **DRIN:** Prisment-Produkt-Code — Next.js-PWA (Web/API) + LangGraph-Python-
  Agents + zugehörige Dependencies.
- **DRAUSSEN:** Plattform-/Infra-Härtung (läuft bereits separat über die
  Plattform-Roadmap). Sonst entstünde eine zweite, konkurrierende Security-
  Roadmap. Berührungspunkte (z. B. Andockung an den nightly) werden referenziert,
  nicht hier mit-gebaut.

## Ziel (Soll-Zustand, grob)

Drei zusammenhängende Bausteine:

1. **Standard-Bestandsaufnahme (eigene Session, wie vom Menschen gewünscht).**
   Welche bewährten Security-Standards gelten für Next.js speziell und für
   Software-Produkte generell (z. B. OWASP Top 10 / OWASP ASVS, Next.js-
   Security-Best-Practices, Dependency-Hygiene, Secret-Handling). Pro Standard:
   was ist bei Prisment bereits erfüllt, was fehlt, wo muss der Code angepasst
   werden. Ergebnis ist die Faktensammlung + der Standard-Katalog.

2. **Automatisierte Checks im Produkt-CI.** Sowohl Dependency-Audit
   (`npm audit` / Dependabot für Next.js, `pip-audit` für Python) ALS AUCH
   SAST (z. B. Semgrep / CodeQL) — der Mensch will beides. Ein veraltetes
   Dependency wie das Next.js, das den Vorfall verursachte, muss künftig
   automatisch + früh auffallen.

3. **Laufender Pflege-Prozess** („Programm-Handbuch" / Security-Standard-Katalog
   des Produkts). Der Prozess hält die Standards und die Prüfungen aktuell und
   prüft den Code laufend dagegen. Setzt bewusst auf das bestehende Pflege-
   Muster auf (siehe „Bezug" unten) — kein neu erfundener Mechanismus.

## Andockung an den nightly (Option, vom Menschen begrüßt)

Der nightly macht bereits OS/Container-Updates + CVE-Korrelation, läuft aber
aktuell noch nicht sauber (steht ohnehin zur Überarbeitung). Der Produkt-
Dependency-Scan (npm/pip) könnte dort andocken statt isoliert im Produkt-CI zu
leben — Vorteil: eine Update-/CVE-Pipeline statt zwei Orte für dieselbe Sache.
**Offene Frage für die Spec:** andocken vs. bewusst getrennt im Produkt-CI
(CI fängt's beim Commit, nightly fängt neu bekannt gewordene CVEs an bereits
gemergtem Code — evtl. beide Ebenen, klar abgegrenzt). Reihenfolge mit der
nightly-Überarbeitung klären.

## Offene Fragen für die spätere Spec (Faktensammlung-Material)

- **Welche Standards konkret anwendbar:** OWASP-Familie, Next.js-spezifisch,
  Supply-Chain (pinned versions — vgl. Verfassung 05 Lieferketten-Hygiene,
  gilt bisher OS-seitig, hier auf Produkt-Dependencies erweitern).
- **SAST-Tool-Wahl:** Semgrep vs. CodeQL vs. anderes — Kosten, Sprach-Abdeckung
  (JS/TS + Python), CI-Integration, False-Positive-Last.
- **Wo lebt das CI:** Gitea-Actions (eigenes Gitea laut Logbuch E26) vs. extern?
  Bestimmt, wie die Scanner andocken.
- **Update-Pfad für Produkt-Code:** Wie wird sichergestellt, dass Dependencies
  künftig nicht wieder still veralten (die eigentliche Vorfall-Ursache)? Verweis
  auf den Custom-Build-Drift-Befund (`seed-diun-rueckbau.md` Nachtrag).
- **Severity-Schwellen + Reaktion:** Welche CVE-Severity blockt einen Build,
  welche alarmiert nur? (analog Risikoklassen-Logik Verfassung 04).

## Bezug zu bestehendem Pflege-Muster (kein neuer Mechanismus)

Baustein 3 setzt bewusst auf:
- **Phase-9-Pflicht-Tor + Drift-Check** aus `ROADMAP_lebende-bereichs-doku`
  Schritt 8 — Standard-Katalog wird im selben Commit mitgepflegt, nightly/CI
  prüft Drift.
- **`seed-skills-evaluieren`:** Wiederkehrende, fehleranfällige Security-
  Verfahren (z. B. „Dependency sicher updaten + verifizieren") sind Skill-
  Kandidaten beim dritten Auftreten.
- **Verfassung 05 (Lieferketten-Hygiene):** bisher OS-/Container-seitig
  formuliert — dieser Zyklus erweitert das Prinzip auf Produkt-Dependencies.

## Verhältnis zur Verfassung

- `03_Code-Standards.md` ist Platzhalter und wächst aus realen Zyklen; der
  Abschluss dieses Zyklus speist die Security-Standards des Produkt-Codes ein
  (parallel zum Data-Integrity-Seed, der die Integrity-Standards beisteuert —
  beide Zyklen füllen denselben Verfassungs-Platzhalter aus verschiedenen
  Richtungen).

## Stufe / Risiko

**Spur, zwingend.** Security-architektur-gestaltend, berührt Produktiv-Code +
Auth-nahe Pfade, prägt einen Verfassungs-Standard, hat einen realen
Angriffs-Anlass. Risikoklasse `kritisch` → erzwingt Spur (Verfassung 00).
Die vom Menschen gewünschte „eigene Session zur Standard-Findung" ist die
Faktensammlung (Phase 2) dieses Spur-Zyklus.

## Trigger

HOCH priorisiert — aktiv ausgenutzte Lücke ist der bewiesene Schmerz. Die
Standard-Findungs-Session (Faktensammlung) kann zeitnah starten; Reihenfolge
gegen den Data-Integrity-Seed klären (beide berühren denselben Agent-Code —
sinnvoll, die Faktensammlungen zu koordinieren, evtl. Security zuerst, weil
akuter Anlass).
