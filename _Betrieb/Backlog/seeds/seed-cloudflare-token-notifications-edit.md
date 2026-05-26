---
typ: backlog_seed
titel: "Cloudflare-API-Token: Notifications-Edit-Permission ergänzen (Token-Hygiene)"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: prozess
zugkraft: irgendwann
stufe: schritt
beruehrt: ["/opt/infrastructure/environment_a/secrets/cloudflare_api_token", "Cloudflare Dashboard My Profile → API Tokens"]
stand: 2026-05-26
erzeugt_durch: "Schritt-Versuch 2026-05-26: Cloudflare Maintenance Notifications disablen — API antwortete 10405 Method not allowed, Token-Scope reicht nur fürs Lesen."
---

## Anlass

Beim Versuch, die `maintenance_event_notification`-Policy via API zu
disablen, lieferte CF `code: 10405 Method not allowed for this
authentication scheme`. Das Token in
`/opt/infrastructure/environment_a/secrets/cloudflare_api_token` kann
Notification-Policies **lesen**, aber nicht **editieren**. Der konkrete
Schritt (Maintenance-Notifications aus) ist vom Architekt im
Dashboard manuell erledigt worden — die Token-Lücke bleibt.

## Soll-Zustand

Das Token bekommt zusätzlich `Account · Notifications · Edit`, damit
zukünftige Notification-Mutationen (an/aus, Threshold-Änderungen,
neue Policies) per API laufen können — ohne Dashboard-Klick und ohne
Throwaway-Token-Übergabe im Chat.

**Pfad:** `dash.cloudflare.com` → My Profile → API Tokens → bestehendes
Token (id `274b020ee05974b345d1bfb7ffc2fdd1`) → Edit → Permission
`Account · Notifications · Edit` hinzufügen → Continue + Save. Token-
Wert bleibt gleich, kein Re-Deploy nötig.

**Verifikation nach Edit:**
```
CF_TOKEN=$(cat /opt/infrastructure/environment_a/secrets/cloudflare_api_token)
ACC=194105753ab7bf42b3e3a741ecab1557
curl -s -X PATCH -H "Authorization: Bearer $CF_TOKEN" \
  -H "Content-Type: application/json" --data '{"enabled":true}' \
  "https://api.cloudflare.com/client/v4/accounts/$ACC/alerting/v3/policies/<irgendeine-id>"
```
sollte `success:true` liefern (statt 10405).

## Stufe / Risiko

**Schritt** — eine Permission-Erweiterung in der CF-Web-Oberfläche,
reversibel (Permission wieder entfernen). Nur lesendes Mehr-Recht
plus PATCH/PUT auf Notifications, keine Außenwirkung auf
DNS/WAF/Tunnel/Zone.

## Trigger

Irgendwann — kein konkreter Druck. Auslöser könnte sein: nächste
Notification-Mutation, die per API laufen soll (z.B.
Threshold-Anpassung bei DDoS-Alerts, neue Policy für ein Ereignis).
Bis dahin ist Dashboard-Klick + Schritt-Log-Eintrag der Workaround.

## Hinweis (Token-Hygiene generell)

Tokens werden im Dashboard mit minimalem Scope angelegt und im Lauf
der Zeit oft nicht mitgepflegt, wenn neue Aktionen dazukommen. Eine
periodische Token-Scope-Inventur (welches Token kann was?) wäre ein
eigener Sprung-Seed, ist hier aber nicht Teil.
