# Prüfplan

> **Hier steht:** Welche Tests es gibt, was dabei herauskommen muss und was
> davon schon erledigt ist.
> **Hier steht nicht:** Die gemessenen Zahlen. Die stehen mit Datum und Methode
> in [`messungen.md`](messungen.md).

Zehn Tests. Die ersten neun begleiten den Aufbau, der zehnte entscheidet, ob
die Box zugehen darf.

**Eine Zeile pro Test.** Wer einen Test wiederholt, ändert den Status in
derselben Zeile und schreibt die neue Zahl nach [`messungen.md`](messungen.md).
So kann nicht passieren, dass derselbe Test zweimal mit unterschiedlichem
Ergebnis dasteht.

---

## Die Tests

| # | Test | Erwartung | Werkzeug | Stand |
|---|---|---|---|---|
| 1 | **WLAN im Schacht**, mit dem fertigen Aufbau | besser als −70 dBm | `zisterne.yaml` | ⬜ |
| 2 | **Sonde im Eimer**, bekannte Wasserhöhe | Pegel stimmt auf ±2 cm | `zisterne.yaml` | ✅ |
| 3 | **Sonde 10 cm anheben** | Wert sinkt um 10 cm | `zisterne.yaml` | ✅ |
| 4 | **Tiefschlafstrom** messen | unter 100 µA | `werkzeuge/schlaftest.yaml` | ✅ |
| 5 | **15-V-Ausgang im Schlaf** prüfen | 0 V | Multimeter | ⬜ |
| 6 | **48 Stunden Dauerlauf** | Akkuspannung fällt kaum | `zisterne.yaml` | ⬜ |
| 7 | **Box zu, eine Nacht draußen** | innen trocken, Box-Feuchte unter 60 % | `zisterne.yaml` | ⬜ |
| 8 | **Eingebaut mit Zollstock kalibrieren** | Anzeige stimmt | Zollstock | ⬜ |
| 9 | **Klimasensor im Log** | beide I²C-Adressen antworten, Temperatur plausibel | `zisterne.yaml` | ✅ |
| 10 | **Update-Protokoll, dreimal hintereinander** | läuft ohne Handanlegen durch | Home Assistant | ⬜ |

---

## Test 1 — WLAN im Schacht

**Der wichtigste Test vor dem Einkauf.** Kommt im Schacht kein WLAN an, ist
das ganze Konzept hinfällig.

### Grobmessung, vor dem Einkauf

Mit `werkzeuge/wlan-test.yaml` auf irgendeinem ESP32, den du herumliegen hast.
Board an eine Powerbank, in eine Plastiktüte, in den Schacht, Deckel zu, zehn
Minuten warten.

| Ergebnis | Bedeutung |
|---|---|
| besser als −70 dBm | weiter wie geplant |
| −70 bis −80 dBm | Board mit u.FL-Buchse und externe Antenne einplanen |
| schlechter oder nichts | Antenne höher, näherer Accesspoint, im Notfall LoRa |

### Abnahme, mit dem fertigen Aufbau

**Diese Messung läuft über `zisterne.yaml`**, nicht über das Werkzeug. Nur die
echte Konfiguration schaltet auf die externe Antenne um — das sind rund 11 dB
Unterschied. Mit dem Werkzeug auf dem Zielboard misst du die interne
Keramikantenne und bekommst eine Zahl, die für den Endaufbau wertlos ist.

**Vor dem Bohren wiederholen.** Halte den fertigen Aufbau an die geplante
Stelle, Deckel zu, und schau in Home Assistant auf den WLAN-Wert.

---

## Test 4 — Tiefschlafstrom

Der wichtigste Test während des Aufbaus. Er entscheidet über die Laufzeit.

**Aufbau:** XIAO und Akku, sonst nichts. Multimeter in der Plusleitung, eine
Krokoklemme parallel dazu als Brücke.

**Ablesen:** Die LED blinkt, solange das Board wach ist. Ist sie aus, schläft
es — dann die Brücke abziehen und messen.

> **Brücke danach sofort wieder dran.** Beim nächsten Aufwachen zieht das Board
> rund 40 mA. Ohne Brücke fliegt die Sicherung im Multimeter.

**Bei mehr als 100 µA:** Auf dem Board nach einer Power-LED suchen, oder
prüfen, ob der Step-Up wirklich abgetrennt ist.

**Wenn dein Multimeter keine Mikroampere kann** oder die Sicherung durch ist:
Es geht auch ohne, siehe [FAQ](faq.md).

Im Endaufbau lässt sich derselbe Test mit `zisterne.yaml` fahren. Dafür den
Helfer `wach halten` auf **AUS** stellen und den Knopf **Messen und syncen**
drücken — das Gerät misst noch einmal und legt sich dann hin.

---

## Test 10 — Das Update-Protokoll

**Erst wenn das dreimal hintereinander ohne Handanlegen durchläuft, darf die
Box zu.** Danach gibt es keinen Knopf am Gerät mehr, und USB als Notausgang
fällt weg.

Jeder Durchlauf:

1. Helfer `wach halten` auf **AN**
2. Beim nächsten Aufwachen bleibt das Gerät wach
3. Ein Update über Funk läuft in dem Fenster sauber durch
4. Nach dem Neustart ist das Gerät von selbst wieder wach
5. Helfer auf **AUS**, dann Knopf **Messen und syncen** — Gerät geht in den
   Stundentakt zurück
6. Eine Stunde später kommt der nächste Messwert

> Ein Schritt aus der ursprünglichen Fassung ist entfallen: die
> Benachrichtigung „Fenster offen". Sie sollte feuern, wenn ein Wert wieder
> gültig wird — aber die Werte werden nie ungültig, der Auslöser kann also
> nicht ziehen. Begründung in [`messungen.md`](messungen.md).

Die Voraussetzungen dafür sind gemessen und geschlossen: Der Helfer kommt
rechtzeitig an, ein laufendes Update kann nicht mehr unterbrochen werden, und
die Rückkehr in den Stundentakt geht sofort statt nach einer halben Stunde.
Was fehlt, ist der Nachweis, dass alles **dreimal am Stück** zusammenspielt.
