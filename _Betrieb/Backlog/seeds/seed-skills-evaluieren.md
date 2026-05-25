---
typ: backlog_seed
titel: "Skills evaluieren: wiederkehrende operative Verfahren als SKILL.md fixieren"
geltungsbereich: alle
risikoklasse: sicher
status: offen
prioritaet: niedrig
klasse: prozess
zugkraft: irgendwann
stufe: sprung
beruehrt: ["/home/claude-deploy/.claude/skills/"]
stand: 2026-05-24
erzeugt_durch: Framework-Retro nach BOOT-001/PLAT-001
---

## Anlass

Skills (Ordner mit `SKILL.md` + optional Hilfsskripten) fixieren eine exakte,
wiederholbare Vorgehensweise für einen Aufgaben-Typ, sodass Claude (Chat und/oder
Claude Code) das Verfahren gezielt lädt, statt es aus mehreren Dokumenten neu
zusammenzusuchen — und sodass fehleranfällige Verfahren nicht jedes Mal neu
schieflaufen.

Das Wissens-Problem löst das Framework bereits (die Verfassung IST die
organisierte Anleitung). Skills greifen woanders: bei **exakten, wiederholbaren
technischen Verfahren, die nicht schiefgehen dürfen** — nicht bei Strategie,
Diskussion, Einmal-Entscheidungen.

## Erkannte Kandidaten (aus PLAT-001)

1. **Docker-Update** — Container-Update mit Pre-Versions-Capture, Health-Check,
   `--force-recreate` nach Topologie-Edit, Postgres-/State-Container-Inhalts-
   Stichprobe (E19), Rollback-Cache. Hochgradig wiederholbar + fehleranfällig.
2. **Rollback-Cache-Pinning** — `apt-get download pkg=$(dpkg-query -W -f='${Version}' pkg)`
   + Verifikation Cache-Version == installierte Version. Claude Code hat das
   einmal falsch gemacht (neue statt alte Version geladen, A3 in PLAT-001).
   Eigener Seed `seed-apt-get-download-pin.md` existiert bereits.
3. **Container-Recreate-Health-Check** — „Container läuft ≠ Daten da" (E19):
   nach jedem Recreate eines State-Containers Inhalts-Stichprobe als Beweis.

## Offene Fragen — VOR dem Bau zu klären (bewiesen, nicht angenommen)

- **Sync-Mechanik (Kernfrage):** Gilt eine Skill für Chat UND Claude Code? Lädt
  Claude Code Skills aus beliebigem Repo-Pfad oder nur festem Verzeichnis? Sieht
  der Chat dieselbe Skill? Das ist VOR dem ersten Bau zu verifizieren — nicht
  aus dem Gedächtnis annehmen.
- **Schreibrichtung bleibt:** Eine Skill ist eine Datei wie jede andere →
  Claude Code schreibt sie, Chat entwirft den Inhalt (E4 unverändert).
- **Whitelist/Sync:** Wo leben Skills, und werden sie ins GitHub-Sync
  aufgenommen (damit der Chat-Architekt sie sieht) oder nur lokal (Claude Code)?

## Trigger

Erst bauen, wenn ein Kandidat-Verfahren zum **dritten Mal** auftaucht (bewiesene
Wiederholung) — analog zum `seed-pretooluse-hook-allowlist`-Muster: erst
Notwendigkeit beweisen, dann Mechanik bauen. Eine Skill für etwas Einmaliges zu
bauen ist dieselbe Über-Zeremonie, die das Stufen-Modell gerade abschafft.

Vor dem ersten echten Bau: Sync-Mechanik sauber abklären (eigener kurzer
Recherche-Schritt/Sprung).
