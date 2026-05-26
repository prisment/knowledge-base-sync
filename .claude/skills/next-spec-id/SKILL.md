---
name: next-spec-id
description: Vergibt die nächste freie Spec-ID für eine neue Spec in der knowledge-base. Trigger wenn der Nutzer eine neue Spec anlegen will (Stichworte "neue Spec", "Spec anlegen", "PLAT-NNN_SPEC", "PRIS-NNN_SPEC", "INT-NNN_SPEC", "fortlaufende Nummer"). Liefert die nächste Nummer aus main + allen offenen wt/*-Branches + Commit-Messages. NIE Spec-ID selbst raten, dieses Verfahren nutzen.
---

# Skill: next-spec-id

Vor jeder neuen Spec **zuerst** dieses Verfahren aufrufen — nie die ID
raten. Liefert die nächste freie Nummer aus main + allen offenen
`wt/*`-Branches + Commit-Messages, sodass Parallel-Spuren nicht
kollidieren.

## Aufruf

```
next-spec-id PLAT    # → z.B. PLAT-028
next-spec-id PRIS
next-spec-id INT
```

## Quellen

- Skript: `/opt/infrastructure/environment_a/scripts/backlog/next-spec-id.py`
  (prisment-platform Repo)
- Wrapper: `~/bin/next-spec-id`

## Race-Hinweis

Keine Marker-Reservierung. Wenn echt parallel in derselben Minute
zwei Specs vergeben werden, manuell umnummerieren (PLAT-019-Präzedenz).

## Anwendung

1. `next-spec-id <BEREICH>` aufrufen.
2. Antwortzahl als ID in Spec-Frontmatter eintragen
   (`spec_id: <BEREICH>-NNN`).
3. Dateiname analog: `<BEREICH>-NNN_SPEC_<kurz>.md`.

## Wann dieser Skill greift

- Nutzer fragt nach neuer Spec/PRIS-/PLAT-/INT-ID.
- Übergabe-Prompt enthält Platzhalter `PLAT-NNN`/`PRIS-NNN`/`INT-NNN`
  in einem Dateinamen.
- Nutzer sagt „vergib eine fortlaufende Nummer".
