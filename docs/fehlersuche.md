# Wenn etwas klemmt

> **Hier steht:** Symptom, Ursache, Abhilfe.
> **Hier steht nicht:** Warum das Gerät so gebaut ist — das steht im
> [FAQ](faq.md).

Jeder Punkt hier hat beim Bauen einmal einen halben Abend gekostet.

**Die wichtigste Regel:** Erst den Bootlog holen, dann eine Ursache benennen.
Am 18.09.2026 wurden vier Ursachen nacheinander geraten und verworfen — die
echte stand die ganze Zeit im Log.

---

## Das Board meldet sich nicht am USB

**Meistens schläft es einfach.** Im Tiefschlaf schaltet der XIAO seine
USB-Einheit ab, der Port verschwindet vom Rechner. Das ist kein Defekt.

Prüfen, ob überhaupt etwas da ist:

```bash
ioreg -p IOUSB -l -w 0 | grep '"USB Product Name"'
```

Steht dort `USB JTAG_serial debug unit`, ist das Board da.

**Drück kurz RESET**, dann taucht der Port für ein paar Sekunden auf. Das
Fenster ist eng — zum Flashen reicht es nicht, dafür gibt es den Notausgang
weiter unten.

> Hängt ein **Labornetzteil** an der Akkuschiene, löst das Einstecken von USB
> keinen Reset aus. Das Board bleibt im Tiefschlaf und USB bleibt aus. Erst
> das Netzteil abschalten.

---

## Der Notausgang: BOOT halten, RESET tippen

Wenn über Funk nichts mehr geht:

1. **USB bleibt stecken.**
2. **BOOT** gedrückt halten
3. **RESET** kurz drücken und loslassen, BOOT weiter halten
4. eine Sekunde warten
5. **BOOT** loslassen

Das Board hängt jetzt still im Bootloader. Der Port bleibt stehen, statt nach
Sekunden zu verschwinden — daran erkennst du, dass es geklappt hat.

Dann flashen:

```bash
ls /dev/cu.usbmodem*
esphome upload zisterne.yaml --device /dev/cu.usbmodemXXXX
```

> **Die naheliegende Reihenfolge funktioniert nicht.** „Strom weg, BOOT halten,
> USB anstecken" ist der übliche Weg bei ESP32-Boards — hier aber nicht. Mit
> **angelötetem Akku** wird der Chip nie stromlos: USB abziehen nimmt ihm den
> Strom nicht, und der Trick „BOOT halten beim Einschalten" läuft ins Leere.
> Nimm **BOOT + RESET**.

**Nach dem Einbau in den Schacht fällt dieser Weg weg.** Deshalb muss Test 10
aus dem [Prüfplan](pruefplan.md) dreimal durchlaufen, bevor die Box zugeht.

---

## Nach dem Flashen passiert nichts: keine LED, kein Log, kein WLAN

Du hast beim Einstecken **BOOT gehalten**. Dann sitzt das Board im
ROM-Bootloader und startet deine Firmware gar nicht.

Der Reset, den das Flash-Werkzeug danach auslöst, holt es da **nicht
zuverlässig** heraus. Ein Reset über die Steuerleitungen von Hand auch nicht.

**Hilft:** USB abziehen, drei Sekunden warten, wieder anstecken. Ohne BOOT.

---

## Das Board loggt nichts über USB

Das ist Absicht. Die Konfiguration hat die serielle Ausgabe **komplett
abgeschaltet**, das spart Strom. Du siehst am USB gar nichts — auch keinen
Absturz.

Zum Debuggen im `logger:`-Block die Zeile mit der Baudrate auskommentieren und
die Stufe auf `DEBUG` stellen. Danach wieder zurückstellen.

> **Über die Funk-Verbindung kommen nicht alle Zeilen an.** Die Meldungen aus
> dem Startablauf fehlen dort regelmäßig, und während eines Updates gehen auch
> Fortschrittsmeldungen verloren — der Puffer ist klein und das Gerät
> beschäftigt. **Wenn es auf den genauen Ablauf ankommt, hol den Log über
> USB**, nicht über die API. Das geht nur, solange das Board erreichbar ist.

---

## Der ESP findet den ADS1115 nicht

Meist fehlen die Pull-up-Widerstände am I²C-Bus. Der Fehler sieht aus wie ein
Verdrahtungsfehler, ist aber keiner. Wie man das prüft, steht im
[FAQ](faq.md).

Zweithäufigste Ursache: **SCL und SDA vertauscht.** Beim SHT41-Modul steht
`SCL` vor `SDA` — umgekehrt zur gewohnten Sprechweise. Wer nach Gefühl lötet,
vertauscht die beiden.

---

## Der Build bricht ab: „No version is set for shim: uv"

Ab ESPHome 2026.9 läuft der Build direkt über ESP-IDF, und dafür muss `uv` im
Suchpfad liegen. Ist `uv` über einen Versionsmanager installiert, aber ohne
gesetzte Version, bricht der Build ab.

Behoben mit:

```bash
mise use -g uv@<version>
```

---

## Das Update über Funk kommt nicht durch

**Das Gerät ist nur rund neun Sekunden pro Stunde erreichbar.** In dieses
Fenster passt kein Update.

Der richtige Weg: Helfer `wach halten` auf **AN**, dann das nächste Aufwachen
abwarten. Danach bleibt das Gerät bis zu 30 Minuten wach. Ablauf in
[`betrieb.md`](betrieb.md).

**Bricht ein Update trotzdem ab**, war es meist zu früh — im Handshake, bevor
das Schreiben begonnen hat. Das kostet einen Versuch, aber kein Gerät: Es ist
noch kein Byte geschrieben. Einfach nochmal.

---

## Der Pegel zeigt 0,00 m

Der Strom durch die Sonde liegt bei 4 mA oder darunter. Zwei mögliche
Ursachen, und die Anzeige kann sie nicht unterscheiden:

| | |
|---|---|
| **gar kein Strom** | Sonde abgeklemmt, Step-Up kommt nicht hoch, Bürdewiderstand abgerissen |
| **genau 4 mA** | Sonde hat Strom, ist aber trocken oder nicht eingeschwungen |

Zum Trennen hilft der Rohwert: Ist die Spannung am Bürdewiderstand rund 0 V,
fehlt der Strom. Liegt sie bei 0,4 V, fließen genau 4 mA und die Sonde meldet
„leer".

---

## Der Akku zeigt zu wenig an

Der Umrechnungsfaktor für den Spannungsteiler ist ein Nennwert. Zwei
Widerstände mit je 1 % Toleranz können zusammen 2 % danebenliegen — bei 4,1 V
sind das 80 mV.

**Gegen ein Multimeter kalibrieren.** Ablauf in
[`inbetriebnahme.md`](inbetriebnahme.md).

Nach jedem Tausch der Teilerwiderstände neu kalibrieren.

---

## Nie gleichzeitig Labornetzteil und USB

Der Lade-IC im XIAO schiebt sonst Strom in das Netzteil zurück. Netzteile
können keinen Strom aufnehmen, also läuft ihre Spannung hoch — bis etwas
kaputtgeht.

Mit einem **echten Akku** ist USB gleichzeitig dagegen normal. Dann lädt er.

Ausführlich in [`../SICHERHEIT.md`](../SICHERHEIT.md).
