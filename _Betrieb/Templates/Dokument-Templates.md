# Dokument-Templates: Spec · Report · Abschluss-Doku
**Ort:** `_Betrieb/Templates/`
**Zweck:** Standardisierte Vorlagen für die drei Arbeitsdokumente des Iterationszyklus. Modular aufgebaut: ein gemeinsamer YAML-Front-Matter-Header für alle drei (maschinell auswertbar, Obsidian-Properties, später Sync-Skript-parsbar), plus je ein typspezifischer Körper.

---

## Grundprinzipien (gelten für alle drei)

1. **Eine Wahrheit, Zusammenfassung ist nur Ansicht.** Die Zusammenfassung am Dokumentanfang verdichtet, was darunter steht — sie ergänzt NIE Inhalt, der unten fehlt. Sonst entsteht eine zweite Quelle. (Konzept 1.1)
2. **Bewiesen, nicht angenommen.** Jeder Ist-Fakt in einem Report nennt seine Quelle (Befehl/Datei).
3. **Front-Matter-Keys sind verbindlich identisch.** Immer `risikoklasse`, nie mal `risiko` mal `risk`. Abweichende Keys zerstören die Abfragbarkeit. Wer ein neues Feld braucht, ergänzt es HIER im Template, nicht ad-hoc im Einzeldokument.
4. **Die Spec ist die Wahrheit, Report und Abschluss-Doku erben ihre `spec_id`.** Damit ist die Kette Spec → Report → Abschluss maschinell verkettbar.

---

## Gemeinsamer Header (YAML Front Matter)

Jedes der drei Dokumente beginnt mit diesem Block. Felder, die für den Typ nicht gelten, werden weggelassen (nicht leer gesetzt) — Ausnahme: die Pflichtfelder oben.

```yaml
---
typ:             spec            # spec | report | abschluss
spec_id:         PRIS-014        # eindeutige ID; Report/Abschluss erben die ID der Spec
titel:           "DNS-Migration zu Cloudflare"
geltungsbereich: prisment        # plattform | prisment | intern
risikoklasse:    kritisch        # sicher | sicherheitskritisch-akut | kritisch
zyklus_phase:    3               # spec=3 · report=2 (fakten) oder 5 (machbarkeit) · abschluss=7
status:          offen           # offen | in_arbeit | abgeschlossen | pausiert
stand:           2026-05-23
# --- optionale Verknüpfungen ---
report_art:      machbarkeit     # nur bei typ=report: fakten | machbarkeit
seed:            "[[Backlog/seed-dns-migration]]"   # falls aus Backlog entstanden
pausierte_spec:  "[[PRIS-011]]"  # falls dieser Zyklus eine pausierte Spec eingeschoben hat
---
```

**ID-Schema:** `<GELTUNGSBEREICH-KÜRZEL>-<laufende Nummer>`, z.B. `PRIS-014`, `PLAT-003`, `INT-007`. Kürzel: `PLAT` (Plattform), `PRIS` (Prisment), `INT` (Intern). Laufende Nummer ist pro Geltungsbereich fortlaufend.

---

## 1. SPEC — Körper (Phase 3, erstellt vom Chat-Architekt)

```markdown
## Zusammenfassung
[2–4 Sätze, für den Menschen lesbar. Verdichtet das Ziel + den Kern der Lösung.
Enthält nichts, was unten nicht steht.]

## Ziel
[Was soll nach Abschluss dieses Zyklus WAHR sein? Ein klarer Soll-Zustand,
kein Tätigkeitsbündel. "DNS läuft vollständig über Cloudflare, alter Anbieter
abgeschaltet" — nicht "wir arbeiten an DNS".]

## Kontext & Annahmen
[Worauf bauen wir auf, was setzen wir voraus? Diese Annahmen sind genau das,
was die Re-Sync-Prüfung bei einer Wiederaufnahme gegenprüft. Explizit auflisten.]

## Soll-Zustand
[Die eigentliche Beschreibung, WAS gebaut/geändert wird. Konkret genug für
Claude Code — betroffene Komponenten, Pfade, Dienste namentlich. Das WAS,
nicht das Wie-im-Detail (das Wie erarbeitet Claude Code in der Machbarkeit).]

## Akzeptanzkriterien
[Prüfbare Zustände, KEINE Tätigkeiten. Tiefe skaliert mit Risikoklasse:
 - sicher:    "Dienst läuft (Health-Check grün)"
 - kritisch:  vollständige Liste, z.B.
     - [ ] DNS vollständig zu Cloudflare migriert
     - [ ] alter Anbieter nicht mehr erreichbar/autoritativ
     - [ ] Config vor Migration gesichert
     - [ ] Routen A, B, C getestet und erreichbar
WIE geprüft wird, ist Claude Codes operative Sache — gehört NICHT hierher.]

## Rollback-Erwartung
[Was muss reversibel sein, wohin wird im Fehlerfall zurückgerollt?
Bei "sicher": Auto-Rollback-Pfad. Bei "kritisch": expliziter manueller Plan.]

## Nicht Teil dieser Spec
[Explizite Abgrenzung gegen Hineinwachsen. Was hier auftaucht, aber bewusst
ausgeklammert wird → wandert als Abzweigung ins Backlog, nicht in diese Spec.]
```

**Bewusst NICHT in der Spec:** die Bündelung/Arbeitsliste. Die erarbeitet sich Claude Code selbst im Machbarkeits-Report — er kann Abhängigkeiten am echten System besser einschätzen.

---

## 2. REPORT — Körper (erstellt von Claude Code)

Zwei Ausprägungen, gesteuert über `report_art` im Header. Gleicher Header, unterschiedlicher Körper.

### 2a. Faktensammlung (`report_art: fakten`, Phase 2)

```markdown
## Zusammenfassung
[2–4 Sätze: Was wurde am echten System erhoben, Kernbefund.]

## Ist-Zustand
[Der erhobene Zustand des echten Systems. JEDER Fakt nennt seine Quelle —
Befehl, Datei oder Pfad, woher die Aussage stammt. Bewiesen, nicht angenommen.

Beispiel:
- Reverse-Proxy: Traefik 2.10, Config in /etc/traefik/traefik.yml
  (Quelle: `docker inspect traefik | grep Image`)
- Aktuelle DNS-Records: 14 A-Records bei Anbieter X
  (Quelle: `dig`-Auszug, siehe Anhang)]

## Auffälligkeiten / Risiken
[Was beim Erheben aufgefallen ist und für die Spec relevant sein könnte.]
```

### 2b. Machbarkeit (`report_art: machbarkeit`, Phase 5)

```markdown
## Zusammenfassung
[2–4 Sätze: Ist die Spec wie geschrieben umsetzbar? Kernabweichungen?]

## Spec gegen Realität
[Jedes Akzeptanzkriterium der Spec: umsetzbar wie beschrieben / Abweichung /
Blocker. Grobe Abweichungen explizit markiert → "ZURÜCK IN DEN CHAT".]

## Arbeitsliste (selbst erarbeitete Bündelung)
[Claude Codes eigene Bauliste, abgeleitet aus der Spec, referenziert auf deren
Akzeptanzkriterien. Bündelgröße risikoabhängig, mit Stopp-/Testpunkten.

Pro Bündel **Pflicht-Zeile** `kritisch: sicher|kritisch|sicherheitskritisch-akut [Halbsatz Begründung]`
(siehe `01_Spec-Format.md` „Kritikalität pro Bündel"). `sicher`/`kritisch` laufen
autonom (`kritisch` mit Pflicht-Vorsicht: Backup/Verifikation/Restore-Pfad);
nur `sicherheitskritisch-akut` ist Vor-Stopp.

Zusätzlich pro Bündel/Serie **Pflicht-Zeile** `vorgezogen:` — die Mensch-Handlungen,
die VOR dem Lauf nötig sind (sudo/UI/manueller Test) plus etwaige Pauschal-Freigaben
(Compose/Rebuild/Restart) — oder `vorgezogen: keine`.

Beispiel:
- Bündel 1 → erfüllt Kriterium [Auth-Container neu gebaut]: ...
  kritisch: kritisch [Auth berührt, aber reversibel + :prev-Backup]
- Serie A (3 Cluster-Drops) → erfüllt Kriterium [Cluster migriert]: ...
  kritisch: kritisch [reversibel pro Instanz]
  vorgezogen: Pauschal-Freigabe „alle environment_a-Restarts dieses Zyklus"
- Bündel 4 → erfüllt Kriterium [RLS-Policy umgestellt]: ...
  kritisch: sicherheitskritisch-akut [Tenant-Isolation, Versagen leakt live]
  vorgezogen: manueller Cross-Tenant-Test durch Architekt]
```

---

## 3. ABSCHLUSS-DOKU — Körper (Phase 7, im Chat gegengecheckt)

```markdown
## Zusammenfassung
[2–4 Sätze: Was wurde erreicht, ist das Ziel der Spec erfüllt.]

## Ziel-Abgleich
[Jedes Akzeptanzkriterium der Spec einzeln:
 - [x] DNS vollständig migriert — Nachweis: `dig`-Ergebnis zeigt Cloudflare-NS
 - [x] alter Anbieter nicht erreichbar — Nachweis: ...
 - [ ] Routen getestet — ABWEICHUNG: Route C noch offen, siehe unten
Erfüllt / nicht erfüllt / abweichend, jeweils mit Nachweis.]

## Abweichungen vom Plan
[Was lief anders als in der Spec geplant, und warum. Material für das Logbuch.]

## Entscheidungs-Protokoll (Korridor-Review)

[Geballter Review-Anker für die Korridor-Mechanik (siehe
`00_Iterationszyklus.md` Abschnitt „Autonome Ausführung im freigegebenen
Korridor"). Bei Spur **pro Bündel** am Bündel-Ende, beim Sprung einmalig am
Zyklus-Ende. Beim Schritt entfällt.

- **Gebaut:** <1 Satz, was in diesem Bündel/Zyklus entstand>
- **Entscheidungen (Gabelungen ohne Stopp):**
  - <Gabelung 1> → <Wahl> (Warum: <Halbsatz>)
  - <Gabelung 2> → <Wahl> (Warum: <Halbsatz>)
- **Was du wissen solltest:** <Information für den Mensch — kein Veto-Trigger>
- **Wo ich unsicher war:** <ggf. leer>]

## Wissens-Einarbeitung (Vorschlag für Phase 9)
[Claude Codes Vorschlag, was wohin wandert — Mensch gibt frei:
 - → Systemzustand: [welche Datei wird wie aktualisiert]
 - → Verfassung: [nur wenn ein Standard sich ändert — "nie nach unten"]
 - → Logbuch (neuer Eintrag): [das WARUM einer getroffenen Entscheidung]
 - → Archiv: [welche Arbeitsgedächtnis-Dateien dieses Zyklus]
Dies ist der Scharnierpunkt gegen Sediment: nicht nur Rückblick, sondern der
strukturierte Vorschlag für die Wissens-Bewegung beim Abschluss.]
```

**Phase-9-Pflichtaufruf (Seed-Status + Ketten-Kehraus, PLAT-035 Variante δ):** Hatte der Zyklus einen Seed als Auslöser, ruft der abschließende Agent in Phase 9 verbindlich auf:

```
python3 /opt/infrastructure/environment_a/scripts/backlog/phase9_seed_archive.py <spec_id>
```

Das Tool setzt den Seed-`status:` auf `abgeschlossen` und entscheidet, ob der Seed allein ins Archiv geht oder als Lese-Anker in `seeds/` stehen bleibt (falls Folge-Glieder noch offen). Erst beim Abschluss des letzten Kettenglieds wandern alle abgeschlossenen Seeds der Kette gemeinsam in einen Archiv-Ordner. Verfassungs-Verankerung: `_Betrieb/Verfassung/01_Spec-Format.md`, Abschnitt „Autonome Halde".

---

## Backlog-Seed — zusätzliche Pflichtfelder

Seeds im `_Betrieb/Backlog/seeds/` führen drei zusätzliche Pflichtfelder, die das Übersichts-Skript auswertet (Definition: `_Betrieb/Verfassung/01_Spec-Format.md`, Abschnitt „Backlog-Felder").

```yaml
---
typ:             backlog_seed
titel:           "…"
geltungsbereich: alle              # alle | plattform | prisment | intern
risikoklasse:    sicher
status:          offen
stufe:           spur              # spur | sprung | schritt
klasse:          prozess           # security | qualitaet | feature | prozess | wartung   (PFLICHT)
zugkraft:        jetzt             # jetzt | bald | irgendwann                            (PFLICHT)
mission:         ""                # OPTIONAL: kebab-case-Slug aus _Betrieb/Missionen/00_aktive-missionen.md, leer = keine Mission
beruehrt:        ["_Betrieb/Backlog/", "_Betrieb/Verfassung/01_Spec-Format.md"]            # PFLICHT, Ordner-Ebene
autonom_ziehbar: nein              # abgeleitet, nicht geurteilt: ja gdw. freigegebene Spec UND risikoklasse: sicher
stand:           2026-05-25
erzeugt_durch:   "…"
abhaengig_von:                                       # optional
  - "[[_Betrieb/Backlog/seed-anderer-seed]]"
---
```

**Vergabe-Mechanik** wie `stufe`: KI schlägt mit Begründung vor, Mensch revidiert/gibt frei. Bei unklarem Footprint bleibt `beruehrt:` grob/leer und wird beim Spec-Start präzisiert.

**Body-Schluss (Pflicht):** Jeder Seed endet mit folgender Mensch-Sperrzone — Claude Code liest, schreibt aber NICHT hinein. Verfassungs-Verankerung: Sperrzonen-Regel in `_Betrieb/Verfassung/01_Spec-Format.md` direkt unter dem Backlog-Felder-Block.

```markdown

---

## 📝 Eigene Notizen

<!-- Mensch-Sperrzone. Claude Code liest hier, schreibt NICHT hinein.
     Inhalt überlebt jeden Re-Write des Seeds. -->
```

## Verkettung (warum der gemeinsame Header zählt)

Über die identische `spec_id` und die einheitlichen Front-Matter-Keys lässt sich die gesamte Kette eines Zyklus maschinell zusammenführen:
- Obsidian-Properties/Dataview: „alle Dokumente mit `spec_id: PRIS-014`" oder „alle `status: offen` + `risikoklasse: kritisch`".
- Später ein Sync-Skript: dieselben Felder parsen, ohne Fließtext zu interpretieren.

Genau deshalb ist Disziplin bei den Key-Namen Pflicht, nicht Kür.
