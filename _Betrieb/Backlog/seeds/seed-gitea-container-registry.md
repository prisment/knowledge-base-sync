---
typ: backlog_seed
titel: "Gitea-Container-Registry-Pfad für Custom-Builds (End-to-End-Pull-Pfad)"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: prozess
zugkraft: irgendwann
stufe: sprung
beruehrt: ["internal_gitea Container-Registry-Feature", "scripts/build_image.sh", "internal_diun-Konfiguration"]
stand: 2026-05-26
erzeugt_durch: "PLAT-021 Phase 3 — Option (c) bewusst abgezweigt, weil Pull-Pfad (a)+(b) zuerst zu beweisen ist"
---

## Anlass

PLAT-021 baut den Update-Pfad für Custom-Builds als Kombi aus
Base-Image-Pin + Nightly-Trigger (Achse 1) plus Upstream-API-Check
für n8n (Achse 2 Fremdcode) plus Renovate für npm/pip (Achse 2
Eigen-Apps). Das ist ein **Pull**-Modell: nightly fragt aktiv ab und
triggert Rebuild.

Eine konzeptionell sauberere Alternative wäre ein **Push**-Modell:
`build_image.sh` pusht jeden Build (`:<sha>`) ins Gitea-Container-
Registry; Diun watcht von dort. Damit verschwindet die heutige
„Custom-Build-Diun-blind"-Lücke strukturell — Diun sieht jedes
Image, das in der eigenen Registry liegt.

PLAT-021 hat sich bewusst für den Pull-Pfad entschieden, weil:
- Das Gitea-Container-Registry-Feature noch nicht aktiviert ist
  (zusätzliches Bewegteil + Storage-Anstieg).
- Der Pull-Pfad mit Bord-Mitteln auskommt (Docker-Hub-API,
  GitHub-API).
- Diun sowieso perspektivisch zurückgebaut wird
  (`seed-diun-rueckbau`).

## Soll-Zustand

Wenn der Pull-Pfad aus PLAT-021 im Lauf zu wartungsintensiv wird
(z.B. häufige Trigger-Fehler, Race-Probleme zwischen Base-Drift-
Check und Nightly-Apply, Sichtbarkeits-Lücken durch Tag-Konflikte):

- Gitea-Container-Registry aktivieren (`ENABLE_PACKAGES=true` o.ä.).
- `build_image.sh` erweitern: nach erfolgreichem Build zusätzlich
  `docker tag` + `docker push` ins Gitea-Registry
  (`git.prisment.de/admin/<image>:<sha>` + `:latest`).
- Diun-Konfig auf Gitea-Registry-URL umstellen für die 10 Custom-
  Build-Image-Namen.
- AKTUALITAET.md zieht künftig auch aus Diun-Registry-Watch statt
  nur aus nightly-1c.

## Stufe / Risiko

**Sprung.** Konfig-Änderung in Gitea (Feature aktivieren), Build-
Skript-Anpassung, Diun-Konfig-Update. Reversibel pro Komponente.
Risikoklasse `sicher`, weil keine Auth-/Tunnel-/DB-Berührung — die
Registry läuft Container-intern auf `internal_gitea`.

## Trigger

**Irgendwann.** Konkrete Auslöser:
- Pull-Pfad aus PLAT-021 schlägt strukturell fehl (Trigger-Race,
  Fehlerquote im nightly-Report > akzeptabel).
- Diun-Rückbau steht an (`seed-diun-rueckbau` getriggert) und der
  Registry-Push würde den Rückbau vereinfachen.
- Eine neue Custom-Build-App kommt dazu, für die der Pull-Pfad
  nicht mehr ausreicht.

## Hinweis

Nicht starten, bevor PLAT-021 mindestens 2 Wochen sauber gelaufen
ist. Sonst doppeltes Bauteil ohne Beweis welches besser passt.
