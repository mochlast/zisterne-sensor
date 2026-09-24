# Zisternen-Sensor

Füllstandsmessung für eine Betonzisterne. XIAO ESP32C6 mit ESPHome, auf Akku,
per WLAN an Home Assistant. Ein Messwert pro Stunde, dazwischen Tiefschlaf.

**Der Einstieg ist `README.md`.** Von dort führt eine Tabelle durch die Doku
unter `docs/`. Jede Datei dort beginnt mit „Hier steht / Hier steht nicht" —
das sagt, wo ein Inhalt hingehört.

## Drei Dinge, die Schaden anrichten

- **Labornetzteil und USB nie gleichzeitig am Board.** Der Lade-IC schiebt
  Strom ins Netzteil zurück, das kann nicht sinken, die Spannung läuft hoch.
  Ein echter LiPo zusammen mit USB ist dagegen normal.
- **ESPHome mindestens 2026.8.0.** Ältere Versionen rollen jedes OTA nach dem
  nächsten Aufwachen zurück. Geprüft mit `esphome version`.
- **Nach dem Einbau gibt es keinen BOOT-Knopf mehr.** Jedes Update muss über
  Home Assistant und WLAN laufen. Siehe `docs/betrieb.md`, Abschnitt
  „Firmware aktualisieren".

Alles Weitere zur Sicherheit steht in `SICHERHEIT.md`.

## Das Gerät ist fast immer offline

Rund **9 Sekunden pro Stunde** erreichbar. Ein fehlgeschlagener Ping ist
deshalb kein Befund — eng pollen oder Home Assistant fragen.

Home Assistant hängt am MCP-Server `home-assistant`. Damit lassen sich
Zustände, Verlauf, Automationen und Dashboard direkt lesen und schreiben.

## Erst messen, dann vorschlagen

Bei der Fehlersuche zählt der Befund, nicht die Vermutung. Bei diesem Board
heißt das: **Bootlog über USB holen**, bevor eine Ursache benannt wird. Am
18.09. kosteten vier nacheinander geratene Ursachen einen halben Abend; die
echte Ursache war eine veraltete ESPHome-Version.

`zisterne.yaml` hat `baud_rate: 0` — zum Debuggen auskommentieren und
`level: DEBUG` setzen, sonst schweigt das Board am USB.

## Beim Schreiben an der Doku

- **Eine Quelle je Fakt.** Preise und Kennwerte nur in `docs/teileliste.md`,
  Pins und Bauteilwerte nur in `docs/verdrahtung.md`, Messwerte mit Datum nur
  in `docs/messungen.md`, Prüfstatus nur in `docs/pruefplan.md`, Begründungen
  nur in `docs/faq.md`.
- **Im README keine Preise, keine Pin-Nummern, kein YAML** außer den drei
  Schnellstart-Zeilen.
- **Nichts Privates.** Kein WLAN-Name, keine IP aus dem Heimnetz, keine
  Klarnamen, keine lokalen Pfade.
