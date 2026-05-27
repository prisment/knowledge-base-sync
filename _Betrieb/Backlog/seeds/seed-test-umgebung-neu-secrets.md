---
typ: backlog_seed
titel: "Test-Umgebung wieder aufbauen: test.app.prisment.de neu — intern-only, eigene Secrets, eigene Test-DB"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: security
zugkraft: bald
mission: security-haertung-plattform
stufe: spur
beruehrt: ["docker-compose.test.yml (neu)", "secrets/*_test.txt (neu)", "customer_postgres (neue Test-DB)", "Tailscale/Cloudflare-Reichweite", "Traefik-Routes test.app.prisment.de", "scripts/build_image.sh + promote_image.sh"]
stand: 2026-05-26
erzeugt_durch: "Chat-Architekt 2026-05-26 (Wunsch: Test-Bühne für Feature-Entwicklung wieder herstellen) — überschreibt die dünne Erst-Fassung aus Phase-7-Atomisierung HAERTUNGS_SPEC 7.6"
abhaengig_von:
  - seed-code-security-checks.md
  - seed-data-integrity-agents.md
---

## Anlass

Vor dem Next.js-Hack (CVE-2025-55182-Umfeld) gab es eine Test-Familie unter
`test.app.prisment.de`, die für Feature-Entwicklung und Vor-Live-Tests genutzt
wurde. In Phase 1 (HAERTUNGS-Spec 1.4) wurde sie **endgültig abgeräumt** —
Container, Volumes, Secrets, DNS — weil die Test-Secrets im Incident verbrannt
waren UND die Test-Domain öffentlich erreichbar lief (Mit-Angriffsfläche).

Seitdem fehlt jede Test-Bühne. Jede Code-Änderung wird live an Grubi (dem
einzigen Live-Tenant) getestet — auf Dauer untragbar, spätestens ab Tenant #2/3
gefährlich (jeder Regressionsbug = Brand mit Telefonanruf, siehe
`seed-code-security-checks.md` Mit-Baustein „Test-Suite + CI").

## Ziel (Soll-Zustand, grob)

Wiederherstellung einer Test-Umgebung — diesmal **strukturell sicher**, nicht
wie die alte:

- **Domain wieder vorhanden:** `test.app.prisment.de` (PWA Web) — analog ggf.
  weitere Sub-Hosts, falls Agents oder Admin separat testbar sein müssen.
  Offen für die Spec: ein Web-Host oder Voll-Spiegel der Hostnamen-Familie.
- **Intern-only** als Sicherheits-Anker: erreichbar nur über Tailscale (oder
  Cloudflare Zero Trust / Access-Policy, je nach Spec-Entscheidung) — **niemals**
  ein öffentlicher Traefik-Router ohne Auth-Tor. Die alte Test-Familie fiel
  genau daran.
- **Eigene Secrets-Familie:** `secrets/*_test.txt`, **nie** identisch mit Live.
  Eigene OAuth-Apps (Google/Meta-Test-Apps), eigene API-Keys, eigene
  Crypto-Keys. Pflicht-Verfahren: Secret-Generierung mit Mensch-Hand — kein
  Skript darf Live-Secrets versehentlich nach Test kopieren.
- **Eigene Test-DB** im `customer_postgres`-Hub: DB `test_db` (oder
  `pwa_app_test`) mit eigenen Test-Tenants, **keine** Berührungspunkte zu
  Live-Mandanten. RLS gilt auch hier (verhindert versehentliches Cross-Reading,
  selbst wenn die DB nur Test-Daten enthält — die RLS-Mechanik bleibt geprüft).
- **Promote-Pfad sauber:** Images werden gebaut als `:test`-Tag
  (`scripts/build_image.sh`), Test-Compose nutzt `:test`, nach grünem Test-Lauf
  Promote auf `:latest` per `scripts/promote_image.sh` — der Skill
  `image-build-promote` deckt das Verfahren bereits ab.
- **Compose-Trennung:** eigener Compose-Block (`docker-compose.test.yml`) oder
  Profile in der Haupt-Compose. Spec entscheidet — Trade-off siehe „Offene
  Fragen".

## Abhängigkeiten (Pipeline-Reihenfolge)

Dieser Zyklus **setzt zwei laufende Spuren voraus** und startet erst, wenn
beide Phase 9 (abgeschlossen) erreicht haben:

1. **`seed-code-security-checks.md`** (Code-Security-Standards + CI-Checks für
   Next.js + LangGraph). Begründung: Die Test-Bühne ist genau der Ort, an
   dem CI-Pipeline + SAST + Dependency-Audit zuerst „grün" sein müssen,
   bevor Code überhaupt nach Live promotet werden darf. Erst wenn die Check-
   Schichten definiert sind, weiß diese Spec, welche Tore am Promote-Pfad
   sitzen.

2. **`seed-data-integrity-agents.md`** (zentrale Datenpunkt-Definition +
   Verifikation der Agent-Übergaben). Begründung: Die LangGraph-Test-Agents
   in der Test-Familie müssen gegen die zentrale SSOT laufen — sonst baut
   man die Test-Bühne auf einer Daten-Vertrags-Lage auf, die sich gleich
   wieder ändert. Reihenfolge sauberer: SSOT erst stabilisieren, dann
   Test-Familie dagegen kalibrieren.

**Konsequenz für die Reihenfolge:** Spec dieses Seeds frühestens schreiben,
wenn aus beiden Vorgänger-Specs die Akzeptanzkriterien stehen — sonst
verhandelt diese Spec Annahmen, die sich später drehen.

## Vorhandene Bausteine (nichts neu erfinden)

- **Skill `image-build-promote`** — Promote-Pfad `:test` → `:latest` ist
  fertig, getestet, dokumentiert. Diese Spec konsumiert ihn, definiert ihn
  nicht neu.
- **`customer_postgres`-Hub** — zentrale Postgres-Instanz mit User/DB-
  Trennung existiert (siehe `Plattform/Systemzustand/Datenbanken/
  PostgreSQL-Hub.md`). Test-DB wird dort als zusätzliche Datenbank
  angelegt, nicht als zweiter Container.
- **Traefik + Cloudflare-Setup** (Logbuch E26) — Routes für interne Hosts
  sind als Muster vorhanden (Tailscale-only-Pattern aus n8n/Grafana
  übernehmen).
- **RESTORE.md** listet `test.app.prisment.de A <NEUE-IPv4>` bereits als
  Wiederaufbau-Punkt für DNS — der Architekt setzt den DNS-Record, sobald
  Spec-Phase erreicht.
- **Pre-commit-Hook + Token-Scanner** (im `prisment-platform`-Repo)
  verhindert weiterhin, dass Test-Secrets ins Repo rutschen — bleibt
  unverändert wirksam.

## Offene Fragen für die spätere Spec

- **Reichweite-Mechanismus:** Tailscale (einfach, MAC-Limit) vs.
  Cloudflare Access (komfortabler, mehr bewegliche Teile). Beides ergibt
  intern-only — Wahl entscheidet, wie Architekt + ggf. Co-Tester
  zugreifen.
- **Compose-Trennung:** separate `docker-compose.test.yml` (klare
  Sichtbarkeits-Trennung) vs. Compose-Profiles (DRY, aber riskanter weil
  ein Tippfehler im Profile-Filter Test- und Live-Container vermischt).
- **Daten-Seeding:** wie kommen Test-Daten in `test_db`? Anonymisierter
  Snapshot aus Live? Synthetische Fixtures? Mischung? Datenschutz-Folgen
  klären (siehe `seed-sma-dsgvo-sammel.md`).
- **CI-Integration:** Soll die Test-Familie als Deploy-Target der
  Gitea-Actions-Pipeline laufen (Push auf `main` → automatisch
  `:test`-Build + Deploy nach Test) oder bleibt der Promote manuell?
  Entscheidung hängt am Output von `seed-code-security-checks.md`.
- **LangGraph-Agents:** alle 5 Agents mit-spiegeln oder nur die jeweils
  aktiv entwickelten? Trade-off Ressourcen vs. echte End-to-End-Tests.
- **Lebensdauer der Test-Daten:** TTL/Reset-Strategie — täglicher Reset,
  per Push-Trigger, oder dauerhaft? Verhindert Daten-Müll-Ansammlung.

## Sicherheits-Wand (Lehren aus dem Vorfall)

- **Niemals** öffentlicher Traefik-Router ohne Auth-Schicht für Test-
  Hosts. Wenn intern-only nicht möglich, dann mindestens Basic-Auth +
  IP-Whitelist + WAF-Härtung — aber als zweite Wahl, nicht Default.
- **Niemals** Live-Secrets in Test re-using, auch nicht „nur
  übergangsweise". Eigene Secrets-Familie ist Pflicht-Tor.
- **Niemals** Live-DB als Read-Target für Test (auch nicht „nur lesend").
- Test-Domain-DNS-Record erst setzen, wenn Stack steht — kein
  „Platzhalter live", weil der sofort gescannt wird.

## Stufe / Risiko

**Spur** — viele Bauteile (neue Secrets-Familie, neue DB, neue
Compose-Topologie, neue Traefik-Routes, neue Tailscale-/Access-Policy,
neue CI-Anbindung). Reversibel als Block (alles zusammen wieder weg).
Risiko niedrig, **solange intern-only durchgehalten wird** — fällt
diese Wand, ist es sofort `sicherheitskritisch-akut`. Risikoklasse
`sicher` greift nur, wenn die Pflicht-Tore in der Spec hart sind.

## Trigger

**Bald** — startet, sobald `seed-code-security-checks.md` und
`seed-data-integrity-agents.md` Phase 9 erreicht haben. Vorher kein
Spec-Schreiben, sonst verhandelt diese Spec Annahmen, die sich noch
drehen.
