---
typ: backlog_seed
titel: "DSGVO-Auftragsverarbeitungsvertrag mit Cloudflare unterzeichnen"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: prozess
zugkraft: bald
mission: kunde-2
stufe: schritt
beruehrt: ["Cloudflare Account Settings", "Datenschutz-Doku (Prisment/Strategie?)"]
stand: 2026-05-26
erzeugt_durch: "Phase-7-Atomisierung ROADMAP_cf_migration Block 8.3"
---

## Anlass

Mit der CF-Migration (Pro-Plan aktiv, Tunnel + Access für 7 Tools +
Public-Proxy für 4-6 Hostnamen) gehen Mandanten-Identitäten und
Login-Daten über Cloudflare als US-Anbieter. DSGVO verlangt einen
Auftragsverarbeitungsvertrag (DPA). Cloudflare bietet einen Standard-
DPA an, der via Dashboard angenommen werden kann. Aktuell nicht
unterzeichnet.

## Ziel (Soll-Zustand, grob)

- Cloudflare-Standard-DPA unterzeichnen (im CF-Account-Settings-
  Bereich).
- Bewertungsdokument erstellen:
  `Prisment/Strategie/DSGVO_Cloudflare_DPA_Bewertung.md` —
  welche Mandantendaten gehen wo durch (Tool-Logins, PWA-Sessions,
  Webhooks), welche Sub-Prozessoren nutzt Cloudflare, was steht im
  DPA, was sind die Auftragsverarbeiter-Pflichten.
- Bei Mandanten-Kontakt mit personenbezogenen Daten (z.B. Endkunden
  in Voice-Konversationen): Liste der Mandanten, bei denen der DPA
  mit Prisment auch CF-Sub-Prozessoring erwähnen muss.

## Stufe / Risiko

**Schritt** — pure Dokumentations- und Vertragsaktion. Null
technisches Risiko.

## Trigger

Bald — Rechtspflicht ab dem Moment, wo Mandantendaten über CF
laufen (= heute). Kein Eilfall im technischen Sinn, aber
Compliance-Schuld pro Tag.
