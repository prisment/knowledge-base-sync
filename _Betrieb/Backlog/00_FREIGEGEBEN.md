---
typ: derivat
titel: "Freigegebene Halde (generiert)"
erzeugt_durch: "scripts/backlog/generate_uebersicht.py (PLAT-035 Bündel 1)"
hinweis: "GENERIERT aus Seeds mit autonom_ziehbar: ja. Nicht händisch editieren — Änderungen am Seed."
---

# Freigegebene Halde

Seeds, deren freigegebene Spec `risikoklasse: sicher` trägt und die im Seed `autonom_ziehbar: ja` haben (PLAT-033 / PLAT-034). Ein Block = eine Abhängigkeitskette; innerhalb sortiert Kahn (Wurzel zuerst), Sekundärschlüssel `zugkraft`. Blocker friert die betroffene Kette ein (Verfassung 01, Abschnitt "Autonome Halde").

## Block 1 — next-spec-id: untracked Working-Tree-Specs einbeziehen, damit IDs nicht kollidieren

| # | Datei | zugkraft | abhängig von |
|---|-------|----------|--------------|
| 1 | `seeds/seed-next-spec-id-working-tree.md` | bald | - |

## Block 2 — Notausgang-Doku: schriftliche Rescue-System- und Telefon-Tailscale-Prozedur

| # | Datei | zugkraft | abhängig von |
|---|-------|----------|--------------|
| 1 | `seeds/seed-notausgang-doku.md` | bald | - |

## Block 3 — systemd-resolved Cache: Origin-IP statt CF-Edge bei Hairpin-Aufrufen vom Server selbst

| # | Datei | zugkraft | abhängig von |
|---|-------|----------|--------------|
| 1 | `seeds/seed-dns-cache-resolved-hairpin.md` | irgendwann | - |

---

**SSOT:** Seed trägt Wahrheit (`autonom_ziehbar`-Feld), Halde ist Ansicht.
