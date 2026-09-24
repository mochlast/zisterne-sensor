# Teileliste

> **Hier steht:** Was du kaufst, welche Kennwerte es haben muss und was es
> ungefähr kostet.
> **Hier steht nicht:** Warum ein Teil so gewählt wurde — das steht im
> [FAQ](faq.md). Und nicht, was wohin gelötet wird — das steht in
> [`verdrahtung.md`](verdrahtung.md).

Preise sind Richtwerte in Euro, ohne Versand. Die Spalte **ca.** ist der
Preis für die genannte **Menge**, nicht pro Stück.

> **Bestellnummern überleben Links.** Bei Reichelt tippst du sie unter
> „Warenkorb" → „Schnellerfassung" direkt ein, dann brauchst du gar keinen
> Link: `MPR 100`, `METALL 220K`, `METALL 100K`, `METALL 10,0K`,
> `METALL 2,00K`, `2N 7000`, `BKL 10120978`, `DELOCK 60625`, `DELOCK 60479`,
> `KONTAKT 74313-AA`.

---

## Kern

| Teil | Menge | Muss haben | ca. | Beispiel-Link |
|---|---|---|---|---|
| **Pegelsonde QDY30A**, 0–2 m, **4–20 mA**, 5 m Kabel | 1 | Ausgang 4–20 mA (nicht 0–5 V), Belüftungskapillare im Kabel, Versorgung 12–32 V | 25 | https://de.aliexpress.com/item/1005007340652374.html |
| **Seeed XIAO ESP32C6**, Variante **mit Header** | 1 | u.FL-Buchse für die externe Antenne, Ladeteil an Bord | 7 | https://botland.de/xiao/24783-seeed-xiao-esp32-c6-wifi-bluetooth-seeedstudio-113991254-5904422385705.html |
| **ADS1115** Breakout, 16 Bit, I²C | 1 | Adresse `0x48`, Pull-ups auf dem Modul | 6 | |
| **SHT4x** Breakout (SHT40/41/45) | 1 | Adresse `0x44`, 3,3 V | 6 | https://de.aliexpress.com/item/1005009158072400.html |
| **Step-up Pololu U3V16F15**, fest 15 V | 2 | fest 15 V, Anlauf ab 2,7 V, Lötpads im 2,54-mm-Raster | 10 (je 5) | https://botland.de/aufwartswandler/22470-u3v16f15-aufwartswandler-15v-2a-pololu-4946.html |
| **LiPo 103450**, ca. 2000 mAh, mit JST-PH-2.0-Stecker | 1 | **Schutzplatine unter dem Schrumpfschlauch — Pflicht** | 10 | |
| **u.FL-Antenne 2,4 GHz** | 1 | u.FL/IPEX-Stecker | 2 | |

> **Zwei Step-up kaufen.** Einer kostet 5 €. Ein zweiter Gang in den Schacht
> kostet mehr.

> **Beim Bestellen der Sonde sind meist zwei Auswahlfelder falsch vorbelegt:**
> Standard ist „0-5V output" und „1m Range 1m Cable". Du brauchst
> **4-20mA output** und **2m Range 5m Cable**.
>
> Frag den Verkäufer vorher:
> *„Does the cable have a vent tube (air capillary) for atmospheric
> compensation? And can you ship the 2 meter range version with 4-20mA
> output?"*

Warum genau diese Teile: [FAQ](faq.md), Gruppe **Bauteilwahl**.

---

## Kleinteile Elektronik

| Teil | Menge | Muss haben | ca. | Beispiel-Link |
|---|---|---|---|---|
| **Bürdewiderstand 100 Ω** | 2 | **0,1 %, TK25** — keine 1-%-Ware | 0,60 | https://www.reichelt.de/de/de/shop/produkt/widerstand_metallschicht_100_ohm_0207_0_6_0_1_-12773 |
| Widerstand **220 kΩ**, 1 % | 2 | Metallschicht | 0,20 | https://www.reichelt.de/de/de/shop/produkt/widerstand_metallschicht_220_kohm_0207_0_6_w_1_-11628 |
| Widerstand **100 kΩ**, 1 % | 2 | Metallschicht | 0,20 | https://www.reichelt.de/de/de/shop/produkt/widerstand_metallschicht_100_kohm_0207_0_6_w_1_-11458 |
| Widerstand **10 kΩ**, 1 % | 2 | Metallschicht | 0,20 | https://www.reichelt.de/de/de/shop/produkt/widerstand_metallschicht_10_0_kohm_0207_0_6_w_1_-11449 |
| Widerstand **2 kΩ** | 1 | Toleranz egal, 5 % reichen | 0,10 | https://www.reichelt.de/de/de/shop/produkt/widerstand_metallschicht_2_00_kohm_0207_0_6_w_1_-11578 |
| **AO3401A** P-MOSFET | 1 | SOT-23, 0,05 Ω bei 3,7 V Gate | 2 | |
| **2N7000** N-MOSFET | 2 | TO-92 | 0,50 | https://www.reichelt.de/de/de/shop/produkt/mosfet_n-ch_60v_0_115a_0_4w_1_7r_to-92-41141 |
| **SOT-23-auf-DIP-Adapterplatine** | 2 | für den AO3401A | 2 | |
| **Buchsenleiste 2,54 mm**, 1×40 | 2 | gerade, als Sockel für den XIAO | 6 | https://www.reichelt.de/de/de/shop/produkt/female_header_2_54mm_straight_1x40-266702 |
| **Lochrasterplatine doppelseitig**, 5×7 cm | 1 | Montagelöcher in den Ecken | 1 | |
| **Schraubklemmen KF128**, 2-polig, RM 2,54 | 4 | fürs Sondenkabel | 2 | |
| **JST-PH-2.0-Kabelset** | 1 Set | drei Kabel werden gebraucht | 3,50 | |

> **Der Bürdewiderstand ist das einzige Teil, bei dem Sparen direkt in den
> Messwert geht.** Billige „0,1 %" sind oft 5 %. Kauf ihn dort, wo die Toleranz
> im Datenblatt steht. Begründung im [FAQ](faq.md).

Welcher Widerstand wohin gehört: [`verdrahtung.md`](verdrahtung.md).

---

## Gehäuse und Dichtung

| Teil | Menge | Muss haben | ca. | Beispiel-Link |
|---|---|---|---|---|
| **Leergehäuse**, ca. 130×94×57 mm, **IP66, ohne Vorprägung** | 1 | glatte Wände, damit du selbst bohrst | 10 | https://www.elektronetshop.de/detail/01994381ff9573b3b1bf2b69f6e953db |
| **Montageplatte aus Isolierstoff**, passend zum Gehäuse | 1 | **Isolierstoff, kein Stahlblech** | 4 | https://www.elektronetshop.de/spelsberg-montageplatte-19500901-typ-tk-mpi-1309/ |
| **Außenbefestigungslaschen**, passend zum Gehäuse | 1 Set | rasten außen ein, kein Loch im Gehäuse | 4 | https://www.elektronetshop.de/spelsberg-befestigungslaschenset-19400101-typ-tk-abl/ |
| **Kabelverschraubung M16, IP68** | 1 | Klemmbereich 4–8 mm (Sondenkabel Ø 7 mm) | 5,50 | https://www.reichelt.de/de/de/shop/produkt/kabelverschraubung_m16_schwarz_ip68_2_stueck-375254 |
| **Belüftungsstopfen M12, IP67/68, mit Membran** | 1 | Druckausgleich, damit die Box nicht „atmet" | 2 | https://www.reichelt.de/de/de/shop/produkt/ventilation_plug_m12_black_2_pcs-371154 |
| **Schutzlack für Platinen**, 400 ml | 1 | Sprühdose | 18 | https://www.reichelt.de/de/de/shop/produkt/anti-corrosion_varnish_plastic_70_400_ml_insulating_varnish-329027 |
| **Silica-Gel mit Farbindikator** | 1 Beutel | Farbe zeigt, wann es voll ist | 5 | |

> **Polystyrol ist spröde.** Beim Bohren der 16,5-mm-Öffnung reißt es leicht
> ein: langsame Drehzahl, kein Druck, von innen ein Stück Holz dagegen, und
> in zwei Schritten aufbohren. Wer das nicht riskieren will, legt ein
> zweites Gehäuse als Reserve dazu — siehe **Optional**.

> **Die Montageplatte muss aus Isolierstoff sein.** Ein Blech unter der Antenne
> wirkt wie ein Spiegel und kostet dich das WLAN. Bei vielen Herstellern gibt
> es beide Varianten unter fast gleicher Nummer — Beschreibung lesen.

---

## Befestigung

| Teil | Menge | Muss haben | ca. | Beispiel-Link |
|---|---|---|---|---|
| **Nylon-Abstandsbolzen M2 × 8 mm**, Innengewinde beidseitig | 4 | **Nylon, nicht Metall** | 3 | |
| **Nylon-Schrauben M2 × 6 mm** | 8 | passend zu den Bolzen | 2 | |
| **M4-Schrauben, Muttern, Scheiben, Edelstahl A2** | 4 Sätze | für die Befestigungslaschen | 3 | |
| **EPDM-Dichtscheiben M4** | 4 | gegen Wasser durch die Bohrlöcher | 2 | |
| **Dübel + Edelstahlschelle** | 1 | Zugentlastung fürs Sondenkabel | 2 | |
| **Edelstahlseil + Schäkel** | 1 | die Sonde hängt daran, nicht am Kabel | 5 | |

> **Miss die Montagelöcher deiner Platine nach**, bevor du M2 kaufst. Eine
> passende Schraube muss locker durchrutschen. Warum Nylon: [FAQ](faq.md).

---

## Werkzeug

Kein Verbrauchsmaterial — das hast du entweder oder du kaufst es einmal.

| Werkzeug | Wofür | ca. |
|---|---|---|
| **Stufenbohrer 4–20 mm** | M16 braucht 16,5 mm, M12 braucht 12,5 mm | 10 |
| **Bohrer 2,2 mm** | Löcher in die Montageplatte | 2 |
| Lötkolben, Entlötlitze, Seitenschneider | Aufbau | — |
| **Multimeter mit µA-Bereich** | Prüfplan Test 4 | — |
| Zollstock | Kalibrieren nach dem Einbau | — |

> **Dein Multimeter kann keine Mikroampere?** Kein Problem, es geht auch mit
> einem Widerstand und dem Spannungsbereich. Anleitung im [FAQ](faq.md).

---

## Optional

| Teil | Wofür | ca. | Beispiel-Link |
|---|---|---|---|
| **TP4056-Lademodul**, Typ-C, mit Schutzschaltung | lädt den Akku außerhalb der Box mit 1 A statt mit 100 mA — spart bei der Jahreswartung zwei Stunden | 3 | |
| Zweiter **Belüftungsstopfen** mit ePTFE-Membran (z. B. IP68/IP69K) | hält länger dicht als die einfache Variante | 7 | |
| **Zweites Leergehäuse** als Reserve | falls das erste beim Bohren einreißt | 10 | |

---

## Was es ungefähr kostet

| Gruppe | ca. |
|---|---|
| Kern | 66 € |
| Kleinteile Elektronik | 18 € |
| Gehäuse und Dichtung | 49 € |
| Befestigung | 17 € |
| **Summe Material** | **ca. 150 €** |
| Werkzeug, einmalig | 12 € |

**Was den Preis am stärksten bewegt:**

| Posten | Spanne |
|---|---|
| Sonde (Fernost vs. deutsche Marke) | 25 € bis 180 € |
| Gehäuse (Restposten vs. Listenpreis) | 10 € bis 18 € |
| Schutzlack | 18 € — die Dose reicht für viele Projekte |

Wer den Schutzlack schon hat und nur einen Step-up kauft, landet bei
**ca. 127 €**. Mit einer Sonde von einer deutschen Marke statt aus Fernost
werden daraus schnell **300 €**.

---

## Fallen beim Kauf

| Falle | Gegenmittel |
|---|---|
| Sonde kommt als 0–5 m statt 0–2 m | Vor dem Kauf im Chat bestätigen lassen |
| Sonde kommt ohne Belüftungskapillare | Vorher fragen, Text oben |
| Sonde kommt mit 0–5 V statt 4–20 mA | Beide Auswahlfelder prüfen |
| Sondenkabel zu kurz | Tiefe + Weg zur Box + 1 m Reserve |
| „0,1 %"-Widerstand ist in Wahrheit 5 % | Beim Händler mit Datenblatt kaufen |
| Montageplatte ist aus Stahlblech | Beschreibung lesen, Isolierstoff wählen |
| Step-up lässt sich nicht einstellen | Kein einstellbares Modul kaufen. Begründung im [FAQ](faq.md) |
| LiPo ohne Schutzplatine | **Nicht verwenden.** Siehe [`../SICHERHEIT.md`](../SICHERHEIT.md) |
| JST-Stecker passen farblich nicht | Bei JST nicht genormt. Beide Hälften vor dem Löten zusammenstecken |
