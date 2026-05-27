---
typ: backlog_seed
titel: "Pending-Kernel-Whiptail-Dialog bei -updates-Apply beobachten/unterdrücken"
geltungsbereich: plattform
risikoklasse: sicher
status: offen
klasse: wartung
zugkraft: irgendwann
mission:         ""
stufe: schritt
beruehrt: ["scripts/"]
stand: 2026-05-24
erzeugt_durch: PLAT-001 (Block 1 Apply-Bündel)
---

## Anlass

Bei jedem `-updates`-Apply in PLAT-001 (vor dem Block-2-Reboot) erschien ein
debconf-Whiptail-Dialog „pending kernel restart" während des
`apt-get install`-Laufs. Hat den Apply nicht blockiert (env_keep für
`DEBIAN_FRONTEND=noninteractive` + `NEEDRESTART_MODE=a` greift via
`sudoers-Drop-In claude-deploy-apt-env`), aber **erzeugt Rauschen** in den
Logs und ist genau die Klasse von „still-eingeklemmten" Dialog-Pfaden, die
nachts unbeaufsichtigt zur stillen Hängen-Falle werden können.

## Erwartung nach dem Block-2-Reboot

Nach dem Reboot auf `6.8.0-117-generic` ist `/var/run/reboot-required` weg,
und damit sollte der Dialog beim nächsten `-updates`-Apply **nicht mehr**
auftauchen.

## Soll-Zustand

- **Erst beobachten:** Beim nächsten `-updates`-Apply (betreut, da 1b
  noch nicht autonom) prüfen, ob der Whiptail-Dialog erneut auftaucht.
- **Falls ja:** gezielter Suppress-Mechanismus im nightly-Apply-Pfad
  (z. B. `DEBCONF_NONINTERACTIVE_SEEN=true` zusätzlich zu
  `DEBIAN_FRONTEND=noninteractive`, oder `apt-listchanges` deaktivieren
  in der Apply-Umgebung).
- **Falls nein:** Seed schließen, Beobachtung als „durch Reboot erledigt"
  protokollieren.

## Trigger / Abhängigkeit

- Niedrig priorisiert, reine Beobachtung. Trigger ist der nächste
  `-updates`-Apply (Architekt-betreut, bis PLAT-002 1b scharf schaltet).

## Offene Fragen für die spätere Spec (falls nötig)

- Welche debconf-Env-Variable ist der saubere Off-Switch für genau diesen
  Dialog (nicht generisch alle Dialoge unterdrücken, sondern gezielt)?
- Verhältnis zu `needrestart`-Konfiguration: könnte der Pending-Kernel-
  Dialog ein needrestart-Hook sein, der über `NEEDRESTART_MODE=a` nicht
  vollständig ausgeschaltet ist?
