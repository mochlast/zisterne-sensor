# Gemessen, nicht geschätzt

> **Hier steht:** Jede Zahl, die im Projekt eine Entscheidung getragen hat —
> mit Datum, Methode und Ergebnis.
> **Hier steht nicht:** Was noch zu prüfen ist. Das steht in
> [`pruefplan.md`](pruefplan.md).

Fast jede Festlegung in diesem Projekt geht auf eine Messung zurück, nicht auf
eine Annahme. Diese Datei ist der Beleg dazu. Sie ist chronologisch, weil
mehrere Befunde erst zusammen Sinn ergeben.

Wer nur die Ergebnisse will: ganz unten steht **Die Zahlen auf einen Blick**.

---

## 19.08.2026 — Geht überhaupt WLAN im Schacht?

Erste Messung, noch bevor irgendetwas gekauft wurde. Ein ESP32-DevKit mit
PCB-Antenne an einer Powerbank, an zwei Stellen im Schacht.

| Position | RSSI | Ergebnis |
|---|---|---|
| direkt unter dem Kunststoffdeckel | **−65 bis −67 dBm** | stabil |
| ca. 1 m tiefer, auf Höhe Beton | **keine Verbindung** | meldet sich gar nicht |

**Folge:** Der Beton schirmt vollständig ab. Die Antenne muss ganz nach oben,
die Box darf tiefer sitzen. Damit braucht das Board eine u.FL-Buchse — und
damit fiel die Wahl auf das XIAO ESP32C6.

> Diese Messung entstand mit einem **anderen Board** als dem später verbauten.
> Sie ist damit eine Vorentscheidung, kein Abnahmetest. Die Wiederholung mit
> dem fertigen Aufbau steht in [`pruefplan.md`](pruefplan.md) als Test 1.

---

## 31.08.2026 — Der MT3608 fällt durch

Drei Step-Up-Module aus derselben Lieferung, alle drei geprüft.

**Keines ließ sich einstellen.** Die Ausgangsspannung entsprach immer der
Eingangsspannung minus Diodenabfall: bei 3,7 V hinein kamen **3,469 V** heraus.
Das Poti tat nichts, auch nach hundert Umdrehungen nicht.

Die Recherche ergab einen bekannten Serienfehler — Begründung und Belege im
[FAQ](faq.md). Ersatz wurde der Pololu U3V16F15 mit fest eingestellten 15 V.

---

## 01.09.2026 — Tiefschlafstrom am Steckbrett

Gemessen mit der Shunt-Methode über 1 kΩ, weil der µA-Bereich des Multimeters
feiner aufgelöst werden sollte.

**15,8 mV an 1 kΩ = 15,8 µA.**

Damit war klar, dass das Laufzeitziel von über einem Jahr erreichbar ist. Die
Methode steht im [FAQ](faq.md), der Ablauf in [`pruefplan.md`](pruefplan.md).

---

## 02.09.2026 — Die Sonde misst, und die Krokoklemmen lügen

**Sonde am Labornetzteil**, 15 V, Rückweg über den Bürdewiderstand:

| | |
|---|---|
| Sonde in Luft | 0,401 V (Erwartung 0,400 V) |
| 23 cm Wasser | 23,8 cm angezeigt |

Damit waren Messprinzip und Umrechnung bestätigt.

**Der wichtigere Befund kam nebenbei.** Derselbe Aufbau, nur andere
Verbindungen:

| Verbindungen | Abweichung zum Multimeter | Fehler im Pegel |
|---|---|---|
| viele Krokoklemmen | +54,7 mV | **6,8 cm** |
| weniger Krokoklemmen | −7,5 mV | 0,9 cm |
| eine Krokoklemme, Rest gesteckt | −3,1 mV | **0,2 cm** |

Der größte Fehler in der Messkette ist **nicht** der ADC und **nicht** die
Sonde. Es sind Übergangswiderstände. Für 55 mV Fehler reichen bei 4,5 mA schon
**12 Ω** in der Masseleitung — das schafft eine müde Krokoklemme.

Daraus wurde die Sternpunkt-Regel in [`verdrahtung.md`](verdrahtung.md), die
wichtigste Layoutvorschrift des Projekts.

---

## 18.09.2026 — Die fertige Platine

### Der Schalter tut, wofür er da ist

| Zustand | Strom am Eingang des Step-Up |
|---|---|
| Step-Up abgetrennt | **0,0 µA** (unter der Auflösung des Multimeters) |
| Step-Up durchgeschaltet, Sonde ab | **1138 µA** |

Über 24 Stunden wären die 1138 µA rund 27 mAh — der Akku wäre in Wochen leer.
Genau deshalb wird der Step-Up am Eingang komplett abgetrennt, statt ihn nur
unbelastet laufen zu lassen.

Pro Messung läuft er rund 1,3 Sekunden. Sein Leerlauf kostet damit etwa
0,01 mAh am Tag.

### Tiefschlafstrom auf der echten Platine

**15,8 µA** — identisch zum Steckbrettwert vom 01.09. Der Aufbau hat also
nichts dazugelegt.

### Die Antennenumschaltung bringt 11 dB

Gleiches Board, gleicher Platz auf dem Basteltisch, gleicher Accesspoint. Der
einzige Unterschied war der Umschaltblock in der Konfiguration:

| Antenne | RSSI |
|---|---|
| intern (Keramik), ohne Umschaltung | −69 dBm |
| extern (u.FL), mit Umschaltung | **−58 dBm** |

> **Die absoluten Zahlen gelten nur für diesen Platz.** Verwertbar ist allein
> die Differenz, weil beide Messungen am selben Ort entstanden.

11 dB sind Faktor 12 in der Leistung. Wer den Umschaltblock löscht, merkt es
erst, wenn das Gerät im Schacht nicht mehr antwortet.

### Ein halber Abend an geratenen Ursachen

Jedes Update über Funk fiel nach dem nächsten Aufwachen auf die alte Firmware
zurück. Vier Ursachen wurden nacheinander vermutet und verworfen. Die echte
stand am Ende im Bootlog: eine **zu alte ESPHome-Version**.

Daraus wurde die Regel, die seitdem im Projekt gilt: **Erst den Bootlog holen,
dann eine Ursache benennen.** Der Mechanismus steht im [FAQ](faq.md).

---

## 20.09.2026 — Sonde im Eimer, Akku dran

| Prüfung | Ergebnis |
|---|---|
| Zollstock 23 cm | Anzeige **0,226 m** — 4 mm Abweichung |
| Akkubetrieb | Sonde und Step-Up laufen am LiPo, kein Spannungseinbruch |
| Akkumessung | 3,97 V angezeigt bei 4,00 V eingespeist — 0,7 % daneben |

Am Abend wurde der erste Nachtlauf gestartet: Akku frisch geladen auf 4,19 V,
Sonde im Wasser bei 23 cm, Intervall 60 Minuten. Erwartet wurden zwölf
Messpunkte bis zum Morgen.

---

## 21.09.2026 — Der Nachtlauf liefert nichts

### Null von zwölf

Letzter echter Wert um **22:25:03** (Pegel 0,233 m, Akku 4,124 V). Zwei
Minuten später gingen alle Werte verloren und kamen nicht wieder.

Durch Messung ausgeschlossen:

| Verdacht | Befund |
|---|---|
| Akku leer | nein, 4,124 V beim letzten Wert |
| Schlafdauer zu lang | nein, exakt 60 Minuten laut Log |
| ESPHome zu alt | nein, aktuelle Version |
| Bekannter ESPHome-Fehler | nein, beide Projekt-Tracker durchsucht |
| Antenne lose | nein, Stecker sitzt fest |
| Firmware weicht von der Datei ab | nein, Zeitstempel passen |
| Sonde oder ADC defekt | nein, 0,585 V am Shunt = 5,85 mA = 0,23 m |

### Die Ursache: zwei Sekunden

Der Bootlog zeigte es: Das Gerät schlief **2 Sekunden nach jedem Start** ein —
bevor das WLAN stand. Vier Startversuche, jedes Mal dieselben zwei Sekunden.

Ursache war ein Schalter zum Wachhalten. Sein Zustand wurde beim Start
wiederhergestellt, und genau das löste die daran hängende Schlafaktion aus.
Die Sperre dagegen half nicht, weil sie nur den anderen der beiden Schlafwege
blockiert.

Das Gerät hatte die ganze Nacht sauber gemessen und die Werte ins Nichts
geschickt.

**Behoben, indem der Schalter durch einen Button ersetzt wurde.** Ein Button
hat keinen Zustand — der Fehler ist damit nicht abgefangen, sondern unmöglich.
Begründung im [FAQ](faq.md).

| | vorher | nachher |
|---|---|---|
| Wachzeit | 2 s | **9 s** |
| WLAN | nie verbunden | verbunden |
| Werte in Home Assistant | keine | 0,232 m / 4,128 V |

### Akkuanzeige kalibriert: 93 % → 99 %

Bei vollem Akku zeigte die Anzeige 93 %. Zwei Ursachen, beide gemessen.

**Erstens der Teilerfaktor.** Im Code stand der Nennwert. Gegen ein Multimeter
gemessen zeigte der Sensor **65 mV zu wenig**:

| | |
|---|---|
| Sensor | 4,113 V |
| Multimeter | 4,178 V |
| Neuer Faktor | 4,178 / (4,113 / 2,0) = **2,0316** |

Gegenprobe mit einem zweiten Wertepaar: 4,181 / (4,1188 / 2,0) = 2,0302. Die
beiden liegen 0,07 % auseinander — kein Zufallstreffer, sondern ein echter
systematischer Fehler. Die beiden Widerstände liegen zusammen rund 1,6 %
daneben, völlig normal bei 1-%-Typen.

**Zweitens das Kurvenende.** Der letzte Punkt lag bei 4,20 V und ist im
Betrieb nicht erreichbar. Begründung im [FAQ](faq.md).

**Was es nicht war:** Die erste Vermutung war, dass der Step-Up die Spannung
unter Last herunterzieht. Gemessen: **3 mV.** Die Messreihenfolge wurde
trotzdem umgestellt — Akku zuerst, ohne Last — weil der Innenwiderstand mit
dem Alter steigt.

### Die restlichen Anforderungen

| | Messung |
|---|---|
| Helfer kommt rechtzeitig an | Gerät kam um 09:39:14 hoch und **blieb wach** |
| Sofort schlafen legen | Gerät war 2,5 Minuten nach dem Befehl weg, statt auf die 30-Minuten-Notbremse zu warten |
| Update über Funk | `OTA successful` in **5,3 Sekunden**, ohne Knopfdruck |

---

## 22.09.2026 — Der Lauf, der zählt

Erster vollständiger Lauf nach der Reparatur, mit aktiver Nachtruhe.

### Die drei Zahlen

| | |
|---|---|
| **Messpunkte** | **10 gültige** von 11 |
| **Spanne des Pegels** | 0,2298 bis 0,2306 m = **0,78 mm** |
| **Akkuabfall über die Nacht** | 4,1528 → 4,1511 V = **1,8 mV** in 8 h 52 min |

Der elfte Punkt stand auf 0,00 m. Kein Gerätefehler — die Sonde war zu dem
Zeitpunkt zum Bohren der Kabeldurchführungen abgeklemmt, und 0 mA ergibt
rechnerisch 0,00 m.

### Die 800 ms reichen

Das war die längste offene Frage: Die Sonde ist zwischen zwei Messungen eine
Stunde stromlos, und die Einschwingzeit war unter dieser Bedingung nie
getestet. Der Schwellwert für „reicht nicht" lag bei 1 cm pro Stunde.

**Gemessen: 0,78 mm über 17 Stunden.** Nicht pro Stunde — insgesamt.

### Die Nachtruhe greift

| Zeit | |
|---|---|
| 21:02:29 | letzte Messung des Tages |
| *8 h 52 min* | *keine einzige Meldung* |
| 05:54:39 | erstes Aufwachen |
| **06:00:01** | Anker, eine Sekunde neben dem Ziel |

### Der Wecker geht rund 1 % vor

Zwischen zwei Messungen liegen nicht 60:00, sondern konstant **59:36**:

```
14:05:18 → 15:04:54 → 16:04:33 → 17:04:10 → 18:03:45 → 19:03:18 → 20:02:53 → 21:02:29
   59:36     59:39     59:37     59:35     59:33     59:35     59:36
```

Der ESP32 zählt im Tiefschlaf mit einem internen RC-Oszillator, keinem Quarz.
Der geht rund 0,8 bis 1 % falsch und driftet mit der Temperatur.

Über neun Stunden Nachtruhe summiert sich das auf gut fünf Minuten — das Gerät
wachte um 05:54 statt um 06:00 auf. **Das Skript fängt das selbst ab:** Um
05:54 ist es noch Nachtruhe, es rechnet gegen die echte Uhrzeit neu und schläft
weiter bis 06:00:01.

Die Nachtruhe korrigiert damit ihren eigenen Gangfehler. Ohne diese Prüfung
wäre das Gerät um 05:54 in den Stundentakt gefallen und der Anker wäre weg.

### Während eines Updates schläft nichts ein

Ein halb geschriebener Flash ist der schlimmste Fall, den dieses Gerät haben
kann — nach dem Einbau gibt es keinen Knopf mehr.

Zuerst die Voraussetzung: Die Sperre greift **vor dem ersten Byte**.

```
15:11:18.983  Starting update      <- Sperre wird hier gesetzt
15:11:19.120  Progress: 0.1%       <- erstes Byte, 137 ms später
15:11:24.550  Update complete
```

**Der Nachweis kam über einen Gegenversuch.** Drei Starts hintereinander,
gleiche Firmware, gleiches verkürztes Testfenster von einer Sekunde, Helfer
jedes Mal auf AN. Einziger Unterschied: ob ein Update lief.

| Start | Update lief | Ergebnis |
|---|---|---|
| 2 | **ja** | kein Einschlafen, Update komplett |
| 3 | nein | **sofort eingeschlafen** |

Der Schlafversuch fällt rund vier Sekunden nach dem Start, also mitten in die
Schreibphase. Ohne Update schläft das Gerät dort sofort, mit Update nicht.

> **Die Log-Zeile der Sperre fehlt im Mitschnitt trotzdem.** Sie steht
> nachweislich in der Firmware. Im selben Mitschnitt fehlen auch mehrere
> Fortschrittsmeldungen, die in einem früheren Lauf da waren — der USB-Log
> verliert während eines Updates Zeilen, weil der Puffer klein und das Gerät
> beschäftigt ist. Der Gegenversuch ist ohnehin das stärkere Argument.

**Ein Teilbefund nebenbei:** Bricht ein Update schon im Handshake ab, ist noch
kein Byte geschrieben. Genau das passierte in einem früheren Versuch — 476 ms
nach Beginn des Handshakes schlief das Gerät ein, das Update starb, der Flash
blieb unberührt. Das kostet einen Versuch, kein Gerät.

---

## 23.09.2026 — Aufräumen

### Die Werte bleiben stehen

Lange stand die Vermutung im Raum, Home Assistant verliere die Werte nach
einem Neustart über Funk. **Stimmt nicht.**

Gemessen in einem Fenster mit Update-Neustart und anschließendem Einschlafen:
kein einziges Mal wurde ein Wert ungültig. Auch über die Nachtruhe von
8 h 52 min nicht.

Am 20.09. verschwanden die Werte, weil das Gerät **nie wiederkam** — nicht
wegen des Updates.

### Ein Knopf statt zwei

Zwei Knöpfe wurden zu einem zusammengelegt, der einen kompletten Zyklus
ausführt. Gemessen:

| Zeit | |
|---|---|
| 11:20:12 | Druck, Wachhalten **AN** |
| 11:20:13 | frischer Wert |
| 11:20:29 | **noch wach** |
| 11:20:41 | Druck, Wachhalten **AUS** |
| **11:20:45,75** | alle drei Sensoren bei Home Assistant |
| 11:21:01 | **schläft** |

Die Werte gehen also raus, **bevor** das Gerät schläft. Genau dafür wartet der
Ablauf nach dem Messen auf die Verbindung.

### Der Klimasensor läuft

24,4 °C und 51 % bei Zimmertemperatur. Beide I²C-Adressen antworten.

---

## Die vier Anforderungen ans Update über Funk

Zu Beginn standen vier Lücken im Weg. Alle sind geschlossen.

| | Anforderung | Stand | Beleg |
|---|---|---|---|
| G1 | Der Wachhalte-Helfer kommt rechtzeitig an | ✅ 21.09. | blieb wach ab 09:39:14 |
| G2 | Benachrichtigung, wenn das Gerät erreichbar wird | ⊘ hinfällig 23.09. | Werte werden nie ungültig, der Auslöser kann nicht ziehen |
| G3 | Update gegen die Wachzeit-Notbremse absichern | ✅ 22.09. | Gegenversuch, drei Starts |
| G4 | Sofort zurück in den Stundentakt | ✅ 21.09. | weg 2,5 min nach dem Befehl |

---

## Die Zahlen auf einen Blick

| Größe | Wert | Gemessen am |
|---|---|---|
| Tiefschlafstrom | **15,8 µA** | 01.09. und 18.09., zwei Aufbauten |
| Leerlaufstrom des Step-Up, abgetrennt | **0,0 µA** | 18.09. |
| Leerlaufstrom des Step-Up, eingeschaltet | 1138 µA | 18.09. |
| Wachzeit pro Messung | rund **9 s** | 21.09. |
| Weckabstand | **59:36** statt 60:00 | 22.09. |
| Genauigkeit gegen Zollstock | 4 mm bei 23 cm | 20.09. |
| Pegelspanne über 17 Stunden | **0,78 mm** | 22.09. |
| Akkuabfall über 8 h 52 min Nachtruhe | **1,8 mV** | 22.09. |
| Gewinn durch die externe Antenne | **11 dB** | 18.09. |
| Teilerfaktor der Akkumessung | **2,0316** statt 2,0 | 21.09. |
| Dauer eines Updates über Funk | **5,3 s** | 21.09. |
