---
typ: backlog_seed
titel: "Korridor-Beobachtung: 4 Datenpunkte aus PLAT-013 nach 1–3 Spuren auswerten"
geltungsbereich: alle
risikoklasse: sicher
status: offen
klasse: prozess
zugkraft: bald
stufe: sprung
beruehrt: ["_Betrieb/Verfassung/", "_Betrieb/Templates/"]
stand: 2026-05-25
erzeugt_durch: PLAT-013 Phase 7 (Beobachtungspunkte konsolidiert)
basiert_auf_spec: "[[Plattform/Archiv/PLAT-013/PLAT-013_SPEC]]"
---

## Anlass

PLAT-013 hat das Korridor-Modell verankert, mit vier expliziten Beobachtungspunkten, die erst nach 1–3 realen Korridor-Spuren mit Daten belegbar sind. Damit sie nicht in der archivierten Abschluss-Doku verschwinden, hier als eigenständiger Seed konsolidiert. Auswertung passiert geballt nach genug Erfahrung, nicht punktuell.

## Die vier Datenpunkte

1. **Stille-Stufe** (3. Sichtbarkeits-Stufe)
   - Heute: nicht aktiv. Alles ist mindestens Information.
   - Datenbasis: Information-Protokolle der ersten Korridor-Spuren — welche Wie-Cluster wurden mehrfach ignoriert vom Mensch?
   - Frage: ab wann und für welche Kategorien Stille einführen?
   - Trigger: wenn ein klar erkennbares Cluster „nie relevant" identifizierbar ist.

2. **Sprung-Renaissance**
   - Heute: in den 14 Tagen vor PLAT-013 wurde keine einzige Stufe Sprung benutzt. Alles wurde Schritt oder Spur.
   - Frage nach 3 Korridor-Spuren: ist die Stufe Sprung mit dem Korridor-Modell („Spec-Freigabe vorne, dann autonom durchziehen") wieder attraktiver geworden? Oder bleibt sie strukturell tot?
   - Bei tot: Verfassungs-Bereinigung (Stufe entfernen) — eigener Folge-Zyklus mit eigener Spec, weil Stufen-Modell-Änderung mehrere Verfassungs-Dateien berührt.

3. **Kritikalitäts-Liste-Wachstum**
   - Heute: feste Liste mit 4 Clustern + 2 Testfragen für Graubereich.
   - Datenbasis: Entscheidungs-Protokolle — wenn ein autonom gelaufenes Bündel sich im Nachhinein als kritisch entpuppt hätte, fehlt ein Cluster.
   - Mechanik: Phase-9-Pflicht-Tor, Mensch gibt frei (E3-konform). Liste wächst nur per bewusster Architekten-Entscheidung.

4. **Stopp-Auslöser-Schärfe**
   - Frage: stoppt Claude Code an Stellen, die kein Wohin-Urteil brauchen?
   - Trigger: zwei konkrete Vorkommen → ein Stopp-Auslöser ist zu weit gefasst → Schärfung im Folge-Mini-Zyklus.

## Voraussetzung — keine Spec, bevor das erfüllt ist

**Mindestens drei abgeschlossene Korridor-Spuren** unter PLAT-013-Mechanik (gerechnet ab PLAT-011-Wiederaufnahme). Jede Spur liefert mindestens ein Entscheidungs-Protokoll. Auswertung dann geballt — bringt nichts, nach einer einzigen Spur Schlüsse zu ziehen.

## Vorgehen beim Auswerten (für die spätere Spec)

- Jede Spur: Entscheidungs-Protokoll(e) sichten, jeden der vier Punkte mit Belegen füllen.
- Wenn Datenbasis reicht: Architekt entscheidet pro Punkt: handeln (Folge-Zyklus oder Verfassungs-Schritt) oder weiter beobachten.
- Nicht alle vier Punkte müssen gleichzeitig handeln — können einzeln getriggert werden.

## Stufe / Risiko

**Sprung** — abgegrenzt (Auswertung + ggf. einzelne kleine Verfassungs-Anpassungen). Eskaliert zu Spur, falls die Auswertung mehrere parallele Verfassungs-Änderungen erfordert (insbesondere Sprung-Renaissance + Stille-Stufe gemeinsam → Spur). Risikoklasse `sicher`: reine Beobachtung + Doku-Anpassung.

## Bezug

- Voraussetzung-Anker: **PLAT-013** (Korridor-Modell — der Mechanik-Boden, gegen den hier beobachtet wird).
- Ergänzt durch: Logbuch E29 (verworfene Alternative 2 = Stille verschoben; Kontextbindung = Stille-Stufe scharfstellen wenn Datenbasis reicht).
