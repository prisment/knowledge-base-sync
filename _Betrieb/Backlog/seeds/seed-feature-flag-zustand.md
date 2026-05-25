---
typ: backlog_seed
titel: "Feature-Flag als anerkannter Zustand: gebaut, aber nicht scharf"
geltungsbereich: alle
risikoklasse: sicher
status: offen
prioritaet: niedrig
klasse: prozess
zugkraft: irgendwann
stufe: sprung
beruehrt: ["_Betrieb/Verfassung/"]
stand: 2026-05-24
erzeugt_durch: Framework-Retro nach BOOT-001/PLAT-001
---

## Anlass

In PLAT-001 trat mehrfach das Muster „Mechanik gebaut, aber nicht scharf
geschaltet" auf (Block 1b autonom, Block 2 Auto-Reboot — beide gebaut + unter
Aufsicht bewiesen, aber autonome Schaltung auf PLAT-002 verschoben, weil der
PreToolUse-Hook fehlt). Das wurde jeweils als „Abweichung vom Plan"
dokumentiert — obwohl es kein Fehler ist, sondern ein bewusster, sauberer
Zwischenzustand.

Profis nennen das ein **Feature Flag**: gebaut, aber abgeschaltet, bis eine
Bedingung erfüllt ist. Das Framework kennt diesen Zustand bisher nicht als
Konzept und zwingt ihn deshalb in die Schublade „Abweichung", was ihn
fälschlich nach Problem aussehen lässt.

## Soll-Zustand (grob, für die spätere Spec)

- Ein anerkannter Dokument-/Front-Matter-Zustand für „gebaut, nicht scharf":
  z. B. Akzeptanzkriterium-Marker `[~] gebaut, scharf ab <Bedingung>` statt
  `[ ]` (offen) oder `[x]` (erfüllt).
- Klar definierte „Scharf-Bedingung" (was muss wahr sein, damit das Flag
  umgelegt wird) + wo sie verfolgt wird (Backlog-Seed, der auf das Flag zeigt).
- Abgrenzung zur echten Abweichung: ein Feature-Flag ist geplant + gewollt,
  eine Abweichung ist ungeplant. Beide gehören in unterschiedliche
  Abschluss-Doku-Abschnitte.

## Offene Fragen für die spätere Spec

- Reicht ein Front-Matter-/Checklisten-Konvention, oder braucht es ein eigenes
  kleines Register „offene Flags je Bereich"?
- Verhältnis zur Stufe: Ein Flag-Umlegen ist oft selbst ein Sprung oder Schritt
  (PLAT-002 = Hook bauen ist eher Spur). Klären, ob das Umlegen einen eigenen
  Mini-Zyklus braucht oder als Teil des nächsten ohnehin geplanten läuft.

## Priorität

Niedrig. Kosmetik/Klarheit, kein funktionaler Schmerz. Lohnt sich, sobald das
Muster zum dritten Mal auftritt (aktuell n=2 in PLAT-001).
