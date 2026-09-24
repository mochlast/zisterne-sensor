# Häufige Fragen

> **Hier steht:** Warum das Projekt so gebaut ist und nicht anders.
> **Hier steht nicht:** Was zu tun ist, wenn etwas nicht läuft — das steht in
> [`fehlersuche.md`](fehlersuche.md).

Die Antworten sind kurz. Zahlen, Tabellen und Pin-Nummern stehen im jeweiligen
Fachdokument, damit sie nur an einer Stelle gepflegt werden müssen.

---

## Bauteilwahl

### Warum nicht den MT3608? Der kostet 1 € statt 7 €

Weil er nicht funktioniert. Drei Module aus derselben Lieferung wurden
getestet, **keines** ließ sich einstellen: Die Ausgangsspannung entsprach immer
der Eingangsspannung minus Diodenabfall, das Poti tat nichts.

Das ist ein bekannter Serienfehler, kein Bedienfehler. Ursache ist ein
100-kΩ-Poti in Reihe mit 2,2 kΩ — dadurch liegen die nutzbaren 12 V in den
letzten 3 % des Drehwegs. Mehrere Käufer berichten, dass die Module beim
Verstellen abrauchen.

Reparieren ginge mit einer Drahtbrücke. Lohnt sich nicht: Das Gerät sitzt ein
Jahr allein im Schacht, und wenn 15 V in 3 % des Drehwegs liegen, verschiebt
schon die Temperatur im Winter die Messung.

Stattdessen der **Pololu U3V16F15** mit fest eingestellten 15 V. Kein Poti,
nichts das driften kann. Details und die Absagen an XL6009, LM2577 und TPS61088
stehen in [`teileliste.md`](teileliste.md).

### Warum muss der P-MOSFET SOT-23 sein? TO-92 lässt sich leichter löten

Wegen des Durchgangswiderstands. Der Step-Up zieht am Eingang bis zu 100 mA,
und der Schalter davor darf dabei fast nichts verlieren.

Der **AO3401A** hat 0,05 Ω — bei 100 mA sind das 5 mV. Der **BS250** im
TO-92-Gehäuse hat rund 10 Ω, also 1 V Verlust. Damit bliebe für den Step-Up zu
wenig übrig.

SOT-23 löten geht gut mit einer Adapterplatine, danach hast du normale
Beinchen fürs Lochraster. Der **2N7000** steuert nur das Gate an, da fließen
Mikroampere — der darf TO-92 sein.

### Warum zwei Transistoren für einen Schalter? Einer müsste doch reichen

Der P-MOSFET schaltet die Last, aber sein Gate muss auf **VBAT-Niveau**
gezogen werden, um ihn auszuschalten. Der ESP kann nur 3,3 V. Bei 4,2 V Akku
bliebe der Schalter halb offen.

Der kleine N-MOSFET übersetzt: Der ESP schaltet ihn mit 3,3 V, und er zieht
das Gate des großen auf Masse. Zwei Widerstände sorgen dafür, dass ohne Signal
alles aus ist — der Schalter ist damit **fail-safe**.

Schaltung und Pins in [`verdrahtung.md`](verdrahtung.md).

### Warum nicht das ePaper Driver Board? Das hat doch Akkustecker und Ladeteil

Weil sein Lade-IC den XIAO über den **5-V-Pin** versorgt. Der Weg wäre dann
Akku 3,7 V → Boost auf 5 V → Regler im XIAO auf 3,3 V. Zwei Wandlungen statt
keiner.

Schlimmer: Der verbaute ETA9740 ist ein Powerbank-Chip. Solche Chips haben
einige hundert Mikroampere Ruhestrom und **schalten sich bei kleiner Last oft
selbst ab**. Bei 15 µA Tiefschlaf würde das Gerät nie wieder aufwachen.

Dazu belegt das Board über seinen FPC-Stecker sechs Pins, von denen wir zwei
selbst brauchen.

Für ein Display, das alle 15 Minuten kurz viel Strom zieht, ist das Board
richtig. Für einen Sensor, der 99,9 % der Zeit schläft, genau verkehrt.

### Warum ausgerechnet das Seeed XIAO ESP32C6?

Drei Gründe, in dieser Reihenfolge:

1. **u.FL-Antennenbuchse an Bord.** Im Schacht ist unter dem Kunststoffdeckel
   Empfang, einen Meter tiefer am Beton nicht mehr. Die Antenne muss also nach
   oben, die Box darf tiefer sitzen.
2. **LiPo-Lader an Bord.** Kein zusätzliches Modul, kein zweiter Ruhestrom.
3. **Tiefschlafstrom.** Rund 15 µA, von mehreren Leuten mit einem PPK2
   nachgemessen. Beim DFRobot FireBeetle 2 ESP32-E berichten Nutzer 174 bis
   468 µA, beim XIAO ESP32S3 ebenfalls deutlich mehr.

Bei einer Messung pro Stunde entscheidet der Ruhestrom über die Laufzeit.

### Warum sitzt ein SHT41 in der Box? Die Zisterne misst der doch gar nicht

Richtig, er misst nur das Klima **in der Box**. Er ist eine Frühwarnung, kein
Messwert für die Zisterne.

Die Box atmet durch ein Druckausgleichselement und trocknet mit Silica-Gel.
Beides altert. Ist das Gel gesättigt, steigt die Feuchte in der Box, und
irgendwann fällt Kondenswasser auf die Platine — von außen sieht man bis dahin
nichts.

Der Sensor kostet fast keinen Strom, hängt am ohnehin vorhandenen I²C-Bus und
braucht keine zusätzlichen Pins. Die Schwellwerte stehen in
[`betrieb.md`](betrieb.md).

### Warum Nylon-Abstandsbolzen und nicht Metall?

Drei Gründe: kein Rost im feuchten Schacht, kein Metall unter der Antenne, und
kein Kurzschluss — die Lochrasterplatine ist doppelseitig, ein Metallbolzen
kann unten ein Pad berühren, das man oben nicht sieht.

Aus demselben Grund ist die Montageplatte aus Isolierstoff und nicht aus
Stahlblech: Ein Blech unter der Antenne wirkt wie ein Spiegel. Siehe
[`einbau.md`](einbau.md).

---

## Bauteilwerte

### Warum 100 Ω als Bürdewiderstand, und warum muss der 0,1 % haben?

Weil 20 mA daran genau 2,0 V erzeugen — sicher unter den 3,3 V, die der
ADS1115 verträgt, und die Umrechnung geht im Kopf auf: Spannung mal 10 ergibt
den Strom in Milliampere.

Die 0,1 % sind kein Luxus. Ein 5-%-Widerstand würde die Anzeige um bis zu
10 cm verschieben — mehr als die Sonde selbst an Fehler hat. Er braucht
zusätzlich 25 ppm/K, sonst wandert die Anzeige mit der Temperatur.

Werte und Umrechnungstabelle in [`verdrahtung.md`](verdrahtung.md).

### Wofür sind die 2 kΩ vor dem ADC-Eingang?

Als Notausgang für den Fall, dass der Bürdewiderstand abreißt — kalte
Lötstelle, gebrochenes Bein. Dann sucht sich der Sondenstrom einen anderen Weg,
und der einzige verbleibende ist die Schutzdiode im ADC-Eingang. Ohne
Vorwiderstand stirbt der ADS1115.

2 kΩ begrenzen den Fehlerstrom auf etwa 6 mA, der Chip verträgt 10 mA. Der
Preis ist ein Messfehler von unter einem Millimeter Wasserstand. Die
Wertetabelle steht in [`verdrahtung.md`](verdrahtung.md).

### Warum 15 V aus dem Step-Up? Die Sonde will doch nur 12 V

Weil am Bürdewiderstand schon 2 V abfallen. Bei 12 V Speisung kämen an der
Sonde nur 10 V an — zu wenig, sie braucht mindestens 12 V an ihren eigenen
Klemmen.

Mit 15 V hat sie 13 V und damit Reserve. Die 4 % Toleranz des Moduls sind
egal: Die Sonde ist eine **Stromquelle** und liefert 4–20 mA unabhängig von
der Speisespannung, solange sie genug davon bekommt.

### Warum endet die Akkukurve bei 4,18 V und nicht bei 4,20 V?

Weil eine LiPo-Zelle 4,20 V nur **am Ladegerät** hält. Danach fällt sie binnen
Minuten auf rund 4,18 V.

Mit `4.20 -> 100 %` blieb die Anzeige deshalb bei 97 % stehen, obwohl die
Lade-LED „voll" meldete. Der Punkt war im Betrieb schlicht nicht erreichbar.
Nachgemessen mit dem Multimeter: 4,178 V bei ausgeschalteter Lade-LED.

### Warum reichen 800 ms Einschwingzeit für die Sonde?

Das war lange offen, weil die Sonde zwischen zwei Messungen eine Stunde
stromlos ist — der Fall war nie getestet.

Über 17 Stunden Dauerbetrieb schwankte der Pegel um **0,78 mm**. Nicht pro
Stunde, insgesamt. Der Schwellwert für „reicht nicht" lag bei 1 cm pro Stunde.
Die Zahlen stehen in [`messungen.md`](messungen.md).

### Wie viele Nachkommastellen zeigt die Anzeige, und warum nicht mehr?

Je nach Sensor unterschiedlich, und zwar nach seiner **Genauigkeit**, nicht
nach seiner Auflösung:

- **Pegel und Akkuspannung: drei Stellen.** Der ADS1115 löst feiner auf als
  die dritte Stelle, und die interessanten Größen sind klein — die Pegelspanne
  über eine Nacht lag bei unter einem Millimeter.
- **Box-Temperatur: eine Stelle.** Der SHT41 ist auf ±0,2 °C genau.
- **Box-Feuchte: keine.** ±1,8 % relative Feuchte — da ist selbst die ganze
  Zahl schon optimistisch.

Mehr Stellen zeigen die Nachkommastellen eines Fehlers. Bei den ersten beiden
ist die dritte Stelle für **Veränderungen** verlässlich, nicht für den
absoluten Wert — den stellt man beim Kalibrieren ein.

Wichtig: Die Einstellung ist reine Anzeige. Home Assistant speichert immer den
vollen Wert, Verlauf und Schwellwerte arbeiten mit voller Auflösung.

---

## Aufbau und Montage

### Warum wird die Elektronik nicht vergossen? Dicht wäre doch besser

Drei Gründe, und der erste wiegt am schwersten:

1. **Die Luft-Kapillare im Sondenkabel würde zugegossen.** Sie sorgt dafür,
   dass die Sonde gegen den aktuellen Luftdruck misst. Zugegossen misst sie
   gegen einen festen Druck, und jeder Wetterwechsel verschiebt die Anzeige um
   bis zu 30 cm.
2. Vergussmasse dämpft die WLAN-Antenne.
3. Kein Akkutausch, keine Reparatur mehr.

Stattdessen: dichte Box, Schutzlack auf der Platine, Silica-Gel und ein
Druckausgleichselement. **Die Box muss atmen können** — sonst hat man denselben
Wetterfehler wie beim Vergießen.

### Warum steckt der XIAO in einem Sockel statt festgelötet?

Weil der Tiefschlafstrom die einzige Zahl im Projekt ist, die man vorher nicht
sicher kennt. Fällt der Test schlecht aus, tauscht man das Board und muss
nichts neu bauen — nur Pin-Nummern ändern.

Dazu: Ein defektes Board ist in zwei Minuten gewechselt, und zum Flashen kann
man es herausziehen.

Damit das funktioniert, darf **kein Draht fest vom Board zur Platine gehen**.
Deshalb sitzt auch der Akkuanschluss auf der Platine und nicht am XIAO.

### Wo steckt der Akku — am XIAO oder auf der Platine?

Auf der Platine. Die Akkupads des XIAO liegen auf seiner Unterseite und sind
nicht auf die Stiftleiste geführt. Ein Draht von dort zur Platine würde das
Board festnageln und den Sockel sinnlos machen.

Stattdessen bekommt der XIAO ein kurzes Kabel mit Steckverbinder. „XIAO plus
Kabel" ist dann eine Einheit, die man an einem Stecker abzieht. Pads und
Polung in [`verdrahtung.md`](verdrahtung.md).

### Dichtring außen oder innen?

**Außen**, zwischen dem Sechskantbund und der Gehäusewand. Die Gegenmutter
kommt innen, ohne Dichtung. Gilt für die Kabelverschraubung genauso wie für
das Druckausgleichsventil.

Das Wasser ist außen, dort muss es gestoppt werden. Sitzt der Ring innen,
läuft Wasser erst ins Bohrloch und ins Gewinde und bleibt dort stehen.
Außerdem ist der Bund breit und glatt, während Gegenmuttern oft Zähne haben,
die den Ring einschneiden.

Liegen zwei Ringe bei: Gummi nach außen, harter oder gezahnter nach innen.
Ablauf in [`bauanleitung.html`](bauanleitung.html), Schritt 8.

### Darf ein Steckverbinder ins Sondenkabel? In verdrahtung.md steht das Gegenteil

Ja, das ist unkritisch — die Regel dort gilt für den **Referenzpfad**, nicht
für die Zuleitung.

Die Sonde ist eine Stromquelle, und gemessen wird ausschließlich über dem
Bürdewiderstand. Ein Übergangswiderstand in der Zuleitung sitzt davor und
taucht in der Messung gar nicht auf — er kostet nur Spannungsreserve, und
davon ist reichlich da.

**Im Referenzpfad bleibt die Regel scharf:** Sternpunkt, Masseschiene und die
Leitung zum ADS1115. Dort reichen wenige Ohm für mehrere Zentimeter
Anzeigefehler.

Einziges Restrisiko: Verzinnte Kontakte laufen im feuchten Schacht über Jahre
an. Ein Tropfen Kontaktfett hilft.

### Wie prüfe ich, ob die I²C-Pull-ups schon auf dem Modul sind?

Multimeter auf Widerstand, zwischen Versorgung und Datenleitung messen. Ein
paar Kiloohm bedeuten: vorhanden. „Unendlich" bedeutet: fehlen, dann musst du
sie nachrüsten.

Der umgekehrte Fall ist häufiger und wird oft übersehen: **Zwei Module bringen
zwei Sätze Pull-ups mit**, die parallel liegen. Wird der Wert dadurch zu klein,
überlastet das den Bus. Grenzwerte und Messpunkte in
[`verdrahtung.md`](verdrahtung.md).

Ohne Pull-ups findet der ESP den ADS1115 gar nicht. Der Fehler sieht aus wie
ein Verdrahtungsfehler, ist aber keiner.

---

## Firmware

### Warum muss ESPHome mindestens 2026.8.0 sein?

Sonst bleibt kein einziges OTA auf dem Gerät.

Der Bootloader markiert frisch geflashte Firmware als „pending verify". Erst
wenn ESPHome meldet, dass der Start gut war, wird sie fest übernommen.
Passiert das nicht, fällt das Gerät beim nächsten Reset auf die alte Firmware
zurück.

Der Standardwert dafür ist **eine Minute**. Dieses Gerät ist rund neun Sekunden
wach — die Minute kommt nie.

Ab 2026.8.0 zählt ein sauberes Herunterfahren automatisch als erfolgreicher
Start, und dazu gehört das Einschlafen. Derselbe Mechanismus hängt am
Fehlstart-Zähler: Ohne ihn ginge das Gerät nach zehn Weckvorgängen in den Safe
Mode und bliebe wach, bis der Akku leer ist.

### Warum gibt es zwei Wege in den Tiefschlaf, und warum ist das wichtig?

Weil sie sich unterschiedlich blockieren lassen — und wer das nicht weiß, baut
eine Sperre, die nicht greift.

Der **automatische** Weg läuft ab, wenn die eingebaute Wachzeit-Notbremse
zuschlägt. Gegen den hilft `deep_sleep.prevent`.

Der **manuelle** Weg ist ein ausdrückliches „jetzt schlafen" aus der
Konfiguration. Gegen den hilft `prevent` **nicht** — ESPHome prüft die Sperre
ausdrücklich nur beim automatischen Weg.

Genau daran ist im Projekt schon zweimal etwas gescheitert. Wer das Gerät
zuverlässig wach halten will, muss beide Wege schließen.

Die Stelle im ESPHome-Quelltext, an der das entschieden wird, steht in
[`betrieb.md`](betrieb.md).

### Warum eine eigene Gültigkeitsprüfung statt ESPHomes `until:`?

Weil `until:` nicht prüft, ob die Uhr überhaupt gestellt ist.

Die Nachtruhe rechnet aus, wie lange es bis zur Weckzeit ist. Die Uhrzeit
kommt aus Home Assistant. Ist die nicht da, rechnet die eingebaute Funktion
trotzdem — und die Subtraktion kann überlaufen. Im schlimmsten Fall schläft
das Gerät bis zu **50 Tage**.

Nach dem Einbau in den Schacht gibt es keinen Knopf mehr, mit dem man das
abbricht. Deshalb prüft das Skript selbst: **keine gültige Uhr heißt normaler
Stundentakt.** Der Fehler geht damit in die sichere Richtung — im Zweifel
wacht das Gerät einmal zu oft auf, nie zu selten.

### Warum ein Button zum Schlafenlegen und kein Schalter?

Weil „schlafen" eine **Handlung** ist und kein Zustand.

Ein Schalter hat einen Zustand. Der wird beim Start wiederhergestellt, und
genau das löst die daran hängende Aktion aus — das Gerät schlief zwei Sekunden
nach jedem Start ein, noch bevor das WLAN stand, und meldete tagelang nichts.

Ein Button hat keinen Zustand. Es gibt nichts zu speichern, nichts
wiederherzustellen, und beim Start feuert nichts. Der Fehler ist damit nicht
abgefangen, sondern unmöglich geworden.

Der ganze Hergang steht in [`messungen.md`](messungen.md).

### Warum liegt der Wachhalte-Zustand in Home Assistant und nicht auf dem Gerät?

Weil das Gerät nur rund neun Sekunden pro Stunde erreichbar ist. Etwas **auf**
dem Gerät umzulegen hieße, genau dieses Fenster zu treffen.

Der Helfer in Home Assistant lässt sich dagegen jederzeit umlegen, auch
während das Gerät schläft. Beim nächsten Aufwachen liest der ESP ihn aus und
richtet sich danach. Der Zustand wartet also, statt dass du warten musst.

### Warum heißen meine Entitäten „Zisterne Zisterne Akku"?

Weil Home Assistant den **Gerätenamen** voranstellt. Steht im Namensfeld der
Konfiguration nochmal „Zisterne", kommt es doppelt.

Deshalb sind die Namen dort bewusst kurz: `Akku`, nicht `Zisterne Akku`. Der
verbleibende Präfix stammt aus dem Gerätenamen in Home Assistant, nicht aus
der Konfiguration. Wer ihn kürzen will, benennt das Gerät in HA um.

---

## Messen und Betrieb

### Was ist der Sonden-Offset, und warum zeigt meine trockene Sonde 10 cm an?

Der Offset wird auf den Messwert **addiert**. Er steht für die Höhe, in der die
Sonde über dem Zisternenboden hängt — sie selbst misst ja nur die Wassersäule
über ihrer eigenen Membran.

Steht dort schon die geplante Aufhängehöhe, während die Sonde noch im Eimer
liegt, zeigt sie genau diesen Wert zu viel. Das sieht nach einem Messfehler
aus, ist aber die Konfiguration.

**Zum Testen auf 0 lassen, beim Einbau auf die echte Höhe setzen.** Ablauf in
[`inbetriebnahme.md`](inbetriebnahme.md).

### Wie messe ich Mikroampere, wenn mein Multimeter das nicht kann?

Miss statt des Stroms die Spannung über einem Widerstand in der Zuleitung.

**Merkregel: an 1 kΩ entspricht 1 mV genau 1 µA.** Viele Multimeter lösen
0,1 mV auf und sind damit im Spannungsbereich feiner als in ihrem eigenen
µA-Bereich. Außerdem nutzt man die Spannungsbuchse, und die hat keine
Sicherung.

Wichtig ist die Krokoklemme, die den Widerstand überbrückt: **Sie muss zu
sein, solange das Board wach ist.** Wach zieht es rund 40 mA, an 1 kΩ wären
das 40 V. Erst wenn es schläft, die Klemme abnehmen und ablesen. Ablauf in
[`pruefplan.md`](pruefplan.md).

### Warum misst das Gerät nur einmal pro Stunde und nachts gar nicht?

Weil die Wachzeit alles dominiert. Der Tiefschlaf kostet fast nichts, jeder
Weckvorgang dagegen spürbar — wer Laufzeit braucht, dreht am Intervall und
nicht am Schlafstrom.

Die Nachtruhe spart acht von vierundzwanzig Weckvorgängen und bringt damit
rund fünf Monate Laufzeit. Ein Zisternenpegel ändert sich nachts ohnehin
nicht.

Beides ist einstellbar, ohne die Firmware zu ändern. Wie, steht in
[`betrieb.md`](betrieb.md).
