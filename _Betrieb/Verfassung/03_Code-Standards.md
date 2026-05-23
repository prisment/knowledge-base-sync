---
typ: verfassung
titel: "Code-Standards"
stand: 2026-05-23
aenderung: "nur nach oben, nur durch bewusste Freigabe"
---

# 03 — Code-Standards

## Code gehört NICHT in die Knowledge-Base

Die Knowledge-Base dokumentiert ÜBER Code, sie enthält ihn nicht. Echter Code (Skripte, App-Code, Workflow-Definitionen) lebt im separaten Code-Repo. Grund: Code an zwei Orten = zwei Versionen = zweite Wahrheit = Bruch des Single-Source-of-Truth-Prinzips.

Konsequenz: `.py`-Dateien, n8n-Workflow-JSONs, Shell-Skripte und Ähnliches werden bei der Migration NICHT in die neue Struktur einsortiert, sondern als „gehört ins Code-Repo" markiert und ausgesondert.

## Single Source of Truth

Jede Information existiert genau einmal. Ansichten (SVG-Übersichten, Zusammenfassungen) werden aus der einen Quelle ABGELEITET, nie als zweite Quelle gepflegt. Eine veraltete Doku ist schlimmer als keine, weil man ihr vertraut.

## Standards-Platzhalter (wachsen mit der Arbeit)

Konkrete Code-Vorgaben (Test-Strategie, CI/CD, Data-Integrity, Tenant-Isolation) werden aus den realen Zyklen heraus eingearbeitet — beginnend mit dem Security-Refactor (RLS + Tenant-Isolation) und der Data-Integrity-Pipeline. Bis dahin gilt diese Datei als Platzhalter, der nach jedem relevanten Zyklus über Phase 9 wächst (nie nach unten).
