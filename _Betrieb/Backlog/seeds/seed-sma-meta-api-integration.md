---
typ: backlog_seed
titel: "Meta Graph API Integration (echte Analytics, BoostCard, Voice-Learning-Trigger, Hashtag-Performance)"
geltungsbereich: prisment
risikoklasse: kritisch
status: offen
klasse: feature
zugkraft: bald
stufe: spur
beruehrt: ["langgraph/analytics/", "langgraph/content/", "pwa/pwa-api/", "n8n/"]
stand: 2026-05-25
erzeugt_durch: ROADMAP_SMA-Zerlegung (Schritt 14 + 13.4 + 15.2 + 14.6 + 44.6-Abhängigkeit + Voice-Learning P0-PARKED + BoostCard-Followup)
---

## Anlass

Analytics-Agent ist architektonisch fertig, aber alle Engagement-Metriken sind
heute Stub-Daten. Damit hängen mehrere Folge-Features fest, die in der alten
SMA-Roadmap als unabhängige Schritte gelistet waren, aber faktisch ein
gemeinsames Bündel sind:

- Boost-Check-Agent kann ohne echte Engagement-Daten keine Empfehlung treffen
- Hashtag-Performance-Feedback (alte Schritt 13.4) braucht Reach/Impressions
- Voice-Learning-Auto-Trigger ist PARKED bis Performance-Daten da sind
  („Top-Performer-Phrasen" — Learning-Loop gemeinsam mit Analytics
  re-konzipieren)
- BoostCard im Chat-Stream zeigt heute keine echten Trigger-Gründe
- Geposted-Section (alte Schritt 44.6, eigener Seed) zeigt heute "—" oder
  "bald" statt echte Likes/Engagement
- Mandanten-Profilbild-Upload (alte 14.6) bleibt MVP "Initiale + display_name",
  bis Insta-/FB-Avatar gerendert werden kann
- Monatsreport-Presentation-Data-Node (alte 15.2) bleibt synthetisch

App-Review bei Meta dauert 1–3 Wochen — der Antrag muss früh raus, parallel
zur Arbeit am Code.

## Ziel (Soll-Zustand, grob)

Ein zusammenhängendes Bündel, das die Stub-Schicht zwischen Posts und echtem
Engagement schließt:

1. **Meta Developer Setup + App-Review-Antrag.** App anlegen, Scope
   `instagram_manage_insights` + `pages_read_engagement` beantragen.
2. **OAuth-Flow pro Mandant.** Long-Lived Page Access Token, Persistenz in
   `tenant_configs` (oder eigener Tabelle), Token-Refresh-Worker vor Ablauf
   (60 Tage), `/internal/notify` type=admin_alert bei Fehler.
3. **Sync-Worker.** Cron täglich (über APScheduler in pwa-api, NICHT n8n —
   Migration ist durch): recent Posts via Graph API → Metriken in
   `analytics_reports` / neue `post_metrics`-Tabelle.
4. **Boost-Check-Agent.** Bekommt echte Trigger-Gründe aus Post-48-72h-Daten
   statt heuristischer Schätzung.
5. **Hashtag-Performance-Feedback.** Top-Performer pro Tenant in
   `hashtag_performance.json`/-Tabelle; Content-Agent nutzt das in der
   `select_hashtags()`-Rotation.
6. **Voice-Learning-Trigger.** Performance-Signal triggert das bereits
   existierende `/voice-learning`-Endpoint pro Tenant (System-APScheduler
   wöchentlich oder ereignisbasiert nach genug Datenpunkten — Entscheidung in
   der Spec).
7. **PWA-Anbindung.** BoostCard im Chat-Stream zeigt echte Trigger,
   Geposted-Section zeigt echte Metriken, StatusCard verlinkt zu
   Performance-View pro Post.
8. **Mandanten-Profilbild-Upload.** PWA-Onboarding-Form +
   `customer/assets/{tenant_id}/profile.jpg` + Spalte
   `tenants.profile_picture_url`. Wird in Mockups + Geposted-Section als
   Avatar gerendert.
9. **Presentation-Data-Node.** Monatsdaten aggregieren als strukturiertes
   JSON für die Konzept-Präsentation (löst alte 15.2).

## Offene Fragen für die spätere Spec

- **App-Review-Strategie:** ein App-Account für Prisment-Platform mit
  Multi-Tenant-OAuth, oder pro Mandant eigene Meta-App? (Aufwand vs.
  Skalierung — Standard ist Multi-Tenant über eine Platform-App.)
- **Token-Storage:** Klartext in `tenant_configs.config.meta_access_token`
  reicht für MVP, oder Secret-Store? (DSGVO-relevant — gehört in den
  DSGVO-Sammel-Seed mit.)
- **Sync-Frequenz:** täglich pro Post bis N Tage alt, dann eingefroren? Oder
  längerer Tail für Trend-Analysen?
- **Voice-Learning-Trigger-Logik:** wöchentlicher Cron vs.
  ereignisbasiert (N approved Posts seit letztem Lauf)?
- **Hashtag-Performance-Persistenz:** weiter als JSON in Gitea, oder
  Migration zu DB-Tabelle (analog zu A3-Konsolidierung)?
- **Boost-Empfehlung-Schwelle:** ab welchem Engagement-Verhältnis triggert
  die BoostCard? (Heuristik in Faktensammlung am echten Grubi-Datensatz
  erarbeiten, sobald Metriken da sind.)

## Stufe / Risiko

**Spur.** Architektur-gestaltend (neuer externer Sub-Processor, neue
Datenpfade, mehrere abhängige Folge-Features), berührt 4 Agents + PWA + n8n-
respektive APScheduler-Worker + neue DB-Tabellen. Risikoklasse `kritisch`
(externer Service, Token-Handling, Mandanten-Daten via Meta) — erzwingt Spur.

App-Review-Wartezeit (1–3 Wochen) ist eigener Zeit-Faktor und gehört in
die Bündelung — Antrag-Bündel zuerst, Code-Bündel parallel zur Wartezeit.

## Trigger / Reihenfolge

Bald — sobald die laufenden Sicherheits-/Härtungsprojekte (HAERTUNGS_SPEC +
ROADMAP_cloudflare_migration) durch sind oder das System auf Mandant #2
stabil läuft. App-Review-Antrag kann allerdings JETZT raus, weil die
Wartezeit unabhängig vom Code-Stand ist.

## Verweis

- `seed-sma-geposted-section.md` — hängt direkt davon ab.
- `seed-sma-dsgvo-sammel.md` — Meta als neuer Sub-Processor muss in AVV/DSFA.
