---
typ: backlog_seed
titel: "Aktualitäts-Vollabdeckung: jede Komponente aktuell + jederzeit beweisbar (Sammel-Seed)"
geltungsbereich: plattform
risikoklasse: kritisch
status: offen
klasse: security
zugkraft: bald
stufe: spur
beruehrt: ["alle eigenen Container", "alle Gitea-Repos", "nacht-aufgaben.md", "nightly Tool-Set"]
stand: 2026-05-26
erzeugt_durch: "PLAT-020 Folge-Beobachtung + Architekten-Frage 2026-05-26: „sind alle Apps wirklich aktuell und woran machen wir das fest?""
verbundene_seeds:
  - "[[_Betrieb/Backlog/seeds/seed-diun-rueckbau]]"
  - "[[_Betrieb/Backlog/seeds/seed-base-image-versionen]]"
  - "[[_Betrieb/Backlog/seeds/seed-dependency-monitoring-renovate]]"
  - "[[_Betrieb/Backlog/seeds/seed-pretooluse-hook-allowlist]]"
---

## Anlass — die ehrliche Frage

Architekt am 2026-05-26 nach dem ersten erfolgreichen Nightly-Lauf:

> „Sind jetzt alle meine Applikationen von n8n bis zu Next.js wirklich
> aktuell und woran machen wir das fest? Das ist mein Ziel, das müssen
> wir erreichen."

Direkte Antwort heute, ehrlich nach Komponenten getrennt:

| Komponente | aktuell? | Wie geprüft? |
|---|---|---|
| Container aus Registry (gitea, twenty, postgres, redis, ollama, open-webui, whisper, traefik, cloudflared, umami, nginx, diun) | **vermutlich ja** | Diun täglich, nightly Aufgabe 1, am 2026-05-26: 13 unchanged, 0 updated |
| OS-Pakete `-security` | **ja** | unattended-upgrades autonom, dpkg-Log + Mai-2026-Patches belegt |
| OS-Pakete `-updates` 1a/1b | **ja, weil 0 offen** | nightly Aufgabe 3, am 2026-05-26: 0 upgraded |
| Custom-Build-Container (`n8n-internal`, `langgraph-*`, `admin-web`, `cf-alarm`, `pwa-api`, `ollama-proxy`) | **nein, mind. eins nachweislich nicht** (n8n: 2.13.3 vs. Upstream 2.21.x) | gar nicht — Diun kann nicht, nightly listet sie nur in 1c als "nicht prüfbar" |
| npm-Deps in `pwa-web`, `landingpage`, `admin-web` | **nein, unbekannt** | gar nicht — Aufgabe 2 ist im nightly-Tool-Set unausführbar (kein Internet-Zugriff fürs CVE-Lookup) |
| pip-Deps in `pwa-api`, `langgraph-*` | **nein, unbekannt** | gar nicht (analog) |
| Base-Images in eigenen Dockerfiles (Node-LTS, Python-Slim) | **vermutlich nein** | gar nicht — Base-Images werden nur bei Rebuild zufällig mit aktualisiert |

**Kurzfassung:** Registry-Images + OS sind belegt aktuell. Alles Eigene
(Custom-Builds + App-Deps + Base-Images) ist **strukturell ungeprüft** —
n8n ist der eine Fall, in dem wir es zufällig bemerkt haben.

## Ziel-Definition: was heißt „aktuell"? was heißt „beweisbar"?

**Aktuell** bedeutet pro Komponente, gestaffelt nach Klasse:

- **Sicherheitsrelevant (CVE bekannt):** ≤ 7 Tage nach Disclosure
  gepatcht oder explizit als „bewusst nicht patchbar" dokumentiert
  (mit Begründung + Kompensations-Maßnahme).
- **Patch/Minor (kein CVE):** ≤ 30 Tage nach Upstream-Release
  übernommen, sofern Health-Check + Rollback-Pfad existieren.
- **Major-Sprünge:** **nicht** autonom — bewusste Spec-Entscheidung
  mit Migration-Notes + betreutem Apply.

**Beweisbar** bedeutet: für jede Komponente jederzeit aufrufbar:
laufende Version, Upstream-Latest, Drift-Tage, letzter Update-Check,
letzter Apply. Kein „failed: erwartet", kein stilles Sammelbecken,
keine Annahme.

## Bestehende Bausteine (verlinkt, nicht dupliziert)

| Baustein | Adressiert | Status |
|---|---|---|
| `seed-diun-rueckbau` (Nachtrag 2026-05-25) | Custom-Build-Update-Pfad (Klasse B), drei Optionen: Base-Image-Pin / Upstream-API-Check / Gitea-Registry-Push | offen, Sprung — eigentlich Spur (Nachtrag hat Stufe nicht erhöht, sollte) |
| `seed-base-image-versionen` | Node-LTS / Python-Slim in eigenen Dockerfiles | offen, Sprung |
| `seed-dependency-monitoring-renovate` | npm/pip-Deps-CVE-Beobachtung via Renovate/Dependabot in Gitea | offen, Sprung, **bald** — direkter Treffer für die Aufgabe-2-Lücke |
| `seed-pretooluse-hook-allowlist` (PLAT-002) | macht autonomes 1b-`-updates`-Apply möglich (heute nur betreut) | offen, Spur, **bald** |

## Fehlende Bausteine (neu, hier eröffnet)

### B1 — OCI-LABEL für alle Custom-Builds
- **Problem:** nightly 1c kann für 9 von 10 Custom-Builds keine echte
  Version zeigen — nur Image-Created-Datum, weil
  `org.opencontainers.image.version` LABEL fehlt im Dockerfile.
- **Fix:** Pro Custom-Build-Dockerfile ein `LABEL
  org.opencontainers.image.version="<sha>"` (aus Git) ODER `="<semver>"`
  (aus package.json / pyproject.toml). Beim Build via Build-Arg setzen.
- **Stufe Schritt** (eine Zeile pro Dockerfile + Build-Skript-Anpassung).
- Kann mit `seed-diun-rueckbau` mit-erledigt werden, ist aber
  unabhängig genug für einen eigenen Mini-Schritt.

### B2 — CVE-Scan für App-Deps im nightly (eng umrissene Allowlist-Routine)
- **Problem:** nightly Aufgabe 2 verlangt externe API-Queries
  (GitHub Security Advisories, NVD, MITRE, npm Registry, Docker Hub),
  aber das Tool-Set erlaubt keine `curl`-/HTTP-Calls. Daher meldet der
  nightly seit BOOT-001 jede Nacht „übersprungen" für Aufgabe 2.
- **Zwei Wege:**
  - **(a)** Hartes Skript `_Betrieb/Skripte/nightly/cve-scan-deps.sh`
    mit fixierten API-Endpunkten (Allowlist), `claude --print` ruft
    das auf statt selbst zu queryen. Saubere Lösung, aber Skript-Pflege.
  - **(b)** Aufgabe 2 in `nacht-aufgaben.md` zu „nur betreutes Bündel"
    umetikettieren UND auf Renovate setzen (siehe
    `seed-dependency-monitoring-renovate`). Pragmatischer, weniger
    Eigen-Werkzeug.
- **Empfehlung:** zuerst (b) — wenn Renovate sauber läuft, ist (a)
  vermutlich redundant. Stufe Sprung, klasse: prozess.

### B3 — Aktualitäts-Dashboard (das eigentliche „beweisbar")
- **Problem:** auch wenn alle Update-Pfade laufen, fehlt eine
  jederzeit-aufrufbare Übersicht „pro Komponente: laufende Version,
  Upstream-Latest, Drift". Heute existiert das verstreut: Diun-Log
  + nightly-Report + manuelles `docker inspect`.
- **Fix:** Ein Skript / eine Doku-Datei
  `Plattform/Systemzustand/00_Uebersicht/AKTUALITAET.md`, regelmäßig
  vom nightly regeneriert. Spalten:
  Komponente · laufende Version · Upstream-Latest · Drift-Tage ·
  letzter Apply · CVE-Status · prüfbar ja/nein.
- **Stufe Sprung** (Generator-Skript + Format + Cron-Anbindung).
- Direkter Treffer für die Architekten-Frage „woran machen wir das
  fest" — wenn diese Datei grün ist, ist das System aktuell.
- Vorbedingung: B1 (OCI-LABEL) für ehrliche Versions-Spalte bei
  Custom-Builds.

### B4 — n8n-Drift konkret schließen (2.13.3 → 2.21.x)
- **Problem:** n8n läuft 8 Minor-Versionen hinter Upstream.
- **Lösung:** Re-Build des `environment_a-n8n-internal`-Images auf
  Upstream-Stable, Health-Check (Workflows laufen, Trigger-DB ok),
  Rollback-Pfad via `prev-<timestamp>`-Tag.
- **Stufe Sprung** (klar abgegrenzter Bugfix; major-Sprung-Block nur
  wenn Workflow-Inkompatibilität gefunden wird, sonst Minor-Kette).
- Vorbedingung: Update-Pfad-Mechanik aus `seed-diun-rueckbau` muss
  zumindest skizziert sein, sonst löst dieser Sprung nur einen Fall —
  besser direkt durch die Pipeline.

## Reihenfolge (Vorschlag, revidierbar)

```
1. seed-dependency-monitoring-renovate (npm/pip-CVE-Sicht — größtes
   blindes Auge, sicherheitsrelevant, klar umsetzbar via Renovate-Setup).
2. seed-diun-rueckbau (Custom-Build-Update-Pipeline-Vollabdeckung —
   eigentlich Spur, Stufe sollte erhöht werden).
3. B1 OCI-LABEL (kleiner Schritt, macht B3 + 1c-Sichtbarkeit ehrlich).
4. B4 n8n-Drift konkret schließen (erster echter Pipeline-Lauf
   durch die in 2 gebaute Mechanik).
5. seed-base-image-versionen (Node-LTS / Python-Slim systematisch).
6. B3 Aktualitäts-Dashboard (krönt die Architektur — wenn 1–5
   laufen, hat das Dashboard echte Daten, sonst wären die Zellen leer).
7. B2 entfällt vermutlich (Renovate aus 1 deckt den App-Deps-Teil ab).
```

PreToolUse-Hook (`seed-pretooluse-hook-allowlist`) ist orthogonal —
schaltet autonomes 1b-Apply scharf, ist aber für die Beweisbarkeit
nicht zwingend. Kann parallel laufen, wann es zeitlich passt.

## Akzeptanzkriterium für „Vollabdeckung erreicht"

Wenn das Dashboard B3 für JEDE der folgenden 6 Klassen grüne Spalten
zeigt (laufende Version + Upstream-Latest + Drift-Tage):

1. Registry-Container (heute via Diun ✓)
2. Custom-Build-Container (heute Lücke)
3. OS-Pakete `-security` (heute ✓)
4. OS-Pakete `-updates` 1a/1b (heute ✓)
5. npm-Deps in eigenen Apps (heute Lücke)
6. pip-Deps in eigenen Apps (heute Lücke)

Plus: für JEDE bekannte CVE (NVD-Feed) ist innerhalb 7 Tagen entweder
gepatcht oder dokumentiert begründet, warum nicht.

## Risiken / Auffälligkeiten

- **Renovate vs. Eigen-Skript-Frage** wird in
  `seed-dependency-monitoring-renovate` schon angerissen — Entscheidung
  dort, hier nur Verweis.
- **Major-Sprung-Disziplin** (Next.js 14 → 15, n8n 2.13 → 2.21) ist
  Hand-Arbeit, nicht autonom — das ist Absicht (siehe PLAT-001 v2). Das
  Dashboard B3 muss Majors als „nicht-autonom-Apply" markieren, damit
  Architekt sieht, wo bewusste Eingriffe nötig sind.
- **„Aktualitäts-Vollabdeckung" — Schnitt-Logik bei Spec-Start:** Die
  sieben Bausteine werden bei der Spec-Erarbeitung als zusammen-
  hängende Spur geführt (Phase 2 erhebt alle, Phase 3 formt die
  Spec) — keine separate Roadmap-/Schirm-Datei. Bausteine, die im
  Spec-Lauf eigenständig werden, werden als Folge-Seeds abgezweigt.
  Siehe Verfassung 00, Stufen-Abschnitt: keine Planungs-Ebene über
  der Spur (PLAT-022). Heute bleibt es Seed, weil die Bausteine
  noch nicht alle ihre Detail-Klärung haben.
- **Was Folge-Seed nicht ist:** ein Versuch, Diun durch etwas Neues zu
  ersetzen. Diun bleibt der Registry-Klasse-Checker bis sein Rückbau
  selbst eine Spec geworden ist (`seed-diun-rueckbau`).

## Verweise

- Heutiger Nightly-Report mit den Lücken:
  `Plattform/Arbeitsgedaechtnis/nacht-report-2026-05-26.md`
  (Aufgabe 1c, Aufgabe 2, Empfehlungs-Block).
- PLAT-020 Abschluss-Doku: Folge-Beobachtungen 2 + 3 sind hier
  konsolidiert + erweitert (Architekten-Frage als Treiber).
- HARDENING-Spec-Kontext: Sicherheits-Achse insgesamt liegt in
  `Plattform/Arbeitsgedaechtnis/HAERTUNGS_SPEC_PRISMENT.md`.
