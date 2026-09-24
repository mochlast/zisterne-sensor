# Verdrahtung

> **Hier steht:** Was wohin gehört — Schaltplan, Pins, Bauteilwerte, Sternpunkt.
> **Hier steht nicht:** Warum ein Bauteil so gewählt wurde ([FAQ](faq.md)), in
> welcher Reihenfolge gelötet wird ([`bauanleitung.html`](bauanleitung.html))
> und wo die fertige Box hinkommt ([`einbau.md`](einbau.md)).

Board: **Seeed Studio XIAO ESP32C6**

---

## Schaltplan

**Gezeichnet:** [auf Circuit Canvas ansehen](https://circuitcanvas.com/p/m6995ric96ytq5sfibu)

Darunter dieselbe Schaltung als Text. Die Netzliste ist die Quelle, gegen die
sich Tippfehler prüfen lassen.

```
LiPo ── JST-Stecker ── JST-Buchse auf der Platine
                       ├── rot     ── VBAT-Schiene
                       └── schwarz ── GND-Schiene

VBAT-Schiene ── JST-Stecker ── Kabel ── XIAO Pad "B+"  (Unterseite, neben D5)
GND-Schiene  ── (gleiches Kabel) ───── XIAO Pad "B−"   (Unterseite, neben D8)

VBAT-Schiene ── AO3401 Source (P-Kanal)
             AO3401 Gate ──┬── 100k ── VBAT-Schiene
                           └── 2N7000 Drain
                               2N7000 Gate ──┬── 10k ── GND
                                             └── XIAO D0 (GPIO0)
                               2N7000 Source ── GND
             AO3401 Drain ── Pololu VIN
                             Pololu GND ── GND

Pololu VOUT (15 V fest) ── Sonde braun (+)
Sonde blau (−) ────────┬── 100 Ω 0,1 % ── Sternpunkt ── GND
                       └── 2 kΩ ── ADS1115 A0

ADS1115 VDD ── XIAO 3V3       ADS1115 SDA ── XIAO D9  (GPIO20)
ADS1115 GND ── Sternpunkt     ADS1115 SCL ── XIAO D10 (GPIO18)
ADS1115 ADDR ── GND  (Adresse 0x48)

SHT4x-Modul (4-polig), Pins von links nach rechts:

   Pin 1  VIN ── XIAO 3V3      (meist kein Regler auf dem Modul! max. 3,6 V)
   Pin 2  GND ── GND-Schiene   (NICHT an den Sternpunkt)
   Pin 3  SCL ── XIAO D10 (GPIO18)
   Pin 4  SDA ── XIAO D9  (GPIO20)

   ACHTUNG: SCL steht VOR SDA. Umgekehrt zur gewohnten Sprechweise.
   Die Pinreihenfolge kann je nach Breakout abweichen — Aufdruck lesen.
   Feste Adresse 0x44, es gibt nichts einzustellen.
   Alle vier Leitungen direkt vom XIAO abgreifen, nicht vom ADS1115
   durchschleifen.

Akku-Messung:
VBAT-Schiene ── 220k ──┬── ADS1115 A1
                       └── 220k ── XIAO D5 (GPIO23)  (LOW nur beim Messen)

Antenne: u.FL-Stecker auf die Buchse am XIAO.

Kapillare des Sondenkabels: offen in der Box enden lassen.
Box hat ein Druckausgleichselement + Silica-Gel.
```

Nicht im Plan, weil im Modul verbaut: die **Antennenumschaltung** im XIAO
(GPIO3 / GPIO14) und die **Schutzplatine** am LiPo. Die Abblockkondensatoren
sitzen auf den Breakout-Modulen.

---

## Pinbelegung XIAO ESP32C6

| Label | GPIO | Funktion |
|---|---|---|
| D0 | GPIO0 | Sonde an/aus (2N7000-Gate) |
| D5 | GPIO23 | Akku-Spannungsteiler an/aus |
| D9 | GPIO20 | I²C SDA |
| D10 | GPIO18 | I²C SCL |
| — | GPIO3 | RF-Switch scharf schalten (**LOW = an**), intern |
| — | GPIO14 | Antennenwahl (**HIGH = externe u.FL**), intern |
| — | GPIO15 | gelbe User-LED, intern. Ist ein Strapping-Pin — ESPHome warnt, das ist normal. |

Im Tiefschlaf werden alle Pins hochohmig. Damit sind Sonde, Spannungsteiler
und RF-Switch automatisch aus. Das ist gewollt.

### Die Antennenumschaltung

GPIO3 und GPIO14 sind nicht herausgeführt. ESPHome schaltet sie trotzdem — im
`on_boot`-Block mit Priorität 700, also nach den GPIO-Ausgängen und vor dem
WLAN.

**Diese Umschaltung fehlt leicht, und der Fehler ist unsichtbar.** Ohne sie
funkt der XIAO über seine interne Keramikantenne statt über die u.FL-Buchse.
Das Gerät läuft trotzdem, nur der RSSI ist dann falsch — und damit jede
Entscheidung, die darauf beruht.

| Datei | schaltet um? |
|---|---|
| `zisterne.yaml` | **ja** |
| `werkzeuge/wlan-test.yaml` | nein — für irgendein ESP32-Board gedacht, nicht für den XIAO |
| `werkzeuge/schlaftest.yaml` | nein, braucht kein brauchbares WLAN |

**Miss den RSSI im Schacht mit `zisterne.yaml`**, nicht mit dem Werkzeug auf
dem XIAO. Was die Umschaltung bringt, ist gemessen:
[`messungen.md`](messungen.md).

### Freie Pins

Frei bleiben **D1, D2, D3, D4, D6, D7, D8**.

**D6 und D7 nicht belegen.** Das sind `GPIO16` und `GPIO17`, also UART0 TX und
RX. Im Normalbetrieb werden sie nicht gebraucht — Flashen und Loggen laufen
beim ESP32-C6 über USB Serial/JTAG (`GPIO12`/`GPIO13`), und `zisterne.yaml`
setzt `baud_rate: 0`. Sie sind die Reserve für zwei Fälle:

1. USB Serial/JTAG hängt sich auf — dann ist UART0 der Notausgang.
2. Plan B „LoRa" (siehe `werkzeuge/wlan-test.yaml`) — manche Module sprechen
   UART.

---

## I²C-Bus

Am Bus hängen zwei Teilnehmer. Die Adressen kollidieren nicht:

| Bauteil | Adresse | Einstellbar? |
|---|---|---|
| ADS1115 | `0x48` | ja, über `ADDR`. `ADDR` an GND ergibt `0x48`. |
| SHT4x | `0x44` | nein, fest im Chip |

Beide hängen an `D9` (SDA) und `D10` (SCL) und werden mit 3,3 V versorgt.
Viele SHT4x-Module haben **keinen Spannungsregler** — `VIN` geht direkt an den
Chip. Der verträgt maximal 3,6 V. Nie an `5V` hängen.

`zisterne.yaml` hat `scan: true` — im Log müssen beide Adressen auftauchen.

### Pull-ups prüfen

Der I²C-Bus braucht zwei Widerstände, die SDA und SCL auf 3,3 V hochziehen.
Die meisten Module haben sie schon drauf, aber nicht alle.

Prüfen mit dem Multimeter (Widerstandsbereich): zwischen **VDD und SDA** sowie
zwischen **VDD und SCL** sollten je ca. **4,7 bis 10 kΩ** messbar sein.

Misst du dort „unendlich", löte je einen **4,7-kΩ-Widerstand** von SDA nach
3V3 und von SCL nach 3V3.

**Zwei Module, zwei Sätze Pull-ups.** Haben ADS1115 und SHT4x beide welche,
liegen sie parallel:

| Auf den Modulen | Zusammen | Bewertung |
|---|---|---|
| 2 × 10 kΩ | 5 kΩ | gut |
| 2 × 4,7 kΩ | 2,35 kΩ | noch in Ordnung |
| 2 × 2,2 kΩ | 1,1 kΩ | **zu wenig** — der Bus zieht 3 mA |

Miss im Zweifel `VDD` gegen `SDA` bei gestecktem Sensor nach: **unter 2 kΩ ist
zu wenig.** Dann die Pull-ups auf einem der beiden Module auslöten.

Wie man am Modul überhaupt erkennt, ob Pull-ups drauf sind und welchen Wert
sie haben: [FAQ](faq.md).

---

## Der Sternpunkt

**Der größte Fehler in dieser Messkette ist nicht der ADC und nicht die
Sonde. Es sind Übergangswiderstände.** Für 55 mV Fehler reichen bei 4,5 mA
schon **12 Ω** in der Masseleitung — das schafft eine müde Krokoklemme. Die
Messung dazu steht in [`messungen.md`](messungen.md).

Zwei Regeln für den Endaufbau:

1. **Sondenkabel und Bürdewiderstand kommen in Schraubklemmen** (`KF128`),
   nicht auf Steckverbinder. Leitungen kurz halten.
2. **Der Fußpunkt des 100-Ω-Widerstands ist der Referenzpunkt.** Von dort
   gehen **zwei getrennte** Leitungen weg: eine zum Step-up-Minus (die trägt
   den Strom) und eine zum `GND` des ADS1115 (die trägt nur die Referenz).
   Nicht hintereinander hängen, sonst läuft der Messstrom durch die
   Messreferenz.

### Sternpunkt konkret

Auf der Lochrasterplatine ist der Sternpunkt **ein einziges Lötauge**:
Loch `4/8`, der Fußpunkt des 100-Ω-Widerstands. Dort treffen sich drei Dinge:

| | Was | Wohin | Stärke |
|---|---|---|---|
| 1 | unteres Bein des 100 Ω | — | — |
| 2 | Draht zur GND-Schiene | `4/8` → `4/10` | dick, trägt den Sondenstrom |
| 3 | Draht zum ADS1115 | `4/8` → `11/13` | dünn, trägt nur die Referenz |

Draht 3 wird **an dasselbe Lötauge** gelötet, nicht an das Ende von Draht 2
und nicht irgendwo an die GND-Schiene. Alle drei Verbindungen teilen sich nur
den Zinnklecks, sonst nichts.

**Prüfen:** zwischen `4/8` und dem `GND`-Pin des ADS1115 muss Durchgang
bestehen.

---

## Die Bauteilwerte

### 100 Ω Bürdewiderstand

| Strom | Spannung | Bedeutung |
|---|---|---|
| 4 mA | 0,400 V | Zisterne leer |
| 12 mA | 1,200 V | halb voll |
| 20 mA | 2,000 V | 2,00 m Wasser |

2,0 V liegt sicher unter den 3,3 V des ADS1115. Der Widerstand muss **0,1 %
und 25 ppm/K** haben — ein 5-%-Widerstand verschiebt die Anzeige um bis zu
10 cm.

### 2 kΩ vor `A0`

| Wert | Fehlerstrom im Fehlerfall | Messfehler bei 2,000 V |
|---|---|---|
| 1 kΩ | ~11 mA — **zu viel** | 0,3 mV = 0,4 mm |
| 1,5 kΩ | ~8 mA | 0,5 mV = 0,6 mm |
| **2 kΩ** | **~6 mA** | **0,7 mV = 0,8 mm** |
| 10 kΩ | ~1 mA | 3,3 mV = 4 mm — zu viel |

Der ADS1115 hält laut Datenblatt 10 mA am Eingang aus. 2 kΩ hat davon klar
Abstand. Die Toleranz des Widerstands ist egal, 5 % davon sind 0,03 mV.
Wozu der Widerstand überhaupt gut ist: [FAQ](faq.md).

### 220k / 220k Spannungsteiler

Halbiert die Akkuspannung: 4,2 V → 2,1 V. Passt in den ADS1115.

**Das untere Ende hängt an `D5` (GPIO23), nicht an GND.** So fließt nur Strom,
während gemessen wird. Der Teiler braucht keinen Schutzwiderstand — die
220 kΩ begrenzen selbst.

Der Umrechnungsfaktor in `zisterne.yaml` ist ein **kalibrierter** Wert, kein
Nennwert. Ablauf: [`inbetriebnahme.md`](inbetriebnahme.md).

### 15 V aus dem Step-up

Die Sonde braucht **12 bis 32 V an ihren Klemmen**. Am 100-Ω-Bürdewiderstand
fallen bei 20 mA schon 2 V ab. Bei 12 V Speisung kämen an der Sonde nur 10 V
an — zu wenig. Mit 15 V hat sie 13 V und damit 1 V Reserve.

Die 4 % Toleranz des Moduls sind egal: 15 V ±4 % minus 2 V Bürde ergibt 12,4
bis 13,6 V. Warum ein Modul mit **fester** Spannung und keins mit Poti, steht
im [FAQ](faq.md).

### MOSFET-Schalter

Der N-Kanal ist ein **2N7000** im TO-92-Gehäuse. Flache Seite zu dir, Beine
nach unten: `S – G – D` von links.

Der Step-up zieht auch im Leerlauf Strom — genug, um den Akku in Wochen zu
leeren. Deshalb wird er am Eingang komplett abgetrennt. Die Zahlen stehen in
[`messungen.md`](messungen.md).

Der Pololu hat **keinen Enable-Pin**, nur `VIN`, `VOUT` und `GND`. Deshalb
braucht es den MOSFET-Schalter überhaupt.

Der Schalter ist **fail-safe**: Ohne Signal vom ESP zieht der 10k das
2N7000-Gate auf GND, der 100k zieht das AO3401-Gate auf VBAT, und der Step-up
ist aus. Das gilt auch, wenn der XIAO gar nicht im Sockel steckt.

### SHT4x in der Box

Er misst Temperatur und Luftfeuchte **innen in der Box**, nicht im Wasser. Er
ist eine Frühwarnung vor Kondenswasser, kein Messwert für die Zisterne. Was
die Werte bedeuten, steht in [`betrieb.md`](betrieb.md), warum er drin ist im
[FAQ](faq.md).

Die eingebaute Heizung bleibt **aus** (`heater_max_duty: 0.0`). Sie würde nur
Akku kosten.

---

## Der XIAO steckt in einem Sockel

Der XIAO wird **nicht** direkt auf die Platine gelötet.

- Kauf ihn in der Variante **„mit Header"** — dann sind die Stiftleisten schon
  dran.
- 2 × **Buchsenleiste** je 7-polig auf die Lochrasterplatine löten (aus einer
  40-poligen Leiste abschneiden). Vergoldete Kontakte nehmen, verzinnte laufen
  im feuchten Schacht mit den Jahren an.

Warum gesteckt: [FAQ](faq.md).

### Die Akkupads

Der XIAO hat **keinen Akkustecker**, sondern zwei Lötpads auf der Unterseite:

| Pad | Liegt neben |
|---|---|
| **B+** | Beschriftung **D5** |
| **B−** | Beschriftung **D8** |

**Vor dem Löten mit dem Multimeter gegenprüfen.** Verpolt zerstört es das
Board.

Auf die Pads kommt **nicht** direkt der Akku, sondern ein Kabelstück von etwa
8 cm mit einer **JST-PH-2.0-Buchse** am anderen Ende. Sonst wäre der XIAO an
die Platine gefesselt und nicht mehr steckbar.

**Reihenfolge beim Bauen:** Erst das JST-Kabel an B+ / B− löten, **dann** den
XIAO in den Sockel stecken. Mit gestecktem Board kommst du an die Pads auf der
Unterseite kaum noch ran.

### Wo der Akku steckt

Auf der Platine, nicht am XIAO. Dafür werden **zwei fliegende JST-Kabel** in
die Sammelschienen gelötet. Kabelenden direkt in die Löcher — ein JST-Rastermaß
von 2,0 mm passt nicht auf 2,54-mm-Lochraster.

| Kabel | rot (+) | schwarz (−) | Zweck |
|---|---|---|---|
| JST-**Buchse** | `16/1` | `13/10` | hier steckt der Akku ein |
| JST-**Stecker** | `15/1` | `12/10` | geht in die Buchse am XIAO-Kabel |

Die Kette ist also: **Akku → Platine → Kabel → XIAO.** Damit hängen VBAT- und
GND-Schiene direkt am Akku, und der XIAO bleibt steckbar.

Zwei Dinge, die dabei schiefgehen:

1. **Polung.** Billige JST-Kabel haben rot und schwarz nicht immer gleich
   belegt. Du hast drei solcher Kabel im Aufbau. Miss alle durch, bevor der
   Akku das erste Mal steckt.
2. **Reihenfolge beim Ausbauen.** Erst den Akku ziehen, dann den XIAO. Die
   VBAT-Schiene ist stromführend, auch wenn der XIAO nicht im Sockel sitzt.

Die zwei Steckkontakte im Akkupfad sind unkritisch. Durch sie läuft nur der
Versorgungsstrom, **kein Messstrom**. Der Messkreis (Sternpunkt → GND-Schiene
→ Pololu GND) bleibt komplett auf der Platine.

---

## Sondenkabel

Die Adernfarben sind je nach Hersteller unterschiedlich. Üblich: **braun = +**
(Versorgung), **blau = −** (Signal/Rückweg). Im Zweifel das Datenblatt lesen.
Bei der 2-Leiter-Schaltung fließt derselbe Strom durch Versorgung und Signal.

Der dritte „Draht" ist keiner: Das ist die dünne **Luft-Kapillare**. Sie darf
**nicht** abgeschnitten, geknickt oder verklebt werden. Sie endet offen in der
Box.

---

## Was wo weitergeht

| Frage | Datei |
|---|---|
| In welcher Reihenfolge löte ich das? | [`bauanleitung.html`](bauanleitung.html) |
| Wie kommt die Platine in die Box? | [`bauanleitung.html`](bauanleitung.html), Schritt 7 und 8 |
| Wo kommt die Box hin? | [`einbau.md`](einbau.md) |
| Warum ist das so und nicht anders? | [`faq.md`](faq.md) |
| Welche Zahl wurde wann gemessen? | [`messungen.md`](messungen.md) |
