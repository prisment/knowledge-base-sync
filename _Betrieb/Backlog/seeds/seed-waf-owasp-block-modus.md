---
typ: backlog_seed
titel: "Cloudflare WAF OWASP Core Ruleset von 'log' auf 'block' umstellen (nach Live-Tuning)"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: security
zugkraft: irgendwann
stufe: schritt
beruehrt: ["Cloudflare Zone prisment.de → Security → WAF"]
stand: 2026-05-26
erzeugt_durch: "Phase-7-Atomisierung ROADMAP_cf_migration Phase-7-Backlog"
---

## Anlass

OWASP Core Ruleset ist aktuell im LOG-Modus (Block 5.5 der CF-
Migration). Heißt: Treffer werden gezählt, aber Requests gehen
durch. Default ist konservativ, weil OWASP-Pattern False-Positives
auf legitimen PWA-Traffic produzieren kann (CSRF-Token, Multipart-
Form, JSON-Bodies). Nach mehreren Tagen oder Wochen Live-Beobachtung
sollte ausreichend klar sein, welche Patterns wir scharfschalten
können.

## Ziel (Soll-Zustand, grob)

- CF-Dashboard → Security → Events: OWASP-Treffer der letzten 14-30
  Tage sichten.
- Pro Treffer-Pattern entscheiden: false-positive (Exception
  einbauen) oder echter Angriff (auf BLOCK setzen).
- Sensitivity-Level prüfen (aktuell „Medium", evtl. „Low" um
  False-Positives weiter zu senken bei gleichzeitig schärferem
  Block-Modus).
- Endzustand: OWASP-Ruleset auf BLOCK, dokumentierte Exceptions
  für die wenigen False-Positive-Patterns.

## Stufe / Risiko

**Schritt** — Dashboard-Klick. Aber Aussperrungs-Risiko für legitime
Endnutzer wenn Tuning unvollständig ist. Pflicht: Beobachtungs-
Fenster nach Umstellung (24h), Rollback per Klick.

## Trigger

Irgendwann — frühestens 14 Tage nach Phase 5/6 (PWA wieder live mit
echtem End-User-Traffic), sonst fehlt die Datenbasis fürs Tuning.
