# Wegweiser — Plattform

Geltungsbereich: alles, was über Prisment hinaus weiterleben würde
(Infrastruktur, geteilte Dienste, Sicherheit). Abschalt-Test: „Würde auch ohne
ein einzelnes Projekt weiterexistieren (dient mehreren / der gemeinsamen Bühne)?"

Aktueller Wissensstand:
- [Systemzustand/Infrastruktur/](Systemzustand/Infrastruktur/)
- [Systemzustand/Geteilte-Dienste/](Systemzustand/Geteilte-Dienste/)
- [Systemzustand/Sicherheit/](Systemzustand/Sicherheit/) — enthält u. a.
  `nacht-aufgaben.md` (dauerhafte operative Sicherheits-Konfiguration, gespiegelt).

Laufender Zyklus:
- [Arbeitsgedaechtnis/](Arbeitsgedaechtnis/) — Specs, Reports, Abschluss-Dokus des
  aktiven Zyklus. Hier liegen auch die flüchtigen `nacht-report-<datum>.md`
  (Morgen-Reports der nächtlichen Routine).

Begründungen und Abgeschlossenes:
- Logbuch: zentral unter [`_Betrieb/Logbuch/ENTSCHEIDUNGS_HISTORIE_FRAMEWORK.md`](../_Betrieb/Logbuch/ENTSCHEIDUNGS_HISTORIE_FRAMEWORK.md) — seit E24 ein gemeinsames Logbuch für alle Bereiche.
- [Archiv/](Archiv/) — abgeschlossene Zyklen.

## Service-Registry — Pflicht vor jedem Container-/Dienst-Deploy (PLAT-026)

Jeder neue Container oder Dienst in `environment_a` braucht VOR dem Deploy
einen Eintrag in [`Systemzustand/00_Uebersicht/service-registry.json`](Systemzustand/00_Uebersicht/service-registry.json).

Ohne Registry-Eintrag wirken weder Drift-Erkennung noch Auto-Apply, und der
Coverage-Audit (Cron 04:40) legt am nächsten Morgen einen Auto-Seed
`coverage-gap-container-<name>` im Backlog an.

Verfahren: Skill [`service-onboarding`](../.claude/skills/service-onboarding/SKILL.md).
Schema-Doku: [`service-registry.md`](Systemzustand/00_Uebersicht/service-registry.md).
