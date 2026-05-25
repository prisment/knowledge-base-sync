---
typ: roadmap
titel: "SMA — Social Media Automation Qualitätsarbeit"
geltungsbereich: prisment
status: in_arbeit
stand: 2026-05-25
umzug: "2026-05-25 von Prisment/Arbeitsgedaechtnis/SMA_ROADMAP.md hierher (PRIS-015 Bündel 7)"
---
# Roadmap – Social Media Automation Qualitätsarbeit

**Ziel:** Vom technisch-fertigen System zur produktiven Qualität: Outputs (Interview, Content, Redaktionsplan) müssen so gut sein, dass ein realer Mandant sie ohne große Nachbearbeitung verwenden kann.
**Stand:** 2026-05-25 — Telegram-EOL durch PLAT-009 abgeschlossen, ⬜-Schritte mit Telegram-Bezug re-evaluiert (PRIS-015 Bündel 7). ✅-Schritte beschreiben historischen Verlauf und bleiben unverändert.

---

## 🎯 Aktive Roadmap — priorisierte Arbeitsliste

> Diese Liste ist die **aktuelle Arbeitsreihenfolge**. Die historische Schritte-Liste (1–43) weiter unten ist chronologische Doku; alle Detail-Sektionen (Schritt 17, 18, 19, 21, 22, 23, 41 etc.) bleiben als technische Referenz erhalten.

### ✅ Chat-First-Migration (2026-05-18 bis 2026-05-20): Schicht 1–4 vollständig

> Komplette Chat-First-App nach Spec `CHAT_FIRST_SPEC.md`. Alle alten
> Schritt-für-Schritt-Wizards (Onboarding/Check-Wizard, Composer) sind
> durch eine deterministische Konversations-Maschine (`conversation/
> orchestrator.py`) ersetzt. App-Hauptansicht ist `ChatStream`.

**Schicht 1 — Fundament (Woche 1-2):** ✅
- ChatStream + ChatInput (Text + Voice via Whisper)
- 8 Karten-Typen: PushPermissionCard, PlanCard, PostCard, VariantSliderCard,
  PhotoUploadCard, BoostCard, StatusCard, ScheduleSetupCard + QuickReplyMessage
- Greeting + PushPermissionCard + Hamburger (4 Einträge)
- `chat_messages` + `chat_conversation_state`-Tabellen (Mig 015)

**Schicht 2 — Onboarding (Woche 3-4):** ✅
- Pfad 1 komplett im Chat: Push → Plan-Preview → Interview → Three-Styles
  → Variant-Pick → Foto → Mockup → Send → Schedule-Setup → Complete
- 13 Onboarding-Phasen, ~12 ALLOWED_ACTIONS
- Three-Styles mit Compliance-α
- Multi-Post-Loop nach Schedule (Sub-C5b)
- Plan-Regen wenn nicht vorhanden

**Schicht 3 — Wochenflow + Post-Lifecycle (Woche 5-6):** ✅
- Pfade 2, 3, 3b, 5, 6, 8 im Chat
- APScheduler triggert Chat-Events (interview_cron_tick,
  interview_reminder_tick, posting_zeitpunkt_tick, auto_post_publish_tick)
- Inline-Edit für Plan-Topics + Caption (Sheet-Pattern analog Plan-Edit)
- Auto-Post-Toggle in Profil (Stub bis Meta-API)
- Plan-Regeneration Event-getriggert am Monatsende
- Mig 016 (`topics.scheduled_date`), Mig 017
  (`posts.posting_reminder_sent_at`), Mig 018 (auto-post + plan-regen)

**Schicht 4 — Helpdesk-Agent + Free-Text (Woche 7-8):** ✅
- Neuer Container `langgraph_helpdesk` (Voyage AI Embeddings + Haiku-
  Klassifikator) + Mig 019 (`helpdesk_chunks` + `helpdesk_audit_log`)
- 8 FAQ-Markdown-Files in `helpdesk_knowledge/`
- Intent-Klassifikator (4 Klassen): Routing / Support / Action / Fallback
- Action-Tools mit Bestätigung (5 Tools)
- Zweistufige Email-Eskalation (info@prisment.de)
- Konversations-History (letzte 6 Messages an Klassifikator)
- Spontan-Post-Pfad 4 (vollautomatisch via Interview-Agent breaking_news
  + Content-Agent one-style + Format-Auswahl Story/Feed/Karussell)

**Offene Lücken (Bundles für Polish-Phase, Stand 2026-05-20 Audit):**

**Bundle 1 — Funktions-Gaps:** ✅ Live (2026-05-21)
- ✅ 1.1 Helpdesk Klasse-1-Routing rendert PlanCard / StatusCard / Klärungs-
  texte direkt (pwa-api ist Single-Source-of-Truth).
- ✅ 1.2 `resume_interview` in `TOOL_DISPATCH` + Prompt + `_describe_tool`.
- ✅ 1.3 Admin-Mail bei `all_blocked` (verified, läuft via Adapter).

**Bundle 1.5 — Plan-Polish:** ✅ Live (2026-05-21)
- ✅ Done-Marker (grüner Haken) in PlanCard + RedaktionsplanList für
  Topics mit Post-Status `approved` oder `published` (motiv_id-Cross-
  Post-aware).
- ✅ Spontan-Post bekommt sprechenden Titel aus User-Eingabe (statt
  hardcoded „Spontan-Post").
- ✅ StatusCard + `/posts/published`: Format in Meta-Zeile + Geposted-
  Seite optisch auf StatusCard-Stil angeglichen.

**Bundle 2 — Hardening (nächste Iteration):**
- Rate-Limiting für Helpdesk (Spec E.12: 60/h Tenant, 30/h User)
- `helpdesk_log_retention_tick` (Spec E.10: 90-Tage-Archivierung)

**Bundle 3 — Tech-Debt:**
- Alte Routen entfernen (`/onboarding`, `/answers`, `/analytics`,
  `/posts/[id]`, `/posts/[id]/composer`) — D.1 verlangt das, sind
  aktuell unverlinkt aber vorhanden
- Pre-Mig-014-Schema versioniert in `pwa/migrations/` ablegen

**Aktiver Deep-Dive (Korbinian-getrieben):**
- 🔄 Interview-Prompt-Tiefen-Diskussion (Task #50) — iterativ am echten
  Mandanten, Architekt führt; pwa-api bleibt aufnahmebereit für
  Prompt-Patches im interview-Agent.

**Followups (parkiert, kommen nach Bundle 2/3):**
- LLM-Hashtag-Fallback wenn Pool-Match knapp (Task #48)
- BoostCard-Trigger via Analytics-Worker (braucht Meta-API)
- Caption-Edit auf PostCard Phase B (aktuell nur Phase A)
- Status-Icon `◯ pending Review` in PlanCard (vorerst bewusst weg-
  gelassen — Aesop-Ruhe schlägt Vollständigkeit, 2026-05-21)

### ✅ n8n-Cron-Migration (2026-05-18): Cron-Trigger → Code-Scheduling

> Alle 4 n8n-Cron-Workflows (weekly-interview, monthly-cycle, redaktionsplan-letzter-freitag, daily-boost-check) deaktiviert. Cron-Trigger laufen jetzt im APScheduler-Worker in `pwa-api`. Klasse A (Interview) tenant-konfigurierbar via Wizard/Profile, Klasse B (Redaktionsplan) als generische `scheduled_jobs`-Queue, Klasse C (Analytics) bewusst nicht migriert — kommt mit Analytics-Neukonzept. Onboarding-Hook setzt erstes Interview +1min nach Abschluss. Migration 014, Backfill durch, n8n-JSONs unter `n8n/workflows/_disabled/`. Spec: `04_Architektur/ROADMAP_n8n-cron-migration.md`. Erste Live-Bewährungsprobe: Mo 25.05. 9:00 (Interview Grubis), 1. Juni 4:00 (Redaktionsplan Grubis).

### ✅ Tenant-ID-Migration (2026-05-13): grubis-weine → ULID

> Hybrid-Schema: ULID als interne `id` + menschen-lesbarer `slug` + `customer_number` für CRM/ERP-Anschluss. Vorher waren tenant-IDs Slugs (z.B. "grubis-weine") — nicht stabil bei Namensänderung, nicht CRM-tauglich, datenschutzlich grenzwertig (sprechende ID in Logs).

**Schema-Erweiterung `tenants`:**
- `id` VARCHAR(64) PK → ULID-Format `t_<26 Crockford-Base32>` (zeit-sortierbar)
- `slug` VARCHAR(64) UNIQUE — menschen-lesbarer Identifier (URL, Gitea-Repo-Name)
- `customer_number` VARCHAR(16) UNIQUE — CRM-/ERP-tauglich (`K-0001`, `K-0002`, ...)
- `display_name` — UI-Anzeige

**Beispiel:**
- alt: `grubis-weine`
- neu: id=`t_01KRH8GXBXBY4YEM3HPZ9QRR3Y` / slug=`grubis-weine` / customer_number=`K-0001` / display_name=`Grubis Weine`

**Migration durchgeführt 2026-05-13:**
- 14 Tabellen in agent_data + 3 in pwa_app — atomar in Transaktion
- JSON-Selbst-Referenzen umgeschrieben (posts.media_paths URLs, presentations.draft_data)
- Asset-Pfad: `/customer/assets/grubis-weine/` → `/customer/assets/t_01KR.../`
- 2 n8n-Workflows mit hartcodierter tenant_id gepatcht (DB-direkt)
- Snapshot (pg_dump + tar) in `/tmp/tenant_migration_20260513_200933/`
- Aktive Browser-JWTs verweisen noch auf alte ID → User-Re-Login einmalig nötig

**Konsequenzen für Folge-Arbeit:**
- Schritt 14 (Meta API): kann `customer_number` als externe Referenz nutzen, `slug` für URL-Routing, `id` als interne FK
- Schritt 45 (Onboarding): neue Tenants bekommen ab sofort direkt ULID + Slug + Customer-Number
- Pwa-API/Agents: tenant_id ist URL-/Body-Parameter — kein API-Vertrag betroffen, nur die *Werte* sind ULIDs statt Slugs
- Künftige Code-Erweiterungen die slug/customer_number anzeigen wollen: einfach aus `tenants`-Tabelle ziehen (DB-Schema-Update ist erledigt)

### ✅ UX-Reset (2026-05-13): Bottom-Nav raus, Avatar-Menu rein

> Pattern wie moderne B2C-SaaS (Linear / Cash App / Robinhood): Single-Screen-Home + Workflow-Overlays + Avatar-Menu oben rechts. Bottom-Nav komplett entfernt.

- `components/UserMenu.tsx` neu — Avatar-Initiale + Dropdown mit Redaktionsplan / Geposted / Spontan posten / Frag Prisment / Profil / Team / Logout.
- **Workflow-Indikator:** pulsierender roter Punkt am Avatar wenn `pending_total > 0` oder `active_workflow != null`. Layout liest `/queue` server-side für initialen State.
- `layout.tsx`: Bottom-Nav entfernt, `pb-24 → pb-8`, UserMenu im Header.
- Alle `fixed bottom-16` (Action-Bars) → `fixed bottom-0` (nichts mehr darüber).
- `/posts`-Liste bleibt nur als History-Filter (z.B. `?status=published` für Geposted), nicht mehr Hauptnavigation.

### 🟥 GRUBI-LIVE-ERKENNTNISSE (2026-05-12) → SCHRITT 44–46 — ABSOLUTER VORRANG

> Erster Live-Test mit Grubi 2026-05-12. **Schritte 44–46 sind die nächsten 3 Großblöcke** und ziehen ALLEM unter dieser Sektion vorbei. Viele P1/P2-Items werden durch die UX-Neudefinition obsolet oder neu geschnitten — wir bewerten P1/P2 nach Abschluss von Schritt 44 neu. Volle Details + Workflow-Beschreibungen in eigener Doku **`PWA_DESIGN.md`** im Projektordner.

**Was Grubi am wichtigsten ist (Priorität 1):**
1. Fertiger Post + frictionless Posting an FB/Insta. Er muss in der App den fertigen Post (mit Hashtags, Foto/Video, alles drin) sehen können — er muss in Insta/FB **nichts mehr selbst zusammenbauen**.
2. Posts müssen **gefallen** — nicht nur authentisch sein. Authentisch UND ansprechend sind zwei verschiedene Dinge.
3. Es muss **schnell** gehen. Interview läuft schon gut, aber Content-Generierung ist zu langwierig + intransparent.

**Was Grubi NICHT gefallen hat:**
1. UX zu kompliziert — wirkt wie Business-ERP statt End-User-Easy-Posting.
2. Email-Notifications waren störend — **nur noch Push** zum richtigen Zeitpunkt.
3. Workflow nicht selbsterklärend — er brauchte Erklärung.
4. Er konnte nichts direkt ändern.

**Versprechen das wir einlösen müssen:** „Interview — Foto — Fertig!" In einem Sitz. Ohne Zwischenstop. Mit fertigem Post am Ende.

---

#### ⬜ Schritt 44 — UX & Workflow radikal vereinfachen (Frictionless-Workflow)

**Übergeordnetes Ziel:** End-User-Experience radikal vereinfachen. User wird intuitiv durchgeleitet ohne Erklärung und hat danach seine Posts. „Interview – Foto – Fertig."

##### 44.1 ✅ Redaktionsplan ohne Freigabe — IMPLEMENTIERT 2026-05-13
- Plan wird einfach erstellt, ist da, fertig. **Keine Freigabe-Pflicht mehr.**
- Es gibt weiterhin den Menüpunkt „Redaktionsplan"
- Themen können dort manuell vom User direkt überschrieben werden
- Zusätzlich: Option den Redaktionsplan-Agent für Überarbeitung zu triggern
- Wenn User gar nichts macht: auch OK — Plan ist nutzbar
- *Konsequenz für bestehende Bugs:* 41.5 + 41.6 (Revise-Spinner / 2. Revision tot) werden teilweise obsolet — die Plan-Revision wird optional / sekundär.

##### 44.2 ✅ Interview-Agent direkter nachfragen — IMPLEMENTIERT 2026-05-13 (Prompt-Edit)
> Zeitpunkt-Konfiguration kommt in Schritt 45 (Onboarding) — hier nur der Prompt-Sharper.
- User legt Zeitpunkt selbst fest (kommt unter Onboarding-Schritt 45)
- User bekommt Push zum festgelegten Zeitpunkt → führt Interview
- Interview-Agent muss DIREKTER nachfragen wenn Antwort sperrlich/kurz:
  - „Ich brauche von dir mehr Inhalt zu den Themen, bitte 2–3 Sätze über X und Y"
  - **NIE Worte in den Mund legen** — kein „Du hast ja sicher gemeint, dass…"
  - Normale Nachfragen weiterhin OK
- Konsequenz: Interview-Agent-Prompt erweitern um „Mehr-Inhalt-Forderung" wenn Antwort < N Wörter oder evident sperrlich.

##### 44.3 ✅ Content-Generierung pro Topic + parallel — FIXED 2026-05-13 11:22
- Aktuelles Verhalten (problematisch): Interview komplett durch → Content-Agent startet 1 großen Job für alle Topics → mischt Themen.
- **Neu:** Interview-Agent triggert Content-Agent **direkt nach Topic 1** (nach erster vollständiger Beantwortung). Pro Topic ein separater Content-Run, kein Misch-Job.
- Parallelität: Während User Topic 2 beantwortet, generiert Content-Agent bereits Post 1 zu Topic 1.
- Wenn User mit Interview durch ist, sind die Posts (oder Teile davon) idealerweise schon fertig.
- Performance-relevant: massive Wartezeit-Reduktion am Ende.

##### 44.4 ✅ Content-Prüfung im Flow — IMPLEMENTIERT 2026-05-13
Nach Interview-Ende landet der User **sofort im gleichen Workflow** zur Content-Prüfung.
- **Erste Seite pro Post: Regie-Brief-Ansicht** (Text + Bildbeschreibung etc., wie aktueller Pending-Post)
- Zwei Buttons: **„Weiter"** und **„Anpassen"**
  - „**Anpassen**" = explizites Feedback. Der Post wird als **Negativ-Beispiel** vermerkt + Anweisungen aus Feedback extrahiert (bestehender Mechanismus aus Bug 41.12 Fix). User schreibt was er anders haben will.
  - „**Weiter**" = **implizites Positiv-Feedback**. Alles was via Weiter durchläuft wird automatisch als Positiv-Beispiel vermerkt (Mechanismus aus Schritt 42 Voice-Learning).
- Weiter → **Bild/Video-Seite**: zwei Optionen
  - „Bild/er oder Video hinzufügen"
  - „später hinzufügen"
- Nächste Seite: **Fertiger-Post-Simulation** — Ansicht sieht aus wie Insta/Facebook-Post. Foto (evtl. mit Schrift-Overlay), Caption, Hashtags — alles komplett wie der Endpost.
- User sagt **„Fertig"** oder kann dort noch manuelle Anpassungen machen (Text-Edit, Hashtag-Edit etc.). Endet aber immer mit „Fertig".
- Selbiger Workflow für Post 2, Post 3, ...

##### 44.5 ✅ Home-Tab / Queue — IMPLEMENTIERT 2026-05-13
> Status-Symbole grün/rot für Media-Verfügbarkeit + Säule unter Topic-Titel. Post-Cards verlinken direkt zu `/posts/{id}/check`. Echte Posting-Likes/Analytics warten auf Schritt 14 Meta API.

- Alle „fertigen" Posts landen in der **Queue**
- **Ganz oben:** Was als nächstes ansteht. Datum + Uhrzeit vorhanden.
- Auf einen Blick: „Nächster Post am 14.5 um 18:00"
- **Status-Symbole pro Post:**
  - „eingeplant"-Symbol
  - „alles grün" wenn Foto/Video vorhanden
  - **rotes Foto-/Video-Symbol** wenn Medien noch fehlen
- Posts in der Queue sind anklickbar → führen zur fertigen Post-Simulation
- Bei Video: Sekunde 1 als Vorschaubild verwenden
- **Bilder/Videos werden auf unserem Server gespeichert** (eigener Asset-Storage, idealerweise existierender `customer/assets/{tenant}/...`)
- **Am Post-Tag:** Push kommt → User landet direkt bei dem Post in der App → Übergabe an Insta/FB → frictionless
- Nach Übergabe: automatisch „gepostet" markieren ODER mit User-Bestätigung
- Post verschwindet aus Queue, wandert in „Gepostet"-Section

##### 44.6 Geposted-Section
- Extra Bereich mit allen geposteten Posts
- Übersicht: wann geposted, aktuelle Likes/Engagement
- **Soll motivieren** (Erfolgs-Visualisierung)
- Abhängigkeit: echte Metriken brauchen Schritt 14 (Meta API). Davor: Stub mit „—" oder „bald".

##### 44.7 ✅ Offener-Job-Indikator + Auto-Resume — IMPLEMENTIERT 2026-05-13
- Wenn User mid-Workflow die App schließt (z.B. bei Post 2 keine Lust mehr) → das ist ein **offener Job**
- Beim Wiedereinstieg landet der User **direkt dort wo er aufgehört hat** (Post 2, nächste Frage etc.)
- App symbolisiert das mit einem **Punkt auf dem Handy-Desktop / PWA-Icon** (Badge = offener Workflow)
- Das ist das **Kernfeature von Prisment**: User soll in EINEM Sitz die Woche abarbeiten.
- Versprechen: „Interview – Foto – Fertig!"

##### 44.8 ✅ Email-Notifications abdrehen — FIXED 2026-05-13
- **Nur noch Push**, keine Email mehr (Grubi-Wunsch — Email war störend)
- Push zum richtigen Zeitpunkt:
  - Wenn neuer Workflow auf User wartet
  - Am Posting-Tag, kurz vor geplantem Zeitpunkt
  - Erst-Onboarding-Push-Sequenz (Schritt 45 / 45.6)
- Email-Fallback-Cron + Email-Sofort-Fallback (Bug 41.11) **deaktivieren oder behutsam zurückdrehen** — Push als einziger Kanal
- *Konsequenz für 41.11:* Code bleibt drin, wird per Config-Switch ausgeschaltet. Falls Push komplett ausfällt: Re-Aktivierung möglich.

##### 44.x Konsequenzen für bestehende Bugs/Schritte
- **Schritt 41 (Karussell-Composite-Renderer)** ist **Teil von 44.4** — die Fertige-Post-Simulation MUSS die Karussell-Slides mit Text-Overlay zeigen. Composite-Renderer wird damit Kern-Komponente.
- **Bug 41.13** (Progress-Anzeige Content-Agent) wird **Teil von 44.3 + 44.4** — wenn Content pro Topic parallel läuft, ist die Progress-Anzeige Teil des Flow-UI.
- **Bug 41.3 + 41.4** (PWA-Schema) bleiben Quick-Fixes — am besten direkt vorm UX-Umbau mitnehmen.
- **Bug 41.5 + 41.6** (Plan-Revise) werden durch 44.1 (Plan ohne Freigabe) entwertet — Plan-Revise wird optionale Sekundärfunktion.

---

#### 🟢 Schritt 45 — Onboarding-Workflow (Walking-Skeleton im Test 2026-05-14)

**Stand 2026-05-14:** Walking-Skeleton-Approach gewählt — alle 7 Sub-Steps minimal funktional in einem Sitz, dann iterieren. Architektur-Entscheidungen (Korbinian-Freigabe):
- **1a** Schritt 29 → Schritt 45 verkettet — eine Flag `onboarded_at`, wird erst nach 45.7 gesetzt
- **2b** Topic-Auswahl für 45.2: erstes pending Topic mit `format IN ('feed','post')` (Karussell/Reel/Story skip)
- **3c** Walking-Skeleton — minimale Variante aller 7 Sub-Steps, dann iterieren

**Sub-Step-Status:**
- 45.0 ✅ Trigger-Mechanik (Schritt 29 → 45, onboarded_at-Flag-Verschiebung)
- 45.1 ✅ Redaktionsplan-Tour
- 45.2 ✅ 1-Topic-Interview
- 45.3 ✅ /onboard/three-styles content-agent Endpoint + Warteseite
- 45.4 ✅ Swipe-Picker + Auto-Feedback (Positiv + 2× Negativ)
- 45.5 ✅ Foto-Upload + Post-Simulation (reuse 44.4)
- 45.6 ✅ Erst-Post via Web-Share
- 45.7 ✅ Abschluss + onboarded_at setzen

**Stand 2026-05-15 (Walking-Skeleton-Lauf abgebrochen, Re-Konzeption nötig):**

45.0–45.3 funktionieren grundsätzlich (Push-Step → Plan-Tour mit Banner → Interview mit 1 feed_bild/instagram-Topic → 3-Styles-Generierung im content-agent). 45.4–45.7 sind als **eigene State-Machine in `/onboard/content/`** gebaut und das ist der Hauptkritikpunkt von Korbinian — die nächste Session muss das **komplett ins echte App-Menü** verlegen:

> Ich will eigentlich das normale Menü durchlaufen, kein neues erfinden. Es ist ja eine DEMO.

**Konkrete Anpassungen für nächste Session (alle aus Korbinians Test 2026-05-15):**

1. **3-Stile-Picker (45.4) — Vollbild-Slider statt Liste.** Die 3 Drafts liegen aktuell untereinander in einer Liste mit „Diesen nehmen"-Button. Soll: **Vollbild-Karussell zum horizontalen Swipen** (Pattern wie Instagram-Story-Picker). Pro Post eine Vollbild-Karte mit Caption + Hashtags + Foto-Idee + „Diesen nehmen".

2. **Pick-Step UI = echte Post-Detail-UI.** Aktuell ist die Pick-Karte ein eigenes minimalistisches Component. Soll: nach Auswahl landet der User im **echten `/posts/{id}/check`-CheckWizard** (44.4-Pattern). Voraussetzung: `/onboarding/content/pick` legt den gewählten Draft als Post in der DB an (macht er schon) — danach Redirect auf `/posts/{post_id}/check` mit Onboarding-Banner.

3. **Foto-Step (45.5) — Upload zusätzlich zur Kamera-Aufnahme.** Aktuelles `<input type="file" capture="environment">` öffnet bei iOS direkt die Kamera, kein Galerie-Picker. Soll: zwei Buttons „Foto aufnehmen" und „Aus Galerie wählen" (ohne `capture`-Attribut beim zweiten Input). ODER: einfach das **echte 44.4-Media-Upload-Pattern** wiederverwenden (CheckWizard hat das schon).

4. **Posting-Step (45.6) — „später"-Option wie bei normalen Posts.** Aktuell zwingt der Wizard zum sofortigen Posten („An Instagram/Facebook senden" + „Ich habe gepostet"). Soll: derselbe Flow wie bei normalen Posts — User kann den Post auf **„approved"** stellen (landet in der Queue), oder optional sofort sharen. Konkret: anstatt 45.6 eine eigene Page, nutze die `Approve`/`Mark-Posted`/`Skip`-Buttons aus dem normalen CheckWizard.

5. **Text-Editierbarkeit der 3 Posts.** User muss Caption/Hashtags bei den 3 Drafts editieren können (auch BEVOR er einen wählt) — das ist kritisch fürs Onboarding-Voice-Learning, weil der Inhaber im ersten Kontakt zeigt was ihm wirklich gefällt. Aktuell sind die Drafts read-only. Soll: entweder Inline-Edit im Slider-Karten, ODER Manual-Edit nach Pick im echten CheckWizard (= Variante 2 oben löst das automatisch).

**Konsequenz: `/onboard/content/*` Frontend wird umgebaut**
- `OnboardingFlow.tsx` reduziert sich auf 2 Steps: `wait` (3-Styles-Generierung) + `pick` (Vollbild-Slider mit Edit). Danach Redirect auf `/posts/{post_id}/check?tour=1`.
- `/posts/{id}/check` bekommt einen Onboarding-Banner (analog zu /plan + /answers).
- 45.7 (Abschluss) wird ausgelöst nachdem im CheckWizard ein „Approve" oder „Mark-Posted" passiert — `/api/me/onboarded` POST + Redirect zu `/`.

**Open Polish-Issues (vom Walking-Skeleton-Lauf, separat von der Re-Konzeption):**
- **AnswerInput-Textarea zu klein** (rows=1, ~44px). Bei Focus sollte sie auf 4–6 Zeilen aufpoppen — vor allem nach Voice-Transkription steht alles in einer winzigen Zeile.
- **`write_final_session` speichert `generated_questions` nicht in `sessions.metadata`** — `/interview/conversation` gibt nach close `messages=[]` zurück. Workaround steht: `userSubmittedRef` + sessionStorage-Bridge in ConversationStream. Sauberer Fix: im interview-agent beim final write auch `metadata = {"generated_questions": [...]}` persistieren.
- **Image-Build-Bug**: `docker restart` ersetzt das Image nicht. Bei Agent-Code-Change immer `docker compose up -d --force-recreate <service>`. (Dreimal hier reingelaufen, einmal in Schritt 45.2-Interview-Agent und einmal in 45.3-Content-Agent.)
- **Test-Push-Subscription-Sync** beim Test-DB-Spiegeln: Live-Push-Subscriptions werden in die Test-DB mitkopiert → Test-Stack feuert Push an Live-Endpoint. Fix war: `DELETE FROM auth.push_subscriptions` in Test, User muss sich im Test-Browser neu für Push registrieren. Sollte beim nächsten `pwa_test_env.sh sync` automatisch passieren.

**Heutige Code-Änderungen liegen in /opt/infrastructure/environment_a/:**
- `langgraph/interview/app/main.py`: InterviewMode += "onboarding"
- `langgraph/interview/app/nodes.py`: select_topics filtert auf format=feed_bild + kanal=instagram + max_topics=1 im onboarding-mode
- `langgraph/interview/app/main.py`: content-trigger nach Topic-Close + Session-Close wird im onboarding-mode unterdrückt
- `langgraph/content/app/state.py`: ContentState.style_hint
- `langgraph/content/app/nodes.py`: style_hint hängt Modifier an REGIE_BRIEF user_prompt
- `langgraph/content/app/demo.py`: run_demo_post `style_hint` + `topic_id/title/kanal` Params + neue `run_onboarding_three_styles` (3 ThreadPoolExecutor-Calls mit professionell/bodenständig/alternativ)
- `langgraph/content/app/main.py`: POST /onboard/three-styles
- `pwa-api/app/routes/onboarding.py`: 4 neue Endpoints (content/next-topic, content/start-interview, content/three-styles mit fmt=feed_bild kanal=instagram hardcoded, content/pick)
- `pwa-api/app/bridges/langgraph.py`: interview_run_onboarding + content_onboard_three_styles
- `pwa-web/app/onboard/OnboardWizard.tsx`: drastisch reduziert auf 1 Step (Push), Auto-Skip wenn schon granted
- `pwa-web/app/layout.tsx`: Onboard-Gate erlaubt /plan + /answers für non-onboarded
- `pwa-web/app/plan/page.tsx`: Onboarding-Banner wenn ?tour=1
- `pwa-web/app/answers/page.tsx`: tourMode-Prop an ConversationStream
- `pwa-web/app/answers/ConversationStream.tsx`: TourBanner, tourMode-Logik (auto-start-interview, userSubmittedRef für Redirect-Bug-Fix, sessionStorage Q+A-Bridge), flex-Layout statt fixed-bottom AnswerInput
- `pwa-web/app/answers/AnswerInput.tsx`: Voice → /interview/voice (transcribe-only) → text in textarea zum Editieren → User klickt Send
- `pwa-web/app/onboard/content/page.tsx` + `OnboardingFlow.tsx`: State-Machine wait → pick → photo → post → done (wird beim Re-Konzept reduziert)

**Live-Stand:** A1+A2.1+A2.2+A3 sind alle Live (vor 45). Schritt 45 läuft NUR im Test-Stack — Live ist davon nicht betroffen.

**Stand 2026-05-17 — Test-Stack-Run pausiert, weil neues Content-Bot-Feature dazwischenkommt:**

Korbinian hat den 45-Test-Run heute bewusst beendet, weil ein neues Feature
am Content-Bot direkten Einfluss aufs Onboarding-Ergebnis hat. Erst dieses
Feature umsetzen, dann zurück zu Schritt 45.

Was seit dem 2026-05-15-Abbruch dazwischen umgesetzt wurde:
- ✅ CheckWizard-tour-Mode + horizontaler 3-Draft-Slider + choose-Step
  (löst Korbinian-Kritik-Punkt 1 und 2 — keine eigene State-Machine mehr)
- ✅ 3-Styles Refactor zu Single-Source-of-Truth Loader (demo.py nutzt
  jetzt denselben node_load_context wie der echte /run-Pfad)
- ✅ Whisper auf `faster_whisper`+VAD umgestellt — keine Kyrillisch/CJK-
  Halluzinationen mehr bei kurzem/leisem Audio
- ✅ OpenWebUI-RAG raus aus allen 4 Agents (Variante A) — Voice-DB läuft
  jetzt direkt im Sonnet-Prompt, 3-Drafts-Wallclock erwartet 120s → ~35s
- ✅ Mono-Repo `prisment-platform` + SHA-Tagging-Build-Workflow + Pre-commit-Hook

Offen für die nächste 45-Session:
- E2E-Test mit faster_whisper + Variante-A — Wallclock-Wert verifizieren
- Korbinian-Kritik-Punkte 3 + 4 (Foto-Galerie, Approve-statt-Sofort-Post)
  sind im CheckWizard-Refactor enthalten, aber noch nicht durch Korbinian
  manuell verifiziert
- Polish-Issues aus 2026-05-15 (AnswerInput-Textarea auf 4–6 Zeilen,
  `write_final_session` sauberer Fix für `generated_questions`)

Test-Stack ist sauber für Wiederaufnahme — WIP-Sessions geschlossen, alte
draft_choice-Posts auf rejected, push_subscriptions geleert (User muss im
Test-Browser neu registrieren).

**Stand 2026-05-17 abend — Onboarding-UI-Polish + Voice-Check-Tool-Use:**

Korbinian hat das neu deployte Onboarding getestet (CheckWizard tour-mode +
3-Styles). Drei Befunde, alle gefixt:

1. ✅ **AnswerInput-Textarea wächst dynamisch** nach Voice-Transkription
   (`scrollHeight`-basiertes auto-grow, capped `max-h-64`). Roadmap-Polish-
   Punkt aus 2026-05-15 erledigt.
2. ✅ **CheckWizard Tour-Banner** brand-konform: dezente Card mit
   `border-accent bg-accent-pale/30`, analog `/plan` und `/answers`.
   Vollflächiger Akzent-Streifen mit Mini-Schrift raus.
3. ✅ **3-Draft-Slider-Mechanik repariert**: translateX-Math war falsch
   (`-100%` bei width=300% schob 3 Slides weit nach links — sichtbar
   "leerer" Slide), Tap-Areas mit `z-10` fingen Touch-Swipe ab und
   blockierten die Geste. Beides raus, sichtbare Pfeil-Buttons unter dem
   Slider als Backup.
4. ✅ **JSONB-Codec im asyncpg-Pool**: `regie_brief` kam als JSON-String
   aus der DB → `rb.caption` undefined → leere Slide. Codec registriert,
   einheitlich mit psycopg2-Pfad. (`pwa-api/app/db.py`)
5. ✅ **Voice-Check auf Anthropic-Tool-Use migriert**: `max_tokens=512` +
   minimales JSON-Schema im Prompt ließen Haiku in der issues-Liste
   Detail-Objekte schreiben → Truncation → JSONDecodeError. Neuer Helper
   `tool_use_helpers.call_with_structured_output()` erzwingt valides JSON
   nach Pydantic-Schema, `max_tokens=2048` als Marge. Pattern für die
   anderen 7+ Stellen mit `_parse_json_from_llm` dokumentiert.
6. ✅ **Voice-Check im Onboarding-Pfad geskippt** (Option C, Architekt-
   Entscheid 2026-05-17): `skip_voice_check=True` in
   `run_onboarding_one_style`. Onboarding-Wartezeit ~17s pro Variante
   (3 parallel via ThreadPoolExecutor) statt ~30-40s. Stil-Konsistenz-
   Check ist im Onboarding inhaltlich überflüssig — der User wählt
   visuell zwischen 3 Drafts.

**Progressive Generation eingebaut + wird im Follow-up-PR zurückgerollt:**
Heute mittag wurde Progressive-Generation (Variante 1 inline sync, 2+3 als
BackgroundTasks mit Polling-Frontend) implementiert. Mit Architekt-Entscheid
zu Compliance-α (Compliance vor Response an PWA) wird das wieder aufgegeben
— ein Draft mit pendingem Compliance-Status darf nicht inline an die UI.
Wartezeit-Gewinn nicht durch Progressive sondern durch skip_voice_check
(siehe Punkt 6).

**✅ Compliance-im-Onboarding (Variante α) — abgeschlossen 2026-05-17:**
- ✅ Compliance-Check parallel zu Generierung im Demo-Pfad (3× Haiku
  parallel via ThreadPoolExecutor in `run_onboarding_three_styles`)
- ✅ Response-Schema: `posts` (gefiltert), `blocked_count`, `all_blocked`,
  `block_reasons`, `topic_id`
- ✅ Frontend: Hard-State bei `all_blocked` (Wording: "Wir konnten leider
  keine passende Variante erstellen, die unsere Compliance-Regeln
  erfüllt. Wir melden uns persönlich bei dir."); dezenter Hinweis bei
  `blocked_count > 0` via sessionStorage-Bridge → CheckWizard
- ✅ Progressive-Generation komplett zurückgerollt (BackgroundTasks,
  draft_generating/draft_failed-Statuswerte, Polling-Logik raus)
- ✅ Admin-Notification per Email (SMTP, `ADMIN_EMAIL`-Env mit Default
  korbinian.schnall@prisment.de) bei `all_blocked` — fire-and-forget
- ✅ Wording konsequent: keine "rechtliche Prüfung" → "Compliance-Regeln"
- **Smoke-Test grün**: Grubis Onboarding mit Veltliner-Caption,
  Wallclock 18s, 3 Drafts ok, 0 blocked, 0 warnings
- Commit: `578f46e`

**Pending: Compliance-Check auf Tool-Use migrieren (Pattern aus Voice-Check):**
- Modell + max_tokens + Parse-Pattern identisch zum gefixten Voice-Check
- Risiko-Level "mittel" (siehe E4-Status-Report im PR-Verlauf)
- ~30 min Aufwand mit dem etablierten Helper, separater PR

Commits an prisment-platform:
- `1170122` Progressive-Generation + Slider-Polish + AnswerInput-Autogrow
  (Progressive im α-PR wieder rausgenommen)
- `50a4ec9` asyncpg JSONB-Codec + Wait-Text
- `6e8da01` Voice-Check Tool-Use Migration
- `ad49e4c` Voice-Check Skip im Onboarding
- `578f46e` Compliance-Check im Onboarding (Variante α)
- `6cfb371` interview-agent von sonnet-4-5 auf 4-6 angeglichen
- `0ae3c4d` JSONB-Doppel-Encoding-Fix + 1. Chat-Scroll-Versuch
  (Chat-Scroll-Versuch war Pfusch → durch G1 ersetzt)
- `3548aa5` UI-Polish G (Chat + Slider):
  - G1 Chat: Standard-Mobile-Pattern (h-[100dvh] fix, AnswerInput
    flex-shrink-0 statt sticky-bottom, dvh-Auto-Shrink beim Keyboard,
    KEIN onResize-Tracking mehr — das war die Quelle des Scroll-Springens)
  - G2 Slider: Banner + "Variante X/Y" + Dots gepinnt oben in
    choose-Step, Pfeile + Pick-Button unten, nur POST-Inhalt scrollt
  - G4 Slider: JS-Touch-Handler raus, CSS scroll-snap-x-mandatory,
    onScroll → choiceIdx-Tracking, robust gegen Auto-Swipe
  - G3 Test-DB: tenants.sub_modus + vertriebs_kanaele aus Live
    synchronisiert (war NULL/leer, Test-Sync war vor PR 6)

**Offen aus Korbinians Test-Runde**:
- Punkt 5 (zu wenig Emojis) und Punkt 6 (UI zu nackt im Slider —
  Format-/Kanal-/Säule-Pills + Topic-Titel fehlen) werden vom
  Architekten geklärt, dann separater PR

Weitere Commits (2026-05-17/18):
- `1cd960d` Slider-Optik: Kanal·Format-Header, Caption-Anker,
  Hashtag-Chips, Camera-Icon für Foto-Idee
- `92a38a9` + `aac72f1` Playbook-emoji_regeln Schema-Migration auf
  dichte_min/optimal/max (5 JSONs). Min-Korrektur handwerk_produkt +
  gastronomie_erlebnis auf min=1.
- `a126c8f` + `f1edb7c` build_emoji_instruction konsumiert neue
  Felder; Ziel-zuerst-Wording im Prompt; Position direkt nach
  format_regeln; VoiceDB-force_zero-Override mit sichtbarem Log.
- `92b6f58` Dreistufige select_hashtags (Title-Match → Description-
  Match → Säulen-Fallback). Pool-Erweiterung Grubis auf 12-15
  Tags/Kategorie + neue Kategorie `sortiment_breit` (#blaufränkisch
  raus aus produkt). 10 Unit-Tests grün. EXTRACTION_PROMPT mit
  Qualitäts-Block + hashtag_unsicher-Output-Feld.
- `64dcf3b` + `61eebec` Chat-Layout-Reset: useChatLayout-Hook
  (visualViewport-Höhe + Top-Offset via DOM-Messung), fixed
  Container statt dvh-Layout. Auch im Slider angewendet.
- `0ae3c4d` JSONB-Doppel-Encoding-Fix (defensiver Encoder).
- Endgültiger Chat-Fix (Android Pixel/Chrome — Korbinian-Test 2026-05-18):
  Viewport-Meta-Tag `interactive-widget=resizes-content` (Layout-
  Viewport schrumpft beim Keyboard-Open) + `onFocus`-Handler in
  Textarea triggert nach 300ms `scrollContainer.scrollTop = scrollHeight`
  → letzte Bot-Bubble bleibt sichtbar. Cross-Platform robust.
  Korbinian: "funktioniert".

---

#### ✅ Schritt 45 — Onboarding-Workflow (urspruengliche Spec)

> **Status:** Walking-Skeleton komplett umgesetzt (siehe Sub-Steps-Status oben).
> Block bleibt als historische Spec stehen. Live-Tests durch Grubi haben den
> Flow validiert, weitere Polish-Iterationen werden ad-hoc gemacht.

**Auslöser:** Im Live-Test hat sich gezeigt: Interview allein liefert zu wenig Voice-Material UND wir wissen danach immer noch nicht **was dem User gefällt**. Außerdem ist eine App immer kompliziert für neue User. Wir brauchen einen geführten Onboarding-Workflow der den User durch das gesamte Tool führt und mit einem **echten ersten Post** endet.

##### 45.1 Redaktionsplan-Tour (informativ)
- User landet zuerst auf dem Redaktionsplan
- Nur **informativ** — er sieht den Plan, lernt was er ist
- Es wird gezeigt **wie er Änderungen machen kann** (Topics überschreiben, Agent triggern)
- Aber keine Pflicht hier was zu tun

##### 45.2 Erstes Interview zu einem konkreten Topic
- User landet im Interview-Modus
- Das Topic ist konkret das **nächste im Redaktionsplan**
- Interview wie gewohnt, aber: nur 1 Topic (kein Bundle)
- User beantwortet → fertig

##### 45.3 Warteseite + 3-Stile-Generierung im Hintergrund
- Nach Interview-Ende: **Wartesymbol** mit Text wie „Prisment erstellt ein paar Beispiele um zu sehen was dir gefällt…"
- Dauert kurz (~30–60 Sek)
- Im Hintergrund werden **3 Posts mit verschiedenen Stilen** erstellt:
  1. **Professionell**
  2. **Bodenständig**
  3. **Alternativ / besonders**

##### 45.4 3-Stile-Auswahl (Swipe-Picker → automatische Feedback-Generierung)
- Die 3 Posts werden im **Vollbild-Modus** angezeigt: Post 1, Post 2, Post 3
- User swiped durch alle 3 und **pickt intuitiv was ihm gefällt**
- **EXTREM WICHTIG:** Nach Auswahl haben wir automatisch:
  - **1 Positiv-Sample** (der gewählte Post → in implizites positives Feedback)
  - **2 Negativ-Samples** (die nicht-gewählten Posts → in explizites negatives Feedback)
- Sofort durch das Feedback-System verarbeiten (Bug 41.12 + Schritt 42 Mechanismen)
- → VoiceDB wird mit 3 starken Datenpunkten gefüttert bevor irgendwas live geht

##### 45.5 Foto-Upload + fertiger Post
- Direkt im Anschluss: **Foto/Video-Upload** für den gewählten Post (wie 44.4 Bild-Seite)
- Dann: **Fertige-Post-Simulation** wie in 44.4 — User sieht den Post wie er auf Insta/FB aussieht

##### 45.6 Erst-Post live absetzen (Vertrauensbildung)
- Post landet in der Queue
- **Übergabe an Insta/Facebook wird sofort durchgespielt** — kein Warten auf Posting-Tag
- Im Onboarding setzt der User **seinen ersten echten Post ab**
- Empfehlung für Beispiel-Topic: **etwas Einfaches, ein Feed-Bild** — da kann nicht viel schiefgehen
- User lernt wie es geht, fasst Vertrauen + Motivation
- Wir wissen sofort ob es funktioniert
- User muss sich festlegen: „das gefällt mir, das poste ich" ODER er ändert nochmal (= weiteres explizites Feedback)
- Nicht zu stark drängen — aber zum Post **verführen**. Er muss loslegen!

##### 45.x Architektur-Hinweise
- Onboarding ist **eigenes großes Thema** — wird mehrere Sub-Steps haben
- Essentiell damit wir mit echten Mandanten starten können
- Nur über Onboarding lernen wir die Voice maßgeschneidert
- Doku: Detail-Workflows in `PWA_DESIGN.md`

---

#### ⬜ Schritt 46 — Komplette UX-Ausmistung

**Ziel:** Alles bestehende hinterfragen ob redundant und raus.
- Jeder Screen / jede Section / jedes Menü wird auf Notwendigkeit geprüft
- Maxima simpel — End-User darf nicht überfordert sein
- Reduktions-Pass nach Schritt 44 + 45 fertig sind, weil dann klar ist was wirklich gebraucht wird
- Doku: alte Screens die wegfallen werden in `PWA_DESIGN.md` als „Removed" gekennzeichnet

---

> **Hinweis:** Alle weiteren Detail-Workflows, UX-Sketches und Akzeptanzkriterien für Schritt 44–46 werden in der Begleit-Doku **`08_Projekte/Social_Media_Automation/PWA_DESIGN.md`** geführt. Die Roadmap ist Status + Reihenfolge, die PWA-Design-Doku ist das Spec-Dokument.

---

### 🔴 P0 — Output-Qualität & technische Engpässe

1. ✅ **Schritt 41 — Karussell-Composite-Renderer (Canvas)** (2026-05-17)
   Composite-Renderer mit 5-Point-Mid-Band-Gradient, Cover-Slide-Profil-Grid-Fix, Cache-Bust via `?v=updated_at`. PWA rendert Composite-Bilder vor Web-Share.

2. ⬜ **Voice-Learning automatisieren (Folge zu Schritt 42) — PARKED**
   Endpoint `/voice-learning` ist live (analysiert letzte N approved Posts, extrahiert wiederkehrende Phrasen als latente Style-Anchors, E2E mit Grubi verifiziert). Was fehlt ist der **Trigger**: war urspruenglich als n8n-Cron So 18:00 geplant, mit Code-Scheduling-Migration entweder als System-APScheduler-Job (wie email_fallback, ~30 Min) oder als `scheduled_jobs(job_type='voice_learning')` pro Tenant (~45 Min, dafuer pausierbar).
   **Entscheidung: PARKED bis nach Analytics-Block.** Voice-Learning + Analytics-Performance-Learning (Top-Performer-Phrasen) sind verwandte Learning-Loops — beides nach Schritt 14 (Meta API) gemeinsam re-konzipieren. Bis dahin nicht automatisieren, manueller Trigger des Endpoints fuer Test/Polish reicht.

---

### 🟡 P1 — UI/UX-Schmerzen aus Live-Test (Mandanten-Vertrauen)

3. ✅ **Bug 41.13 — Content-Agent-Progress in PWA sichtbar** (durch Schritt 44.3 + 44.4 obsolet — Per-Topic-Trigger zeigt Progress im Flow-UI direkt)

4. ✅ **Bug 41.5 + 41.6 — Redaktionsplan-Revise-Flow** (durch Schritt 44.1 entwertet — Plan ohne Freigabe-Pflicht, Revise ist Sekundärfunktion. Falls Bedarf bei echten Mandanten: neu schneiden.)

5. ✅ **Bug 41.3 + 41.4 — PWA-Schema-Bugs** (2026-05-13)
   - 41.3 ✅ `PostSummary.topic_id: str | None = None` + `PostDetail.topic_id: str | None = None`; Route-Code defensiv mit `or None`/`or ""`
   - 41.4 ✅ `SlideBrief.swipe_hint: str | bool | None = None` — Schema toleriert beide Typen

6. ✅ **Schritt 44.7 — Offener-Job-Indikator + Auto-Resume** (2026-05-13)
   Queue-Endpoint liefert `active_workflow` (resume oder start) + `pending_total`. Home-Page zeigt prominenten Banner ganz oben mit Direktlink zur Check-Seite. PWA-Badge wird via `navigator.setAppBadge` gesetzt. CheckWizard markiert Post beim Öffnen als `in_review`. „Fertig" navigiert zum nächsten pending Post oder zurück zur Home.

---

### 🟢 P2 — Optionale Features (wenn P0 + P1 sauber)

6. ⬜ **Schritt 14 — Meta API Integration (5 Substeps)**
   Echte Analytics statt Stub. Größter Langzeit-Hebel.
   ⚠️ Meta App-Review dauert 1–3 Wochen → **App-Review-Antrag JETZT stellen**, parallel weiterarbeiten.

7. ⬜ **Schritt 38 — WebAuthn / Passkeys (Diskussion-Entscheid)**
   Hybrid-Login (Magic-Link + 6-stelliger Code) ist seit Schritt 37 live. Nach Grubi-Praxistest: zweigleisige UX akzeptabel oder Passkey-Schritt vorziehen?

9. ⬜ **Schritt 16 — Webshop-Anbindung Prestashop → Meta (6 Substeps)**
   Custom Audiences + Lookalike, nightly Delta-Sync. Eigener Hebel, hängt nicht an 14.

10. ⬜ **Schritt 13.4 + 15.2 — Analytics-Folge-Items**
    Hashtag-Performance-Feedback + Presentation-Data-Node — hängen beide an Schritt 14 (Meta API), kommen erst danach.
    **Code-Scheduling-Hinweis (2026-05-17):** Beim Neubau von Analytics müssen die zugehörigen Cron-Trigger in `public.scheduled_jobs` angelegt werden (Migration 014). Worker dispatcht über `job_type` — neue Typen z.B. `analytics_boost_check`, `analytics_monthly_report` in `app/scheduling/worker.py::_dispatch_scheduled_job` ergänzen. Die alten n8n-Analytics-Workflows werden in Paket 8 der n8n-Cron-Migration archiviert (`n8n/workflows/_disabled/`), nicht migriert. Spec für die Migration: `04_Architektur/ROADMAP_n8n-cron-migration.md`.

---

### ⚫ P3 — Vor Go-Live: Datenschutz (Schritt 19)

> Stand 2026-05-13: Mandanten-Datenschutzdokumente sind erstellt. LangSmith bleibt erhalten — wird anonymisiert statt deaktiviert.

11. ⬜ **19.1 LangSmith ANONYMISIEREN** (NEU geschnitten, war: deaktivieren)
    Pseudonymisierung der Felder die nach LangSmith gehen: tenant_id, Mandanten-Name, authentic_phrases, qa_pairs-Inhalte. Mapping-Tabelle (intern → pseudo) im jeweiligen Agent. Genaue Feld-Liste klären.
    *Aufwand: ~2–3h sobald Felder fix.*

12. ⬜ **19.2 Anthropic DPA prüfen** — Status klären (DPA da oder Pseudonymisierungs-Konzept nötig?)

13. ✅ **19.3 Mandanten-Datenschutzerklärung erstellt** *(2026-05-13 durch Korbinian)*

14. ⬜ **19.4 n8n Execution-Purging** — 30-Tage-Retention konfigurieren *(~30 Min)*
15. ⬜ **19.5 Docker Log-Rotation** — `max-size: 10m, max-file: 3` in compose *(~30 Min, manuell)*
16. ⬜ **19.6 WIP-State-Expiration** — automatisch löschen nach Session-Abschluss *(~1h)*
17. ⬜ **19.7 OpenWebUI-Offboarding-Prozess dokumentieren** *(~30 Min)*
18. ⬜ **19.8 DPIA-Entscheidung** — Art. 35 DSGVO-Prüfung
19. ⬜ **19.9 Prompt-Redaction** — entfällt vermutlich wenn 19.2 via DPA gelöst ist

---

### 📋 Quick-Wins (in jeder freien Minute)

- ⬜ Push-Subscription für `korbinian.schnall@prisment.de` anlegen (PWA öffnen → „Benachrichtigungen erlauben" → fertig) — 1 Min
- ✅ Bug 41.1 RAG-Timeout-Fix war im Tooling-Pass 42.2 enthalten — Bug-Header weiter unten als ✅ markiert

---

## Schritte (Historie — chronologische Tat-Liste)

1. ✅ **Interview Agent: Kompakt-Modus** – Bundle-Fragen, weniger Hin-und-Her im Telegram-Flow (TB-010, Commit `6359b8b`)
2. ✅ **Content Agent: Format = strategische Funktion** – FORMAT_STRATEGISCHE_FUNKTION, RAG-Query auf Geschichten/Formulierungen statt IDs (TB-011, Commit `f67f2ae`)
3. ✅ **OpenWebUI RAG in alle 4 Agents** – `search_voice_db()` integriert, Mandanten-Collection-ID aus Registry (TB-007)
4. ✅ **Onboarding Tooling** – Audio-Upload via n8n → Gitea, Python-Script transkribiert (Whisper) + extrahiert (Sonnet) → 12 VoiceDB-Dateien (TB-010, Commits `ac87bd3`, `bf98511`, `b913c25`)
5. ✅ **Onboarding-Interview neu strukturieren** – Fragen + Schema + Auto-Aktivierung
   - 5.1 ✅ Schema-Audit: mandant_config.json + redaktionsplan_konzept.json vereinheitlicht (TB-012)
   - 5.2 ✅ Pflichtfelder-Liste festlegen → `onboarding_pflichtfelder.md` (20 Pflichtfelder, inkl. VoiceDB-Blöcke)
   - 5.3 ✅ Interview-Fragen neu schreiben in `onboarding.md` – Sachfragen vorne, Voice durch Produkt/Kunden-Stories, Persönliches am Schluss (TB-012)
   - 5.4 ✅ EXTRACTION_PROMPT überarbeitet: FEHLT-Sentinel, typ-Klassifizierungsregeln, Block-Referenzen, max_tokens→16000, FEHLT-Audit im Preview (TB-012)
   - 5.5 ✅ Auto-Activation: `active: false → true` nach Push wenn 0 FEHLT-Felder; `--activate-only` für manuellen Nachlauf (TB-012)
6. ✅ **Algorithmus-Integration** – Pareto-Prinzip: die wichtigsten Engagement-Signale format-spezifisch und dosiert in Content + Analytics verankert
   - 6.1 ✅ `algorithmus_regeln` in `strategie_defaults.json` – Signalhierarchie Instagram/Facebook, format-spezifische Ziele, KPI-Pflichtfelder
   - 6.2 ✅ Content Agent `prompts.py` – FORMAT_STRATEGISCHE_FUNKTION + FORMAT_REGELN + JSON_SCHEMA + FORMAT_SPEZIFISCH_CHECKS + VOICE_CHECK erweitert
   - 6.3 ✅ Analytics Agent – Boost-Bug gefixt, get_boost_trigger_reason() neu, Signal-Ranking im Report
   - 6.4 ✅ Container Rebuild + gestartet (content + analytics, beide healthy)
7. ✅ **Voice & Bild Input via Telegram** – Telegram Router v5 erweitert: Voice via Whisper transkribiert; Bilder freundlich abgelehnt
8. ✅ **Content Agent: Assistenz-Modus** – Haiku-Classifier erkennt Beratungsfragen vs. Workflow-Trigger; `/assist` Endpoint
9. ✅ **Musik-Empfehlung im Content Agent** – `audio_vorschlag` für Reel + Story verfeinert
10. ✅ **Legal & Security Guardrails** – Regelkatalog (6 BLOCK + 6 WARN), node_compliance_check (Haiku), WARN → ⚠️ in Telegram
11. ✅ **End-to-End Test mit grubis-weine** – Interview geführt, onboarding_import.py durchlaufen, VoiceDB befüllt, active:true (TB-012)
12. ✅ **Testumgebung** – Test-Container, Test-Bot @prisment_test_bot, promote-Scripts, test_env.sh
13. ✅ **Hashtag-System Basis** – Kuratierte Sets (5 Kategorien) im Onboarding extrahiert; säulenbasierte Rotation im Content Agent
    - 13.1 ✅ Schema: `hashtag_sets` in mandant_config.json + tenant_onboarding.py Template
    - 13.2 ✅ Onboarding: hashtag_sets im EXTRACTION_PROMPT als Pflichtausgabe
    - 13.3 ✅ Content Agent: `select_hashtags()` – säulenbasierte Rotation via hashtag_rotation.json
    - 13.4 ⬜ Analytics: hashtags_used persistieren; hashtag_performance.json; Top-Performer-Feedback (abhängig von Schritt 14)
17. ✅ **Archetyp-System & Cross-Posting** – Inhaber liefert 2 Motive/Woche, System erzeugt plattformgerechte Posts daraus. Content_mix wird durch Archetyp-Template ersetzt. Erstes großes Architektur-Update seit dem Start.
    - 17.1 ✅ **Konfiguration & Schema** – `archetyp_templates.json` in Gitea + `content_archetyp`-Feld in mandant_config; grubis-weine: `handwerk` gesetzt
    - 17.2 ✅ **Onboarding: Archetyp-Erkennung** – Decision Tree (4 Fragen) in EXTRACTION_PROMPT; content_archetyp Pflichtfeld; _collect_fehlende() + Preview erweitert
    - 17.3 ✅ **Redaktionsplan: Motiv-basierte Slot-Generierung** – `compute_slots_from_archetyp()` in tools.py; state.archetyp_templates; motiv_id/is_cross_post/cross_post_typ/motiv_typ in Topics; Container rebuilt + healthy
    - 17.4 ✅ **Content Agent: Cross-Posting-Awareness** – active_motiv_id/is_cross_post/cross_post_typ/motiv_typ in State; build_cross_posting_kontext() in prompts.py; REGIE_BRIEF_SYSTEM_PROMPT mit {cross_posting_kontext}; beide Container rebuilt + healthy
    - 17.5 ✅ **E2E Test & Container-Rebuild** – grubis-weine Juni 2026: 16 Topics, 6 Cross-Posts, 10 Motiv-IDs, 4 Story-Flex-Slots, IG:10/FB:6 – alles korrekt – grubis-weine Archetyp `handwerk`; Redaktionsplan für einen Monat generieren; Ergebnis prüfen: korrekte Motiv-Paare, Story-Slots, Regie-Briefe mit Cross-Posting-Hinweisen
18. ✅ **Personas & Säulen nach Archetyp** – Mehrpersonen-Mandate (Inhaber-Duo, Team); Content-Säulen deterministisch aus Archetyp statt manuell definiert.
    - 18.1 ✅ **Säulen-Templates** – `saeule_defaults` (5 Archetypen × 3 Säulen mit funktion-Feld) in `archetyp_templates.json`; EXTRACTION_PROMPT Säulen-Vorlage nach Archetyp; funktion-Feld in Konzept-Template; Preview zeigt Funktionen
    - 18.2 ✅ **Personas: Config & VoiceDB** – `personas`-Block in mandant_config; `--persona` Flag in onboarding_import.py; mensch.md bleibt als Fallback; keine Container
    - 18.3 ✅ **Redaktionsplan: Persona-Slot-Zuweisung** – `assign_persona_slots()` annotiert Topics deterministisch mit persona_id; bevorzugt Säule 1; Container-Rebuild
    - 18.4 ✅ **Content Agent: Persona-Kontext** – active_persona_id in State; persona_X.md aus VoiceDB laden wenn gesetzt; REGIE_BRIEF_SYSTEM_PROMPT Persona-Block; Container-Rebuild + healthy
19. ⬜ **DSGVO-Konformität** – Vollständige Analyse liegt vor (2026-05-09); alle Befunde müssen vor erstem echten Mandanten-Betrieb adressiert werden *(Details unter "Schritt 19 – DSGVO")*
14. ⬜ **Analytics Agent: Meta API Integration** – Agent architektonisch fertig, Metriken müssen aus Meta Graph API kommen
    - 14.1 ⬜ Meta Developer Setup *(manuell, Korbinian)*: App anlegen, `instagram_manage_insights` beantragen → App Review 1-3 Wochen
    - 14.2 ⬜ OAuth-Flow pro Mandant: Long-Lived Page Access Token → in Gitea als `meta_access_token`
    - 14.3 ⬜ n8n Workflow "Meta Metrics Sync": Cron täglich → recent Posts via Graph API → Metriken in `content/posts/{post_id}.json`
    - 14.4 ⬜ Token-Refresh: automatisch vor Ablauf (60 Tage); pwa-api `/internal/notify` type=admin_alert bei Fehler
    - 14.5 ⬜ End-to-End Test: Boost-Check + Monatsbericht mit echten Meta-Daten
    - 14.6 ⬜ **Mandanten-Profilbild-Upload** (Korbinian-Wunsch 2026-05-13). Wird in der Insta-/FB-Post-Simulation (Schritt 44.4 Screen C) als Avatar genutzt. Aktuell MVP: Initiale + display_name. Pfad: PWA-Onboarding-Form + Upload nach `customer/assets/{tenant_id}/profile.jpg`, neue Spalte `tenants.profile_picture_url`.
15. ✅ **Kunden-Präsentation nach Onboarding** – HTML-Präsentation aus mandant_config + Redaktionsplan; Korbinian reviewt, schickt ab
    - 15.1 ✅ HTML-Schablone + Assembler: `presentation_template.html.j2` (Jinja2) + `generate_presentation.py` – liest Gitea-Daten, ruft Claude einmalig für personalisierte Texte, rendert HTML → `/tmp/presentation_{tenant}_{YYYY-MM}.html`
    - 15.2 ⬜ Analytics Agent → Presentation Data Node: Monatsdaten aggregieren als strukturiertes JSON
    - 15.3 ✅ konzept.prisment.de – HTML live im Browser, Link + Passwort per Telegram
        - nginx-Container (presentation_server) + shared Volume presentation_html
        - docker-compose.yml: presentation-server Service + Volume-Mount in langgraph-content
        - /presentation/draft → HTML ins Volume → konzept_url + password zurück
        - /presentation/publish → re-rendert aus Gitea-Entwurf → JSON {konzept_url, password}
        - n8n "Entwurf" (Pd6t71ig0fbe0jai): Editor-Link + konzept_url per Telegram
        - n8n "Versenden" (4ERuNfOkxnzSbTc3): sendet URL + Passwort via /presentation/publish
        - URL-Format: https://konzept.prisment.de/{tenant}-{monat}-{jahr}
        - Passwortschutz: btoa()-Overlay in HTML, Passwort im Entwurf-JSON gespeichert
    - 15.4 ✅ Onboarding-Integration: nach Datenprüfung → "Präsentation: Entwurf erstellen" manuell starten (bestehender Workflow Pd6t71ig0fbe0jai); Prozess in onboarding.md Phase 4 dokumentiert
    - 15.5 ✅ Web-Editor – Browser-basierter Editor für Claude-Texte
        - GET/POST /presentation/editor/{tenant_id} – FastAPI Endpoints in langgraph_content
        - app/editor.py – Gitea lesen/schreiben via httpx; selbstständiges HTML-Formular (kein Framework)
        - GET /presentation/preview/{tenant_id} – Präsentation direkt im Browser rendern
        - edit_url in /presentation/draft Response zeigt jetzt auf Editor statt Gitea-JSON
        - DNS: A-Record agent-content.prisment.de → Server-IP (manuell durch Korbinian)
        - Sicherheit: Authentik SSO-Middleware über Traefik (agent-content.prisment.de)
37. ✅ **iOS-Login via 6-stelligem Code (2026-05-12)** – Hybrid-Flow: Magic-Link für Android/Desktop (1-Klick), Code-Eingabe für iPhone-User. 37.1–37.3 fertig, 37.4 E2E-Test im Grubi-Termin live.
38. ⬜ **Diskussion: WebAuthn / Passkeys** – Einheitlicher Login ohne Hybrid-Switch; nach Grubi-Praxistest entscheiden *(Details am Ende der Roadmap)*
39. ✅ **Karussell-Post-Optimierung (2026-05-12)** – Slide-Typen + Sub-Patterns + Wortlimits + technischer Quality Gate; variable Slide-Anzahl 3–8 (Bias 5–6); Demo-Pipeline rendert N Slides dynamisch *(Details unter "Schritt 39")*
40. ✅ **Manual-Post-Wizard in PWA (2026-05-12)** – Composer-Wizard sammelt Bilder/Video, kopiert Caption+Hashtags in Clipboard, übergibt Medien via Web Share API an Instagram/Facebook/LinkedIn-App. Slide-Texte pro Karussell-Step kopierbar. Letztes "Posten" macht der User in der Ziel-App, kommt zurück → "Gepostet ✓" *(Details unter "Schritt 40")*
43. ✅ **Implicit Voice-Learning aus approved Posts (2026-05-12)** – Endpoint `/voice-learning` analysiert die N letzten Approvals ohne Revisions via Haiku, extrahiert wiederkehrende Phrasen als latente Style-Anchors. E2E mit grubis-weine: 3 Anker extrahiert. Cron `T: Cron: Voice-Learning (Sonntag 18:00)` als Folge-Aufgabe offen. *(Details unter „Schritt 42")*
42. ✅ **Tooling-Pass: 4 Aufräumarbeiten (2026-05-12)**
    - 42.1 ✅ Neuer Cron-Workflow `T: Cron: Redaktionsplan (Letzter Freitag 09:00)` (ID `QP86uaJEpfoPwLrh`); ersetzt den archivierten `GswWi3RoKYdsHla8`. Pattern analog zu den 3 anderen T:-Crons + zusätzliche Pre-Filter-Code-Node die alle Freitage außer dem letzten verwirft (Cron-Syntax kann „letzter Freitag" nicht ausdrücken). `target_month` wird per inline n8n-Expression auf Nächste-Monat-YYYY-MM berechnet. Helper `_build_json_body_expr()` + `LAST_FRIDAY_FILTER` Konstante in `create_cron_workflows.py`. Sentinel `$$NEXT_MONTH$$` neu.
    - 42.2 ✅ RAG-Timeout in allen 4 LangGraph-Agents auf 30s + 1 Retry-on-TimeoutException (`{interview,content,redaktionsplan,analytics}/app/tools.py search_voice_db`). Adressiert Bug 41.1. Container rebuilt.
    - 42.3 ✅ PWA-Plan-Ansicht: KW + Wochentag-String ersetzt durch kompaktes Datum-Label „Mo 6.5.". Backend (`pwa-api/routes/plan.py`) berechnet `posting_date` via `datetime.fromisocalendar(year, kw, iso_day)` und liefert es im `PlanTopic`-Schema mit. Frontend `app/plan/page.tsx` + `app/plan/pending/page.tsx` rendern `t.posting_date || t.wochentag || "Flexibel"`.
    - 42.4 ✅ Demo-Karussell in Konzept-Präsentation (`konzept.prisment.de`) wählt jetzt deterministisch die Säule mit `funktion="expertise"` (Fallback erste Säule wenn keine vorhanden). Grund: über sich selbst zu posten (vertrauen / Mensch) ist die größte Hemmschwelle für Neukunden — ein Experten-Demo wirkt machbarer und ist die ehrlichere Werbung. Änderung in `langgraph/content/app/presentation.py:500` im `_generate_synthetic_demo()`. Auto-Trigger des Workflows „T: Präsentation: Entwurf erstellen" für grubis-weine als E2E-Test.
16. ⬜ **Webshop-Anbindung (Prestashop → Meta)** – Kundendaten segmentiert in Custom Audiences + Lookalike; DSGVO-konform; nightly Delta-Sync
    - 16.1 ⬜ Meta Business Setup *(manuell)*: Marketing API Zugang, System User Token, Ad Account ID
    - 16.2 ⬜ Prestashop Daten-Extraktion: alle Käufer + Top-Käufer + Warenkorbabbrecher
    - 16.3 ⬜ Custom Audience Aufbau: SHA-256-Hashing, Lookalike 1%/5%, Cold Audience
    - 16.4 ⬜ Exclusion-Logic: bestehende Käufer bei Lookalike/Cold excludieren
    - 16.5 ⬜ Nightly Sync (Cron 02:00): Delta-Sync, Sync-Log in Gitea, pwa-api `/internal/notify` type=admin_alert bei Fehler
    - 16.6 ⬜ Onboarding-Integration: `webshop_typ` in mandant_config; bei Aktivierung Workflow klonen

---

## Schritt 17 – Detailplan Archetyp-System & Cross-Posting

### Architektur-Grundsätze (geklärte Entscheidungen)

- **content_mix wird durch Archetyp-Template ersetzt.** Kein manuelles Setzen mehr, kein Raten durch Claude. Das Template definiert den Monatsplan deterministisch.
- **Motiv-Prinzip:** 1 Motiv = 1 Material das der Inhaber liefert = bis zu 2 plattformgerechte Posts. Der Inhaber produziert ~2 Motive/Woche, das System erzeugt 18-21 Posts/Monat daraus.
- **Story-Slots sind flexibel.** Stories passen nicht ins Mo/Mi/Fr-Raster. Sie werden als eigener Block am Ende des Redaktionsplans ausgewiesen (`wochentag: "flexibel"`).
- **Cross-Posting-Typen:**
  - `adaptiert`: Gleiches Foto/Video, komplett andere Caption (Reel, Feed-Bild)
  - `kernaussage`: Karussell-Thema → eigenständiger Facebook Text-Post (kein Bild nötig)
- **Nie:** Instagram-Hashtags auf Facebook. Reel nicht teilen/verlinken – immer nativ hochladen.

### 17.1 – Konfiguration & Schema (Block A)

**Dateien:** `archetyp_templates.json` (neu in Gitea tenant-registry), `mandant_config.json` Schema, `tenant_onboarding.py`

**`archetyp_templates.json` – Struktur pro Archetyp:**
```json
{
  "handwerk": {
    "beschreibung": "Lokales Produkt, Prozess, Handwerk – Emotion schlägt Hochglanz",
    "branchen": ["Weinhändler", "Bäcker", "Metzger", "Florist", "Schreiner"],
    "kaufmotiv": "Emotion, Authentizität, Herkunft",
    "ton_leitlinie": "Rau und echt schlägt Hochglanz – zeige den Prozess, nicht das Produkt",
    "inhaber_liefert_pro_woche": "1 Prozess/Produkt-Video + 1 Produkt/Mensch-Foto",
    "monatlicher_plan": [
      {"format": "reel",          "kanal": "instagram", "anzahl": 2, "motiv_typ": "prozess_video",
       "cross_post": {"kanal": "facebook", "format": "reel", "typ": "adaptiert"}},
      {"format": "feed_bild",     "kanal": "instagram", "anzahl": 2, "motiv_typ": "produkt_foto",
       "cross_post": {"kanal": "facebook", "format": "feed_bild", "typ": "adaptiert"}},
      {"format": "karussell",     "kanal": "instagram", "anzahl": 2, "motiv_typ": "thema",
       "cross_post": {"kanal": "facebook", "format": "facebook_post", "typ": "kernaussage"}},
      {"format": "story",         "kanal": "instagram", "anzahl": 4, "motiv_typ": "spontan",
       "cross_post": null, "wochentag": "flexibel"},
      {"format": "facebook_post", "kanal": "facebook",  "anzahl": 2, "motiv_typ": "thema_derivat",
       "source": "aus_karussell_thema", "cross_post": null}
    ],
    "breaking_news": {"instagram": 1, "facebook": 1, "cross_post_typ": "adaptiert"},
    "monat_gesamt": {"instagram": 11, "facebook": 7, "motive_pro_woche": 2}
  },
  "gastronomie": { ... },
  "vertrauen":   { ... },
  "dienstleistung": { ... },
  "gesundheit":  { ... }
}
```

**mandant_config.json:** neues Pflichtfeld `content_archetyp`, `content_mix` bleibt als abgeleitetes Feld erhalten (aus Template befüllt, manuell überschreibbar).

**Akzeptanzkriterien 17.1:**
- [x] `archetyp_templates.json` in tenant-registry mit allen 5 Archetypen vollständig
- [x] `content_archetyp` Pflichtfeld in tenant_onboarding.py Template
- [x] mandant_config grubis-weine: `content_archetyp: "handwerk"` gesetzt

---

### 17.2 – Onboarding: Archetyp-Erkennung (Block D)

**Dateien:** `onboarding_import.py` (EXTRACTION_PROMPT), `tenant_onboarding.py`

**Decision Tree im EXTRACTION_PROMPT:**
```
ARCHETYP-KLASSIFIZIERUNG:
Frage 1: Ist das Kernprodukt/-angebot fotografierbar und visuell ansprechend?
Frage 2: Was kauft der Kunde primär → Produkt | Erlebnis | Lösung | Expertise | Transformation
Frage 3: Ist Emotion oder Vertrauen der primäre Kaufauslöser?

Ergebnis-Mapping:
  handwerk:       foto=ja, kaufmotiv=produkt,      ausloeser=emotion
  gastronomie:    foto=ja, kaufmotiv=erlebnis,     ausloeser=emotion
  vertrauen:      foto=egal, kaufmotiv=expertise,  ausloeser=vertrauen
  dienstleistung: foto=ja (Ergebnis), kaufmotiv=loesung, ausloeser=verlässlichkeit
  gesundheit:     foto=ja (Menschen), kaufmotiv=transformation, ausloeser=empathie
```

**Akzeptanzkriterien 17.2:**
- [x] EXTRACTION_PROMPT extrahiert `content_archetyp` als Pflichtfeld (Decision Tree 4 Fragen)
- [ ] grubis-weine Re-Onboarding: Archetyp wird korrekt als `handwerk` erkannt

---

### 17.3 – Redaktionsplan: Motiv-basierte Slot-Generierung (Block B)

**Dateien:** `redaktionsplan/app/tools.py`, `redaktionsplan/app/nodes.py`, `redaktionsplan/app/prompts.py`

**Neue Funktion `compute_slots_from_archetyp()`** ersetzt `compute_format_distribution()`:
- Lädt `archetyp_templates.json` aus tenant-registry
- Erzeugt Slot-Liste nach monatlichem_plan des Archetyps
- Cross-Post-Paare: gleiche `motiv_id`, unterschiedliche kanal/format
- Karussell-Derivate: gleiche `motiv_id` wie Karussell, `source: "aus_karussell_thema"`
- Story-Slots: `wochentag: "flexibel"`, separat am Ende des Plans
- Fallback: wenn kein Archetyp → altes `compute_format_distribution()` Verhalten

**Erweitertes Topic-Schema:**
```python
{
  ...bestehende Felder...,
  "motiv_id":       "uuid4",    # shared im Cross-Post-Paar (None wenn nicht cross-post)
  "is_cross_post":  False,      # True = abgeleitet aus einem anderen Primär-Post
  "cross_post_typ": None,       # "adaptiert" | "kernaussage" | None
  "motiv_typ":      "thema",    # was der Inhaber liefert: prozess_video | produkt_foto | thema | spontan
}
```

**TOPICS_WITH_FORMAT_CTA_PROMPT** – Motiv-Paare sichtbar machen:
```
FORMAT + CTA + MOTIV-ZUWEISUNG (bereits festgelegt – bitte exakt übernehmen):
KW19 Mi: reel       | instagram | motiv_id: M001 ← Primär (Inhaber dreht ein Video)
KW19 Mi: reel       | facebook  | motiv_id: M001 ← Cross-Post (gleicher Clip, andere Caption)
KW19 Fr: karussell  | instagram | motiv_id: M002
KW20 Mo: facebook_post | facebook | motiv_id: M002 ← Derivat aus Karussell-Thema (kein Foto nötig)
KW20 Mi: story      | instagram | flexibel
```

**Akzeptanzkriterien 17.3:**
- [ ] `compute_slots_from_archetyp()` implementiert, alle 5 Archetypen erzeugen korrekte Slot-Listen
- [ ] Cross-Post-Paare teilen motiv_id
- [ ] Story-Slots haben `wochentag: "flexibel"` und erscheinen als separater Block
- [ ] Fallback auf altes Verhalten wenn `content_archetyp` fehlt

---

### 17.4 – Content Agent: Cross-Posting-Awareness (Block C)

**Dateien:** `content/app/nodes.py`, `content/app/prompts.py`

**State-Erweiterung:**
```python
active_motiv_id:       str  # aus topic
active_is_cross_post:  bool
active_cross_post_typ: str | None  # "adaptiert" | "kernaussage"
active_motiv_typ:      str  # prozess_video | produkt_foto | thema | spontan
```

**`REGIE_BRIEF_SYSTEM_PROMPT` – konditionaler Cross-Posting-Block:**

Wenn `is_cross_post=True` und `cross_post_typ="adaptiert"`:
```
CROSS-POSTING (Motiv {motiv_id}):
Dieses {format} auf {kanal} verwendet dasselbe Foto/Video wie der Instagram-Post.
→ Der Inhaber muss NUR EINMAL produzieren.
Instagram-Version: kurze emotionale Caption, 3-5 Hashtags, Hook in Satz 1
Facebook-Version (dieser Post): längerer Text, persönliche Ansprache, Frage am Ende, KEINE Hashtags
```

Wenn `cross_post_typ="kernaussage"`:
```
FACEBOOK TEXT-POST (Kernaussage aus Karussell-Thema {motiv_id}):
Das Karussell erscheint nur auf Instagram. Dieser Facebook-Post transportiert die Kernaussage
als reinen Text — kein Bild nötig. Kein Verweis auf das Karussell.
Länge: 80-200 Wörter. Frage am Ende. Kein Hashtag.
```

**`FORMAT_STRATEGISCHE_FUNKTION["reel"]` – Facebook-Variante:**
```python
if kanal == "facebook":
    return """STRATEGISCHE FUNKTION: COMMUNITY & GESPRÄCH (Facebook Reel)
    Gleiches Video wie auf Instagram – komplett andere Caption-Strategie.
    ALGORITHMUS-SIGNAL: Kommentare > Watch Time auf Facebook.
    - Caption: 50-150 Wörter, persönlich, Frage am Ende
    - Kein Hook-Satz nötig (Facebook zeigt Video-Thumbnail, kein Text-Hook)
    - KEINE Instagram-Hashtags
    - kommentar_frage: Pflichtfeld"""
```

**Regie-Brief JSON-Schema: neues Feld `cross_posting_hinweis`:**
```json
"cross_posting_hinweis": "Dieses Foto/Video erscheint auf Instagram UND Facebook. Nur einmal produzieren."
```

**Akzeptanzkriterien 17.4:**
- [ ] Cross-Posting-Hinweis erscheint im Regie-Brief wenn `is_cross_post=True`
- [ ] Facebook Reel bekommt Kommentar-Frage statt Watch-Time-Hook
- [ ] Kernaussage-FB-Post: kein Bild, kein Hashtag, Frage am Ende
- [ ] Story: kein Cross-Posting-Hinweis (bleibt wie bisher)

---

### 17.5 – E2E Test & Container-Rebuild (Block E)

**Was getestet wird:**
- grubis-weine `content_archetyp: "handwerk"` setzen
- Redaktionsplan für Juni 2026 generieren
- Erwartetes Ergebnis: 11 IG + 7 FB Topics, davon 4 Cross-Post-Paare (2× Reel-Paar, 2× Feed-Bild-Paar), 2 Karussell-Derivate, 4 Story-Slots (flexibel)
- Content Agent: Regie-Brief für ein Reel-Paar prüfen (FB ohne Hashtag, mit Kommentar-Frage)
- Container: redaktionsplan + content rebuild + promote

**Akzeptanzkriterien 17.5:**
- [ ] Redaktionsplan erzeugt korrekte Anzahl und Typen
- [ ] Motiv-Paare korrekt verknüpft
- [ ] Inhaber-Hinweis "nur einmal produzieren" im Regie-Brief sichtbar
- [ ] Kein Instagram-Hashtag im Facebook-Post
- [ ] Beide Container healthy nach Rebuild

---

## Aktueller Schritt – Detail

**Schritt 17.1 gestartet.** Plan freigegeben von Korbinian (2026-05-08).

**Als nächstes:**
1. `archetyp_templates.json` mit allen 5 Archetypen erstellen und in tenant-registry Gitea pushen
2. `mandant_config.json` Schema: `content_archetyp` Pflichtfeld in tenant_onboarding.py
3. grubis-weine mandant_config: `content_archetyp: "handwerk"` manuell setzen

---

## Geklärte Annahmen

- **Dosierung ist Pflicht:** Format bestimmt Signal (1:1-Mapping, Algorithmus-Regeln).
- **Kein Engagement Baiting:** algorithmisch bestraft → verboten.
- **DM-Share-Moment ist Design-Anweisung**, erscheint nicht als Caption-Text.
- **Story ohne Sticker ist verschenktes Potenzial** – sticker_typ Pflichtfeld.
- **Facebook Reel nie teilen/verlinken** – immer nativ hochladen.
- **Keine Instagram-Hashtags auf Facebook** – die eine Regel die nie gebrochen wird.

---

---

## Schritt 18 – Detailplan Personas & Säulen nach Archetyp

### Architektur-Grundsätze (geklärte Entscheidungen)

- **Persona-Posts sind Annotationen, keine neuen Slots.** Der Archetyp-Template bestimmt die Slot-Anzahl. `assign_persona_slots()` annotiert bestehende Topics nachträglich mit `persona_id` — keine Slot-Anzahl ändert sich.
- **Abwärtskompatibilität über `personas.aktiv: false`.** Bestehende Mandanten ohne Personas-Config sind unberührt. Kein Pflicht-Upgrade.
- **`mensch.md` bleibt als Fallback.** Neue Mandanten bekommen `unternehmen.md`. Content Agent prüft beide Pfade (neuer zuerst, alter als Fallback) — kein Breaking Change.
- **Zwei Onboarding-Modi:** Haupt-Onboarding (Firma + erste Stimme) läuft wie bisher. Persona-Ergänzung via `--persona <id>` Flag: separates Transkript → `persona_X.md` + Config-Update.
- **Säulen sind Defaults, keine Pflicht.** Der Archetyp-Template schlägt 3 Säulen vor. Claude kann Namen anpassen, Beschreibungen aus dem Transkript füllen — aber die strategische Struktur (Vertrauen/Expertise/Conversion) bleibt.

### 18.1 – Säulen-Templates (Block A)

**Dateien:** `archetyp_templates.json` (Gitea), `onboarding_import.py`

**`saeule_defaults`-Block** in jedem Archetyp-Eintrag:
```json
"saeule_defaults": [
  {"id": "saeule_1", "name": "Hinter den Kulissen", "funktion": "vertrauen",
   "beschreibung": "Zeigt den Menschen und den Prozess – Nahbarkeit entsteht durch Einblick"},
  {"id": "saeule_2", "name": "Handwerk & Wissen", "funktion": "expertise",
   "beschreibung": "Erklärt warum Qualität wichtig ist – Vertrauen durch echtes Fachwissen"},
  {"id": "saeule_3", "name": "Das Produkt", "funktion": "conversion",
   "beschreibung": "Zeigt konkrete Produkte – löst Kaufentscheidungen aus"}
]
```

**EXTRACTION_PROMPT-Ergänzung:** Neuer Block "SÄULEN-VORSCHLAG (Archetyp-Defaults)" der die 3 vorgefertigten Säulen des erkannten Archetyps als JSON-Vorlage mitgibt. Claude soll Namen und Beschreibung ans Transkript anpassen, aber `funktion` (vertrauen/expertise/conversion) unverändert lassen.

**Akzeptanzkriterien 18.1:**
- [x] `saeule_defaults` in `archetyp_templates.json` für alle 5 Archetypen (mit `funktion` + `persona_bevorzugt`)
- [x] EXTRACTION_PROMPT: Säulen-Vorschlag nach Archetyp; `funktion`-Feld gesetzt; Wochentage korrigiert (Fr=vertrauen, Mi=expertise, Mo=conversion)
- [x] tenant_onboarding.py + onboarding_import.py Konzept-Template mit `funktion`-Feld aktualisiert
- [ ] Test: neues Onboarding → content_saeulen spiegeln Archetyp-Struktur wider

### 18.2 – Personas: Config & VoiceDB (Block B)

**Dateien:** `tenant_onboarding.py`, `onboarding_import.py`, `onboarding_import.py --persona`

**mandant_config.json** — neues Pflichtfeld:
```json
"personas": {
  "aktiv": false,
  "persona_posts_pro_monat": 0,
  "personen": []
}
```

**EXTRACTION_PROMPT-Ergänzung:** Personas-Block — erkennt aus dem Interview ob mehrere Personen vorkommen, wie viele Posts pro Monat pro Person gewünscht sind.

**`--persona <persona_id>` Flag:** Separater Modus in `onboarding_import.py` — lädt Transkript, befüllt nur `persona_X.md` (5 VoiceDB-Blöcke: expertise, stil, geschichte, formulierungen, was_unterscheidet), aktualisiert `personas.personen`-Array in mandant_config.

**Akzeptanzkriterien 18.2:**
- [ ] `personas`-Block in tenant_onboarding.py Template
- [ ] EXTRACTION_PROMPT erkennt und extrahiert Personas-Config
- [ ] `--persona` Flag funktioniert: schreibt `persona_X.md` + updatet mandant_config

### 18.3 – Redaktionsplan: Persona-Slot-Zuweisung (Block C)

**Dateien:** `redaktionsplan/app/tools.py`, `redaktionsplan/app/nodes.py`

**`assign_persona_slots(topics, personas_config)`** — nach LLM-Topic-Generierung:
1. Liest `personas.personen` und `posts_pro_monat` pro Person
2. Bevorzugt Topics in Säule 1 (funktion: vertrauen) und Säule 3
3. Verteilt gleichmäßig über KWs (keine zwei Persona-Posts in derselben KW wenn vermeidbar)
4. Annotiert Topics mit `persona_id: "persona_1"` etc.
5. Bei `personas.aktiv: false`: gibt Topics unverändert zurück

Topic-Schema-Erweiterung: `"persona_id": null`

**Akzeptanzkriterien 18.3:**
- [ ] `personas.aktiv: true, 2 Personen à 2 Posts` → exakt 4 Topics mit persona_id gesetzt
- [ ] Gleichmäßige KW-Verteilung: Persona-Posts in verschiedenen Wochen
- [ ] `personas.aktiv: false` → kein Verhalten verändert

### 18.4 – Content Agent: Persona-Kontext (Block D)

**Dateien:** `content/app/state.py`, `content/app/tools.py`, `content/app/nodes.py`, `content/app/prompts.py`

**`read_persona_voice_db(gitea_repo, persona_id)`** in tools.py — liest `voice-db/onboarding/persona_X.md`.

**State:** `active_persona_id: str = ""`

**`node_set_active_topic`** liest `persona_id` aus Topic.

**`node_generate_regie_brief`** — wenn `active_persona_id` gesetzt:
- Lädt `persona_X.md` via `read_persona_voice_db()`
- Baut `persona_kontext`-Block für den System-Prompt
- RAG-Query bekommt Persona-Namen als Kontext-Hint

**REGIE_BRIEF_SYSTEM_PROMPT** — neuer optionaler Block `{persona_kontext}`:
```
PERSONA-KONTEXT (dieser Post zeigt {persona_name}):
{persona_md_inhalt}
→ Schreibe in der Ich-Perspektive von {persona_name}, nicht als "das Unternehmen".
```

**Akzeptanzkriterien 18.4:**
- [ ] Regie-Brief für Topic mit `persona_id` enthält Persona-spezifischen Kontext
- [ ] Topic ohne `persona_id` → kein Unterschied zum bisherigen Verhalten
- [ ] E2E: Test-Mandant mit 2 Personas → korrekte Regie-Briefe pro Person

---

## Schritt 19 – DSGVO-Konformität

**Ausgangslage (2026-05-09):** Vollständige DSGVO-Analyse durchgeführt. Gesamturteil: **nicht DSGVO-konform**. Muss vor Echtbetrieb mit zahlenden Mandanten abgeschlossen sein.

### Kritische Befunde (sofortiger Handlungsbedarf vor Go-Live)

#### 19.1 ⬜ LangSmith-Tracing **ANONYMISIEREN** (statt deaktivieren) — Entscheidung getroffen 2026-05-13
**Befund:** In allen 4 `app/main.py` ist LangSmith-Tracing aktiv (`LANGSMITH_TRACING_V2=true`). Alle LLM-Calls — inklusive Mandanten-Name, Interview-Antworten und Biografie-Material — werden damit an LangChain Corp (USA) übertragen und dort gespeichert. Das ist eine internationale Drittübertragung an einen zusätzlichen Sub-Processor.

**Entscheidung (Korbinian 2026-05-13):** LangSmith bleibt aktiv (wertvoll für Debugging + Qualität-Monitoring). Stattdessen werden alle personenbezogenen Felder vor dem LLM-Call pseudonymisiert. LangSmith ist im Mandanten-Datenschutzdokument bereits erwähnt.

**Was umzusetzen ist:**
- Pseudonymisierungs-Schicht vor LLM-Call: Mandanten-Name → stabiler Hash/UUID, authentic_phrases sanitised wenn personenbezogene Daten enthalten, qa_pairs-Inhalte: Personennamen/Adressen/Mail durch Marker ersetzen
- Mapping-Tabelle pro Tenant (intern → pseudo), persistent in customer_postgres
- Alle 4 Agents (interview, content, redaktionsplan, analytics) durchgehen
- Test: LangSmith-Trace einer Test-Session prüfen — keine klar-Mandanten-Identifikation mehr sichtbar

**Akzeptanzkriterien:**
- [ ] Pseudonymisierungs-Helper in shared module (oder pro Agent dupliziert wie bisher)
- [ ] alle 4 Agents anonymisieren Mandanten-Name, Personennamen, Mail-Adressen, Telefonnummern vor LLM-Call
- [ ] Test-Session erstellt → LangSmith-Trace manuell geprüft → klar kein Mandanten-Klartext mehr
- [ ] Mandanten-Datenschutzerklärung erwähnt LangSmith mit Hinweis „pseudonymisiert"

#### 19.2 ⬜ Anthropic API — Data Processing Agreement prüfen
**Befund:** Bei jedem LLM-Call werden ohne Pseudonymisierung gesendet: Mandanten-Name, Branchen-Kontext, authentische Zitate des Inhabers, vollständige Interview-Antworten (`qa_pairs`, `authentic_phrases`, `session_summary`). Anthropic's Standard-API-Terms erlauben unter Umständen Nutzung für Modell-Verbesserungen.

**Optionen:**
- A) Anthropic Business DPA abschließen (regelt Training-Opt-out explizit)
- B) Pseudonymisierung der PD vor API-Call einführen (display_name → UUID, Kontext-Injektion anonymisieren)

**Akzeptanzkriterien:**
- [ ] Anthropic API-Terms auf aktuelle Training-Opt-out-Klauseln geprüft
- [ ] DPA abgeschlossen oder Pseudonymisierungs-Konzept entworfen
- [ ] In Mandanten-AVV als Subprozessor gelistet

#### 19.3 ✅ FIXED 2026-05-13 — Mandanten-Datenschutzerklärung erstellt (Art. 13 DSGVO Info-Pflicht)
**Befund (vor Fix):** Mandanten wurden nicht darüber informiert, welche ihrer Daten an externe Dienste (Anthropic, LangSmith, etc.) übertragen werden.

**Erledigt (Korbinian 2026-05-13):** Datenschutz-Infoblatt + AVV-Template erstellt; LangSmith als Sub-Processor explizit erwähnt mit Hinweis dass Daten pseudonymisiert werden (siehe 19.1).

---

### Mittlere Befunde (umzusetzen nach Go-Live, spätestens in Monat 1)

#### 19.4 ⬜ n8n PostgreSQL — Execution-Purging einrichten
**Befund:** n8n speichert jede Workflow-Execution komplett in PostgreSQL — alle Node-Outputs (heute v.a. Onboarding-Audio-Workflow, Präsentation, Git RAG Sync; historische Executions enthalten zusätzlich Telegram-Chat-IDs + Nachrichtentexte aus pre-PLAT-009-Zeiten). Keine Retention-Policy erkennbar. Daten akkumulieren unbegrenzt. Historische Telegram-Execution-Logs gehören mit-bereinigt.

**Lösung:** n8n Execution-Pruning aktivieren (entweder via n8n Settings "Prune data" oder SQL-Job 30-Tage-Bereinigung).

**Akzeptanzkriterien:**
- [ ] n8n Execution-Pruning konfiguriert (empfohlen: 30 Tage)
- [ ] Getestet: Executions älter als X Tage werden gelöscht
- [ ] Löschintervall in Mandanten-Datenschutzerklärung dokumentiert

#### 19.5 ⬜ Docker-Logs — Rotation aktivieren + PD-Maskierung prüfen
**Befund:** Standard JSON-File-Logging ohne Rotation. Tenant-IDs und generierter Content (inkl. Inhaber-Informationen) landen im Log-Stream und verbleiben dauerhaft auf dem Host-Filesystem (`/var/lib/docker/containers/`). Keine automatische Bereinigung.

**Lösung:** `max-size` + `max-file` in docker-compose für alle 4 Agents setzen; prüfen ob sensitive Inhalte aus Log-Ausgaben entfernt werden können.

**Akzeptanzkriterien:**
- [ ] Log-Rotation in docker-compose konfiguriert (z.B. max-size: 10m, max-file: 3)
- [ ] Log-Output der Agents geprüft: kein vollständiger Regie-Brief-Text in Logs
- [ ] Container-Config angepasst (erfordert docker-compose.yml-Änderung → manuell durch Korbinian)

#### 19.6 ⬜ Gitea Session-Daten — Expiration für WIP-State einführen
**Befund:** `sessions/{session_id}-wip.json`-Dateien enthalten vollständige Interview-Dialoge mit Antworten der Inhaber. Sie werden nach Session-Abschluss nicht automatisch gelöscht und akkumulieren im Gitea-Repo.

**Lösung:** In `interview/app/tools.py` nach erfolgreicher Session-Fertigstellung: WIP-State löschen. Alternativ: Cron-ähnliche Bereinigung alter WIP-States (> 30 Tage) in Analytics-Agent.

**Akzeptanzkriterien:**
- [ ] WIP-State wird nach Session-Abschluss automatisch gelöscht
- [ ] Oder: Bereinigungslogik für WIP-Files > 30 Tage implementiert
- [ ] Abgeschlossene Sessions (`sessions/{id}.json`) haben definiertes Aufbewahrungsdatum

#### 19.7 ⬜ OpenWebUI RAG — Löschprozess bei Mandanten-Offboarding definieren
**Befund:** Onboarding-Dateien (`persona_1.md`, `mensch.md`, `werte.md`) mit biografischen Daten der Inhaber werden in OpenWebUI indexiert und auf dem Host-Volume gespeichert. Kein dokumentierter Prozess für die Löschung bei Vertragsende.

**Lösung:** Offboarding-Checkliste erstellen (welche Collection-IDs zu löschen, welche Gitea-Repos zu archivieren/löschen); in `tenant_onboarding.py` Offboarding-Funktion ergänzen.

**Akzeptanzkriterien:**
- [ ] Offboarding-Checkliste dokumentiert (OpenWebUI Collection, Gitea Repo, n8n Workflows)
- [ ] Löschbarkeit der RAG-Collection via OpenWebUI API getestet
- [ ] Offboarding-Prozess in Technischer Dokumentation verankert

---

### Architekturelle Maßnahmen (langfristig / optional je nach Modell)

#### 19.8 ⬜ Datenschutz-Folgenabschätzung (DPIA, Art. 35 DSGVO)
**Befund:** Profiling-Daten (Biographical data, Persönlichkeitsprofil, Interview-Daten) werden verarbeitet und an externe Dienste übertragen. Bei hochrisikoreichem Profiling ist eine DPIA Pflicht.

**Was zu tun ist:** Formale DPIA-Dokumentation erstellen (Zweck, Risiken, Mitigationen, Verantwortlichkeiten). Kann als internes Dokument beginnen.

**Akzeptanzkriterien:**
- [ ] Entscheidung: DPIA verpflichtend oder nicht (Prüfung Art. 35 Abs. 3)?
- [ ] Wenn ja: DPIA-Dokument erstellt und dokumentiert

#### 19.9 ⬜ Prompt Redaction — PD-Minimierung in LLM-Calls (optional, aufwändig)
**Befund:** Mandanten-Name (`display_name`), Branche und authentische Zitate werden direkt in System-Prompts injiziert. Anthropisches Model-Training-Risiko bei Standard-Tier.

**Lösung:** Nur bei Bedarf (falls 19.2 nicht anders gelöst): `display_name` aus Prompts entfernen → neutral formulieren; PD-Inhalte nur im Output-Rendering ergänzen.

**Akzeptanzkriterien:**
- [ ] Konzept entworfen (welche Felder pseudonymisierbar?)
- [ ] Implementiert (niedrige Priorität wenn 19.2 via DPA gelöst)

---

## Aktueller Schritt – Schritt 15 (Kunden-Präsentation)

**15.1–15.5 abgeschlossen ✅**
- `POST /presentation/draft` → Claude-Texte → `editorial/presentation_texts_YYYY-MM.json` in Gitea → `edit_url` zeigt auf Web-Editor
- `GET/POST /presentation/editor/{tenant_id}` → Browser-Editor für Texte (app/editor.py)
- `GET /presentation/preview/{tenant_id}` → Präsentation live im Browser
- n8n "Entwurf" (Pd6t71ig0fbe0jai): Editor-Link + konzept_url per Telegram
- n8n "Versenden" (4ERuNfOkxnzSbTc3): URL + Passwort nach publish
- DNS: A-Record `agent-content.prisment.de` noch ausstehend (manuell Korbinian)

## Schritt 20 – Redaktionsplan-Qualität (nach Testphase 09.05.2026)

**Befunde aus erstem Echttest mit grubis-weine:**

### 20.1 ✅ Konzept: Jahresüberblick-Block
Neue Section "06 — Jahresüberblick" im Präsentations-Template eingefügt (nach Säulen, Abschluss → 07).

### 20.2 ✅ Konzept: Saisonale Schwerpunkte sichtbar machen
`saisonale_schwerpunkte` aus `redaktionsplan_konzept.json` in `_build_context()` zu `jahres_ueberblick` geparst (Q1–Q4, Titel + Beschreibung). Template rendert 2×2 Grid.

### 20.3 ✅ Redaktionsplan: "ab"-Datum statt nur KW
KW-Header zeigt jetzt "KW 19 – ab 05.05.:" + jede Topic-Zeile mit Datum (Mo 05.05.:).

### 20.4 ✅ Redaktionsplan: Crosspost-Kennzeichnung als Text
" 🔄" ersetzt durch " ↩ Cross-Post (adaptiert)" mit Typ-Info.

### 20.5 ✅ Redaktionsplan: Kanal fehlt komplett
Kanal jetzt pro Post als "📸 Instagram" / "📘 Facebook" sichtbar.

### 20.6 ✅ Redaktionsplan: Säulen-Zuweisung deterministisch (strukturell)
Root Cause: `wochen_slots` null → LLM wählte Säulen nach Content-Fit statt definierter Rotation.
Fix: `_build_wochentag_saeule_map()` leitet Mapping aus `content_saeulen[].wochentag` ab, Slots werden
vor dem LLM-Call mit saeule_id/name annotiert. Im Enrich-Schritt: Slot-Wert schlägt LLM-Wert.

### 20.7 ✅ Redaktionsplan-Ausgabe: HTML-Ansicht statt nur Telegram
HTML-Template `redaktionsplan_template.html.j2` (passwortgeschützt, KW-Blöcke, Flex-Topics).
Content Agent: neues `generate_redaktionsplan_html()` + Endpoint `POST /redaktionsplan/html`.
Redaktionsplan Agent: `plan_url` + `plan_password` in State + ConfirmResponse.
n8n Telegram Router v5: IF-Node prüft plan_url → 2 separate Nachrichten (Link zuerst, Passwort getrennt).

---

## Schritt 21 – Kunden-Präsentation: Marketing-Sektionen (Feedback nach erstem Kundengespräch)

**Auslöser:** Erster Mandant fühlte sich emotional nicht abgeholt. Präsentation zu trocken.

### 21.A ✅ Template: 3 neue Sektionen vor Konzept-Teil
- "01 Das Problem" — 3 Karten (Zeit / Ahnung / Wirkung) + Closing-Satz
- "02 Die Demo" — Telegram-Chat-Simulation + Regiebrief-Vorschau + fertiger Post (dynamisch)
- "03 Die Argumente" — 6 Argumente im 2×3-Grid
- Überleitung als eigenes Element
- Bestehende Sektionen umnummeriert: 01→04 bis 07→10

### 21.B ✅ Datenlogik: Demo-Sektion dynamisch aus Kundendaten
- `list_directory()` in gitea_client.py
- `_load_demo_data(gitea_repo)` in presentation.py: lädt erste Session-QA + ersten Post-Regiebrief
- `_build_context()` um 8 Demo-Variablen erweitert (inkl. `demo_available` Guard)
- Alle 3 Presenter-Funktionen rufen `_load_demo_data()` auf
- Container rebuild + verified healthy + Testrender grubis-weine erfolgreich

### 21.C ✅ Demo: Mini Redaktionsplan + Telegram Chat + Instagram-Mockup
**Entscheidungen:**
- Demo ist immer synthetisch (nie rohe Transkript-Daten → Fehlerrisiko)
- `_load_demo_data()` ruft direkt `_generate_synthetic_demo()`, kein Fallback-Logik mehr
- Mini-Redaktionsplan: 3 KWs × 3 Posts, exakt gleiche Spalten wie echter Redaktionsplan (wochentag/datum/format-emoji/kanal-badge/säule/thema)
- KW-Daten + Datumsberechnung in Python; Themen von Claude (9 stück, je 3 pro Säule)
- Instagram-Mockup: vollständiges authentisches Layout (Profil + Gradient-Tile + Hook-Text overlay + Caption + Hashtags + Engagement) — Format ohne Bild ist echter Text/Quote-Post
- Layout: Row 1 full-width (Redaktionsplan-Tabelle), Row 2 2-col (Chat links, IG rechts)
- Framing-Zeile: "Das ist Ihr erster Post. Nach drei Monaten klingt das noch echter."
- `_DEMO_EMPTY` um neue Felder erweitert: `demo_plan_wochen`, `demo_chat_pairs`, `demo_ig_*`

### 21.D ✅ Argumente: Redesign mit großen Emojis + Farbvarianten
- arg01 als Hero-Karte (dark bg, volle Breite, großes ⚡-Emoji links, Titel + Fließtext)
- arg02–06 im 3×2-Grid (3 Spalten, 2 Reihen davon 3+2): je großes Emoji (30px), Kurztitel (Serif), Fließtext (12px)
- 3 Farbvarianten alternierend: col-a (warm/accent-pale), col-b (blau), col-c (grün)
- Print: args-featured + arg-card mit print-color-adjust exact

### 21.E ✅ Pricing: 3 Pakete + empfohlenes_paket aus Config
- Starter 399 / Pro 599 / Premium 849 (mit `slug`-Feld)
- `_build_context()` liest `config.get("empfohlenes_paket", "pro")` → setzt `empfohlen`-Flag dynamisch
- Empfohlenes Paket: accent-pale Hintergrund + accent-Border + fetter Badge + Paketname in accent
- Closing-Sentence: "Starten Sie mit dem Paket das zu Ihrem heutigen Schritt passt …"
- `.paket-grid` → 3 Spalten; `.paket-preis` 24px (kompakter für 3-col)
- Container rebuilt + healthy

### 21.F ✅ Neues HTML-Design übernehmen + Jinja2-Variablen mappen (Commit `6a1d671`)
**Auslöser:** Korbinian hat mit einem anderen KI-Tool ein aufwendigeres HTML generiert (mehr CSS, JS, Effekte). Karussell-Post mit KI-generierten Bildern und Text.
- Komplettes Template ersetzt: scroll-snap Sektionen, IntersectionObserver, 4-Slide-Demo-Slider
- 17 neue Demo-Variablen (Regiebrief + Karussell): `_DEMO_EMPTY` + `_generate_synthetic_demo()` + `_build_context()` erweitert
- Claude-Prompt für Synthese um `ig_likes`, `regie`-Block, `carousel`-Block erweitert; max_tokens 4000
- Pricing-Tabelle: dynamische empfohlen-Spalte via `class="pricing-table empfohlen-{{ empfohlenes_paket_slug }}"` + CSS
- Paket-Slugs: "pro" → "standard" (Breaking-Change bereinigt)
- Instagram-Karussell mit Autoplay (3500ms setInterval)
- Sections 03/04 statisch, 01/02/05/06/07 dynamisch; Footer statisch (info@prisment.de, Bad Griesbach)
- Passwort-Overlay wieder eingebunden
- Test: HTTP 200, 72KB, alle 18 Checks OK; grubis-weine Demo korrekt generiert

### 21.G ✅ Bug: Wochentag im Mini-Redaktionsplan immer Montag
**Ursache:** `monatlicher_plan`-Einträge in `archetyp_templates.json` haben kein `wochentag`-Feld.
**Fix:** `wt = slot.get("wochentag")` → `wt = saeule.get("wochentag", "Montag")` (1 Zeile, presentation.py:322)
Container rebuild + Test: Tage korrekt Mo/Mi/Fr aus Content-Säulen.

### 21.H ✅ VoiceDB-gesättigte Synthese + KI-Bilder (Gemini Imagen 3)

**Architektur (finalisiert 2026-05-10):**

**H.A — VoiceDB-Synthese (2-stufig)**
- A.1: Zusätzliche VoiceDB-Dateien laden: `stories.md`, `style-anchors.md`, `product-knowledge.md` (optional, leer = kein Fehler)
- A.2: Call 1 (Haiku): Interview-Antwort aus echten VoiceDB-Daten destillieren (kein Halluzinieren)
- A.3: Call 2: Content Agent eigene Prompts (`app.prompts`) direkt importieren → Post wird exakt wie echter Output generiert (gleiche Hashtag-Logik, CTA-Regeln, Format-Regeln)

**H.B — KI-Bilder via Gemini Imagen 3**
- B.1: Archetyp-basierte Bildprompt-Templates (regelbasiert, kein LLM-Call)
- B.2: Gemini Imagen 3 API (`imagen-3.0-generate-002`), 4 Bilder pro Request (ein Bild pro Karussell-Slide)
- B.3: Caching in Gitea: `editorial/presentation_cover_{YYYY-MM}_{slide}.jpg.b64`
- B.4: Template: Alle 4 Carousel-Slides mit echtem Bild als Hintergrund + dunkler Overlay

**Infrastruktur:**
- Secret: `/opt/infrastructure/environment_a/secrets/gemini_api_key` (Korbinian legt an)
- docker-compose.yml: `gemini_api_key` Secret + Mount in langgraph-content
- main.py: `gemini_api_key` in `_inject_secrets()` ergänzen
- Neue Vars: `demo_carousel_img_0..3` (base64 data URLs, leer = Gradient-Fallback)

### 21.I 🔄 Demo durch echte Produktion-Pipeline (Authentizitäts-Umbau)

**Auslöser:** Demo ist *das* Verkaufsargument. Synthetische Demo-Texte beweisen nichts. Lösung: Demo läuft durch die *echte* Produktions-Pipeline, nur 2 Dinge bleiben synthetisch (Interview-Imitation, Bilder).

**Säule 1 — Stimm-Imitator auf Sonnet**
- Bisher: Haiku destilliert Interview-Pair aus VoiceDB (halluziniert "Spitzenküchen")
- Neu: Sonnet mit strengem Anti-Halluzinations-Prompt; wörtliche Phrasen aus style-anchors; konkrete Geschichten aus stories.md; ehrliches "nichts passendes" wenn VoiceDB leer
- File: `presentation.py::_generate_synthetic_demo()` Call 1

**Säule 2 — Echter Content Bot als Demo-Generator**
- Neuer Endpoint: `POST /demo/generate_post` im Content Agent
- Input: tenant_id + synthetic interview pair (frage/antwort)
- Lädt mandant_config + konzept + VoiceDB aus Gitea wie im Echtbetrieb
- Erstellt synthetische Session-State (mit `mode: "demo"`)
- Führt **vollen LangGraph-Workflow** aus (alle Nodes, alle Tools, alle Prompts)
- Returns: kompletter Post (caption, hashtags, regiebrief, carousel)
- presentation.py: `_generate_synthetic_demo()` ruft diesen Endpoint statt eigenen Sonnet-Prompt
- **Effekt:** Demo-Post ist 100% identisch zu echtem Monats-Post — gleiche Regeln, gleiche Voice-Checks, gleiche Cross-Posting-Logik

**Säule 3 — Echter Redaktionsplan-Agent als Demo-Generator**
- Check ob `monatlicher_redaktionsplan_{YYYY-MM}.json` für aktuellen Monat in Gitea existiert
- Wenn ja: lesen + erste 3 Wochen anzeigen
- Wenn nein: Redaktionsplan-Agent triggern (HTTP-Call), warten auf Ergebnis, dann anzeigen
- presentation.py: `_load_demo_data()` ersetzt synthetischen Plan durch echten Plan-Read

**Säule 4 — Bilder: konsistentes Master-Setup**
- Master-Setup pro Archetyp: identischer Tisch/Licht/Komposition, nur Motiv variiert
- Seed-Parameter wieder rein (deterministisch pro Monat+Tenant)
- Strikter Negativfilter: no people, no faces, no text on bottles, no brand names, no labels with text
- (Vision-basierte Qualitätskontrolle = Säule 4B, später wenn 4A nicht reicht)

**Kosten/Demo:** ~$0.10 → ~$1.50–3 (Sonnet statt Haiku + voller Content-Graph + Redaktionsplan-Generator). User-Investitions-Freigabe erteilt 2026-05-10.

**Reihenfolge:**
1. ✅ Säule 1: Stimm-Imitator Sonnet — `_generate_synthetic_demo` Call 1 nutzt jetzt Sonnet, neuer strenger Anti-Halluzinations-Prompt (verbietet "Spitzenküchen"-Stereotype)
2. ✅ Säule 2: `/demo/generate_post` Endpoint + neues Modul `app/demo.py` mit `run_demo_post()` — führt echte Nodes (load_context, set_active_topic, generate_regie_brief, voice_check) ohne Gitea-Save/Telegram
3. ✅ Säule 3: `_load_real_plan_topics()` — liest `editorial/redaktionsplan.json` wenn vorhanden, sonst HTTP-Call an `langgraph_redaktionsplan:8003/run`, synthetic fallback bei Fehler. `_build_demo_plan_from_real_topics()` mapt echte Topics auf 3 KWs × 3 IG-Posts.
4. ✅ Säule 4: `_IMAGE_MASTER_SETUP` Konstante mit identischer Szene/Licht/Komposition pro Archetyp + striktem `_IMAGE_NEGATIVE` Filter (no people, no text on bottles, no brand names)
5. ✅ Container rebuilt + healthy (2026-05-10)

**Was Mitarbeiter 2 jetzt rauswirft:** Die Demo schreibt NICHT mehr ihren eigenen Karussell-Post per Demo-Prompt. Stattdessen ruft sie `run_demo_post()` auf, das die exakt gleichen Nodes und Prompts wie `/run` benutzt. Der Demo-Post ist also identisch zu einem echten Monats-Post — nur mit synthetischem Interview-Pair als Input.

---

## Schritt 22 – PWA Kunden-App (`app.prisment.de`)

**Auslöser:** 6 von 8 letzten Bugs waren Telegram-Routing. Kunden-Onboarding "Bitte Telegram-Bot einrichten" ist Hürde. `telegram-router.json` ist 2383 Zeilen Lava. Lösung: PWA als **alleiniger Kunden-Touchpoint**.

**Vollständige Spezifikation:** siehe `PWA_KUNDEN_APP.md` im selben Ordner.

**Kern-Entscheidungen (validiert mit Korbinian 2026-05-11):**
- Next.js 15 + TypeScript + Tailwind (Frontend `pwa-web`)
- FastAPI + Postgres (Backend `pwa-api`, neuer Container, eigene DB `pwa_app`)
- NextAuth.js Magic-Link **getrennt** von internem Authentik → Kunden kommen NIE an interne Tools
- Web Push via `pywebpush` + Service Worker (statt Novu = overkill)
- Email-Fallback nach 4h ohne Push-Acknowledgment (SMTP via existing secrets)
- Whisper-Container bereits da → Voice-Recording mit MediaRecorder API → Transkript
- 4 Tabs Mobile-First: Heute / Posts / Antworten / Mehr
- Multi-User pro Tenant, keine Rollen (alle haben gleiche Rechte)
- Aufwand: ~26 Tage + 5 Puffer (4-6 Wochen Vollzeit-Coding)

**Funktionsabdeckung (was die PWA ersetzt):**
- ✅ Post-Freigabe (statt "OK 1"-Telegram-Parsing)
- ✅ Post-Feedback / Revision
- ✅ Wöchentliches Interview mit Voice-Input
- ✅ Redaktionsplan-Approval
- ✅ Analytics + Boost-Empfehlungen
- ✅ Breaking News (Voice spontan)
- ✅ Assist-Chat (Beratungsfragen an Bot)
- ✅ Team-Einladungen
- ⬜ Onboarding (bleibt explizit raus — macht Korbinian manuell)

### Build-Steps Schritt 22

22.1 ✅ **Verzeichnis-Struktur + Skeleton**
- `/opt/infrastructure/environment_a/pwa/pwa-web/` (Next.js)
- `/opt/infrastructure/environment_a/pwa/pwa-api/` (FastAPI)
- `/opt/infrastructure/environment_a/pwa/migrations/001_initial_schema.sql`
- `/opt/infrastructure/environment_a/pwa/scripts/gen-vapid.sh`
- `/opt/infrastructure/environment_a/pwa/docker-compose-snippet.yml`

22.2 ✅ **pwa-api Backend (FastAPI)**
- Module: auth, db, push, email, fallback_cron, bridges (langgraph, whisper, gitea)
- Alle Endpoints aus PWA_KUNDEN_APP.md §6
- Schema-Migration
- `/internal/notify` für Agent-Trigger

22.3 ✅ **pwa-web Frontend (Next.js)**
- NextAuth.js Config + Magic-Link Provider
- App-Router: `/`, `/posts`, `/posts/[id]`, `/answers`, `/answers/[topic_id]`, `/more`, `/plan`, `/plan/pending`, `/analytics`, `/breaking`, `/assist`, `/profile`, `/team`, `/auth/signin`, `/auth/verify`
- Komponenten: PostCard, SlideViewer, VoiceRecorder, BotChat, TenantSwitcher, BottomNav
- PWA-Manifest + Service Worker (next-pwa)
- Tailwind-Theme (cleaner Tool, blau `#2563eb`)

22.4 ✅ **Dockerfiles + Compose-Snippet**
- Multi-stage Next.js Build (standalone output)
- Python 3.12 + uvicorn für pwa-api
- Snippet-File mit Service-Definitionen für Korbinians Merge

22.5 ✅ **VAPID + Secret-Generator-Script**
- gen-vapid.sh erzeugt Public/Private Keypair
- Skript erzeugt random nextauth_secret + pwa_db_password
- Anweisungen für Korbinian zum manuellen Anlegen

22.6 ✅ **Notification-Trigger im Content-Agent**
- Hook in `node_save_post_draft`: ruft `pwa_api:/internal/notify` mit type=post_pending
- Analog für: interview_due, plan_review, boost_tip, monthly_report

22.7 ✅ **Deployment-Runbook**
- Schritt-für-Schritt-Doku in PWA_KUNDEN_APP.md §10.3
- Was Korbinian manuell macht (DNS, secrets, DB, compose merge)

22.8 🔄 **E2E-Test mit Korbinian** — Login & UI testen ab `https://app.prisment.de`
- Phase nach Build: erst grubis-weine, dann 2. Tenant
- Test-Checkliste aus PWA_KUNDEN_APP.md §12

---

## Schritt 23 – Daten-Migration Gitea → Postgres + Trennung Kunden/Internal

**Auslöser:** Gitea-as-DB ist Phase-1-Tech-Debt. Mandanten-Daten und Korbinians knowledge-base liegen physisch im selben Container — Trust-Boundary fragil. Außerdem: kein ACID, keine Indizes, base64-Bilder bloaten Git.

**Vollständige Spezifikation:** `DATEN_MIGRATION_POSTGRES.md` im selben Ordner (16 Sektionen, 18 Tabellen, 7 Phasen).

**Kern-Entscheidungen (validiert mit Korbinian 2026-05-11):**
- **Voll-Migration zu Postgres** (nicht Hybrid) — Mandanten-Daten 100% raus aus Gitea
- **`customer_postgres` als eigene Instanz** (separates Volume, eigenes Backup)
- **Assets (Bilder/HTML) auf lokalem Filesystem** unter `/opt/.../customer/assets/{tenant}/...`, eigener `assets_server` nginx auf `assets.prisment.de`
- **Hetzner Storage Box nur für Backup**, nicht Live-Storage
- **Admin-UI** `admin.prisment.de` mit Authentik-Auth, vollständige CRUD-UI für Tenants/VoiceDB/Personas/Configs/Jobs/Audit
- Was bleibt in Gitea: Code + Korbinians knowledge-base + read-only Archive-Snapshots

**Plan B (Trennung Kunden/Internal) zu 80% automatisch integriert:** eigene Postgres, eigene Netzwerke, Backup-Trennung, Volume-Disziplin, Gitea-Orgs — alles Teil von Schritt 23.

### Build-Steps Schritt 23

23.1 ✅ **Postgres + Schema-Setup** *(1 Tag)*
- Container `customer_postgres` mit eigenem Volume + Network `db_customer`
- DB `agent_data` (18 Tabellen) + Move von `pwa_app` aus `internal_postgres`
- Migration-Files `002_agent_data_schema.sql` + `003_pwa_app_move.sql`

23.2 ✅ **Asset-Storage Filesystem + nginx** *(0.5 Tage)*
- Container `assets_server` (nginx public-read)
- Subdomain `assets.prisment.de` mit Let's Encrypt
- Pfad-Konvention `/customer/assets/{tenant_id}/...`

23.3 ✅ **Migration-Script** *(1.5 Tage)*
- `scripts/migrate_gitea_to_postgres.py` (idempotent, dry-run + verify-Mode)
- Liest alle Mandanten-Files aus Gitea → Inserts in Postgres
- base64-Bilder → Asset-Filesystem + URLs nach DB
- Test gegen grubis-weine

23.4 ✅ **Code-Refactor 4 Agents + pwa_api** *(3 Tage → real 2026-05-11)*
- Sync Postgres-Helpers `shared/db/db_sync.py` (psycopg2 + ThreadedConnectionPool) in alle 4 Agents + pwa_api kopiert
- `shared/db/gitea_client.py` Compat-Wrapper mit `_route_path` (Postgres) + Gitea-Fallback
- `_REPO_ALIAS` + `_resolve_repo`: transparente Mappings `admin/tenant-registry → internal/tenant-registry`, `admin/<tenant> → archive/<tenant>`
- VoiceDB-Reads + Writes auf Postgres (`voicedb_md` Routing-Branch)
- Image-Cache: Filesystem `/customer/assets/{tenant_id}/images/presentation/{monat}/slide_*.jpg` statt Gitea-base64; `presentation.py` direkt geschrieben + via assets.prisment.de ausgeliefert
- `pwa_db_password` Secret + `db_customer` Network in alle 4 Agents in `docker-compose.yml` ergänzt
- `psycopg2-binary==2.9.10` in `requirements.txt` aller Agents

23.5 ✅ **Admin-UI `admin.prisment.de`** *(3 Tage → real 2026-05-11)*
- ✅ Container `admin_web` (Next.js); `admin_api` durch Next.js-Proxy `/api/proxy/[...path]` → `pwa_api/admin/*` ersetzt
- ✅ Authentik Proxy-Provider + Outpost-Middleware (`authentik@file`)
- ✅ Audit-Tabelle `admin_audit`, Audit-Logging in jedem Schreib-Endpoint
- ✅ Alle 11 Sidebar-Bereiche live: Dashboard, Tenants (+ Detail mit VoiceDB-Editor / Personas / TenantForm / JSON-Config), Voice DB (Cross-Tenant), Personas (Cross-Tenant), Configs, Pläne, Posts (mit Status-Filter), Jobs, Archetypen, Audit, System
- ✅ pwa_api Cross-Tenant Endpoints (`/admin/voicedb`, `/personas`, `/configs`, `/plans`, `/posts`, `/jobs`, erweitertes `/system/health`, Detail-Reads `/tenants/{id}/config`, `/tenants/{id}/plan/{month}`)
- ✅ E2E getestet: alle 11 Routes HTTP 200, kein API-Fehler-Banner

23.6 ✅ **Cleanup + Plan B Reste** *(1.5 Tage → real 2026-05-11)*
- Gitea Organisationen `internal` + `archive` angelegt
- `tenant-registry`, `knowledge-base` → `internal/`
- Mandanten-Repos (`grubis-weine`, `test-mandant`) → `archive/` (read-only)
- Backup-Pipeline um `customer_postgres` Dumps erweitert (Schritt 3b in `backup.sh`)
- Volume-Disziplin: `/opt/infrastructure/environment_a/customer/{postgres,assets}` getrennt von `/var/lib/docker/volumes` (Restic-exclude)
- ✅ **n8n Workflow-Tagging** (2026-05-11): 19 aktive/inaktive Workflows mit `T:` (Tenant-Daten) bzw. `I:` (Infrastructure) Prefix via API umbenannt (`patch_workflow_tagging.py`). 8 archivierte und 2 `old_*` Workflows unverändert. JSON-Exports unter `05_Entwicklung/n8n-workflows/` und `infrastructure/environment_a/n8n/workflows/` nachgezogen.

23.7 ✅ **E2E-Test + Cutover** *(0.5 Tage → real 2026-05-11)*
- Redaktionsplan-Agent `/run` für `grubis-weine 2026-05`: 200 OK, 16 Topics, kein 301 mehr (Repo-Alias greift)
- Content-Agent `/presentation/draft`: 200 OK, 5 Slides (~1.3 MB each) auf `/customer/assets/grubis-weine/images/presentation/2026-05/`
- Public-Asset-URL `https://assets.prisment.de/grubis-weine/images/presentation/2026-05/slide_0.jpg` → HTTP 200, 1.28 MB
- DB-Eintrag `presentations` für `grubis-weine` 2026-05 vorhanden (draft_data + konzept_url)
- Backup-Script-Erweiterung getestet (siehe Schritt 23.6)
- (Performance-Vergleich + 30-min Maintenance-Window nicht durchgeführt — niemand arbeitet aktiv mit dem System, Cutover faktisch live)

**Aufwand gesamt:** ~11 Tage. Real-Time mit Claude: ~2.5h.

---

## ✅ Erledigt nach Schritt 23 — app.prisment.de Debug (2026-05-11)

**Befund:** AuthJS `AdapterError: getaddrinfo EAI_AGAIN internal_postgres`.
Source-Code (`pwa/pwa-web/lib/secrets.ts:21`) zeigte korrekt auf `customer_postgres`,
aber das laufende `pwa_web`-Image war noch von vor der Umstellung — der Hostname
war im Next.js-Build (`.next/server/chunks/*.js`) hartcodiert eingebrannt.

**Fix:** `docker compose up -d --build pwa-web` — Rebuild mit aktuellem Source.

**Verifikation:**
- `/api/auth/signin/nodemailer` liefert 302 → `/api/auth/verify-request`
  (vorher: 302 → `/api/auth/error?error=Configuration`)
- `verification_token`-Row in `customer_postgres/pwa_app` geschrieben
- Keine Adapter-Errors in pwa_web Logs

---

## Schritt 24 — Telegram-Removal (2026-05-11+)

**Ziel:** Telegram komplett aus dem System. Begründung: DSGVO-Aufwand für Telegram-Bot-Hosting (Drittland-Datenfluss, AVV), Kunden-Einrichtungs-Last (Bot-Token, Chat-ID einrichten), Architektur-Komplexität. **Stattdessen:** alles über die PWA mit Push-Notifications + Email-Fallback.

**Voraussetzung:** PWA-Notify-Infrastruktur bereits vorhanden (`pwa_api /internal/notify`, VAPID-Push, Email-Fallback-Cron 15min, `notifications`/`push_subscriptions`/`user_settings`-Tabellen).

**Stand 2026-05-11:** Telegram-Helper-Funktionen als No-Op gestubt (kein Netzwerk-Call mehr). Alle Telegram-abhängigen n8n-Workflows deaktiviert. Echtbetrieb mit Telegram **nicht mehr möglich** — bis PWA-Pendants implementiert sind, gibt es keine User-Kommunikation aus Cron-Triggers heraus. Da noch keine aktiven Mandanten: kein Drama.

### 24.1 ✅ PWA-Pendants für die Telegram-Flows (2026-05-11)

**24.1.a Notify-Trigger im Agent-Code:**
- Interview `/run` → `_notify_pwa_interview_ready` mit deep_link `/answers`
- Analytics `node_write_boost_flags` → `_notify_pwa_boost_recommended` mit deep_link `/analytics`
- Analytics `node_write_report` → `_notify_pwa_monthly_report` mit deep_link `/analytics`
- Content `node_save_post_draft` → `post_pending` (bestand bereits)

**24.1.b 3 neue n8n-Crons** (alle ohne Telegram, mit `errorWorkflow=I: SYSTEM Global Error Handler`):
- `T: Cron: Wöchentliches Interview (Montag 09:00)` — GET `/internal/tenants` → Loop → POST `langgraph_interview /run` mode=weekly
- `T: Cron: Täglicher Boost-Check (10:00)` — Loop → POST `langgraph_analytics /run` mode=boost_check
- `T: Cron: Monatlicher Zyklus (1. des Monats 09:00)` — Loop → POST `langgraph_analytics /run` mode=monthly_report
- Script `/home/claude-deploy/create_cron_workflows.py` für Re-Runs/Reproduzierbarkeit

**24.1.c Boost-Approve-UI** in PWA `/analytics`:
- Eigene Client-Component `BoostCard.tsx` mit Accept/Reject-Buttons
- PATCH `/analytics/boosts/{rec_id}` → `accepted`/`rejected`/`executed`
- Filter Default `?status=pending`

**24.1.d Onboarding-Audio-Upload:**
- pwa-api `/api/tenants/{id}/onboarding/audio` (POST File-Upload, Whisper-Transkription)
- PWA-Page `/onboarding` mit Client-Component `OnboardingUpload.tsx`
- Max 100 MB Audio, Authentik-geschützt über Tenant-Membership
- Claude-Extraktion + VoiceDB-Push macht weiterhin `onboarding_import.py` (Stand-alone-Script, manuell durch Korbinian)

**24.1.e pwa-api Internal-Endpoint** `/internal/tenants` (AGENT_SECRET-geschützt) — listet aktive Tenants für n8n-Cron-Loops; ersetzt Gitea-tenant_registry.json-Aufruf aus n8n.

**Verifizierte E2E:** `/internal/tenants` liefert grubis-weine; `/internal/notify` schreibt notifications-Row, Email-Cron sendet bei 0 Push-Subscriptions; 3 Crons aktiv in n8n.

### 24.2 ✅ Code-Bereinigung Agent-Layer (2026-05-11)
- `send_telegram_message`, `TELEGRAM_BOT_TOKEN`, `TELEGRAM_API` aus tools.py der 3 Agents entfernt
- `_get_telegram_chat_id` aus Content + Analytics entfernt
- Telegram-Nodes (`node_send_telegram`, `_revision_telegram`, `_boost_telegram`, `_report_telegram`) komplett aus nodes.py + graph.py entfernt; Graph-Edges enden jetzt direkt bei END nach save/write
- `telegram_bot_token` aus `_inject_secrets`-Listen in allen 3 Agent main.py
- `docker-compose.yml`: telegram_bot_token + telegram_chat_id + telegram_webhook_secret Mounts aus n8n + 3 Agents raus
- Analytics-Response: `telegram_sent`-Feld aus `BoostCheckResponse` und `MonthlyReportResponse` entfernt
- AnalyticsState: `telegram_sent`/`telegram_error`/`telegram_partial_failure` entfernt

### 24.3 ✅ Code-Bereinigung pwa-api + Admin + Schema (2026-05-11)
- Migration `006_drop_telegram_chat_id.sql` angewendet → `tenants.telegram_chat_id` Spalte weg
- `TenantCreate` + `TenantUpdate` Pydantic-Modelle in `routes/admin.py`: `telegram_chat_id`-Feld weg, INSERT/UPDATE-Queries angepasst
- `shared/db/db_sync.read_tenant_registry`: SELECT + Mapping um Telegram-Feld bereinigt; in alle 5 Konsumenten synced
- `admin_web/app/tenants/[id]/TenantForm.tsx`: Input-Feld und Default-State um Telegram-Eintrag bereinigt

### 24.4 ✅ n8n-Workflows: 21 Telegram-abhängige + old_* gelöscht (2026-05-11)
- 21 Workflows hart gelöscht (alle Telegram-Routers, alle alten Interview-Workflows, alle Crons mit Telegram-Nodes, alle TEST:-Varianten, alle `old_*`, Präsentations-Workflows mit Telegram-Hinweisen)
- Verbleibend aktiv: 4× `I:` (Korbinian-internal Mattermost/Git/RAG) + `T: Git RAG Sync – Multi-Tenant`
- Verbleibend archiviert: `Redaktionsplan Agent – Cron`, `n8n Workfow Doku RAG`
- n8n-Credentials `Prisment_content_bot` und `Telegram Bot Test` → manuell in n8n-UI löschen (ausstehend, dokumentationspflichtig)

### 24.5 ✅ Secrets aufräumen (2026-05-11)
- `secrets/telegram_bot_token`, `telegram_chat_id`, `telegram_webhook_secret` gelöscht (claude-deploy-owned)
- `secrets/telegram_bot_token_test` (root-owned) — Aufräum-Script unter `/home/claude-deploy/remove_telegram_test_secret.sh`, braucht `sudo bash <script>`
- `docker-compose.yml` Secrets-Block: alle Telegram-Definitionen entfernt

### 24.6 ✅ Onboarding-Doku (2026-05-11)
- `08_Projekte/Social_Media_Automation/onboarding.md` aktualisiert
- Phase 1: `--telegram-chat-id` Flag entfernt, Admin-UI-Alternative dokumentiert
- Phase 2b: Audio-Upload-Weg neu beschrieben (PWA `/onboarding` oder lokal)
- Phase 4: Präsentation via Content-Agent-Endpoint statt n8n-Workflow; Mandant erhält Push-Notification statt Telegram
- Checkliste aktualisiert (customer_postgres statt Gitea-Files)
- Datenschutzerklärung-Update kommt mit DSGVO-Block (separat)

**Aufwand-Schätzung 24:** ~3-4 Tage in mehreren Sub-Sessions. Wird sequenziell pro Sub-Punkt durchgegangen.

---

## Schritt 25 — Git-Cleanup (2026-05-11+)

**Ziel:** Nach Migration zu Postgres + Telegram-Removal sind die letzten Gitea-Repos obsolet. Klare Trennung „Mandanten-Daten = customer_postgres" / „Korbinian-Doku = knowledge-base".

### 25.1 ✅ strategie_defaults zu DB migriert (2026-05-11)
- Migration `007_strategie_defaults.sql`: neue Tabelle mit id (PK), payload (jsonb), updated_at
- Backfill `/home/claude-deploy/backfill_strategie_defaults.py`: kopiert aus Gitea internal/tenant-registry/strategie_defaults.json → Row id='global'
- Neue Funktion `read_strategie_defaults()` in `shared/db/db_sync.py`, an alle 5 Konsumenten synced
- 3 Agents (Content, Redaktionsplan, Analytics) `load_strategie_defaults()` umgestellt
- Verifiziert: `boost_regeln.mindest_performance_multiplikator=1.5` aus DB

### 25.2 ✅ tenant-registry Repo archiviert (2026-05-11)
- Code-Refactor: `load_tenant_config(tenant_id)` in 4 Agents nutzt jetzt `db_sync.read_tenant_registry()` direkt (statt Gitea-API über TENANT_REGISTRY_REPO)
- `load_archetyp_templates()` in Redaktionsplan + `presentation.py` Content auf `db_sync.read_archetyp_templates()` umgestellt
- `read_archetyp_templates()` in db_sync: enthält jetzt alle 6 Migration-005-Felder (beschreibung, kaufmotiv, monat_gesamt, branchen_beispiele, breaking_news, inhaber_liefert_pro_woche); `cross_posting_targets` gedroppt (war F1-R2)
- `TENANT_REGISTRY_REPO` ENV-Variable aus docker-compose.yml entfernt (4 Stellen)
- Gitea-Repo `internal/tenant-registry` via Gitea-Admin-API archiviert (read-only)
- `TENANT_REGISTRY_REPO` Eintrag in `.env` ist obsolet — kann manuell entfernt werden (claude-deploy hat keine Schreibrechte)

### 25.3 ✅ archive/* Repos archiviert (2026-05-11)
- Entscheidung: archivieren statt löschen (reversibel)
- `archive/grubis-weine` und `archive/test-mandant` via Gitea-Admin-API auf `archived=true` gesetzt
- Daten redundant in customer_postgres — bei Bedarf später per Gitea-UI hart löschen
- Admin-Token `claude-archive-25` nach Gebrauch aus DB gelöscht

### 25.4 ✅ knowledge-base aufräumen (2026-05-11)
- 10 veraltete n8n-Workflow-JSONs aus `05_Entwicklung/n8n-workflows/` entfernt (Telegram-Router, alte Crons, Onboarding-Audio-Workflow)
- 3 neue Workflow-Exports abgelegt: `cron_weekly_interview.json`, `cron_daily_boost_check.json`, `cron_monthly_cycle.json`
- README.md komplett neu geschrieben: Telegram-frei, neue Architektur (Crons → /internal/tenants → Agent → /internal/notify)

### 25.5 ✅ Backup-Pipeline (2026-05-11)
- Keine Anpassung nötig — `backup.sh` sichert komplette docker-volumes generisch, keine spezifischen archive/-Pfade. Wenn 25.3 später durchgeführt wird, schrumpft das Volume-Snapshot automatisch.

---

## Schritt 26 — Persona-Anzeige + Multi-Persona-Onboarding (2026-05-11)

**Ziel:** Vor Freitag-Kunde (2 Personas) müssen (1) Admin-UI Persona-Name/Rolle anzeigen statt technischer Key und (2) Onboarding-Pipeline muss 2 Personas sauber durchziehen können.

**Hintergrund (gefunden während Block 2.1 Test 2026-05-11):**
- Admin-UI zeigt `personas.persona_key` ("persona_1") weil `personas.name` und `personas.rolle` Spalten **leer** sind
- Echte Quelle für Name/Rolle ist `tenant_configs.config.personas.personen[]` (JSONB-Array) — dort steht für grubis-weine: `{"id":"persona_1","name":"Inhaber","rolle":"Inhaber & Gründer","posts_pro_monat":5}`
- INSERT in `personas` (siehe `langgraph/{interview,content}/app/db_sync.py:713`) schreibt nur `voice_md`, nicht name/rolle → DB-Spalten name/rolle sind faktisch ungenutzte Legacy
- onboarding_import.py Z.405-418 kennt Multi-Persona-Konzept im EXTRACTION_PROMPT, dokumentiert 4 Fälle (0/1/2-gleich/2-ungleich Personen) — Hauptprompt extrahiert aber nur **persona_1** in mandant_config.personas.personen[0]
- Separater `run_persona_import(--persona persona_X)` Modus existiert (Z.808) und schreibt voice-db/onboarding/persona_X.md + aktualisiert mandant_config — **muss aber pro Persona manuell mit eigenem Audio aufgerufen werden**

### 26.1 ⬜ Admin-UI: Persona-Anzeige aus mandant_config
- Admin-API-Endpoint für Personas-Liste muss aus `tenant_configs.config.personas.personen[]` lesen (JOIN mit `personas`-Tabelle für voice_md-Existenz)
- ODER: bei jedem mandant_config-Write `personas.name`/`personas.rolle` in DB-Spalten mitschreiben (Single-Source bleibt JSONB)
- Empfehlung: Variante 1, weil DB-Spalten dann komplett raus können (Migration-Cleanup später)

### 26.2 ⬜ Onboarding-Flow für Multi-Persona klären
- **Audio-Strategie**: 1 Onboarding-Audio + N Persona-Audios (1 pro Person), oder 1 großes Audio mit Sprecher-Markern?
- Wenn separate Audios: PWA-Upload-UI braucht "Persona X" Button + Verkettung mit `--persona persona_2`
- Wenn 1 Audio mit Markern: EXTRACTION_PROMPT muss erkennen "ab hier spricht Person 2" und beide voice_md gleichzeitig erzeugen (komplexer, fehleranfälliger)
- Entscheidung muss vor Freitag stehen

### 26.3 ⬜ Pragma-Fix für Freitag-Kunde (falls 26.2 nicht in time)
- Manuelles Anlegen: nach Hauptonboarding → 2× `onboarding_import.py --tenant X --persona persona_1 --transcript ...` und `--persona persona_2 --transcript ...`
- Vorher `tenant_configs.config.personas.personen[]` auf 2 Einträge erweitern (`UPDATE tenant_configs ...` oder Admin-Edit-UI)
- Beide voice_md werden vom Content-Agent via `personas` Tabelle abgerufen (`active_persona_id` aus Redaktionsplan → `read_persona(tenant_id, persona_key)`)

### 26.4 ⬜ E2E-Test mit 2 Personas
- Test-Mandant mit 2 Personas anlegen
- Redaktionsplan generieren: `assign_persona_slots()` muss beide Personas im Plan haben (Verteilung gemäß `posts_pro_monat` pro Persona)
- Content-Agent: Posts müssen je nach `active_persona_id` korrekt in der richtigen Stimme generiert werden
- Admin-UI: beide Personas mit Namen sichtbar, je voice_md editierbar

---

## Schritt 27 — Admin-UI: Configs-Editor + Archetyp-Detail (2026-05-11)

**Ziel:** Lücken im Admin-UI schließen, die in Block 2 Sichtung gefunden wurden. Korbinian muss `tenant_configs.config` (17 JSONB-Keys) und Archetyp-Felder im Admin lesen/bearbeiten können, ohne auf direkten DB-Zugriff oder Re-Onboarding angewiesen zu sein.

**Hintergrund (gefunden während Block 2.3 + 2.5 Test 2026-05-11):**
- `/configs` Tab (`app/configs/page.tsx`) zeigt nur Liste mit `keys_count` + Verlinkung auf `/tenants/{id}` — aber `/tenants/[id]/page.tsx` enthält keinen Configs-Editor, nur Stammdaten + Personas-Liste + VoiceDB. Damit ist `tenant_configs.config` **nirgendwo** im UI editierbar.
- `/archetypes` Tab (`app/archetypes/page.tsx`) zeigt nur `name + id + ton_leitlinie`. `beschreibung`, `kaufmotiv`, `monat_gesamt`, `branchen_beispiele` werden gar nicht angezeigt; keine Klick-Detail, kein Edit.

### 27.1 ⬜ Configs-Editor im Tenant-Detail
- Im `/tenants/[id]/page.tsx` einen neuen Bereich "Configs" einbauen (z.B. als 3. Spalte oder unter VoiceDB)
- Initial: Read-Only JSON-Viewer für alle 17 Keys (pretty-printed, kollapsibel)
- Then: einzelne Sub-Keys editierbar (z.B. `hashtag_sets`, `posting_zeiten`, `cta_profil` als strukturierte Forms; Rest als JSON-Textarea-Fallback)
- API: `PATCH /admin/tenants/{id}/config` mit JSON-Body → `write_mandant_config()` in `db_sync.py`
- Audit-Log-Eintrag pro Edit

### 27.2 ⬜ Archetyp-Detail-View
- `/archetypes/[id]/page.tsx` neu anlegen → zeigt `beschreibung`, `kaufmotiv`, `monat_gesamt`, `branchen_beispiele`, `breaking_news`, `inhaber_liefert_pro_woche`
- Liste auf `/archetypes` mit Klick-Link zur Detail-Seite
- Edit-Modus optional (Archetypen sind global, selten geändert)

### 27.3 ⬜ Audit + Versions-History
- `tenant_configs` hat aktuell nur `updated_at` — keine History. Bei Edit-UI sollte vorherige Version in eigene Tabelle (analog `voicedb_history`)
- Hinweis: kann auch Phase 2 sein, wenn 27.1 nur strukturierte Sub-Keys editierbar macht und Edit-Häufigkeit niedrig ist

---

## Schritt 28 — pwa-api + n8n: Migration zu Postgres vervollständigen (2026-05-11)

**Ziel:** Die unvollständige Datenpfad-Migration aus Schritt 23.4 abschließen. Symptom in Test-Block 3: PWA zeigte keinen Plan, weil pwa-api noch via `bridges/gitea.py` von Gitea-Files las, während Agents nur in customer_postgres schreiben.

**Hintergrund (gefunden während Block 3 Test 2026-05-11):**
- pwa-api routes `plan.py`, `interview.py`, `dashboard.py`, `posts.py`, `analytics.py` lasen alle via `gitea.read_json(repo, path)` aus Gitea-Files (`editorial/redaktionsplan.json`, `editorial/pending_job.json`, `sessions/X.json`, `posts/X.json`, `editorial/post_index.json`, `analytics/monatsreport_*.json`) — diese Files werden seit Schritt 23 NICHT mehr geschrieben (Agents routen via `gitea_client.py` direkt auf `db_sync.write_*`)
- pwa-api db_sync.py ist eine vollständige Kopie des Agent-db_sync mit allen Read-Funktionen (`read_editorial_plan`, `read_pending_job`, `read_session`, `read_post`, `read_post_index`, `read_analytics_report`) — wurde nur in `analytics.py` + `admin.py` schon benutzt
- (Historischer Stand 2026-05-11) n8n-Crons `T: Cron: Monatlicher Zyklus`, `T: Cron: Wöchentliches Interview`, `T: Cron: Täglicher Boost-Check`, `Telegram-Router` lasen alle `tenant_registry.json` direkt aus `admin/tenant-registry` (Gitea) — Repo ist nach 25.2 archiviert/read-only, Daten sind dort eingefroren. Mit n8n-Cron-Migration (Feature-Log 2026-05-18) wurden alle Cron-Trigger ins APScheduler-Code-Scheduling von pwa-api umgezogen, mit PLAT-009 (2026-05-25) sind alle Cron- und Telegram-Workflows aus n8n gelöscht. Out-of-sync-Risk damit erledigt.

### 28.1 ✅ pwa-api Routes auf db_sync umstellen (2026-05-11)
- `plan.py`: `/current` → `db_sync.read_editorial_plan(tenant_id)`; `/pending` → `db_sync.read_pending_job(tenant_id)` (Format-Mapping: `pending.agent == "redaktionsplan"`, `pending.job_id`, `pending.vorgeschlagener_plan`)
- `interview.py`: `/current` + `/answer` → `db_sync.read_pending_job` + `db_sync.read_session(tenant_id, session_id)`
- `dashboard.py`: `db_sync.read_post_index` + `db_sync.read_pending_job` + `db_sync.read_session`
- `posts.py`: `list_posts`/`get_post`/`approve_post`/`feedback_post` → `db_sync.read_post_index` + `db_sync.read_post`
- `analytics.py`: `latest_report` → neue `db_sync.list_analytics_report_months()` + `db_sync.read_analytics_report`
- Alle async-Calls wrappen via `asyncio.to_thread(...)` da db_sync sync (psycopg2) ist
- Container `pwa_api` rebuilt — Verifikation: `db_sync.read_editorial_plan('grubis-weine')` liefert Plan 2026-07 mit 13 Topics; PWA-Plan-Tab zeigt Plan
- `bridges/gitea.py` bleibt als Modul vorhanden (nicht entfernt — minimal-invasiv), aber wird aus Routes nicht mehr aufgerufen

### 28.2 ⬜ bridges/gitea.py entfernen oder als Helper umbauen
- Aktuell wird nichts mehr daraus genutzt. Entweder komplett löschen oder zu reinem `repo_for()` + Optional-Fallback umbauen.
- Lower Priority — kein Funktionsbug, nur Code-Hygiene.

### 28.3 ✅ n8n-Workflows: tenant_registry aus DB statt Gitea
- ✅ Erledigt durch n8n-Cron-Migration (Feature-Log 2026-05-18) + PLAT-009 (2026-05-25):
  alle 4 betroffenen Workflows (`cron-monthly-cycle`, `cron-weekly-interview`,
  `cron-daily-boost-check`, `telegram-router`) sind aus n8n gelöscht. Cron-Trigger
  laufen jetzt über APScheduler in pwa-api (das ohnehin Tenant-Liste aus DB hat).
  Telegram-Router ist mit Telegram-EOL komplett entfernt.

### 28.4 ⬜ E2E-Verifikation
- Cron "Monatlicher Zyklus" manuell triggern → muss aktive Tenants aus DB lesen und gegen Agents weiterleiten
- "Wöchentliches Interview" manuell triggern → Session in DB anlegen
- Beide End-To-End-Push-Notifications für Mandanten-User prüfen

---

## Schritt 29 — Zero-Friction Kunden-Onboarding (Mail → App → Push) (2026-05-11)

**Ziel (wörtlich aus Architekt 2026-05-11):**
> Ich will, dass die Kunden zur Registrierung eine Mail bekommen mit Link zur App. Wenn man draufklickt installiert sich die App automatisch als App und die Push-Nachrichten funktionieren ohne, dass sie was machen müssen. Es soll dann die Berechtigungsanfrage automatisch beim App-Start kommen.

**Soll-Flow:**
1. Architekt legt neuen Mandanten + User an → System sendet Registrierungs-Email mit App-Link
2. Kunde klickt Link → Browser öffnet `app.prisment.de`
3. PWA-Installation **automatisch** (Add-to-Home-Screen-Prompt direkt beim Erstbesuch, ohne dass der Kunde aktiv suchen muss)
4. Beim ersten App-Start: **Permission-Prompt für Push** automatisch
5. Kunde grant → Subscription wird ohne weiteren Klick angelegt
6. Kunde sieht direkt den ersten Bildschirm, ohne irgendwo Toggles umlegen zu müssen

**Heutige Lücke (Block 1.3 Test 2026-05-11):**
- Push wird über `/more → Profil & Einstellungen → Benachrichtigungen → Push-Notifications Toggle` aktiviert (`ProfileForm.tsx:98 togglePush()`)
- A2HS (Add-to-Home-Screen) ist nicht beworben — User müsste selbst "Zum Startbildschirm" im Browser-Menü finden
- Kein Registrierungs-Email-Flow — Login geht heute via Magic-Link (NextAuth EmailProvider), aber das ist nicht "Onboarding mit Auto-Install"

**Technische Bausteine:**
- **Registrierungs-Email:** Admin-Action "Neuen Kunden onboarden" → triggert Email mit Link `https://app.prisment.de/onboard?token=...` (Token einmalig, kurze Lebensdauer)
- **PWA Auto-Install-Prompt:** `beforeinstallprompt`-Event abfangen + nach Login direkt UI-Card "App installieren" zeigen mit großem Button (statt sie im Browser-Menü zu verstecken). Chrome Android + Safari iOS verhalten sich unterschiedlich — iOS hat **kein** beforeinstallprompt, dort braucht es eine Anleitungs-Card "Tippe Teilen → Zum Home-Screen". Für Android ist programmatic Trigger möglich.
- **Auto-Permission-Prompt:** Nach erfolgreichem Login + PWA-Installation einmalig die `subscribePush()`-Funktion aus `ProfileForm.tsx` direkt aus einer Onboarding-Komponente aufrufen. Best Practice: Kurzer Pre-Permission-Screen ("Damit du Posts zur Freigabe siehst, brauchen wir Push-Permission") + dann `Notification.requestPermission()` — sofortiges Prompt ohne Pre-Screen wirkt aufdringlich und User-Permission-Rate sinkt.
- **Silent Re-Subscribe bei jedem PWA-Start:** Service-Worker prüft auf SW-Updates und re-subscribed automatisch, wenn die Subscription invalid wurde (z.B. nach SW-Major-Update). Verhindert dass Kunden nach Deploys manuell neu subscriben müssen (Symptom heute: nach SW v1→v2 musste Korbinian Toggle aus/an).

### 29.1 ✅ Registrierungs-Email mit One-Click-Login-Token (2026-05-12)
**Implementiert:** `_send_welcome_mail()` in `pwa-api/routes/admin.py` erzeugt UUID, hashed sha256(uuid+AUTH_SECRET) → INSERT in `verification_token` (NextAuth-Standard-Tabelle). Mail-Link `…/api/auth/callback/email?callbackUrl=/onboard&token=<plain>&email=…` löst NextAuth-Verifizierung aus → User eingeloggt → Redirect zu `/onboard`. Brand-Design-Mail (Off-White-BG, Brick-Button, Inter).

**Trigger:** Beim `POST /admin/users` mit `send_welcome_email=true` (default). Plus separater Endpoint `POST /admin/users/{id}/welcome-mail` für Re-Send.

**Admin-UI:** Checkbox "Welcome-Mail jetzt senden" im "+ Neuer User"-Modal + "Welcome-Mail (erneut) senden"-Button in User-Detail.

### 29.2 ✅ Onboarding-Flow nach Login (2026-05-12)
**Implementiert:** Neue Route `/onboard` mit 3-Step-Wizard (`OnboardWizard.tsx`):
- **Step 1** Willkommen + "Was du bekommst" (3 Stichpunkte)
- **Step 2** App-Installation: Chrome Android via `beforeinstallprompt`-Event-Capture; iOS Safari mit bebilderter Anleitung "Teilen → Zum Home-Bildschirm"; Auto-Skip wenn `display-mode: standalone`
- **Step 3** Push-Permission via existing `subscribePush()`-Pattern (VAPID-Key + pushManager.subscribe + POST /push/subscribe)
- "Fertig" → `POST /api/me/onboarded` (setzt `users.onboarded_at = NOW()`) → redirect `/`

### 29.2.alt ✅ Onboarding-Gate (Middleware)
**Implementiert:** `middleware.ts` checked `req.auth.user.onboarded` (aus JWT). Wenn `false` + nicht auf `/onboard` → Redirect. JWT-Callback in `auth.ts` lädt `users.onboarded_at` beim ersten Login + bei `update`-Trigger.

**Schema:** `ALTER TABLE users ADD COLUMN onboarded_at TIMESTAMPTZ NULL`. Bestehende User wurden auf `onboarded_at = NOW()` gesetzt, damit sie nicht in den Wizard rutschen.

### 29.3 ⬜ Silent Re-Subscribe bei jedem PWA-Start
- `ServiceWorkerReg.tsx` erweitern: nach SW-Registration prüfen ob `pushManager.getSubscription()` existiert und für aktuellen VAPID-Key gültig ist; falls nicht, neu subscriben (silent, ohne neuen Permission-Prompt — Permission ist persistent)
- Schützt vor Subscription-Verlust nach SW-Updates oder FCM-410-Cleanups

### 29.4 ⬜ End-to-End Test mit echtem Test-User
- Admin-UI: "Kunde einladen" Action → erstellt User + Magic-Link-Token mit eingebetteter `active_tenant`-Vorauswahl
- Email-Template: erklärt was Prisment macht, einziger CTA-Button "App öffnen"
- Klick → Login + Redirect auf `/onboard` (neue Route, nicht Magic-Link-Default)

### 29.2 ⬜ Onboarding-Flow nach Login
- Neue Route `/onboard` zeigt 3 Steps: (1) Willkommen, (2) "App installieren" mit A2HS-Trigger, (3) Push aktivieren
- A2HS: `beforeinstallprompt`-Event abfangen, eigenen "Install"-Button rendern statt Browser-Default
- iOS Fallback: Bebilderte Anleitung "Teilen → Zum Home-Screen"
- Push-Step: Pre-Permission-Card → Button → `subscribePush()`

### 29.3 ⬜ Silent Re-Subscribe beim PWA-Start
- `ServiceWorkerReg.tsx` erweitern: nach SW-Registration prüfen ob `pushManager.getSubscription()` existiert und für aktuellen VAPID-Key gültig ist; falls nicht, neu subscriben (silent, ohne neuen Permission-Prompt — Permission ist persistent)
- Schützt vor Subscription-Verlust nach SW-Updates oder FCM-410-Cleanups

### 29.4 ⬜ End-to-End Test mit echtem Test-User
- Neuer Test-Mandant + Test-User
- Architekt sendet Einladungs-Mail
- Test-User auf zweitem Handy: Mail öffnen → Klick → Auto-Install → Auto-Permission → Push kommt durch ohne dass User irgendwo Toggles bedient hat
- iOS + Android getrennt verifizieren

---

## Schritt 30 — Datenanonymisierung + LLM-Kosten-Tracking (2026-05-12)

**Ziel:** Vor jedem LLM-Call werden personenbezogene Daten (PII) des Mandanten anonymisiert (DSGVO + Tenant-Vertraulichkeit). Parallel: jeder LLM-Call wird mit Input/Output-Token-Verbrauch + Kosten erfasst und im Admin-Dashboard pro Tenant + insgesamt aggregiert dargestellt.

**Hintergrund:** Aktuell gehen Klarnamen (Inhaber, Kunden, Adressen, Telefonnummern) in jeden Anthropic-Call. Compliance-Risk. Außerdem fehlt jegliche Kostenkontrolle — Architekt sieht nicht wo das Budget hinfließt.

### 30.1 ⬜ Anonymisierungs-Layer in Agents
- Mittel-Layer in `langgraph/*/app/llm_invoke.py` (oder shared util): vor jedem Anthropic-Call werden Tenant-PII durch Tokens ersetzt
  - Personennamen (Inhaber, Persona-Namen aus `tenant_configs.config.personas.personen[].name`) → `[INHABER]`, `[PERSON_A]`
  - Adresse / Stadt / Land aus mandant_config → `[ORT]`
  - Telefonnummern / Email-Adressen → `[KONTAKT]`
  - Konkrete Winzernamen / Lieferantennamen → `[PARTNER_1]`, `[PARTNER_2]` (deterministisch per Hash)
- Nach LLM-Response: De-Tokenisierung mit gleicher Map zurück in den Output
- Map wird pro Call frisch gebaut (kein Persistieren, keine Cache-Leaks zwischen Tenants)
- Edge Case: wenn LLM ein Token nicht zurückgibt (Output ist abstrakt), kein Schaden
- Architektur-Entscheidung: nur Anthropic-Calls (Whisper bleibt clear-text, OpenWebUI-RAG bleibt clear-text — beides läuft on-premise oder bei DSGVO-konformen Providern)

### 30.2 ⬜ Kosten-Tracking-Tabelle
- Neue Tabelle in `customer_postgres.agent_data`:
  ```sql
  CREATE TABLE llm_usage (
    id           UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id    VARCHAR(64) NOT NULL,
    agent        VARCHAR(32) NOT NULL,  -- interview|content|redaktionsplan|analytics
    model        VARCHAR(64) NOT NULL,  -- claude-haiku-4-5-* / claude-sonnet-4-* etc.
    input_tokens INT NOT NULL,
    output_tokens INT NOT NULL,
    cost_eur     NUMERIC(8,4) NOT NULL,
    purpose      VARCHAR(64),  -- "regie_brief" | "interview_question" | "monthly_report" usw.
    erfasst_am   TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    erfasst_datum DATE GENERATED ALWAYS AS ((erfasst_am AT TIME ZONE 'UTC')::date) STORED
  );
  CREATE INDEX idx_llm_usage_tenant_datum ON llm_usage(tenant_id, erfasst_datum DESC);
  ```
- Anthropic-Pricing als Konstanten-Dict in shared utils (`HAIKU_INPUT_PER_MTOK`, `SONNET_INPUT_PER_MTOK` etc.) — bei jedem Call werden `(input_tokens × input_pricing + output_tokens × output_pricing) / 1_000_000` in EUR berechnet
- Wrapper `_llm_invoke_with_retry()` (existiert bereits) erweitern: nach erfolgreicher Antwort `llm_usage`-Row einfügen via `db_sync.write_llm_usage(tenant_id, agent, model, input_tokens, output_tokens, cost_eur, purpose)`

### 30.3 ⬜ Admin-Dashboard Kosten-Widget
- Neue Route `/admin/kosten` (oder neuer Tab in `/admin/system`)
- 3 Views:
  - **Heute:** total + Top-3-Tenants nach Verbrauch
  - **Aktueller Monat:** Diagramm (Tagesverbrauch), Aggregat pro Tenant
  - **Gesamt seit Start:** Tabelle pro Tenant mit Spalten Total-Cost / Avg-Cost-pro-Monat / Letzter-Call
- Drill-Down: Klick auf Tenant → Detail-View mit Aufschlüsselung pro Agent + Modell
- Backend-Endpoint `GET /admin/llm-usage?period=today|month|all&tenant_id?`

### 30.4 ⬜ Budget-Alerts (optional)
- Konfigurierbarer Monats-Budget pro Tenant in `tenant_configs.config.monthly_llm_budget_eur`
- Cron alle 6h: wenn Verbrauch > 80% des Monats-Budgets → Email an Architekt
- 100% → automatische Deaktivierung der Agents für diesen Tenant (active=false in tenants)

### 30.5 ⬜ DSGVO-Audit + Doku
- 19.x-Block in DSGVO-Roadmap erweitern mit "Datenanonymisierung implementiert"
- Im `04_Architektur/` ein Dokument anlegen: welche Felder werden anonymisiert, wie funktioniert die De-Tokenisierung, was passiert bei Tokenisierungs-Fehlern

---

## Schritt 31 — Email-Versand-Strategie / Quiet Hours (2026-05-12)

**Ziel:** Email-Fallbacks sollen nicht nachts um 5:30 Uhr beim Kunden landen. Versandzeiten müssen kontrolliert werden.

**Heutiges Symptom (Architekt 2026-05-12):** Mail "Posts zur Freigabe" + Interview-Fragen kam zwischen 5:30 und 6:00 Uhr — Email-Fallback-Cron läuft alle 15 Min ohne Tageszeit-Filter.

### 31.1 ✅ Quiet Hours definieren (2026-05-12)
**Implementiert:** `_is_quiet_hour()` in `fallback_cron.py` blockiert Email-Versand zwischen 22:00 und 07:00 Europe/Berlin. `run_fallback_check()` returnt früh wenn aktive Quiet Hour.

### 31.A ✅ Push-Bundling (2026-05-12)
**Implementiert:** In `pwa_api/routes/internal.py:notify()` für `BUNDLED_TYPES = {post_pending, interview_follow_up}`: max 1 Push pro 60s pro User. Weitere Notifications landen in DB (sichtbar in PWA-Liste), aber `push_sent_at` bleibt NULL → kein Email-Fallback. Verhindert N-Pushes pro Content-Run.

### 31.1.alt ⬜ Quiet Hours per Tenant konfigurierbar
- Global oder pro Tenant: keine Email-Versendung zwischen z.B. 22:00 und 07:00 Uhr Tenant-Zeitzone
- `tenant_configs.config.email_quiet_hours`: `{"start": "22:00", "end": "07:00", "timezone": "Europe/Berlin"}` (optional, sonst Global-Default)
- `fallback_cron.run_fallback_check` prüft vor jedem Send: wenn in Quiet Hours → skip (Email bleibt offen, wird beim nächsten Lauf außerhalb Quiet Hours versendet)
- Push-Notifications bleiben unverändert (User-Gerät hat eigenes Do-Not-Disturb)

### 31.2 ⬜ Email-Häufigkeit pro Tenant/User
- `user_settings.email_digest_mode`: `instant` | `daily` | `weekly` | `off`
- Bei `daily`: Mails werden in eine Queue gesammelt und einmal pro Tag als Digest versendet (Standard 09:00 Tenant-Zeitzone)
- UI: in `Profil & Einstellungen → Benachrichtigungen` ergänzen

### 31.3 ⬜ Notification-Types granular ein/ausschalten
- `user_settings.notification_types` existiert bereits — aktuell als `Record<string, bool>`
- Sicherstellen dass alle Types abgedeckt sind: `post_pending`, `interview_due`, `interview_follow_up`, `plan_review`, `monthly_report`, `boost_tip`
- Default-Empfehlungen je Type setzen (z.B. `interview_follow_up` standardmäßig nur Push, kein Email-Fallback)

---

## Schritt 32 — Voice-Check Issues im Admin sichtbar machen (2026-05-12)

**Ziel:** Der Voice-Check im Content-Agent ist ein wertvolles Optimierungs-Werkzeug ("Stil-Inkonsistenz", "Zeichenlimit überschritten", "Pflichtfeld fehlt") — aktuell verschwinden die Issues nach erfolgreicher Auto-Korrektur. Architekt will sie pro Mandant verfolgen können, um Voice-Anchors / Prompts laufend zu schärfen.

**Heutiges Verhalten:** Voice-Check setzt `regie_brief._vc_issues` während der Revision, löscht sie bei finalem Erfolg. Keine Historie.

### 32.1 ⬜ Voice-Check-Audit-Tabelle
```sql
CREATE TABLE voice_check_audit (
  id           UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  tenant_id    VARCHAR(64) NOT NULL,
  post_id      VARCHAR(64),
  format       VARCHAR(32),
  attempt      INT NOT NULL,
  passed       BOOLEAN NOT NULL,
  severity     VARCHAR(16),
  issues       JSONB,            -- liste von issue-strings
  resolved_in_revisions INT,     -- 0 wenn sofort durch, sonst nach N Revisionen
  created_at   TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_vc_audit_tenant_date ON voice_check_audit(tenant_id, created_at DESC);
```

### 32.2 ⬜ Eintragung im `node_voice_check`
- Nach jedem Check-Lauf (auch bei `passed=True`) eine Row in `voice_check_audit` schreiben
- Bei erfolgreichem Final-Pass: `resolved_in_revisions = revision_count`

### 32.3 ⬜ Admin-Dashboard "Voice-Check-Insights"
- Neue Route `/admin/voice-checks` (oder Tab im Tenant-Detail)
- Aggregation pro Tenant:
  - **Top Issues** der letzten 30 Tage (häufigste Voice-Check-Befunde)
  - **Avg-Revisions-bis-Pass** (Indikator wie gut der LLM den Stil beim 1. Versuch trifft)
  - **Failure-Rate** (Anteil Posts die `human_review_required` ausgelöst haben)
- Drill-Down: Klick auf Issue → Liste der betroffenen Posts → Direkt-Edit der voice-db/style-anchors.md
- Ableitung: Issues die häufig wiederholen → Hint für Architekt, einen style-anchor zu ergänzen oder no-goes zu erweitern

---

## Schritt 33 — Admin-User-Verwaltung (DRINGEND, 2026-05-12)

**Ziel:** Architekt muss User anlegen, löschen, Tenants zuweisen können. Aktuell vermutlich nur via DB-Edit oder Authentik-Konsole.

### 33.1 ✅ Backend-Endpoints (admin.py) — 2026-05-12
**Implementiert:** `GET /admin/users` (Liste mit tenant_ids array), `GET /admin/users/{id}` (Detail mit Memberships), `POST /admin/users` (anlegen + initial-Memberships), `DELETE /admin/users/{id}` (CASCADE über pwa_app), `POST /admin/users/{id}/memberships`, `DELETE /admin/users/{id}/memberships/{tenant_id}`. Alle Audit-fähig in `admin_audit` (action: `user.create | user.delete | user.membership_add | user.membership_remove`).

### 33.2 ✅ Admin-UI — 2026-05-12
**Implementiert:** `/admin/users` (Liste mit Email/Name/Tenant-IDs/letztem Login), `+ Neuer User`-Modal (Email + Name + Tenant-Multiselect), `/admin/users/[id]` Detail mit Memberships-Verwaltung (Add/Remove via Dropdown) + Gefahrenzone (User-Delete). Sidebar-Eintrag "User-Verwaltung" mit UserCog-Icon ergänzt.

### 33.X ⬜ Backend-Endpoints (urspr. Plan)
- `POST /admin/users` — neuer User anlegen (Email + initial-tenants[])
- `GET /admin/users` — Liste mit Email + tenant_memberships
- `DELETE /admin/users/{id}` — User entfernen (cascade auf memberships)
- `POST /admin/users/{id}/memberships` — Tenant zuweisen
- `DELETE /admin/users/{id}/memberships/{tenant_id}` — Tenant entziehen
- Optional: `PATCH /admin/users/{id}` für Display-Name / aktiv-Flag

### 33.2 ⬜ Admin-UI
- Neue Route `/admin/users` mit:
  - Tabelle: Email, Display-Name, # Tenants, Letzter Login, Aktionen
  - "+ Neuer User" Button → Modal mit Email + Tenant-Multiselect
  - Detail-Page `/admin/users/[id]` mit Tenant-Memberships als editierbare Liste
- Audit-Log: jede User-Aktion in `admin_audit` (action: `user.create | user.update | user.delete | user.membership_add | user.membership_remove`)

### 33.3 ⬜ Integration mit Authentik
- Klärung: legen wir User in Authentik selbst an (über deren API) oder nur in `users`-Tabelle (Authentik wird beim ersten Login Sync machen)?
- Standard-Pfad heute: NextAuth EmailProvider → Magic-Link → erste Anmeldung erzeugt user-row
- Bei Architekt-Anlage: User-Row + Tenant-Memberships pre-erstellen, Magic-Link manuell versenden (Login-Workflow von Schritt 29 nutzen)

---

## Schritt 34 — siehe Schritt 29 (Zero-Friction-Onboarding)

Der Punkt "Registrierungs-Link mit App-Download für neue Kunden" ist bereits in **Schritt 29** abgedeckt (29.1 Registrierungs-Email + 29.2 Onboarding-Flow mit A2HS + 29.3 Silent Re-Subscribe + 29.4 E2E-Test).

Wenn dort weitere Anforderungen entstehen → ergänzen in den 29.x-Unterpunkten.

---

## Schritt 35 — Interview/Redaktionsplan-Timing entzerren + user-konfigurierbar (2026-05-12)

**Ziel:** Aktuell laufen "T: Cron: Monatlicher Zyklus" + "T: Cron: Wöchentliches Interview" möglicherweise gleichzeitig oder kollidieren mit Email-Versand. Architekt will:
1. Saubere zeitliche Entzerrung der Pipeline-Trigger
2. Pro Tenant/User konfigurierbare Slots (z.B. "Mein Interview-Tag ist Dienstag 10:00 statt Montag 09:00")

### 35.1 ⬜ Aktuelle Cron-Timings auflisten + Konflikte analysieren
- "T: Cron: Monatlicher Zyklus" — `1. des Monats 09:00`
- "T: Cron: Wöchentliches Interview" — `Montag 09:00`
- "T: Cron: Täglicher Boost-Check" — `10:00 täglich`
- Konflikt: am 1. eines Monats der ein Montag ist, laufen Monatlicher Zyklus + Wöchentliches Interview gleichzeitig
- Plus: Email-Fallback-Cron alle 15 Min ohne Quiet-Hours-Filter (siehe 31.1) → Email kam 5:30 morgens

### 35.2 ⬜ Pro-Tenant Slot-Konfiguration
- `tenant_configs.config.pipeline_schedule`:
  ```json
  {
    "interview": {"day_of_week": "monday", "hour": 9, "timezone": "Europe/Berlin"},
    "monthly_plan": {"day_of_month": 1, "hour": 9},
    "boost_check": {"hour": 10}
  }
  ```
- n8n-Crons werden multi-tenant: pro Tenant eigene scheduled-execution basierend auf seiner `pipeline_schedule`
- Implementation: aktuelle Crons feuern alle aktiven Tenants in einer Schleife — neue Logik filtert: nur Tenants deren Slot jetzt fällt

### 35.3 ⬜ User-konfigurierbar in der PWA
- `/profile` Tab "Pipeline-Einstellungen":
  - Dropdown "Wann sollen die wöchentlichen Fragen kommen?" → Wochentag + Stunde
  - Dropdown "Wann kommt der Monatsplan?" → Tag im Monat + Stunde
- Backend: PATCH `/api/me/pipeline-schedule` (writes into tenant_configs.config.pipeline_schedule)
- Architekt-Override im Admin-UI bleibt möglich

### 35.4 ⬜ Verifikation
- Test-Tenant mit eigenem Slot anlegen, Cron-Tag abwarten, prüfen ob Trigger nur für diesen Tenant feuert

---

## Schritt 36 — Tenant-Auswahl-Screen nach Login (Multi-Tenant-UX) (2026-05-12)

**Ziel:** User mit 2+ Tenant-Memberships sollen nach Login einen expliziten Auswahl-Screen sehen ("Bei welchem Mandanten willst du arbeiten?"), statt automatisch im ersten Tenant zu landen und über den Header-Switcher wechseln zu müssen.

**Heute:** Nach Login wird `active_tenant = tenant_ids[0]` gesetzt. Bei Single-Tenant (99% der User) unkritisch. Bei Multi-Tenant (Agentur, Architekt, Person mit 2 Firmen): Risiko versehentlich in falschem Tenant zu editieren.

### 36.1 ⬜ Selector-Page nach Login
- Neue Route `/auth/tenant-select` in pwa-web
- Wird vom Login-Flow aufgerufen wenn `tenant_ids.length >= 2`
- Liste aller Tenants des Users mit Display-Name + Branche + Avatar/Logo (falls vorhanden)
- Tap auf Tenant → setzt cookie `active_tenant=<id>` + redirect auf `/`
- Bei 1 Tenant: direkt durch zu `/` (kein Selector)
- Optional "Tenant merken" Toggle → speichert Default in `user_settings.preferred_tenant`

### 36.2 ⬜ Layout-Redirect
- In `app/layout.tsx` (oder Middleware): wenn `tenant_ids.length >= 2` und kein `active_tenant`-Cookie gesetzt → redirect `/auth/tenant-select`

### 36.3 ⬜ Tenant-Switch ohne Page-Reload
- Aktuell: TenantSwitcher setzt Cookie + `location.reload()` — UX-Bruch
- Neu: Server-Action `setActiveTenant(tid)` + `router.refresh()` für sanftes Re-Rendern

### 36.4 ⬜ Architekt-Sonderfall
- Architekt hat ggf. Memberships in allen Tenants — auf der Auswahl-Page wird das eine sehr lange Liste
- Optional: in `user_settings.role` ein `architect`-Flag → eigene Route `/admin/tenants` mit besserer Übersicht statt Login-Selector

---

## Schritt 37 — iOS-Login via 6-stelligem Code (URGENT, 2026-05-12) 🔥

**Deadline:** Heute, ca. 17:00 — Grubi-Termin vor Ort, Grubi nutzt iPhone, Onboarding muss laufen.

**Problem:**
iOS Safari und A2HS-installierte PWA haben **getrennte Cookie-Stores**. Der aktuelle Magic-Link-Flow funktioniert deshalb auf iOS nicht:
1. User klickt Welcome-Mail-Link → öffnet sich in Safari
2. Login-Callback setzt Session-Cookie **in Safari**, nicht in der PWA
3. User installiert PWA via Share-Sheet → öffnet sie → ist nicht eingeloggt
4. Jeder weitere Magic-Link landet wieder in Safari → Endlosschleife

Auf Android funktioniert der Flow sauber, weil Cookies zwischen Browser und installierter PWA geteilt werden.

**Lösung:** 6-stelliger Code statt URL-Link.
1. User installiert PWA (Share-Sheet-Anleitung der Mobile-Install-Wall)
2. Öffnet die PWA vom Home-Screen → /auth/signin
3. Gibt Email ein → bekommt Mail mit 6-stelligem Code
4. Tippt Code direkt in der PWA ein → Login, Cookie wird in der PWA gesetzt

**Stand:** Hybrid-Flow umgesetzt — Mail enthält Magic-Link für Android/Desktop (1-Klick-Login) UND 6-stelligen Code für iPhone-User (manuelle Eingabe in der installierten PWA). 37.1–37.3 fertig, 37.4 E2E-Test läuft live mit Grubi-Termin 2026-05-12.

### 37.1 ✅ NextAuth: 6-stelliger Code als verification_token
- In `auth.ts` → `Nodemailer`-Provider: `generateVerificationToken: () => String(Math.floor(100000 + Math.random() * 900000))`
- `maxAge` auf 15 Minuten reduzieren (Code-basierte Auth braucht kurze Lebenszeit)
- `sendVerificationRequest` umbauen: Mail enthält nur den Code, keinen Klick-Link
- Welcome-Mail-Template in `pwa-api/admin.py` parallel anpassen — sendet Code statt Link (oder beides nebeneinander: Code als Primärweg, Link als Fallback für Desktop)

### 37.2 ✅ /auth/signin: Code-Eingabe-Form
- 2-Step-Form: erst Email-Eingabe → "Code anfordern" → POST an /api/auth/signin/nodemailer
- Dann 6-Felder-OTP-Input (oder ein langes Input mit `inputmode="numeric"` + `autocomplete="one-time-code"` für iOS)
- Submit → POST `/api/auth/callback/nodemailer?email=...&token=123456&callbackUrl=/onboard`
- NextAuth's Nodemailer-Adapter validiert gegen `verification_token`-Tabelle, setzt Session-Cookie
- Bei Erfolg: redirect auf callbackUrl

### 37.3 ✅ Welcome-Mail: Code statt/zusätzlich zu Link
- `_send_welcome_mail` in `pwa-api/app/routes/admin.py`: generiert 6-stelligen Code, schreibt ihn in `verification_token` (hashed wie NextAuth-konform)
- Mail-Body zeigt großen, monospaced Code (z.B. "Dein Code: **123 456**")
- Optional: Klick-Link als sekundärer Weg für Desktop-User behalten

### 37.4 ⬜ E2E-Test auf iPhone
- Korbinian's iPhone: Welcome-Mail → Code in PWA eingeben → /onboard → Wizard durch → /
- Push-Subscription in iOS PWA standalone funktioniert ab iOS 16.4 — vorher prüfen
- Fallback-Notiz wenn Push nicht klappt: User kann auch ohne Push weiter

**Risiko:** Code-Eingabe ist UX-mäßig schlechter als Magic-Link auf Desktop. Deshalb auf Desktop weiterhin Magic-Link bevorzugen (z.B. via UA-Detection im Mail-Template oder beide Wege parallel anbieten).

---

## Schritt 39 — Karussell-Post-Optimierung (2026-05-12) ✅

**Ziel:** Karussell-Posts strukturierter generieren — EIN Gedanke pro Slide, keine Bullet-Listen innerhalb eines Slides, klare Slide-Typen (cover/substanz/cta) mit Sub-Patterns (these_erklaerung, aussage_bedeutung, solo_statement), variable Slide-Anzahl (3–8, Bias 5–6).

### 39.1 ✅ Prompt-Architektur
- `FORMAT_REGELN["karussell"]` umfassend überarbeitet: 3 Slide-Typen, 3 Sub-Patterns mit Beispielen, harte Wortlimits, Bullet-Verbot, visueller Rhythmus
- `JSON_SCHEMA_PER_FORMAT["karussell"]` neue Felder: `slide_type`, `pattern`, `label`, `subtitle`, `swipe_hint`, `sub`, `handle` (alle additiv, alte Felder bleiben)
- `FORMAT_SPEZIFISCH_CHECKS["karussell"]` Pflicht-Liste verschärft mit Wortlimits + Bullet-Verbot

### 39.2 ✅ Technischer Quality Gate
- Neuer `_check_karussell_structure(slide_struktur)` in `nodes.py` läuft vor LLM-Voice-Check
- Validiert: Slide-Anzahl ∈ [3,8], cover=erste/cta=letzte/substanz=mitte, Wortlimits pro slide_type, Bullet-Detection (regex auf `•/–/→/✓/*` mit Linebreak)
- Bei Verletzung: Issues an Voice-Check angehängt, passed=False, Retry-Loop (max 3 → human_review)

### 39.3 ✅ PWA-Schema durchgereicht
- `SlideBrief` (pwa-api/schemas.py) um 7 Optional-Felder erweitert
- `routes/posts.py::_flatten_post` reicht alle neuen Felder durch

### 39.4 ✅ PWA-Frontend
- `posts/[id]/page.tsx` neue `SlideCard`-Komponente: rendert pro slide_type unterschiedlich (Label-Badge, Subtitle bei Cover, Sub+Handle bei CTA), Fallback für Alt-Posts ohne slide_type

### 39.5 ✅ Konzept-Demo (presentation.py + Template)
- `_build_demo_slides_list` reicht neue Felder durch (label/subtitle/sub/handle/swipe_hint/pattern); _BG_FALLBACKS erweitert auf 8 Einträge
- Template-Loop `{% for slide in demo_slides %}` nutzt die neuen Felder
- CSS: `.ig-slide-label`, `.ig-slide-subtitle`, `.ig-slide-cta-sub` ergänzt
- Variable Slide-Anzahl funktioniert end-to-end (Gemini-Bilder werden für N Slides generiert, `_generate_carousel_images` hatte das bereits)

### 39.6 ✅ E2E-Test
- `/demo/generate_post` für grubis-weine, Karussell mit "3 Wein-Mythen"-Antwort → 7 Slides generiert
- Pattern-Mix: A, B, A, C, B (these_erklaerung, aussage_bedeutung, these_erklaerung, solo_statement, aussage_bedeutung)
- Voice-Check passed im 2. Anlauf (Struct-Check triggert beim ersten Versuch ein Regenerate)

**Backwards-Compat:** Alle neuen Felder Optional. Alt-Posts ohne `slide_type` rendern weiterhin via `titel/inhalt`-Fallback in PWA + Presentation.

---

## Schritt 40 — Manual-Post-Wizard in PWA (2026-05-12) ✅

**Ziel:** User vom Regiebrief direkt in den Instagram/Facebook-/LinkedIn-Composer mit Medien vorbefüllt + Caption in Clipboard. Letztes "Posten" tippt der User selbst — Grubis Wunsch nach Kontrolle.

**Architektur:** Web Share API + Clipboard API. Keine Meta-API-Anbindung nötig (kein Auto-Posting). Backend hat den `/mark-posted`-Endpoint bereits, der setzt `status=published` + `posted_via='manual'`.

### Format-Matrix
| Format | Kanal | Medien | Slide-Texte |
|---|---|---|---|
| feed_bild | IG | 1 Foto | – |
| karussell | IG | 3–8 Fotos | ja, pro Slide |
| reel | IG | 1 Video | – |
| story | IG | 1 Foto | – |
| facebook_post | FB | optional | – |
| facebook_event | FB | – | – |
| linkedin_post | LI | optional | – |

### 40.1 ✅ Composer-Route + Wizard-Layout
- Neue Route `/posts/[id]/composer/page.tsx` (Server-Component, lädt Post + Tenant)
- Client-Wizard `Composer.tsx` (State-Machine: 1..N Media-Steps + Review + Success)
- Format-aware: `mediaSpec(post)` ermittelt Anzahl + Typ (image/video/none)
- Nur für `status=approved` Posts erreichbar (andere → Redirect zu Detail)

### 40.2 ✅ Foto/Video-Picker mit Preview
- Pro Step: 2 Buttons "Aufnehmen" (input mit `capture="environment"`) und "Galerie"
- ObjectURL-Preview, "Anderes Foto wählen"-Option
- Karussell: Foto-Idee aus `bild_idee` + Slide-Text-Box (siehe 40.4)

### 40.3 ✅ Clipboard + Web Share API + Toast
- `navigator.clipboard.writeText(caption + cta_text + hashtags)` vor jedem Share
- `navigator.share({title, text, files})` mit Capability-Check (`navigator.canShare`)
- AbortError = User hat Share abgebrochen → Success-Step (User kann trotzdem gepostet haben)
- Toast-Bestätigung "Caption ist in der Zwischenablage" für 2.5s sichtbar
- Fallback wenn Web Share nicht verfügbar: Caption kopierbar, Hinweis App manuell zu öffnen

### 40.4 ✅ Slide-Text-Box pro Karussell-Step
- Pro Karussell-Slide-Step zeigt der Wizard: slide_type-Badge, label, titel, subtitle, inhalt, sub, handle
- Eigener Copy-Button "Slide-Text kopieren" → Clipboard
- Genau dann sichtbar wenn `post.format === "karussell"`

### 40.5 ✅ mark-posted Integration
- Backend `/api/tenants/{tid}/posts/{pid}/mark-posted` existierte bereits → wiederverwendet
- Wizard-Success-Screen: großer "Gepostet ✓"-Button → markiert + Redirect zu `/posts?status=published`
- Alternativ "Nochmal senden" wenn das erste Mal schiefging

### 40.6 ✅ Bestehende `PostedActions.tsx` umgebaut
- Primärer Button "An [Kanal] senden" → Link zu `/posts/{id}/composer`
- Sekundär: "Übersprungen" / "Schon gepostet" (direkter mark-posted ohne Wizard, für Cases wo der User schon manuell gepostet hat)
- `kanal`-Prop von Post-Detail durchgereicht

### 40.7 ✅ Smoke-Test
- pwa-web Container rebuild: kein TypeScript-Fehler
- Route `/posts/x/composer` → 302 → /auth/signin (ohne Login, erwartet)
- Build-Artefakte vorhanden (`/app/.next/server/app/posts/[id]/composer/page.js`)
- UI-Test, Foto-Picker, Web Share API: nur am echten Handy testbar (delegiert)

### Was bewusst NICHT in 40 ist (für später)
- **Composite-Slides mit eingebranntem Slide-Text** (Canvas-Rendering): User muss aktuell den Slide-Text manuell im Insta-Composer als Text-Overlay setzen. Wenn das in der Praxis zu mühselig ist → Schritt 41 als Composite-Renderer
- **HEIC-Konvertierung** für iOS-Fotos: Instagram nimmt HEIC meist; bei Fehler später Backend-Endpoint
- **Video-Trimming für Reels**: Insta-App macht das selber besser
- **Reorder/Drag** zwischen Karussell-Slides: feste Reihenfolge nach Slide-Nummer

### Was am Handy zu testen ist
1. PWA öffnen, Karussell-Post mit Status `approved` aufrufen
2. "An Instagram senden" tippen → Wizard öffnet sich
3. Pro Slide: Foto-Idee + Slide-Text sehen, "Aufnehmen" oder "Galerie" → Bild wählen
4. "Slide-Text kopieren" testen (Clipboard befüllen)
5. Übersicht: alle Bilder als Thumbnails + Caption-Preview
6. "An Instagram" tippen → System-Share-Sheet mit Files → Instagram wählen
7. Instagram-Composer: Caption-Feld antippen → Einfügen, posten
8. Zurück zur PWA → "Gepostet ✓" → Status published, Redirect

### Bekannte Caveats
- **iOS < 16.4**: Web Share API mit Files funktioniert nicht. Grubi hat aktuelles iPhone → kein Issue für Pilot
- **Instagram blockiert Caption-Vorbefüllung absichtlich**: deshalb der Clipboard-Workflow als Mittel der Wahl
- **Browser-Cache nach Update**: User muss ggf. die PWA einmal hart reloaden

---

## ✅ Erledigt nach Schritt 40 — Cron-Workflow `$$TENANT_ID$$`-Bugfix (2026-05-12)

**Symptom:** Manueller Trigger des Workflows `T: Cron: Wöchentliches Interview (Montag 09:00)` für grubis-weine erzeugte keine Interview-Anfrage. Der Agent antwortete `403 Tenant '$$TENANT_ID$$' nicht in tenant_registry`.

**Root Cause:** `create_cron_workflows.py` baute den `jsonBody` per `json.dumps(json.dumps(agent_body)).replace('"$$TENANT_ID$$"', '$json.tenant_id')`. Im doppelt-dumped String sind die Quotes escaped (`\"$$TENANT_ID$$\"`), der `replace`-Pattern matchte daher nie. Folge: der Platzhalter blieb stehen, alle drei aktiven Cron-Workflows (Weekly Interview, Boost-Check, Monatlicher Zyklus) waren tot.

**Fix:**
- `create_cron_workflows.py`: neuer Helfer `_build_json_body_expr()` baut ein n8n-Object-Literal mit `JSON.stringify(...)` und ersetzt Sentinels (`$$TENANT_ID$$`, `$$WEEKLY_SESSION_ID$$`) durch echte Expressions. Session-ID-Konvention `weekly_<8hex>` analog zu `breaking_<8hex>`.
- `patch_cron_jsonbody.py`: patcht die 3 bestehenden Workflows in n8n via API (PUT), behält IDs + active-Status.
- JSON-Exports unter `05_Entwicklung/n8n-workflows/cron_*.json` aktualisiert.

**Verifikation (2026-05-12 17:32 UTC):**
- Direkt-POST an `langgraph_interview /run` mit `mode=weekly` → `status=awaiting_answer`, 3 Topics, gebundelte Frage
- `sessions`-Zeile `weekly_6a5ba29c` mit `mode=weekly`, `status=wip` angelegt
- `pending_jobs.current_interview_session` auf neue Session gesetzt
- `notifications`-Row Typ `interview_ready` für grubis-weine geschrieben (push_sent_at NULL — User hat noch keine Push-Subscription, Email-Fallback-Cron übernimmt)

**Status für nächsten Test:** PWA `/answers` für grubis-weine zeigt die 3 Fragen. Nach Antworten triggert `node_close_session` → `trigger_content_agent` → Karussell-Post zum Freigeben → Manual-Post-Wizard → Instagram.

---

## ⬜ Bekannte Bugs / Follow-ups (entdeckt 2026-05-12 beim E2E-Test grubis-weine)

### Bug 41.1 ✅ FIXED 2026-05-12 (im Tooling-Pass 42.2) — `search_voice_db` Timeout im Content-Agent

**Symptom:** Bei der Content-Generierung für Session `weekly_6a5ba29c` (grubis-weine, 2026-05-12 17:35 UTC) wurde der RAG-Call wiederholt nach 10s mit `httpx.TimeoutException` abgebrochen — Logs: `[search_voice_db] Timeout – kein RAG-Kontext`. Fallback auf leeren String greift sauber, der Content-Agent crasht nicht, aber jeder Post wird ohne Voice-DB-Kontext generiert.

**Folgen:** Voice-Check `passed=False` häufiger → Revisions-Schleife → mehr Token-Verbrauch, schwankende Output-Qualität, Persona-Stimme verwässert.

**Verdächtigt:** OpenWebUI Container `internal_open_webui:8080` oder der nachgelagerte Embedding/LLM-Call dort. 10s sind für RAG mit Collection-Lookup + Embedding-Search + Antwort-Synthese eng.

**Code:** `/opt/infrastructure/environment_a/langgraph/content/app/tools.py:416` (`search_voice_db`), Timeout-Konstante in Zeile 439.

**Quick Fix:** Timeout auf 30s anheben + retry-on-timeout (1 Retry). Echter Fix: OpenWebUI-Performance prüfen (Worker-Settings, Embedding-Modell, Collection-Size).

**Reproduktion:** Direkt-POST an `langgraph_content /run` für grubis-weine, Logs beobachten.

---

### Bug 41.2 ✅ FIXED 2026-05-12 22:15 CEST — `423 Locked` beim Schreiben von `hashtag_rotation.json`

**Symptom:** Content-Agent versucht Hashtag-Rotation persistent zu machen, bekommt aber konsistent `HTTP 423 Locked` von Gitea. Logs: `Client error '423 Locked' for url 'http://internal_gitea:3000/api/v1/repos/archive/grubis-weine/contents/content/hashtag_rotation.json'` → Warning `[select_hashtags] Rotation-Save fehlgeschlagen`.

**Root Cause:** Schritt 25.2 hat alle Mandanten-Repos in `archive/*` verschoben und auf read-only gesetzt. Der Hashtag-Rotation-Code in `tools.py` schreibt aber weiterhin direkt nach Gitea — die Daten-Migration aus Schritt 23 hat diese Datei übersehen.

**Folgen:** Säulen-Rotation memoriert nichts → bei jedem Run wird die gleiche Säule bevorzugt → ungleichmäßige Säulen-Verteilung über die Wochen.

**Code:**
- `/opt/infrastructure/environment_a/langgraph/content/app/tools.py:360` (`load_hashtag_rotation`)
- `/opt/infrastructure/environment_a/langgraph/content/app/tools.py:365` (`save_hashtag_rotation`)
- Aufrufer: `tools.py:391` + `tools.py:404` in `select_hashtags()`

**Fix-Vorschlag:** Rotation-State in Postgres ablegen — entweder neue Tabelle `hashtag_rotations(tenant_id, format, saeule_index, last_used_combos, updated_at)` oder als JSON-Blob in `tenant_configs.metadata.hashtag_rotation`. `gitea_client.py` Routing-Branch ergänzen analog zu den anderen Migrationen aus 23.4.

**Reproduktion:** beliebiger Content-Run für grubis-weine → 423-Zeile erscheint zuverlässig im Container-Log.

**Fix (2026-05-12 22:15 CEST):**
- Neue Postgres-Tabelle `hashtag_rotation(tenant_id, rotation_key, current_idx, updated_at)` in `customer_postgres.agent_data` angelegt (PK + FK auf tenants)
- `content/app/tools.py:load_hashtag_rotation` + `save_hashtag_rotation` umgestellt auf `db_sync._conn()` — UPSERT pro rotation_key
- Container rebuilt
- Verifikation: Bei nächstem Content-Run keine 423-Lock-Warning mehr im Log, `select_hashtags` rotiert sichtbar (saeule_1 → saeule_3 → saeule_2 in drei aufeinanderfolgenden Posts mit unterschiedlichen Hashtag-Kombos)

---

### Bug 41.3 ✅ FIXED 2026-05-13 — `PostSummary.topic_id` requires str, crasht bei NULL

**Symptom:** `GET /api/tenants/grubis-weine/posts?status=pending` liefert 500 mit `pydantic_core.ValidationError: PostSummary.topic_id Input should be a valid string [input_value=None]`. Folge: PWA-Tab „Zur Freigabe" komplett leer, obwohl Posts in der DB sind.

**Root Cause:** `pwa-api/app/schemas.py:26` deklariert `topic_id: str` ohne Optional. Posts ohne Topic-Zuordnung (manuell angelegt, breaking_news ohne Topic-ID, oder Edge-Cases im Content-Agent) brechen die gesamte Liste — nicht nur den einen Eintrag, weil Pydantic den Validation-Error wirft, bevor das Item übersprungen werden kann.

**Folgen:** Ein einziger Post mit `topic_id IS NULL` killt die komplette Pending-Liste in der PWA. Der Tab wirkt einfach leer, kein Hinweis auf den Fehler.

**Code:**
- Schema: `/opt/infrastructure/environment_a/pwa/pwa-api/app/schemas.py:24-33` (`PostSummary`, Zeile 26 `topic_id: str`)
- Caller: `/opt/infrastructure/environment_a/pwa/pwa-api/app/routes/posts.py:90` (`PostSummary(...)`)

**Fix-Vorschlag:** `topic_id: str | None = None` im Schema. Optional zusätzlich: `posts.py:92` Default auf `""` lassen damit kein None reinkommt; aber Schema-Lockerung ist die robustere Lösung.

**Reproduktion:** beliebigen Post via SQL mit `topic_id=NULL` einfügen → `/posts?status=<seiner status>` → 500 + leerer Tab. Aktuell (2026-05-12) durch Workaround `topic_id='manual_test'` auf `post_0aa2c16f` umgangen.

---

### Bug 41.4 ✅ FIXED 2026-05-13 — `SlideBrief.swipe_hint` ist `bool | None`, Content-Agent liefert Strings

**Symptom:** `GET /api/tenants/grubis-weine/posts/<karussell_id>` liefert 500 mit `pydantic_core.ValidationError: SlideBrief.swipe_hint Input should be a valid boolean, unable to interpret input='→ Weiterwischen'`. Folge: Karussell-Post-Detail in der PWA komplett unzugänglich.

**Root Cause:** `pwa-api/app/schemas.py:47` deklariert `swipe_hint: bool | None`. Schritt 39 (Karussell-Optimierung) hat das Feld als Bool eingeführt — die Slide-Generation und/oder Test-Daten liefern aber Strings wie „→ Weiterwischen". Schema und Daten-Realität sind inkonsistent.

**Folgen:** Sobald ein Karussell mit string-`swipe_hint` in der DB landet, ist das gesamte Post-Detail nicht mehr abrufbar — der User sieht den Post in der Liste, beim Antippen passiert nichts/leer.

**Code:**
- Schema: `/opt/infrastructure/environment_a/pwa/pwa-api/app/schemas.py:47` (`swipe_hint: bool | None`)
- Mapper: `/opt/infrastructure/environment_a/pwa/pwa-api/app/routes/posts.py:42` (`swipe_hint=s.get("swipe_hint")` — reicht den Roh-Wert durch)
- Erzeuger: Content-Agent Slide-Generation (Schritt 39); prüfen ob Bool oder String erzeugt wird

**Fix-Vorschlag (zwei Wege):**
1. **Schema-Seite (defensiver):** `swipe_hint: str | bool | None = None`. Konsumenten im Frontend müssen mit beidem klarkommen — aktuell `Composer.tsx` benutzt das Feld nicht, daher harmlos.
2. **Generation-Seite (semantisch sauberer):** Slide-Generation im Content-Agent liefert nur Bools (True wenn Hinweis erwünscht, sonst False/None). Der konkrete Text „→ Weiterwischen" wird im PWA-Frontend als Konstante gerendert.

Empfehlung: Weg 2 — der String gehört in die UI, nicht in die Daten.

**Reproduktion:** Karussell-Post mit beliebigem String-Wert in `regie_brief.slide_struktur[*].swipe_hint` → Detail-Endpoint 500. Aktuell (2026-05-12) durch SQL-Strip des Feldes auf `post_0aa2c16f` umgangen.

---

### Bug 41.5 — PWA Redaktionsplan-Revise zeigt keinen Ladezustand

**Symptom:** Nach Tippen auf „Änderung schicken" / „Plan überarbeiten" in `/plan/pending` (PWA) gibt es keine sichtbare Reaktion. Kein Spinner, kein Button-Disabled-State, kein Toast. Es wirkt als ob die Aktion hängt oder gar nicht abgeschickt wurde — der User würde im Zweifel mehrfach tippen.

**Beobachtet:** Korbinian, 2026-05-12 ca. 20:55 CEST (entspricht ~18:55 UTC) während Live-Test mit grubis-weine. Logs sind über `docker logs pwa_api`, `docker logs pwa_web`, sowie `langgraph_redaktionsplan` für diesen Zeitraum verfügbar (E2E-Trace inkl. /plan/revise call).

**Vermutung:** `PlanActions.tsx` ruft `POST /api/proxy/tenants/.../plan/revise` ab, aber ohne `busy`-State im Button / Loader-Overlay. Der Backend-Call läuft (siehe nächster Bug 41.6, der zeigt dass der zweite Revise-Call gar nicht ankam — verstärkt das UX-Problem).

**Code:**
- `/opt/infrastructure/environment_a/pwa/pwa-web/app/plan/pending/PlanActions.tsx` (UI-Komponente, vermutlich kein Busy-State)
- Endpoint: `pwa-api/app/routes/plan.py:75 revise_plan` → bridge → `langgraph_redaktionsplan /revise`

**Fix-Vorschlag:**
- `useState<boolean>` für `busy`, Button `disabled={busy}` + Loader-Icon während Request
- Optional: Toast „Plan wird überarbeitet…" für Sichtbarkeit
- Pattern wie in `Composer.tsx` (Manual-Post-Wizard) — dort sauber gelöst
- Error-Toast wenn `r.ok === false`

---

### Bug 41.6 — Redaktionsplan-Revise: zweite Änderung kam nicht durch + Output zu detailliert statt im Plan-Format

**Symptom:** Korbinian hat in `/plan/pending` zweimal hintereinander eine Plan-Änderung („Revision") angefordert:
- **1. Revision:** Ein neuer Plan kam zurück, aber statt im ursprünglichen kompakten Topic-Format (Titel + KW + Wochentag + Format + Säule) hat die Revision deutlich detailliertere/längere Topic-Beschreibungen geliefert. Das passt nicht zum vorhandenen UI-Rendering und wirkt überarbeitet ohne Mehrwert.
- **2. Revision:** Hat gar nichts gemacht — kein neuer Plan-Output, kein Update sichtbar. Backend hat möglicherweise nicht reagiert oder Antwort kam nicht im erwarteten Shape an.

**Beobachtet:** Korbinian, 2026-05-12 ca. 20:50–21:00 CEST (18:50–19:00 UTC) während Live-Test mit grubis-weine, `job_850ce942` (Mai 2026 Plan).

**Was es nicht ist:** Bug 41.5 (fehlender Ladezustand) hat es vermutlich verstärkt — beim zweiten Versuch ist unklar ob Korbinian doppelt getippt hat oder ob der Backend-Call wirklich nicht ankam.

**Vermutung Output-Format:**
- `node_revise_plan` im Redaktionsplan-Agent (oder dessen Prompt) ist anders strukturiert als `node_generate_plan` — die Revision baut Topics mit ausführlicheren Feldern, weil der LLM Feedback in lange Erklärungen umsetzt statt in kompakte Slot-Daten.
- Im ursprünglichen Plan-Format sind Topics: `{id, title, kw, wochentag, format, kanal, saeule_id, saeule_name, status}` — kurz und strukturiert.
- Revision-Output sollte exakt dieses Shape behalten, nur Felder aktualisieren — nicht erweitern oder umformulieren.

**Vermutung 2. Revision:**
- Möglich: Job-State nach erster Revision auf `confirmed`/`revising` gewechselt → zweite Revision wurde abgewiesen
- Möglich: pending_jobs-Tabelle wurde nicht resettet → zweite Revision schrieb in andere Row
- Möglich: Frontend hat alten job_id behalten, Backend hat aber neuen erwartet

**Logs für Forensik (entscheidend, bevor sie wegrotieren):**
```
docker logs pwa_web --since 30m 2>&1 | grep -E "plan/revise|revise"
docker logs pwa_api --since 30m 2>&1 | grep -E "revise|plan|grubis"
docker logs langgraph_redaktionsplan --since 30m 2>&1 | grep -E "revise|grubis|feedback"
```
Zeitfenster: 2026-05-12 18:50–19:10 UTC. job_id: `job_850ce942`.

**Code:**
- `pwa-web/app/plan/pending/PlanActions.tsx` (Revise-Button)
- `pwa-api/app/routes/plan.py:75 revise_plan`
- `pwa-api/app/bridges/langgraph.py redaktionsplan_revise`
- `langgraph/redaktionsplan/app/main.py /revise` + `nodes.py node_revise_plan` + Prompt
- pending_jobs-Schema: prüfen ob mehrfach-Revisions sauber chained werden

**Fix-Reihenfolge:**
1. Logs ziehen + analysieren (Zeitfenster oben)
2. Prompt für `node_revise_plan` an `node_generate_plan`-Schema angleichen — gleiches Output-Format, nur veränderter Inhalt
3. Validation im Agent: revise-Response muss gleiche Topic-Felder + Shape liefern, sonst Revision verwerfen
4. pending_jobs Mehrfach-Revision-Test (3× hintereinander)
5. UI-Spinner aus Bug 41.5 zwingend zusammen mit Fix 41.6 ausrollen

---

### Bug 41.7 ✅ FIXED 2026-05-12 21:35 CEST — `mensch.md` + `sprache-ton.md` landen nie im Content-Prompt

**Symptom:** Generierte Posts „klingen nicht nach dem Inhaber". Korbinian, 2026-05-12 ca. 21:20 CEST nach Generierung von 3 neuen Posts für grubis-weine (Session `weekly_043dffc3`, posts: `post_de3c7ff6`, `post_f1f2aecb`, `post_a0c1dd50`): „Ich sehe keine VoiceDB-Ähnlichkeit." Faktencheck der Captions vs. VoiceDB bestätigt — Themen/Fachwortschatz aus dem Interview kommen rüber (Veltliner, Wachau, Pfefferwürze), aber die persönliche Tonlage des Inhabers fehlt.

**Root Cause:** Der Content-Agent injiziert nur **2 von 6** VoiceDB-Files direkt in den Regie-Brief-System-Prompt:
- `style-anchors.md` (1280 Zeichen) via `read_voice_db_anchors()` — `nodes.py:312`, `prompts.py:132`
- `no-goes.md` (829 Zeichen) via `read_no_goes()` — `nodes.py:313`, `prompts.py:135`

Die restlichen **vier** Files werden **nirgends im Post-Generation-Pfad geladen**:
- `mensch.md` (1797 Zeichen — Selbstbeschreibung des Inhabers)
- `sprache-ton.md` (1085 Zeichen — Sprach-/Ton-Regeln, z.B. „mit Gastronomie-Kunden per du")
- `stories.md` (2108 Zeichen — Erzählungen aus Interviews)
- `product-knowledge.md` (2048 Zeichen — Fachwissen)

Diese Dateien werden zwar in die OpenWebUI Collection synced (für RAG-Abfragen) und in `presentation.py` für die Konzept-Präsentation direkt geladen — aber **nicht in `nodes.py:generate_regie_brief`**. Das einzige Voice-Signal aus diesen Files ist der RAG-Snippet (~1000–1400 Zeichen) den `search_voice_db()` zurückgibt, und der ist topic-spezifisch + zeitweise nicht verfügbar (siehe Bug 41.1 / Cold-Start-Timeout).

**Folgen:**
- ~2900 Zeichen Voice-Material (mensch + sprache-ton) kommen garantiert nie ins Modell
- ~4150 Zeichen Story/Wissens-Material kommen bestenfalls als RAG-Snippet
- Posts werden allgemein-höflich-werbend statt persönlich
- Der „Inhaber-Stimme"-Score, den Korbinian visuell prüft, fällt entsprechend niedrig aus

**Code-Anker:**
- `langgraph/content/app/nodes.py:312–320` (`load_context_node`) — nur `style_anchors` + `no_goes` geladen
- `langgraph/content/app/nodes.py:422–427` (`generate_regie_brief`) — Prompt-Format nimmt nur `style_anchors` + `no_goes` als Inhaber-Material
- `langgraph/content/app/prompts.py:131–135` (`REGIE_BRIEF_SYSTEM_PROMPT`) — keine `{mensch}` / `{sprache_ton}` Placeholder
- `langgraph/content/app/tools.py:117–127` — `read_voice_db_anchors()` + `read_no_goes()` existieren, **keine** `read_mensch()` / `read_sprache_ton()`
- Im Gegensatz: `langgraph/content/app/presentation.py:1236–1237` lädt beide Files für die Konzept-Präsentation — Beweis dass die Loader existieren könnten

**Fix-Vorschlag:**
1. Neue Reader in `tools.py`: `read_mensch(gitea_repo)` + `read_sprache_ton(gitea_repo)` analog zu `read_voice_db_anchors` (1-Zeiler je: `read_md_file(gitea_repo, "voice-db/onboarding/mensch.md")`)
2. `load_context_node` (`nodes.py:312`) lädt beide Files in den State (`state.mensch` + `state.sprache_ton`)
3. `state.py` ContentState um beide Felder erweitern
4. `REGIE_BRIEF_SYSTEM_PROMPT` (`prompts.py:118`) bekommt zwei neue Sections:
   ```
   INHABER-PERSÖNLICHKEIT (so beschreibt sich der Inhaber selbst):
   {mensch}

   INHABER-SPRACHE (so spricht der Inhaber tatsächlich):
   {sprache_ton}
   ```
5. `nodes.py:422` Prompt-Format passt beide Variablen rein
6. Token-Budget prüfen: Prompt wächst um ~2900 Zeichen → ~750 Tokens. Im Rahmen, kein Crop nötig.
7. Optional: `stories` + `product-knowledge` ebenfalls injizieren, aber nur als Tail-Append wenn Token-Budget reicht — sonst RAG weiternutzen.

**Reproduktion:** Beliebiger `langgraph_content /run` für grubis-weine → System-Prompt loggen → keine Erwähnung von „sehr gerne isst und trinkt", „mit Lebensfreude kommuniziere", „mit Gastronomie-Kunden per du" (alles direkt aus mensch.md / sprache-ton.md). Posts dieser Session: `post_de3c7ff6`, `post_f1f2aecb`, `post_a0c1dd50` zum Vergleich verfügbar (DB).

**Reihenfolge:** Hoher Hebel auf Output-Qualität, geringer Aufwand (~1–2h Code + Test). Sollte vor weiterem Mandanten-Roll-out gefixt sein.

**Fix (2026-05-12 21:35 CEST):**
- `tools.py`: neue Reader `read_mensch()` + `read_sprache_ton()` (analog zu `read_voice_db_anchors`, je 4 Zeilen)
- `state.py` ContentState: zwei neue Felder `mensch: str = ""` + `sprache_ton: str = ""`
- `nodes.py load_context`: lädt beide Files und schreibt sie in den State
- `nodes.py` Import-Liste: `read_mensch`, `read_sprache_ton` ergänzt
- `prompts.py REGIE_BRIEF_SYSTEM_PROMPT`: zwei neue Sections vor INHABER-IDENTITÄT — „INHABER-PERSÖNLICHKEIT" mit `{mensch}` + „INHABER-SPRACHE" mit `{sprache_ton}`. Style-Anchors-Section umformuliert: jetzt explizit als „konkrete Formulierungen" gekennzeichnet (nicht mehr das einzige Voice-Signal).
- `nodes.py generate_regie_brief` .format()-Call: `mensch` + `sprache_ton` werden mit Defaults „Noch keine … erfasst." reingereicht
- Container rebuilt + started: langgraph_content healthy
- Verifikation: System-Prompt enthält jetzt „sehr gerne isst und trinkt", „mit viel Lebensfreude", „per du", „Gastronomie-Kunden", „INHABER-PERSÖNLICHKEIT", „INHABER-SPRACHE" (alle ✅). Prompt-Länge 7900 chars (~1975 Tokens), Token-Budget okay.
- Posts `post_de3c7ff6`, `post_f1f2aecb`, `post_a0c1dd50` bleiben in DB als Vergleichsmaterial — der nächste `/run` für grubis-weine wird mit dem neuen Prompt arbeiten.

---

### Bug 41.8 ✅ FIXED 2026-05-12 21:46 CEST — `read_post_index` las den falschen Pfad → Idempotenz-Check blind

**Symptom:** Beim Re-Run des Content-Agents für `weekly_043dffc3` (mit fixem 41.7-Prompt) wurden Topics neu generiert, obwohl bereits frische Posts dafür in der DB lagen. Folge: Duplikate (`post_27bcb3ff`, `post_6d44748b`, `post_dcab4822`, `post_608f3d69`) mit leerem `regie_brief` (degraded während Anthropic-API-Overload, weil der Agent auf bereits abgeschlossene Topics nochmal losging).

**Root Cause:** `content/app/tools.py:105 read_post_index()` rief `read_json_file(gitea_repo, "content/post-index.json")` auf. Das Compat-Routing in `gitea_client.py` kennt aber nur `editorial/post_index.json` (mit Unterstrich + anderem Ordner). Pfad-Mismatch → Routing greift nicht → Fallback auf Gitea-Direkt-Read → liefert irgendeine alte Index-Datei (oder leer). Folge: `already_done` ist immer leer, Idempotenz-Filter inaktiv.

**Fix:** `tools.py read_post_index()` ruft jetzt `db_sync.read_post_index(tenant_id=...)` direkt auf (Live-View aus posts-Tabelle). Gitea-Fallback bei DB-Fehler bleibt.

**Verifikation:** Nach Fix + Container-Rebuild zeigt das Log: `Topic 'topic_7d8316' bereits generiert für session 'weekly_043dffc3' – übersprungen` + `Topic 'topic_f082e3' bereits generiert ... übersprungen` + `1 Topics zu verarbeiten` (nur noch der Reel).

---

### Bug 41.9 ✅ FIXED 2026-05-12 22:20 CEST — Redaktionsplan-Agent erzeugt Topics mit Winzer-Frame, ignoriert `branche`

**Symptom:** Topic `topic_f082e3` „Warum leichte Weine mehr Können verlangen als schwere" hat im `description`-Feld:
> Ein verbreitetes Missverständnis: Dass leichte Weine einfacher zu machen seien. Was es wirklich braucht, damit ein Sommerwein frisch, lebendig und trotzdem komplex bleibt – und woran man das im Glas erkennt.

Das ist **Winzer-Perspektive** („zu machen", „was es braucht", „im Glas erkennen") — Grubi ist aber **Weinhändler** (`tenants.branche = 'Weinhandel'`). Der Topic-Brief vergiftet damit alle nachgelagerten Content-Posts, weil der Content-Agent versucht, den Brief zu erfüllen und Winzer-Inhalte halluziniert (siehe Bug 41.10).

**Beobachtet:** 2026-05-12 ca. 21:50 CEST nach Generierung von `post_7f8320e9` für grubis-weine. Caption begann mit „Ein leichter Wein verzeiht nichts. Kein Fehler im Keller, keine falsche Temperatur – alles liegt blank." → Keller-Bezug existiert nirgends in den Inhaber-Daten.

**Root Cause:** Der Redaktionsplan-Agent erbt vermutlich denselben Bug wie 41.7 — er hat keinen direkten Zugriff auf `mensch.md` + `sprache-ton.md` (wo „Weinhandel", „Suche nach Winzern", „mit Kunden per du" stehen) und kennt vermutlich nicht einmal `tenant.branche`. Topics werden auf Basis von Säulen-Funktion (`expertise`) + Monatsthema generiert, ohne den Inhaber-Frame zu prüfen.

**Code-Anker (zu verifizieren):**
- `langgraph/redaktionsplan/app/prompts.py` — Topic-Generierungs-Prompt
- `langgraph/redaktionsplan/app/nodes.py` — `node_generate_topics` o.ä.
- `langgraph/redaktionsplan/app/state.py` — vermutlich kein `mensch` / `sprache_ton` / `branche` Feld

**Fix-Vorschlag (analog zu Bug 41.7):**
1. `mensch.md` + `sprache-ton.md` Reader (oder Import aus `content/tools.py`) in redaktionsplan-Agent verfügbar machen
2. `branche` aus `tenants`-Tabelle laden (mensch.md sollte sie indirekt liefern, aber explizit ist robuster)
3. Topic-Generierungs-Prompt erweitern:
   - Section „INHABER-ROLLE & BRANCHE: {branche}" — z.B. „Weinhandel (= Händler, NICHT Winzer)"
   - Section „INHABER-PERSÖNLICHKEIT" + „INHABER-SPRACHE" wie in 41.7
   - Pflicht-Regel: „Topic-Description darf nur Inhalte vorschlagen, die der Inhaber aus seiner Rolle heraus authentisch erzählen kann."
4. Konkrete Negativ-Liste pro Branche: Weinhandel → keine Vinifikation, kein Keller-Erzählen, kein „so machen wir den Wein"
5. Optional: Validierungs-Pass auf jedes generierte Topic (Branche-Check via Haiku) bevor es gespeichert wird

**Reihenfolge:** Vor weiterem Mandanten-Rollout. Sehr hoher Hebel: ein schlechtes Topic vergiftet 4–5 Posts (Primär + Cross-Post + Revisionen). Aufwand ~2–3h.

**Fix (2026-05-12 22:20 CEST):**
- `redaktionsplan/state.py`: 4 neue Felder `mensch` + `sprache_ton` + `branche` + `unternehmen_beschreibung`
- `redaktionsplan/tools.py`: neue Reader `read_mensch()` + `read_sprache_ton()` + `read_no_goes()` (analog zu content-Agent); `read_md_file` aus gitea_client importiert
- `redaktionsplan/nodes.py:node_load_context`: lädt mensch.md + sprache-ton.md + extrahiert branche aus `mandant_config.unternehmen.branche`
- `redaktionsplan/prompts.py SYSTEM_PROMPT`: neue Sektion „KRITISCHE ROLLEN-REGEL" mit konkretem Verbot/Erlaubt-Beispiel für Weinhandel (kein Vinifikations-Frame, erlaubt Empfehlungen/Begegnungen)
- `redaktionsplan/prompts.py TOPICS_WITH_FORMAT_CTA_PROMPT`: erste Sektion „INHABER-ROLLE + Branche + Beschreibung + mensch + sprache_ton + ROLLEN-CHECK pro Topic", neue Negativ-Beispiele in der GUT/SCHLECHT-Liste
- `redaktionsplan/prompts.py REVISE_PLAN_PROMPT`: Branche-Header + Rollen-Regel ergänzt — Revisions-Output bleibt rollen-treu
- `redaktionsplan/nodes.py`: format()-Calls für TOPICS_WITH_FORMAT_CTA_PROMPT und REVISE_PLAN_PROMPT mit neuen Variablen versorgt
- Container `langgraph_redaktionsplan` rebuilt + healthy

**E2E-Verifikation:** Frischer Redaktionsplan `job_fd3aeb03` für grubis-weine Mai 2026:
- Monatsthema: „Draußen. Frisch. Jetzt." (statt vorher generisches „Sommerweine die man wirklich trinken will")
- Topic 1 alt: „Warum leichte Weine mehr Können verlangen als schwere" + Winzer-Description → neu: „Leichte Weine für den Mai" + Empfehler-Description („Welche Weine aus dem Sortiment passen…")
- Topic 6 neu: „Kochen und Wein zusammen denken" — direkt aus `mensch.md` abgeleitet („Meine Geschichte mit Wein hat in meiner Zeit als Koch begonnen") — Beleg dass die VoiceDB im Topic-Prompt ankommt und genutzt wird
- 3 frische Posts (post_4d3714ea, post_b5c58f90, post_2c99187c) durchgelaufen: alle voice_check passed bei attempt=0 ohne Revisionsschleife (vorher 2–3 Revisionen üblich). Keine Keller-Halluzination, keine Winzer-Perspektive. Style-Anchor „Es bietet sich an" mehrfach organisch verwendet.

---

### Bug 41.11 ✅ FIXED 2026-05-12 22:25 CEST — Push+Email-Fallback greift nicht ohne push_subscription

**Symptom:** Nach Generierung der 3 Mai-Posts für grubis-weine hat `korbinian.schnall@prisment.de` weder Push noch Email bekommen. Die Notifications-Rows lagen mit `push_sent_at=NULL` UND `email_sent_at=NULL` in der DB und wurden vom Fallback-Cron nie aufgegriffen.

**Root Cause:** 2-Stufen-Logik-Loch:
1. `/internal/notify` (`pwa-api/app/routes/internal.py`): wenn `send_to_user` return-Wert `n == 0` (User hat keine push_subscription) → `push_sent_at` bleibt NULL. Es wurde KEIN Sofort-Email versendet.
2. `fallback_cron.py:50` prüft `WHERE n.push_sent_at IS NOT NULL` — Notifications die nie einen Push hatten, werden vom Fallback komplett ignoriert.

Bei `info@grubis-weine.de` (iPhone-PWA-Subscription da) funktionierten Pushes (mit 60s-Bundling-Skip beim 3. Post). Bei Korbinian ohne Subscription → totales Schweigen.

**Code-Anker:**
- `/opt/infrastructure/environment_a/pwa/pwa-api/app/routes/internal.py:111-130` (Push-Send-Block)
- `/opt/infrastructure/environment_a/pwa/pwa-api/app/notifications/fallback_cron.py:50` (WHERE-Klausel)

**Fix (2026-05-12 22:25 CEST):**
- `internal.py`: Nach Push-Versuch wenn `n == 0` UND `notification_type ∈ EMAIL_FALLBACK_TYPES` → Sofort-Email senden + `email_sent_at` setzen. Quiet-Hours-Schutz greift (zwischen 22-7 Berlin-Zeit aufschieben — Email-Bremse, **nicht Push**). Email-Bundling 60s analog zur Push-Bundle-Logik.
- `fallback_cron.py:WHERE`-Klausel auf OR erweitert: `(push_sent_at IS NOT NULL AND push_sent_at < 4h-cutoff AND nicht acked) OR (push_sent_at IS NULL AND created_at < 5min-cutoff)`. Belt-and-Suspenders falls Sofort-Email aus irgendeinem Grund nicht greift.
- Container `pwa_api` rebuilt + healthy.

**E2E-Verifikation 2026-05-12 22:25 CEST:**
- Test-Notification an grubis-weine getriggert
- Log: `[internal/notify] kein Push für korbinian.schnall@prisment.de + Quiet Hours — Email aufgeschoben (fallback_cron prüft)` ✅ erwartetes Verhalten (Quiet Hours zwischen 22-7 Berlin)
- info@grubis-weine.de bekam Push (`push_sent_at` gesetzt)
- Morgen früh nach 7:00 wird der fallback_cron die offenen Korbinian-Rows aufgreifen und Email senden (durch die erweiterte WHERE-Klausel jetzt sichtbar).

**Offene Folge-Frage (User-Anmerkung 2026-05-12):**
Push soll **immer** kommen, Quiet Hours sind nur Email-Bremse. Aktuell funktioniert das so, ABER: bei Korbinian fehlt die push_subscription. Reine Daten-Lücke, nicht Code-Bug. Damit Push für ihn ankommt:
- PWA auf Device öffnen → eingeloggt als korbinian.schnall@prisment.de
- Browser-Permission „Benachrichtigungen erlauben" akzeptieren
- Service-Worker registriert → push_subscriptions-Row entsteht automatisch
- Push-Aktivierungs-UI: `/profile` → Toggle „Push-Notifications"
- Ab dann landet Push sofort, kein Email-Umweg nötig.

---

### Bug 41.12 ✅ FIXED 2026-05-12 22:42 CEST — Post-Approval-Feedback fließt nicht in die VoiceDB zurück

**Symptom:** Beim Approven eines Posts mit Korrektur-Feedback („zu lang, kürzer fassen", „Sprache eher leicht & frisch, direkt") wird der Feedback-Text zwar gespeichert (`approval_audit.feedback` + `post_versions.feedback`) und für die unmittelbare Revision verwendet (`REVISION_FEEDBACK_PROMPT` in `content/nodes.py:1063–1100`) — danach aber nie wieder gelesen.

**Folgen:** Das System lernt aus jeder Inhaber-Korrektur **nichts** für zukünftige Posts. Die wertvollste Voice-Quelle (echte Approval-Workflow-Korrekturen) ist vom Voice-DB-Lernkreislauf abgeschnitten. Nur Interview-Antworten fließen via `append_stories` + `append_style_anchors` in die VoiceDB.

**Audit (2026-05-12, ein subagent-Recon):**

| Feedback-Quelle | Speicherung | In VoiceDB |
|---|---|---|
| Interview-Antworten | `sessions.qa_pairs` | ✅ Ja |
| Breaking-News | `sessions.qa_pairs` | ✅ Ja |
| Post-Revision-Feedback | `approval_audit.feedback` + `post_versions.feedback` | ❌ Nein |
| Plan-Revision-Feedback | ephemeral | ❌ Nein |
| Assist-Chat | ephemeral | ❌ Nein |

**Fix-Plan:**
1. Neuer Node `node_extract_voice_signals_from_revision` in `content/nodes.py` nach `node_save_revision`
2. Haiku-Call: nimmt `revision_feedback` + Caption-VOR + Caption-NACH als Input
3. Output JSON: `{neue_style_anchors:[…], neue_no_goes:[…], persönliche_phrasen:[…], thematische_präferenzen:[…]}`
4. Append in `voicedb_entries` (style-anchors / no-goes) mit Source-Marker `<!-- Source: feedback post_XXX 2026-05-12 -->`
5. Idempotenz: doppelte Anker werden de-dupliziert (gleiche Phrase = Skip)
6. Optional: gleicher Mechanismus für Plan-Revise-Feedback im Redaktionsplan-Agent

**Aufwand:** ~3–4h Code + Prompt-Design + E2E-Test
**Hebel:** sehr hoch — jede Korrektur baut Voice-DB inkrementell auf. Nach 2–3 Wochen aktiver Approve-Sessions deutlich differenzierte Anker.

**Fix (2026-05-12 22:42 CEST):**
- `content/prompts.py`: `EXTRACT_VOICE_SIGNALS_PROMPT` — Haiku-Prompt der aus Feedback + Caption-vorher/nachher JSON mit `neue_style_anchors` + `neue_no_goes` extrahiert. Sparsam: lieber 0 Signale als verrauschte.
- `content/tools.py`: `append_to_voicedb_entry(tenant_id, entry_type, new_lines, source_tag)` — idempotenter Append in `voicedb_entries` mit dedup (case-insensitive substring), Header-Erstellung für neue entry_types, Source-Marker `<!-- {source_tag} -->`, History-Snapshot in `voicedb_history` mit `change_source='agent'`.
- `content/nodes.py`: `node_extract_voice_signals_from_revision()` — läuft nur wenn `revision_count > 0`, lädt Caption-vorher aus `versions[-2]`, ruft Haiku, schreibt Anker + No-Goes. Fehler werden geschluckt (Lernkreislauf ist optional).
- `content/graph.py`: neue Node `extract_voice_signals` in `build_revise_graph()`, Kante `save_revision → extract_voice_signals → END`.
- Container `langgraph_content` rebuilt + healthy.

**E2E-Verifikation 2026-05-12 22:42 CEST:**
- Synthetischer Revise-Call für `post_b5c58f90` (grubis-weine) mit Feedback: „Bitte kürzer fassen, nur 2 Sätze. Und ich sage nie 'einfach' - das ist zu beliebig. Lieber konkrete Wörter wie 'frisch' oder 'klar'."
- Haiku extrahierte:
  - Style-Anker (2): „Konkrete sensorische Deskriptoren statt Füllwörter (frisch, klar, direkt)" + „Maximal 2 Sätze pro Haupttext"
  - No-Go (1): „Das Wort 'einfach' als Füllwort vermeiden"
- Alle 3 Signale in `voicedb_entries` mit Source-Tag `feedback post_b5c58f90 2026-05-12` appended.

---

### Bug 41.13 — Keine UI-Info dass Content-Agent gerade arbeitet (nach Interview-Close)

**Symptom:** Wenn das wöchentliche Interview durch ist und der Content-Agent danach Posts generiert (~5-15 min Laufzeit für 3 Topics), gibt es in der PWA keinerlei Hinweis dass das System gerade arbeitet. Der User sieht entweder die alte Liste (Posts noch nicht da) oder muss raten ob er warten oder neu laden soll.

**Beobachtet:** Korbinian, 2026-05-12, nach Interview-Close für `weekly_043dffc3` — keine Progress-Anzeige in `/posts`, `/answers`, oder auf der Heute-Seite.

**Folgen:** UX wirkt eingefroren. Bei Test-User noch ok, bei echtem Mandanten verwirrend („Habe ich was kaputt gemacht?").

**Fix-Vorschlag:**
- `pending_jobs.current_content_job` Feld neu (oder JSON-Blob in `raw`): wird vom Content-Agent beim `/run`-Start auf `{state: "running", started_at, topics_total}` gesetzt und bei jedem `node_save_post_draft` aktualisiert (`topics_done++`). Nach `END` auf `null` resetten.
- PWA-API Endpoint `GET /api/tenants/{tid}/queue` (existiert schon) um `content_progress` erweitern
- Frontend `/posts` Header: Banner „Content-Agent arbeitet — 1 von 3 Posts fertig" wenn `content_progress.state == "running"`
- Refresh-Polling oder SSE alle 30s während Job läuft

**Aufwand:** ~2–3h
**Reihenfolge:** mittlere Priorität — User-Experience-Verbesserung, nicht Generation-Qualität.

---

### Bug 41.10 ✅ FIXED 2026-05-12 22:05 CEST — Content-Agent ignoriert Inhaber-Widerspruch im Q&A

**Symptom:** Im Interview für `topic_f082e3` widerspricht Grubi der Topic-These **explizit**:
- **Frage:** „Wann ist dir zum ersten Mal aufgefallen, dass ein leichter Wein eigentlich viel schwieriger sein kann als ein schwerer?"
- **Antwort:** „Ein leichter Wein ist **nicht** schwieriger als ein schwerer. Es bietet sich nur an, zum Beginn eines Abends mit einem leichteren Weißwein zu starten…"

Der generierte Post `post_7f8320e9` ignoriert das aber komplett und schreibt „Ein leichter Wein verzeiht nichts" + Halluzinations-Argumentation („Kein Fehler im Keller, keine falsche Temperatur – alles liegt blank"). Der Agent hat den Topic-Titel als Fakt akzeptiert und die Inhaber-Antwort ignoriert.

**Beobachtet:** 2026-05-12 ca. 21:50 CEST. Posts `post_7f8320e9` (alt = vor Bug 41.7-Fix war's übrigens dasselbe Verhalten in `post_de3c7ff6`: auch dort wurde die Topic-These verteidigt).

**Root Cause:** Der `REGIE_BRIEF_SYSTEM_PROMPT` behandelt `topic_title` + `topic_description` implizit als Autorität. Im User-Prompt steht zwar `qa_pairs` zur Verfügung, aber es gibt keine Regel: „Wenn qa_pairs der Topic-These widersprechen, ändere den Frame statt zu halluzinieren." Das LLM optimiert für Topic-Erfüllung + Voice-Match, fällt aber in Allgemeinwissen, wenn der Inhaber das Topic-Premise nicht hergibt.

**Code-Anker:**
- `langgraph/content/app/prompts.py:118 REGIE_BRIEF_SYSTEM_PROMPT`
- `langgraph/content/app/prompts.py REGIE_BRIEF_USER_PROMPT` (qa_pairs + rag_context)

**Fix (2026-05-12 22:05 CEST):**
- `prompts.py REGIE_BRIEF_SYSTEM_PROMPT`: zwei neue Sections am Anfang —
  - „INHABER-ROLLE" mit `{branche}` + `{unternehmen_beschreibung}` als Frame
  - „KRITISCHE ROLLEN-REGEL" mit konkretem Negativ-Beispiel (Weinhandel → kein Keller-Erzählen)
  - „FAKTENTREUE — Q&A-Material ist Ground Truth" mit klarer Regel: Topic-Description ist nur Anregung, Q&A überstimmt
- `prompts.py REGIE_BRIEF_USER_PROMPT`:
  - THEMA umlabelt als „Vorschlag, KEINE Vorgabe wenn das Q&A widerspricht"
  - qa_pairs umlabelt als „GROUND TRUTH, höher gewichtet als das THEMA oben"
  - 3-Punkt-Faktentreue-Check vor dem JSON-Return
- `nodes.py generate_regie_brief`: Branche + Beschreibung aus `state.mandant_config["unternehmen"]["branche"]` + `["beschreibung"]` extrahiert + an .format() gereicht
- Container rebuilt + healthy

**E2E-Verifikation:** Re-Generation desselben Topics `topic_f082e3` für grubis-weine:
- LLM hat den Topic-Titel umgeschrieben: „Warum leichte Weine mehr Können verlangen als schwere" → „Vom Federspiel zum Spätburgunder – so startet ein guter Abend"
- Caption (`post_0a022dcc`):
  > Federspiel, Chardonnay, Spätburgunder – in dieser Reihenfolge.
  > Es bietet sich an, den Abend mit einem leichten, erfrischenden Grüner Veltliner aus der Wachau zu starten...
  > **Nicht weil leichte Weine einfacher wären – das sind sie nicht.** Sondern weil dieser Aufbau einen Abend macht, der sich richtig anfühlt.
- Inhaber-Widerspruch aus dem Q&A explizit übernommen, keine Keller/Vinifikations-Halluzination mehr, Empfehler-Modus durchgehalten.

Vergleich vorher (`post_7f8320e9`) vs. nachher (`post_0a022dcc`) bleibt in DB als Beleg.

---

## ✅ Schritt 41 — Karussell Composite-Renderer (Canvas-Slide-Generation) — 2026-05-13

**Status:** Geplant, vorgemerkt 2026-05-12 nach E2E-Test der Insta-Übergabe.

**Auslöser:** Korbinian hat die Insta-Übergabe für einen Karussell-Post live getestet und den Logikbug bestätigt, der in Schritt 40 als „bewusst nicht in 40" vorgemerkt war.

### Problem-Beschreibung

**Aktueller Composer-Flow (Composer.tsx) für Karussell:**
1. Pro Slide-Step: Bild-Idee + Slide-Text + Foto-Picker. Button „Slide-Text kopieren" setzt Clipboard.
2. Slide N+1: Review mit Caption-Preview.
3. „An Instagram senden": setzt Clipboard auf **Gesamt-Caption**, ruft `navigator.share({files: [...N Bilder]})`.

**Zwei kombinierte Probleme:**

- **Clipboard-Überschreibung:** Pro Slide eine neue Kopie überschreibt die vorherige. Beim Übergang nach Insta liegt nur noch die letzte Slide-Text-Kopie drin — und die wird im Review-Step durch die Gesamt-Caption ersetzt. Slide-Texte landen also nie in Insta.
- **Instagram-Karussell hat nur EINE Caption:** Es gibt im Insta-Karussell-Composer kein Text-Feld pro Slide. Slide-Texte sind in unserem Modell Text, der **im Bild eingebrannt** sein muss. Der User müsste sie manuell als Sticker auf jedes Bild platzieren — unrealistisch ab 3+ Slides.

**Konsequenz:** Karussell-Posts kommen aktuell ohne Slide-Texte in Insta an. Der gesamte Content-Wert eines Karussells (titel/subtitle/inhalt pro Slide) geht beim Posten verloren.

### Lösungsansatz – Canvas-Composite-Rendering

**Idee:** Die PWA rendert pro Slide ein **fertiges Composite-Bild** = User-Foto + Text-Layer eingebrannt = 1080×1350 JPEG. Übergabe an Insta dann mit N fertigen Composites, der User muss in Insta nur noch die Caption einfügen.

**Was die PWA dazu braucht:**
- HTMLCanvasElement (Browser-API, ohne extra Lib)
- Brand-Fonts geladen: Inter Display (Headlines) + Inter (Body) — via `FontFace` API oder direkt aus dem PWA-Bundle
- Layout-Templates pro `slide_type`:
  - `cover`: großer Titel oben, optional Subtitle, Foto unten als Hintergrund mit Dunkel-Overlay 40%
  - `substanz`: Foto als Hintergrund vollflächig, Text-Box unten mit halbtransparentem Background, titel + inhalt
  - `cta`: Foto-Hintergrund + zentrierte CTA + Handle unten
- Text-Wrapping + Auto-Resize (Title-Schrift anpassen je nach Länge)
- Foto auf 1080×1350 croppen + skalieren (Center-Crop)
- Kontrast-Check: bei zu hellen Bildern Overlay-Opacity dynamisch erhöhen
- Export als JPEG-Blob via `canvas.toBlob()`, in File-Objekt verpacken für Web Share API

**Composer-Flow neu:**
1. Pro Slide-Step: User wählt Foto. PWA rendert sofort eine Live-Preview (Canvas mit Text-Overlay eingebrannt).
2. User kann „Foto austauschen" oder Text-Overlay-Position toggeln (oben/unten).
3. Review-Step: zeigt alle N Composites als Thumbnail-Grid.
4. „An Instagram senden": Clipboard = Gesamt-Caption; `navigator.share({files: [composite_1.jpg, ..., composite_N.jpg]})`.
5. User klebt nur die Caption ein, kein Sticker-Gefummel mehr.

### Akzeptanzkriterien

- [ ] Canvas-Renderer `lib/composite-renderer.ts` mit Funktion `renderSlide(photo: File, slide: Slide, slideType): Promise<File>`
- [ ] Layout-Templates für `cover` / `substanz` / `cta` (3 Layouts, hartkodiert per Brand-System)
- [ ] Inter + Inter Display via `FontFace` geladen, Fallback auf system-ui
- [ ] Composer.tsx integriert: bei Karussell-Step nach Foto-Pick → Live-Preview des Composites
- [ ] Review-Step zeigt Composites statt der Raw-Fotos
- [ ] `sendNow()` schickt Composites an Web Share API
- [ ] E2E-Test mit grubis-weine: 5-Slide-Karussell, Composites in Insta sichtbar, Text lesbar auf realen Fotos

### Was bewusst NICHT in 41 ist

- **Manuelle Position-Editierung des Text-Layers** (Drag & Drop): zunächst nur Template-Position. Später ausbaubar.
- **Animierte Karussells** (MP4 statt JPG): Insta unterstützt nur statische Slides im Standard-Karussell.
- **Auto-Layout via LLM:** keine Claude-Aufrufe für Layout-Entscheidungen. Deterministisch via slide_type-Template.
- **Reels-Cover-Composite:** separater Use-Case, später als Schritt 42.

### Aufwand

- Renderer + Templates + Fonts: ~4–6h
- Composer-Integration + Preview: ~2h
- Test + Iteration auf realen Mandanten-Fotos: ~2–3h
- **Gesamt: 1 Tag**

### Reihenfolge

Nach den DSGVO-Themen (Schritt 19) und vor öffentlichem Launch. Vor 41 sollten 41.1 (RAG-Timeout) und 41.4 (swipe_hint) gefixt sein — sonst verliert man Test-Zeit an Folgefehlern.

---

## ✅ Schritt 42 — Implicit Voice-Learning aus approved Posts (2026-05-12 22:41)

**Status:** Geplant, vorgemerkt 2026-05-12 nach Feedback-Audit.

**Auslöser:** Bug 41.12 deckt nur die **explizite** Feedback-Quelle ab (Post-Revisions mit User-Korrekturen). Aber auch das **implizite Approval-Verhalten** ist ein Voice-Signal: wenn der Inhaber 5 Posts in Folge **ohne Feedback** akzeptiert, sind die in diesen Posts verwendeten Formulierungen offenbar passend — sie verdienen einen Platz in der Voice-DB.

### Architektur

**Trigger:** wöchentlicher n8n-Cron `T: Cron: Voice-Learning (Sonntag 18:00)`. Loopt über aktive Tenants. Pro Tenant: POST an neuen Endpoint `langgraph_content:8002/voice-learning`.

**Endpoint-Logik:**
1. Lade letzte N approved Posts (Default N=10) für den Tenant (nur `status='approved' OR 'published'`, ohne Feedback-Korrekturen, also `revision_count == 0`)
2. Wenn N < 3 → nichts zu lernen, früh raus
3. Haiku-Call mit allen N Captions: „Welche Formulierungen, Wendungen oder Tonalitäts-Marker tauchen in mindestens 3 dieser Posts auf? Liste sie als JSON-Array. Nur Phrasen die typisch für die Inhaber-Stimme sind — keine generischen Sätze."
4. Output validieren, gegen existierende `style_anchors` deduplizieren (case-insensitive substring-Match)
5. Append in `voicedb_entries.style-anchors` mit Source-Marker `<!-- Source: implicit_learning N=10 2026-05-19 -->`
6. Optional: schwächere Anker als `latente_anker.md` separat ablegen (eine eigene voicedb_entry mit niedrigerer Prompt-Gewichtung)

### Akzeptanzkriterien

- [ ] Endpoint `/voice-learning/{tenant_id}` in `content/main.py` + `node_run_voice_learning` in `nodes.py`
- [ ] Haiku-Prompt `IMPLICIT_VOICE_LEARNING_PROMPT` in `prompts.py` mit Output-JSON-Schema
- [ ] Dedup-Helper gegen bestehende anchors
- [ ] Source-Tag-Konvention in `voicedb_entries` dokumentiert (auch retrospektiv ableitbar)
- [ ] Neuer n8n-Cron via `create_cron_workflows.py` Pattern (4. Sentinel-fähiger Cron analog zu den 3 bestehenden T:-Crons)
- [ ] E2E-Test: 10 grubis-weine Posts simulieren → Lern-Endpoint triggern → mindestens 2–3 neue Anker in style-anchors.md

### Was bewusst NICHT in Schritt 42 ist

- Live-Feedback aus Post-Approval-Korrekturen (= Bug 41.12, separat).
- Auto-Lern aus Analytics-Performance (Top-Performer-Phrasen extrahieren) — eigener Schritt, abhängig von Meta-API-Anbindung (Schritt 14).
- Persona-spezifisches Lernen (separat pro Persona statt aggregiert).

### Aufwand

- Endpoint + Node + Prompt: ~3h
- Cron-Workflow + Dedup-Logik: ~1.5h
- E2E + Polish: ~1.5h
- **Gesamt: ~1 Tag**

### Implementierung (2026-05-12 22:41 CEST)

- `content/prompts.py`: `IMPLICIT_VOICE_LEARNING_PROMPT` (Haiku, JSON-Output `latente_anker` + `begruendung`)
- `content/nodes.py`: `node_run_voice_learning(tenant_id, n_posts=10)` — standalone (kein State). DB-Query holt letzte N approved/published Posts ohne Versions (= ohne Revisionen). Mindest-Schwelle: 3 saubere Approvals, sonst frühes Skip.
- `content/main.py`: neuer Endpoint `POST /voice-learning` mit `{tenant_id, n_posts}` (AGENT_SECRET).
- Dedup gegen bestehende Anchors via `append_to_voicedb_entry` Helper (case-insensitive substring-Check).
- Source-Tag: `implicit_learning n=10 YYYY-MM-DD`.
- Cron-Workflow (n8n) wird im nächsten Tooling-Pass angelegt — Schedule `0 18 * * 0` (Sonntag 18:00 Berlin), kein Filter, ruft `/voice-learning` für jeden aktiven Tenant.

**E2E-Verifikation 2026-05-12 22:41 CEST:**
- Endpoint mit 4 frischen Grubi-Posts gerufen (alle status=approved, kein revision)
- Haiku extrahierte 3 latente Anker:
  - „Es bietet sich an / Es bietet sich einfach an"
  - „leicht, frisch"
  - „Mit welchem Wein startest du / ihr in den Abend/Sommerabend"
- Alle 3 in `voicedb_entries.style-anchors` appended mit Source-Tag `implicit_learning n=4 2026-05-12`.

**Offen:**
- [ ] n8n-Cron `T: Cron: Voice-Learning (Sonntag 18:00)` über `create_cron_workflows.py` anlegen. Pattern: Cron → load_tenants → split → POST `langgraph_content:8002/voice-learning` mit `{tenant_id, n_posts: 10}`. Wird im nächsten Cron-Pass mitgenommen.

---

## Diskussion — Schritt 38: WebAuthn / Passkeys als einheitlicher Login

**Status:** Offen zur Diskussion (vorgemerkt 2026-05-12)

**Hintergrund:** Schritt 37 hat den iOS-Cookie-Bug pragmatisch über einen Hybrid-Flow gelöst (Magic-Link für Android/Desktop, Code-Eingabe für iOS). UX wirkt aber zweigleisig — Mail enthält bewusst beide Wege, was bei einzelnen Usern Rückfragen produzieren kann.

**Idee:** Passkeys (WebAuthn) als einheitlicher zweiter Login-Faktor nach der Erst-Anmeldung.
- Erst-Login bleibt wie heute (Welcome-Mail mit Code/Magic-Link)
- Direkt im Onboarding-Wizard: Schritt "Login einfacher machen" → Passkey registrieren (Face-ID / Touch-ID / Fingerprint / Windows Hello)
- Ab dann: Login = ein Wisch über den Sensor, keine Mail, kein Code, kein Plattform-Unterschied
- Funktioniert in PWAs auf iOS 16.4+, Android 9+, alle modernen Desktop-Browser
- Löst das iOS-PWA-Cookie-Problem komplett, weil der Passkey hardware-gebunden ist — kein Browser-↔-PWA-Cookie-Roundtrip mehr

**Aufwand:** geschätzt 1–2 Tage
- Lib: `@simplewebauthn/server` (FastAPI) + `@simplewebauthn/browser` (PWA)
- DB: neue Tabelle `webauthn_credentials` (credential_id, public_key, counter, user_id)
- API: `POST /webauthn/register/options`, `POST /webauthn/register/verify`, `POST /webauthn/login/options`, `POST /webauthn/login/verify`
- NextAuth Custom-Provider, der gegen WebAuthn-Verify-Endpoint authentifiziert
- Onboarding-Wizard: neuer Step "Login schneller machen" mit Skip-Option

**Wann sinnvoll:**
- Sobald mehr als 5–10 echte User aktiv sind und der Code-Pfad sich abnutzt
- Vor allem wenn iOS-User dominieren — dort ist der Gewinn am größten
- Spätestens vor öffentlichem Launch oder Self-Service-Onboarding

**Risiken:**
- Browser-Support für Passkey-Roaming (Sync zwischen Geräten) noch uneinheitlich — User mit "passkeylose" Geräten brauchen weiterhin Mail-Fallback
- Initialer User-Education-Aufwand ("was ist ein Passkey?")
- iCloud Keychain / Google Password Manager als Speicher voraussetzt — User ohne aktivierte Sync verlieren Passkeys beim Geräte-Wechsel

**Alternative weiterhin auf dem Tisch:**
- Code-only für alle (verzicht auf Magic-Link) → konsistent, aber 1 Extra-Schritt für 90% der User
- Native iOS/Android-Apps mit Universal Links → Stripe-würdige UX, aber 2 Apps + Store-Submissions = mehrere Wochen Aufwand

**Nächster Schritt:** Architekt-Entscheidung nach Grubi-Erstkontakt: Code-Pfad in der Praxis tragbar oder muss Schritt 38 vorgezogen werden?

---

## Diskussion — Spracheingabe: Apple-Diktat statt Whisper prüfen

**Status:** Offen zur Prüfung (vorgemerkt 2026-05-12 nach Grubi-Live-Test)

**Beobachtung im Praxistest:** Grubi nutzt die native iOS-Spracheingabe (Mikrofon-Taste auf der Apple-Tastatur) zum Befüllen der Antwort-Felder. Die Qualität — vor allem bei Eigennamen, Weingütern, Fachvokabular — ist spürbar besser als das, was unser Whisper-Container aktuell ausspuckt. Auch ohne Tuning kommen perfekt formulierte deutsche Sätze direkt ins Textfeld.

**Aktueller PWA-Flow (Whisper):**
- `VoiceRecorder.tsx` öffnet MediaRecorder-Aufnahme im Browser
- Upload als `audio/webm`/`m4a` an `pwa-api /api/tenants/{tid}/voice/transcribe` (oder ähnlich)
- pwa-api routet an `internal_whisper`-Container (faster-whisper / whisper.cpp), Modell vermutlich `large-v3` oder `medium`
- Transkript zurück, ins Antwort-Feld

**Vorgeschlagene Alternative — Apple/Android-Diktat:**
- Antwort-Felder bleiben ganz normale `<textarea>` Felder
- iOS: User tippt die Mikrofon-Taste auf der eigenen Tastatur, Apple diktiert direkt rein
- Android: Gboard-Sprachsymbol, gleiches Prinzip
- Kein Audio-Upload, keine Whisper-Latenz, keine Server-Last
- Funktioniert offline (auf neueren iPhones läuft das Diktat on-device)

**Pros (wenn das Apple-Diktat reicht):**
- Qualität nach Korbinians Beobachtung deutlich besser
- Keine Whisper-Container-Wartung mehr nötig (RAM, GPU, Updates)
- Latenz quasi 0 — Echtzeit-Streaming ins Textfeld
- Mikrofon-Permission kommt vom OS, nicht vom Browser — weniger UX-Friction
- Spart Inference-Kosten / Strom

**Cons:**
- **Geräte-Abhängigkeit:** alte iPhones (< iOS 16) oder Low-End-Androids haben mieses Diktat oder gar keins. Whisper als Fallback unverzichtbar.
- **Datenschutz:** Apple sendet Diktat-Audio bei älteren Geräten an Apple-Server (außerhalb EU). On-Device-Diktat erst ab iPhone XS+ / iOS 16+. DSGVO-Bewertung nötig — Apple-AVV vs. unsere Whisper-Inhouse-Lösung.
- **Kein Audio-Archiv:** der Roh-Audio steht nicht mehr für spätere Re-Transkription (z.B. wenn ein anderes Modell besser wäre) zur Verfügung.
- **Voice-DB-Pipeline:** Onboarding-Audio-Upload (Korbinian → Whisper → Sonnet) bleibt davon **unberührt** — das ist Datei-Upload, kein Diktat. Nur die laufenden PWA-Interview-Antworten sind betroffen.

**Was zu prüfen ist:**
1. **A/B-Vergleich:** gleicher Text per Whisper vs. per Apple-Diktat von 3–5 echten Mandanten mitschneiden. Wo schlägt Whisper, wo Apple? (Insbesondere Fachvokabular, Eigennamen, lange Sätze, Hintergrundgeräusche.)
2. **DSGVO-Klärung:** wann sendet Apple Audio zu Apple-Servern, wann nicht? Reicht ein Hinweis in der Mandanten-Vereinbarung, oder muss Apple als Sub-Processor in AVV/DSFA aufgenommen werden?
3. **Fallback-UX:** wenn Diktat-Default umgestellt wird, muss es einen sichtbaren „Audio aufnehmen statt tippen"-Button geben (für ältere Geräte oder Voice-Note-Wunsch).
4. **Voice-DB-Konsequenz:** ohne Whisper-Transkripte fehlen die `authentic_phrases`-Extraktionen aus dem Antwort-Audio. Reichen die Text-Antworten als Quelle für Style-Anchors? Vermutlich ja, weil das Diktat die Eigenformulierung ja transparent rüberbringt.

**Optionen für die Entscheidung:**
- **(A) Whisper bleibt Default, Apple-Diktat als optionaler Mode:** „Diktiere lieber direkt ins Feld" als Hint unter dem Voice-Recorder. Nullaufwand, User entscheidet selbst.
- **(B) Apple-Diktat wird Default, Whisper als Fallback:** Voice-Recorder-Button verschwindet aus dem Default-Pfad, taucht nur auf wenn Diktat erkennbar nicht verfügbar ist (Feature-Detection via `webkitSpeechRecognition` o.ä.) oder wenn der User explizit Audio statt Diktat will.
- **(C) Whisper komplett raus:** nur wenn (B) sich praktisch bewährt und DSGVO-Klärung sauber ist — Whisper-Container kann dann stillgelegt werden, Ressourcen frei.

**Empfehlung für ersten Schritt:** Option (A) — kostet einen Hinweis-Text im Voice-Recorder und ist sofort lebensfähig. Erst nach 2–3 Mandanten-Wochen Datensammlung über tatsächliche Nutzung entscheiden, ob (B) oder (C) sich lohnt.

**Aufwand (Option A):** 30 Min. Hinweis-Text + ggf. kleine Re-Position des Voice-Recorder-Buttons.
**Aufwand (Option B):** 0.5–1 Tag. Feature-Detection, UI-Umbau, Fallback-Pfad sauber.
**Aufwand (Option C):** + 0.5 Tag Whisper-Container-Cleanup + Docs.

**Nächster Schritt:** A/B-Vergleich mit 1–2 Mandanten machen, dann entscheiden. Whisper-Container vorerst nicht abdrehen.

---

## Notizen für die nächste Session

**Wo wir stehen (Stand 2026-05-11):**
- Schritt 21 A–H ✅ vollständig live, Container rebuilt + healthy
- Schritt 17 ✅, Schritt 18 ✅, Schritt 15.1–15.5 ✅
- Schritt 22 ✅ PWA Kunden-App live, Login-Fix nachgezogen
- Schritt 23 (Daten-Migration Gitea → Postgres) ✅ vollständig: 23.1–23.7 abgeschlossen am 2026-05-11
- Schritt 19 (DSGVO) ⬜ – vor Go-Live
- Schritt 13.4, 14, 16 warten weiterhin

**Status BLOCKER aus Audit 2026-05-11:** B-1/B-2/B-6/B-7 + B-3/B-4/B-5 ✅ erledigt; B-1 als Defense-in-Depth verbaut.

**Nächste Aktion (priorisiert):**
1. **Schritt 24 (Telegram-Removal)** — PWA-Pendants bauen, Code säubern, Workflows umbauen. Kein User-flow funktioniert aktuell, alle Telegram-Crons deaktiviert.
2. **Schritt 25 (Git-Cleanup)** — strategie_defaults migrieren, dann tenant-registry + archive/* archivieren.
3. **SMA 19.1** LangSmith-Tracing deaktivieren (10 Min, Go-Live-Blocker) — DSGVO
4. SMA 19.4 n8n Execution-Purging (~30 Min) — DSGVO
5. ~~Verbleibende HOCH-Items aus FIX_PLAN_2026-05-11.md~~ — F1-R2 + F1-A1 + archetypes-Backfill erledigt 2026-05-11
6. Security HOCH-5 / HOCH-6 (siehe `04_Architektur/ROADMAP_security-hardening.md`)

**Was in 21.H implementiert wurde (final):**
- `_has_real_content()` — prüft ob VoiceDB-Datei echten Inhalt hat
- `_call_claude()` — Signatur erweitert: `model` + `max_tokens` als optionale Parameter
- `_generate_synthetic_demo()` — `voicedb_mds` + `gitea_repo`; Haiku-Destillations-Call; Gemini-Bilder am Ende
- `_load_demo_data()` — `voicedb_mds` weitergegeben
- `_generate_carousel_images(slide_content=)` — Gemini Imagen 3; Seed für Konsistenz; `_IMAGE_STYLE_ANCHORS` als geteilter Stil-Rahmen; Slide-Texte fließen in Prompts ein; Gitea-Cache
- `_DEMO_EMPTY` — 4 neue Felder `demo_carousel_img_0..3`
- `main.py` — `gemini_api_key` in `_inject_secrets()`
- `docker-compose.yml` — `gemini_api_key` Secret in langgraph-content + globalem secrets-Block
- Template — alle 4 Carousel-Slides: Bild-Background + dunkler Overlay, CSS-Gradient-Fallback

**Nächste Aktion:**
Test: `https://konzept.prisment.de/presentation/preview/grubis-weine` aufrufen
→ Beim ersten Aufruf ~30–60s Bildgenerierung, danach Gitea-Cache (sofort)

**docker-compose.yml Änderungen (2 Stellen):**

Unter `langgraph-content: → secrets:` (nach `telegram_chat_id`) ergänzen:
```yaml
      - gemini_api_key
```

Unter `secrets:` (globaler Block, am Ende vor letzter Zeile) ergänzen:
```yaml
  gemini_api_key:
    file: ./secrets/gemini_api_key
```

---

## ⬜ Späterer Punkt — RAG mit OpenWebUI hinterfragen

**Stand 2026-05-13:** Wir nutzen OpenWebUI als RAG-Backend (`search_voice_db` ruft `internal_open_webui:8080/api/chat/completions` mit Collection-ID). Funktioniert, ist aber Black-Box: keine Kontrolle über Embedding-Modell, Retrieval-Parameter, Reranking, Chunk-Größe. Plus: Cold-Start-Latenz (siehe Bug 41.1 Historie), Wartung eines extra Containers, Drittabhängigkeit.

**Zu prüfen:** eigenes leichtgewichtiges RAG-System bauen
- Embedding-Modell selbst wählen (z.B. multilingual-e5, BGE, Voyage)
- Vektor-Store: pgvector in customer_postgres (existiert schon) — null extra Container
- Chunking-Strategie auf unsere VoiceDB-Files (mensch/sprache-ton/stories/style-anchors) optimiert
- Volle Kontrolle über Retrieval + Reranking-Logik
- Eigene Latenz-Targets garantierbar

**Wann:** Nach Schritt 45 (Onboarding) und nach erstem echten Mandanten-Roll-out — wenn wir wirklich verstehen welche RAG-Queries kritisch sind. Vorher nicht — wäre verfrühte Optimierung.

**Aufwand:** ~2-3 Tage Erstaufbau + 1-2 Tage Tuning. Großer Gewinn bei Latenz + Output-Qualität.

---

## ⬜ Späterer Punkt — Error-Reporting + Security-Checks-Schedule

**Stand 2026-05-13:** Kein systematischer Error-Sammler. Errors stehen verstreut in Container-Logs — niemand sieht sie aktiv. Beim Auth-/Schema-Bug am 2026-05-13 (`relation "posts" does not exist`) fiel das erst im User-Test auf. Security-Härtung-Items aus `04_Architektur/ROADMAP_security-hardening.md` werden einmal angefasst und vergessen — keine Drift-Detection.

**Zu bauen:**

1. **Zentraler Error-Sammler**
   - Container-Logs (pwa-api, 4 LangGraph-Agents, n8n) in einen Stream
   - 500er + Tracebacks in ein Dashboard (Loki/Grafana ODER simpler Python-Sink mit Datei-Aggregation)
   - Push an Korbinian via pwa-api `/internal/notify` type=admin_alert bei kritischen Errors (Email-Fallback ist als `EMAIL_FALLBACK_TYPES` konfiguriert)

2. **Wiederkehrende Security-Checks**
   - LangSmith-Tracing-Status (sollte off bleiben — siehe 19.1)
   - n8n Execution-Aufbewahrungs-Frist (siehe 19.4)
   - Secret-Rotation-Erinnerungen
   - Gitea-Token-Lebensdauer
   - Foundation-Stack unverändert (Traefik darf nicht angefasst werden)
   - Push-Subscription-Tabelle bereinigen (abgelaufene VAPID-Subs)
   - Voice-DB-Source-Tag-Audit („welche Anker kommen aus welchen Quellen — Test-Tags rausfiltern")

3. **Implementierungs-Optionen**
   - **APScheduler in pwa-api** — analog zu bestehenden Cron-Jobs (n8n-Cron-Migration 2026-05-18). Vorteil: Python-nativ, läuft im selben Container wie die Notify-Schicht.
   - **Claude Code im Schedule-Modus** — Claude-Agent fährt 1× täglich los, prüft Akzeptanzkriterien, schreibt Bericht. Vorteil: kann tatsächlich Code lesen + bewerten. Nachteil: API-Cost + Tooling noch nicht erprobt.
   - **Python-Script + systemd-timer** — eigenes `security_audit.py` auf dem Host. Vorteil: simpel. Nachteil: Eigenpflege.

**Empfohlene Reihenfolge:**
1. APScheduler-Job in pwa-api für deterministische Checks (Tabellen-Größe, Token-Alter, etc.)
2. Daily-Report via pwa-api `/internal/notify` type=health_report (mit Email-Fallback)
3. Später optional: Claude-Code-Job für tiefere Analyse („neue 500er-Pattern seit gestern?")

**Wann:** nach Schritt 45 Onboarding + erstem Multi-Mandanten-Betrieb. Vorher nicht sinnvoll — bei einem Live-Tenant sehen wir Errors auch ohne Sammler.

**Aufwand:** ~1 Tag für APScheduler-Job + Daily-Notify. Claude-Code-Job zusätzlich ~0.5–1 Tag.

---

## ⬜ Architektur-Härtung — VOR MANDANT #2 (Audit 2026-05-13)

> Findings aus Architektur-Sweep nach der Tenant-ID-Migration. Stack ist solide, drei strukturelle Schulden + ein Schwarm Quick-Wins. **Alle Punkte unten müssen erledigt sein bevor wir den zweiten Mandanten onboarden** — danach werden sie linear teurer mit jedem neuen Tenant. Reihenfolge: Tests zuerst (gibt uns Sicherheitsnetz für alles andere), dann strukturelle Punkte, dann Quick-Wins.

### 🔴 A1. Test-Suite + CI

**Stand:** Keine pytest-Files, kein `tests/`-Verzeichnis, kein CI-Hook. Jede Änderung wird live an Grubi getestet.

**Warum kritisch:** Aktuell tragbar weil 1 Live-Tenant + Architekt im Loop. Ab Tenant #3 wird jeder Regressionsbug ein Brand mit Telefonanruf. Beispiel von heute (2026-05-13): der `relation "posts" does not exist`-Bug war ein 2-Zeilen-Fix, aber er ist überhaupt erst im UI-Klick aufgefallen — eine Smoke-Test-Suite hätte das beim Push abgefangen.

**Akzeptanzkriterien:**
- `tests/`-Verzeichnis in pwa-api + LangGraph-Agents
- Minimal: 10–15 Smoke-Tests
  - Auth: 401 ohne Session, 403 für fremden Tenant
  - Queue-Bucketing (heute/woche/geplant/zur_freigabe)
  - Approval-Flow (approve, finalize, manual-edit, skip)
  - Read-Endpoints (posts, topics, queue, plan/current)
  - Voice-Hook-Extraktion (Mock-LLM)
- Gitea-CI-Hook der `pytest` bei jedem Push laufen lässt
- Tests laufen gegen lokale Test-DB (nicht Production)

**Aufwand:** ~1 Woche initial, dann pro Feature mitwachsen.

### 🟢 A2. Row-Level-Security (RLS) auf agent_data — A2.1 LIVE

**Stand 2026-05-14:** A2.1 **auf Live promoted**. RLS-Policies sind auf 14 agent_data-Tabellen aktiviert und **wirken Test + Live** über den `tenant_app_user` (NOSUPERUSER, NOBYPASSRLS). pwa-api async-Pool-Endpoints (posts/plan/dashboard) routen Tenant-Daten durch `acquire_tenant(tenant_id)` mit `SET LOCAL app.current_tenant`. E2E-Smoke gegen Tenant A vs Tenant B (in Test-Stack): ✅ Read-Isolation, ✅ Write-Isolation, ✅ INSERT-mit-fremder-tenant_id wird mit `InsufficientPrivilegeError` blockiert. Live-Smoke gegen Grubis-Topics: ✅ 16 Topics sichtbar mit korrektem Tenant, ✅ 0 Topics mit Fake-Tenant.

**Live-Promote-Schritte (2026-05-14, alle erfolgreich):**
- Rollback-Image bereitlag (`pwa-api:rollback-1778709837` vom Phase-A+B-Promote)
- 14 Tabellen in Live `customer_postgres.agent_data` mit RLS + tenant_isolation-Policy versorgt
- `tenant_app_user` in Live angelegt + Grants (Default-Privileges-Block für künftige Tabellen mitgesetzt)
- Live-Smoke: Connection als tenant_app_user OK, RLS-Filter wirkt
- `docker compose up -d pwa-api` mit aktuellem `pwa-api:latest` (= A2-Code) — Container recreated, init-Pool sauber durch
- `/api/health` external = 200 OK
- Korbinians Test gegen `test.app.prisment.de`: Login, Heute-Page mit pulsierendem Workflow-Indikator (vorher latent broken!), Plan-Edit, Post-Detail, Manual-Post-Wizard alles grün

**Was umgesetzt ist (A2.1 — async-Pool-Pfad in pwa-api):**
- ✅ Policies auf: `posts, topics, sessions, voicedb_entries, editorial_plans, editorial_concepts, personas, tenant_configs, pending_jobs, hashtag_rotation, analytics_reports, boost_recommendations, performance_log, presentations`
- ✅ Policy-Logik: `USING/WITH CHECK (current_setting('app.current_tenant', true) IS NULL OR '' OR tenant_id::text = current_setting(...))` — erlaubt Cross-Tenant-Read für Admin/Cron (kein SET), erzwingt Isolation sobald SET LOCAL gesetzt ist
- ✅ Postgres-Rolle `tenant_app_user` (NOSUPERUSER NOBYPASSRLS) im Test-Stack angelegt + Grants auf alle 14 Tabellen
- ✅ `pwa-api/app/db.py`: dritter Pool `tenant_pool()` + `acquire_tenant(tenant_id)` context-manager der TX öffnet + `SET LOCAL app.current_tenant` per `set_config(..., true)` parametrisiert setzt
- ✅ `pwa-api/app/config.py`: `TENANT_APP_DB_URL` aus `AGENT_DB_URL` abgeleitet (gleicher Host, nur User getauscht — Live/Test konsistent ohne Extra-ENV)
- ✅ Routes migriert: `routes/posts.py`, `routes/plan.py`, `routes/dashboard.py` — alle `agent_pool()`/`pool()`-Aufrufe auf Tenant-Tabellen durch `acquire_tenant` ersetzt. Latente Bugs nebenbei mitgefixt (dashboard.py + posts.py nutzten falschen Pool für posts/topics → Try/Except hat es versteckt)
- ✅ E2E-Smoke mit zweitem Fake-Tenant: keine Cross-Reads, UPDATE-Mass attack betrifft nur eigene Rows, INSERT-mit-fremder-tenant_id blockiert

**Bewusst nicht migriert (bleiben auf pwa_user, kein RLS-Schutz — Korbinian-Freigabe „3 ja"):**
- `routes/admin.py` (eigener `_agent_pool` mit pwa_user — Admin braucht Cross-Tenant-View)
- `routes/team/notifications/push/me/internal.py` — nutzen `pool()` (pwa_app DB, RLS-frei)
- `routes/interview/assist/breaking/onboarding.py` — kein direkter async-DB-Zugriff

**A2.2 — db_sync.py tenant-aware in pwa-api (2026-05-14, LIVE):**
- ✅ Zweiter psycopg2-Pool `_tenant_pool` (tenant_app_user) ergänzt + Helper `_tenant_conn(tenant_id)` der `SET LOCAL app.current_tenant` per `set_config()` parametrisiert
- ✅ 34 Tenant-Funktionen in `db_sync.py` migriert: `read/write_editorial_plan`, `read/write_post`, `read_post_index`, `read_queue`, `read/write_pending_job`, `read/write_mandant_config`, `read/write_editorial_concept`, sessions, voicedb, performance_log, boost_recommendations, analytics_reports, presentations, `update_post_status`, `mark_post_*`, `update_topic_status`, `delete_pending_job`, `delete_wip_session`
- ✅ 5 Cross-Tenant-Funktionen bleiben auf `_conn()` (pwa_user, RLS-bypass) wie mit Korbinian abgestimmt: `read_tenant_registry`, `read_archetyp_templates`, `read_strategie_defaults`, `update_boost_recommendation_status` (kein tenant_id im Param, Cron-Pfad), `auto_archive_overdue_posts` (Multi-Tenant Cron-Sweep)
- ✅ Test-Smoke: Fake-Tenant kriegt überall leere Reads, Cross-INSERT-Attack mit fremder tenant_id wird mit `InsufficientPrivilege` blockiert
- ✅ Live-Smoke nach Promote: Grubis sieht 16 Topics + 18 Config-Keys, Fake-Tenant leer, Registry 2 Tenants
- ✅ Rollback-Stand verfügbar: `pwa-api:rollback-1778709837` (Phase-A+B, vor A2). RLS-Policies in DB würden bei Rollback nicht stören weil pwa_user (Superuser) den Bypass macht

**Agents bleiben unverändert:** Die 4 LangGraph-Agents haben jeweils ihre eigene Kopie `db_sync.py` mit dem alten Single-Pool-Modell auf `pwa_user`. Diese Datei in pwa-api ist seit A2.2 die einzige Tenant-aware-Variante. Wer künftig Code zwischen Agents und pwa-api kopiert: Diff prüfen, sonst geht der RLS-Schutz verloren.

**Wann Live-Promote A2.1+A2.2:** ✅ erledigt 2026-05-14. Korbinian-Test gegen `test.app.prisment.de` bestätigt keine funktionalen Regressionen (Login, Heute-Page mit pulsierendem Workflow-Indikator, Plan-Edit, Post-Detail, Manual-Post-Wizard alles grün).

### 🟢 A3. DB-Konsolidierung — LIVE (2026-05-14)

**Stand 2026-05-14:** Auf Test + Live promoted. Alle 9 next-auth-/pwa-Tabellen aus DB `pwa_app` umgezogen in DB `agent_data` unter Schema `auth`. Single DB für alle Verbindungen — Tenant-Daten in `public`, Auth/Pwa-Daten in `auth`.

**Was umgesetzt ist:**
- ✅ Schema `auth` in `agent_data` mit identischer DDL aus `pwa_app` (public→auth Rewrite via sed)
- ✅ 9 Tabellen migriert: users, sessions, accounts, verification_token, push_subscriptions, tenant_memberships, approval_audit, notifications, user_settings
- ✅ Daten via `pg_dump --data-only --inserts | sed` rüber — Row-Count-Parity bestätigt
- ✅ `ALTER DATABASE agent_data SET search_path = auth, public` — non-qualifizierte Tabellen-Refs landen automatisch im richtigen Schema
- ✅ Einziger Konflikt-Name `sessions` (next-auth Web vs Voice-Sessions in public) explizit `public.sessions` qualifiziert in `db_sync.py` + `admin.py`
- ✅ `config.py`: DATABASE_URL default = AGENT_DB_URL
- ✅ `db.py`: `pool()` direkt gegen agent_data, `agent_pool()` als Alias (Kompatibilität)
- ✅ `pwa-web/lib/secrets.ts`: DATABASE_URL-Default auf agent_data umgestellt
- ✅ `pwa-web/auth.ts`: replace("/pwa_app", "/agent_data")-Workaround entfernt — DB-URLs sind ab jetzt identisch
- ✅ `docker-compose.test.yml`: DATABASE_URL explizit auf agent_data
- ✅ Snapshots vor Migration: `/tmp/a3_live_snapshot_*/{pwa_app,agent_data}.dump`
- ✅ Live-Smoke: pwa-api findet users via auth-Schema, RLS-Pfad weiterhin funktional, Voice-Sessions in public eindeutig, externe /api/health + /auth/signin = 200

**Beibehalten (bewusst nicht entfernt):**
- Live `pwa_app` DB existiert noch — als Fallback, falls etwas nachläuft. Wenn nach 1–2 Wochen sauber: DROP DATABASE pwa_app.
- `pwa-api/app/db_sync.py` bleibt psycopg2-sync (in 4 Agents kopiert + zu großer Refactor sonst). Nur DSN zeigt jetzt auf agent_data.
- `routes/admin.py` hat weiterhin eigenen `_agent_pool` — funktional gleich, kein zwingender Refactor

**Rollback-Stand:** `pwa-api:rollback-1778709837` + `pwa-web:rollback-1778713864-pre-a3`. RLS-Policies + auth-Schema in DB bleiben bei Rollback unverändert (kein Schaden); `pwa_app` DB ist intakt.

**Aufwand-Bilanz:** Test + Live in einem Sitz erledigt (~45 Minuten) statt 2–3 Tagen. Hauptgrund: Schema-Separation statt DB-Konsolidierung — keine Daten-Re-Konsolidierung nötig, nur Schema-Move + search_path.

**Post-Promote-Bugfix (gleicher Tag, Korbinians Interview-Test):**
Erst nach Live-Test mit echtem Interview-Flow aufgefallen — der DB-Default `search_path = auth, public` (via `ALTER DATABASE`) wirkte auf alle Verbindungen, auch auf die 4 LangGraph-Agents (gleicher `pwa_user`). Folge: `FROM sessions` in Agent-Code landete auf `auth.sessions` (next-auth Web-Tabelle) statt `public.sessions` (Voice). Content-Agent gab `column "tenant_id" does not exist` zurück. **Fix:** DB-Default zurück auf Postgres-Standard, pwa-api setzt search_path pro Connection (`setup=` callback in asyncpg, `options="-c search_path=..."` in psycopg2/pg). Asyncpg-Spezialität: `init=` ist nur einmal pro Connection, `DISCARD ALL` beim Release setzt SET-Variablen zurück — daher zwingend `setup=` für pro-Acquire-Wiederholung. pwa-web auth.ts bekam `options: "-c search_path=auth,public"` am pg-Pool. Tenant-Pool (RLS) bekam explizit `search_path=public` für defense-in-depth. Live-Verifikation nach Fix: Interview-Antwort → Content-Agent generiert 3 Posts → `/internal/notify` läuft sauber durch → Push kommt an (mit Bundling für `post_pending`).

### 🟡 A4. Cron-Idempotency in n8n-Workflows

**Stand:** `T: Cron: Wöchentliches Interview`, `T: Cron: Monatlicher Zyklus`, `T: Cron: Täglicher Boost-Check` haben keinen `is-already-running`-Check. n8n-Restart oder Doppel-Trigger erzeugt Duplicate-Sessions.

**Warum wichtig:** `editorial_plans` hat UNIQUE auf `(tenant_id, target_month)` und schützt sich. `sessions` nicht — beim nächsten n8n-Crash sehen wir Daten-Müll.

**Akzeptanzkriterien:**
- Cron-Workflows starten mit `SELECT ... FROM sessions WHERE tenant_id=$1 AND status IN ('open','in_progress') AND created_at > NOW() - interval '24h'` und skippen wenn nicht leer
- UNIQUE-Constraint auf `sessions(tenant_id, mode, created_date)` als Sicherheitsnetz

**Aufwand:** 1–2 h.

### 🟡 A5. Pro-Caller Service-to-Service-Auth

**Stand:** Alle 4 Agents + n8n + pwa-api benutzen denselben `AGENT_SECRET` aus `/run/secrets/agent_secret`.

**Warum wichtig:** Bei Leak müssen 6 Container synchron rotiert werden. Kein Audit-Trail welcher Caller einen `/internal/*`-Endpoint genutzt hat.

**Akzeptanzkriterien:**
- Pro Caller eigenes Token: `agent_secret_interview`, `agent_secret_content`, `agent_secret_redaktionsplan`, `agent_secret_analytics`, `agent_secret_n8n`, `agent_secret_pwa_api`
- `validate_agent_secret`-Middleware prüft gegen erlaubte Set + loggt Caller-Identifier
- Rotation-Plan dokumentiert (Sekret-Rotation alle 90d)

**Aufwand:** 1 Tag.

### 🟡 A6. API-Versionierung auf `/api/v1/`

**Stand:** Endpoints unter `/api/tenants/...`, kein `/v1/`. `/admin` außerhalb des `/api/`-Präfix.

**Warum wichtig:** Erste Breaking-Change → keine Coexistenz-Strategie. Mobile-App-Update wird Strafarbeit.

**Akzeptanzkriterien:**
- pwa-api auf `/api/v1/...` umgezogen
- Alte Pfade als 301-Alias mindestens 1 Quartal halten
- `/admin` zu `/api/v1/admin/...` konsolidiert

**Aufwand:** 1–2 h.

### 🟡 A7. Fehlende Indizes

**Stand:** `posts.scheduled_for` und `approval_audit.created_at` haben keine Indizes.

**Warum wichtig:** Bei 100 Tenants × 30 Posts/Monat × 12 Monate = 36k Rows in `posts`. Publisher-Cron-Query auf `scheduled_for` wird Sequential Scan → spürbar langsam. Jetzt kostet der Index 10 Minuten, später eine Performance-Diagnose.

**Akzeptanzkriterien:**
```sql
CREATE INDEX idx_posts_scheduled ON posts(scheduled_for) WHERE status='approved';
CREATE INDEX idx_approval_audit_created ON approval_audit(tenant_id, created_at DESC);
```

**Aufwand:** 10 Min.

### 🟡 A8. PgBouncer + Pool-Konsolidierung

**Stand:** pwa-api hält parallel asyncpg-Pool *und* psycopg2 ThreadedPool auf agent_data (doppelter Footprint). Bei 50+ User × 4 Agents kommt man an `max_connections=100` ran.

**Warum wichtig:** Lineare Skalierung mit Tenants. Kein Akut-Schmerz, aber bei Tenant #20 fängt es an zu wackeln.

**Akzeptanzkriterien:**
- PgBouncer im Transaction-Mode vor customer_postgres
- Pwa-api auf 1 Pool-Typ (asyncpg) konsolidiert — kein Sync-Pool mehr
- Connection-Limits pro Service in pgbouncer.ini dokumentiert

**Aufwand:** 1–2 Tage. **Kann nach A3 entfallen** wenn DB-Konsolidierung den Sync-Pool eh eliminiert.

---

### Reihenfolge / Zeit-Budget

| # | Wann | Punkt | Aufwand |
|---|---|---|---|
| 1 | **Diese Woche** | A1 Test-Suite + CI (Grundstein) | 1 Woche |
| 2 | Direkt danach | A7 Indizes + A4 Cron-Idempotency + A6 API-Versionierung | 1 Tag |
| 3 | Nächste 2 Wochen | A2 RLS | 2–3 Tage |
| 4 | Danach | A3 DB-Konsolidierung | 2–3 Tage |
| 5 | Danach | A5 Per-Caller-Secrets | 1 Tag |
| 6 | Optional | A8 PgBouncer (nur falls Connection-Druck nach A3 bleibt) | 1–2 Tage |

**Total realistisch:** 2,5–3 Wochen konzentrierte Arbeit, vor Mandant #2.

---

## ⬜ Späterer Punkt — Flexibler Interview-Zeitplan (User-Steuerung)

**Stand 2026-05-13:** Interview läuft starr per Cron Montag 9:00. Keine User-Konfiguration, kein Skip, kein Vorziehen.

**Schmerz:** Realität ist nicht starr. User ist im Urlaub → Mail kommt trotzdem. User hat sonntags Zeit zum Vorarbeiten → Cron springt erst Montag an. User will am Wochenende ein zweites Interview für mehr Content → geht nicht.

**Zu bauen:**

1. **Konfigurierbarer Standard-Slot** in `user_settings`
   - Wochentag (Mo/Di/…/So) + Uhrzeit pro User
   - Quiet-Hours-Definition pro User (z.B. keine Push 22–8)
   - Cron iteriert über `user_settings` statt globalem Schedule

2. **„Pause"-Modus** im Profil
   - Toggle „Interview pausiert bis …" mit Datum
   - Cron skippt diese User im pausierten Zeitraum
   - Auto-Resume nach Pause-Ende mit Push-Notification

3. **Manuelles Interview-Trigger** in der PWA
   - Button „Jetzt Interview machen" auf Home (wenn kein aktives offen)
   - Triggert sofort ein Interview-Run für die aktuelle KW (oder die nächste falls schon voll)
   - Gleiche Logik wie der Cron, nur User-getriggert

4. **Mehrere Interviews pro Woche** (Power-Mode)
   - Wenn User schon 1 Interview gemacht hat, kann er ein zweites starten
   - Zweites Interview greift die Topics für die *nächste* KW
   - „Vorarbeiten"-Modus

**Akzeptanzkriterien:**
- User-Settings-Seite mit Schedule-Picker (Wochentag + Uhrzeit, Quiet-Hours, Pause-Date-Picker)
- Cron-Workflow „T: Cron: Wöchentliches Interview" iteriert nach `user_settings.schedule`, nicht mehr globaler Slot
- Button „Jetzt Interview starten" auf Home (sichtbar wenn kein active_workflow + keine offene Session)
- POST `/api/me/interview/trigger` als manueller Entrypoint

**Aufwand:** 1–2 Tage (1 für DB/Backend, 1 für UI). Frontend baut Korbinian-Stil mit Brick-Buttons.

**Wann:** nach den Architektur-Härtung-Items (A1–A8), aber **vor** Mandant #2 — denn jeder Mandant hat eigenen Rhythmus. Globaler Cron skaliert nicht.

---

## ⬜ Späterer Punkt — Posting-Mode UI-Toggle (manual vs. auto)

**Stand 2026-05-13:** Backend-Logik fertig (siehe `CONTENT_MIX_KONZEPT.md` + `compute_slots_from_archetyp` posting_mode-aware). Mode-Switch funktioniert via `POST /api/tenants/{id}/plan/posting-mode` mit `{mode: "manual"|"auto"}` und reorderet existierende Topics (kein Regenerate, keine LLM-Kosten). Aktuell nur API, **kein UI**.

**Was zu bauen ist (Profil-Seite):**

1. **Schalter „Auto-Posting" im Profil/Einstellungen**
   - Brick-Toggle mit zwei Zuständen: „Ich poste selbst" / „Automatisch posten"
   - Text drunter erklärt den Effekt:
     - Manual: „3 Tage/Woche (Mo/Mi/Fr), Instagram und Facebook gleichzeitig — weniger Aufwand"
     - Auto: „Optimale Verteilung über Di/Do für Instagram und Mi/Fr für Facebook"
   - On change → POST `/api/tenants/{id}/plan/posting-mode` mit `{mode}`
   - Bestätigungs-Hinweis „Plan wurde umgestellt — N Topics neu terminiert"

2. **Defaults**
   - Neue Tenants: `posting_mode = "manual"`
   - Wird beim Onboarding gefragt (siehe nächster Punkt)

**Aufwand:** ~1 h (1 Toggle-Komponente + Server-Call + Erfolgs-Toast).

**Wann:** nach Schritt 45 Onboarding-Workflow, oder vorher wenn Korbinian den Toggle für Test braucht.

---

## ⬜ Späterer Punkt — Onboarding-Wizard fragt Posting-Mode

**Stand 2026-05-13:** Beim Onboarding-Flow (Schritt 45) wird der Archetyp ermittelt. Posting-Mode wird aktuell hartcodiert auf `manual` gesetzt. Das ist OK als Default, aber sollte explizit gefragt werden, damit User von Anfang an wissen was sie kriegen.

**Erweiterung im Onboarding-Wizard:**

Zusätzliche Frage nach Archetyp-Ermittlung (oder am Ende des Setups):

> **Wie möchtest du posten?**
>
> ◯ **Ich poste selbst** — Mo/Mi/Fr, Instagram + Facebook am selben Tag
>   *3 Posting-Tage pro Woche, weniger Aufwand für dich*
>
> ◯ **Prisment macht das automatisch** — verteilt optimal über die Woche
>   *4 Tage pro Woche, professionelle Reichweiten-Optimierung*

Auswahl wird gespeichert in `tenant_configs.config.posting_mode`. Default vorausgewählt: **„Ich poste selbst"** (=manual).

**Aufwand:** 30 Min (eine Frage im Wizard-Step + DB-Write beim Submit).

**Wann:** als Teil von Schritt 45 Onboarding-Workflow.

---

## ⬜ Späterer Punkt — RAG-Endzustand: pgvector-Hybrid (Variante C)

**Stand 2026-05-16:** OpenWebUI als RAG ist Tech-Debt (siehe Schritt "OpenWebUI raus aus dem Content-Agent" — Variante A). Aktuell läuft die Voice-DB komplett im Prompt mit. Solange Post-Archiv pro Tenant <100 Posts → bleibt das optimal.

**Trigger für diesen Schritt:** Wenn ein Tenant >300 Posts mit authentischen Phrasen hat UND der Token-Overhead in der regie_brief-Generierung spürbar wird (Anthropic-Kosten oder Latenz).

### Architektur Variante C — Hybrid

1. **Statische Voice-DB** (mensch, sprache-ton, style-anchors, no-goes, personas) bleibt **komplett im Prompt** — ist immer relevant, klein, ändert sich selten. Kein RAG nötig.
2. **Post-Archiv-Phrasen** → `pgvector`-Index in derselben `agent_data` DB:
   - Extension: `CREATE EXTENSION vector;`
   - Spalte: `embedding vector(384)` auf `voice_phrases` (neue Tabelle) oder direkt auf `posts.regie_brief.authentic_phrases`
   - Embedding-Modell: **lokales** `intfloat/multilingual-e5-small` (384-dim, deutsch, ~20ms CPU)
   - Embedding-Container: kleiner FastAPI-Service oder als Lib im langgraph_content
3. **Query**: `SELECT phrase FROM voice_phrases WHERE tenant_id=$1 ORDER BY embedding <=> $2::vector LIMIT 10`
4. **Embedding-Pflege**: beim INSERT eines neuen Posts → Background-Task schreibt Embedding mit. Kein separater Sync-Job nötig.

### Was es eliminiert

- Keine externe Knowledge-Base mehr (kein OpenWebUI als RAG, keine Collection-IDs)
- Keine Ollama-Inferenz im RAG-Pfad
- Keine extra Datenbank — nutzt unser existierendes Postgres
- Embeddings sind synchron mit Posts (referentielle Integrität)

### Aufwand

- pgvector-Setup + Schema-Migration: 1 h
- Embedding-Container oder lokale Lib: 2-3 h
- Backfill-Script für bestehende Posts: 1 h
- Integration in `node_generate_regie_brief`: 1-2 h

**Total: ~1 Tag**, plus Test-Phase.

**Wann:** wenn Variante A spürbar Token-Druck erzeugt. Aktuell nicht absehbar.

---

## ⬜ Späterer Punkt — Repo Phase 3B: CI mit Gitea Actions

**Stand 2026-05-17:** Mono-Repo `prisment-platform` läuft mit Phase 1+2
(Mono-Repo + SHA-Tagging-Workflow + Pre-commit-Hook). Was noch fehlt: ein
automatischer Build- und Smoke-Test bei Push, damit kein kaputter Commit
unentdeckt bleibt.

**Was Phase 3B leisten würde:**
- Gitea-Actions-Workflow `.gitea/workflows/build.yml` auf jeden Push zu `main`
- Pro betroffenem Service (langgraph/content, langgraph/interview, ..., pwa/pwa-api, pwa/pwa-web):
  - `docker build` durchführen (kein Push, nur Validierung)
  - Bei Python-Services: `python -c "from app import nodes, tools, main"` als Smoke
  - Bei Node-Services (pwa-web): `npm run build` oder zumindest TypeScript-Check
- Bei Fail: Push-Author bekommt Notification
- Optional: Markdown-Lint für `*.md` im knowledge-base-Repo (Bonus)

**Voraussetzungen:**
- Gitea Actions muss aktiviert sein (`ENABLED=true` in `[actions]`-Sektion)
- Mindestens ein `act_runner` muss laufen (Docker-in-Docker oder dedizierter Host)
- Image-Build im Runner braucht `/var/run/docker.sock` Mount oder DinD

**Aufwand:** 3-4 h (Runner-Setup + Workflow-Files + Test-Iterationen).

**Trigger zum Aktivieren:** Wenn mehrere Personen pushen oder das System
Produktions-kritisch wird (heute beides nicht der Fall — Solo-Entwicklung,
manueller Build-Check beim Pushen reicht).

---

## ⬜ Späterer Punkt — Repo Phase 3C: Foundation-Stack als zweites Repo

**Stand 2026-05-17:** `/opt/infrastructure/foundation/` (Traefik + Foundation-
Services) ist nicht versioniert. Read-only für claude-deploy, root-owned.
Korbinian müsste das Repo-Setup selbst machen.

**Was Phase 3C leisten würde:**
- Neues Gitea-Repo `admin/prisment-foundation` (privat)
- `cd /opt/infrastructure/foundation && sudo git init` (root, weil dir root-owned ist)
- `.gitignore` für `data/`, Logs, Zertifikate (Let's-Encrypt-Live wird täglich rotiert)
- Initial-Commit: aktueller Stand
- Push an Gitea

**Was es bringt:**
- Komplettheit: alle Stack-Configs versioniert, restic ist nicht mehr die einzige Sicherung
- Audit-Trail bei Traefik-Config-Änderungen (selten, aber kritisch)
- Disaster-Recovery wird einfacher: Foundation-Stack ist mit `git clone` + `docker compose up` nachstellbar

**Was es nicht bringt:**
- Workflow-Vorteil ist gering — Foundation ändert sich extrem selten
- claude-deploy bleibt read-only — Code-Pflege bleibt manuell durch Korbinian

**Aufwand:** 1 h (Setup als root + `.gitignore` mit Care wegen Zertifikaten).

**Trigger zum Aktivieren:** Wenn Foundation-Config häufiger geändert wird
oder Disaster-Recovery-Audit eine vollständige Versionierung fordert.

---

## 🔄 Content-Bot Playbook-Integration (begonnen 2026-05-17)

Strategie-Session 2026-05-17 hat 5 Archetyp-Format-Playbooks geliefert. Integration in den Content-Bot in 7 Phasen.

**Spec & Implementation-Guide:** [`CONTENT_BOT_PLAYBOOK_INTEGRATION.md`](./CONTENT_BOT_PLAYBOOK_INTEGRATION.md) — Single Source of Truth, wird bei jedem PR mit-aktualisiert.

### Phasen-Status

- ✅ **Phase 0** — Playbook-Migration auf einheitliches `cta_logik`-Schema. Alle 5 Playbooks migriert (2026-05-17), Architekt-Freigabe nach Diff-Report.
- ✅ **PR 1** — DB-Schema `archetyp_playbooks` + Sync-Mechanismus. Tabelle in live+test angelegt, 5 Playbooks gesynct, Loader+Cache+Smoke-Test grün, live deployed (2026-05-17, prisment-platform `ce4db84`).
- ✅ **PR 2** — Selektor-Funktion + Komprimierungs-Helpers + Token-Budget-Tests. `app/playbook_selector.py` mit 3 Helper-Funktionen + Pflicht-Tests (8/8 grün), max 1844 Tokens über 153 Kombinationen, live deployed (2026-05-17, prisment-platform `98267c8`).
- ✅ **PR 3** — Hot-Path-Integration. `tenants.playbook_id` Spalte + Resolver (PR 3-A), `node_load_context` lädt Playbook, `node_set_active_topic` führt Selektor aus, `node_generate_regie_brief` rendert neue `ARCHETYP-FORMAT-KONTEXT`-Sektion + Caption-Pflicht-Korridor + Playbook-Emoji-Override. Max 1423 Tokens im echten Render. Grubis (`handwerk_produkt`) live deployed (2026-05-17, prisment-platform `5ef2fc4`).
- ✅ **PR 4** — `node_voice_check` deterministische Playbook-Checks: Caption-Korridor-Verletzung + No-Go-Substring-Check (case-insensitive, Karussell-Slides eingeschlossen). 5 neue Tests (17/17 grün). Säule-2-Element-Heuristik bewusst ausgelassen (im Code dokumentiert). Live deployed (2026-05-17, prisment-platform `3178fcc`).
- ✅ **PR 5** — `node_compliance_check` zweistufig: `extract_compliance_context()` liefert `archetyp_rechtsrahmen_text` (aus Playbook, aktuell nur Gesundheit) + aggregierte `verbotene_phrasen`-Blacklist (über alle 3 CTA-Stufen). COMPLIANCE_CHECK_PROMPT um 2 Platzhalter-Blöcke erweitert, Haiku bekommt zentralen Rechtsrahmen + Blacklist + mandant_config. 4 neue Tests (21/21 grün). Live deployed (2026-05-17, prisment-platform `4115301`).
- ✅ **PR 6** — Onboarding-Auto-Erkennung mit Haiku-Klassifier + Konfidenz-System. Migration 013 (`tenants.sub_modus + vertriebs_kanaele + requires_review`, `archetyp_classification_log` Audit-Tabelle). `archetyp_classifier.py` mit Konfidenz-gestuften Apply-Logik (hoch→setzen, mittel→setzen+review-flag, niedrig→skippen). 24h-Cache + Force-Override. `/internal/classify-archetyp` FastAPI-Endpoint. `onboarding_import.py` mit `--classify-archetyp`-Subcommand und Auto-Integration am Ende des Imports. `admin.list_tenants` liefert die neuen Felder. 10 Classifier-Tests (alle grün) + 2× echter Haiku-Call gegen Grubis (1. Iteration mit Prompt-Schwäche, 2. Iteration sauber: `handwerk_produkt + kurator + [online_versand, b2b_gastronomie]`, alle Konfidenz=hoch). Live deployed (2026-05-17, prisment-platform `f21dd80` + `bf4b1fa`).

**🎉 Playbook-Integration komplett.** Alle 6 PRs durch, Phase 0 inkl.
- ⬜ **PR 4** — `node_voice_check` deterministische Playbook-Checks
- ⬜ **PR 5** — `node_compliance_check` zweistufig (Playbook-Rechtsrahmen + mandant_config)
- ⬜ **PR 6** — Onboarding setzt `sub_modus` + `vertriebs_kanaele` (inkl. Auto-Erkennung)

### Aktueller Schritt — Detail

**Playbook-Integration komplett**. Nächste Schritte sind Test/Validierung im echten Test-Stack-Run mit Grubis und nachgelagert das pausierte Schritt 45 Onboarding (Notiz: das war pausiert in Anhängigkeit von diesem Playbook-Refactor — jetzt kann es weitergehen).

Was an Admin-UI noch fehlt (separates Ticket): Frontend zeigt `requires_review`-Flag + die Klassifizierungs-Felder. Backend liefert die Daten bereits (`/admin/tenants` Endpoint).

### Notizen für die nächste Session

- PR 1 Stand (alles live): 5 Playbooks in `customer_postgres.public.archetyp_playbooks` + `customer_postgres_test.public.archetyp_playbooks` synchronisiert. Loader `load_archetyp_playbook(archetyp_id)` in `tools.py` mit Request-Scope-Cache.
- PR 2 Stand (alles live): `app/playbook_selector.py` mit Selektor + 3 Helpern. Test-Suite unter `tests/test_playbook_selector.py` (8 Tests, 153 Kombinationen geprüft, max 1844 Tokens).
- PR 3 Stand (alles live): Migration 012 mit `tenants.playbook_id`, Resolver mit Legacy-Fallback, `node_load_context` + `node_set_active_topic` + neue Prompt-Sektion. Live für Grubis = `handwerk_produkt`. Integration-Smoke grün.
- Sync-Script: `/opt/infrastructure/environment_a/scripts/sync_playbooks.py --both` für Re-Sync nach Playbook-Updates.
- Bekannte Lücke "B2B-Beispiele Handwerk-Produkt kalt/heiss" ist in Spec-Doku notiert (Erweiterung bei erstem B2B-Mandant)
- PR 6 Onboarding-Auto-Erkennung: Mechanik (heuristisch vs. Haiku) wird in PR 6 entschieden
- **Anpassung Klasse C in Spec §5:** `agent_anweisungen_content` ist `list[str]` (6-8 Bullets), nicht `str` wie ursprünglich geschätzt
- **Schritte-Schema in `post_aufbau_saeule_2`:** Felder `{nr, element, laenge, zweck}` (Selektor + Renderer beachten alle)
