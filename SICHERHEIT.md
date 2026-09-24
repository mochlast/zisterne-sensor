# Sicherheit

> **Hier steht:** Was an diesem Projekt gefährlich ist und wie du es vermeidest.
> **Hier steht nicht:** Wie etwas gebaut wird — das steht in
> [`docs/bauanleitung.html`](docs/bauanleitung.html).

Lies das einmal ganz, bevor du anfängst. Es sind vier Seiten Text, und drei
davon betreffen Dinge, die nicht wieder gutzumachen sind.

---

## Der Schacht ist das Gefährlichste an diesem Projekt

Nicht die Elektronik. Der Schacht.

Eine Zisterne ist ein **enger Raum** im arbeitsschutzrechtlichen Sinn. Was dort
passieren kann, passiert schnell und lautlos:

- **Kohlendioxid sammelt sich am Boden.** CO₂ ist schwerer als Luft und
  verdrängt den Sauerstoff. Man riecht es nicht, man merkt es nicht kommen. Wer
  sich hinunterbeugt, wird ohnmächtig und fällt hinterher.
- **Auch flaches Wasser reicht zum Ertrinken**, wenn du bewusstlos hineinfällst.
- **Der Deckel kann zufallen.** Eine Betonabdeckung hebt niemand von innen an.
- **Die Wände sind nass und glatt.**

### Die Regeln

1. **Steig nicht ein.** Für dieses Projekt musst du nicht in den Schacht. Die
   Box wird oben im Kunststoffbereich montiert, die Sonde wird von oben
   abgelassen. Alles ist vom Rand aus erreichbar.
2. **Arbeite nie allein.** Auch nicht für „nur mal kurz reinschauen". Jemand
   muss oben stehen und dich sehen.
3. **Sichere den Deckel**, damit er nicht zufallen kann.
4. **Lüfte vorher.** Deckel lange vor der Arbeit öffnen.
5. Muss doch jemand hinein: Das ist Arbeit für Leute mit Ausrüstung und
   Ausbildung, nicht für einen Bastelnachmittag.

---

## Der LiPo-Akku

Ein LiPo liefert bei einem Kurzschluss **mehrere Ampere**. Das reicht, um
Leiterbahnen zu verdampfen und Kunststoff zu entzünden.

### Pflicht, nicht Empfehlung

**Kauf nur Zellen mit aufgeklebter Schutzplatine.** Die kleine Platine unter dem
gelben Klebeband am Kabelende schützt gegen Tiefentladung, Überladung und
Kurzschluss. Zellen ohne sie sind ein paar Cent billiger und in diesem Aufbau
nicht vertretbar — das Gerät hängt ein Jahr lang unbeaufsichtigt in einem
feuchten Schacht.

### Beim Bauen

- **Polung vor dem Anlöten messen.** Verpolt bedeutet im besten Fall einen
  toten XIAO, im schlimmsten eine brennende Zelle. Die Pads heißen `B+` und
  `B−`, ihre Lage steht in [`docs/verdrahtung.md`](docs/verdrahtung.md).
- **Nie die Zelle einklemmen, knicken oder anbohren.** Eine beschädigte
  Aluminiumhülle kann ausgasen und sich entzünden.
- **Nicht direkt an der Zelle löten.** Nur an den mitgelieferten Kabeln.
- **Speise die Platine beim Aufbau aus einem Labornetzteil** mit 4,0 V und
  200 mA Strombegrenzung, nicht aus dem Akku. Bei einem Kurzschluss begrenzt
  das Netzteil, der Akku nicht.

### Beim Laden

- **Nicht unbeaufsichtigt laden.** Auf einer nicht brennbaren Unterlage, nicht
  auf dem Teppich, nicht über Nacht.
- **Nicht unter 0 °C laden.** Darunter lagert sich metallisches Lithium an der
  Anode ab. Das ist irreversibel und ein Sicherheitsproblem. Entladen ist bis
  etwa −20 °C unkritisch — nur Laden nicht. Lade den Akku drinnen.
- **Eine tiefentladene Zelle nicht wiederbeleben.** Wenn die Schutzplatine
  abgeschaltet hat und die Zelle lange lag: entsorgen, nicht laden.

### Niemals Labornetzteil und USB gleichzeitig

Der Lade-IC auf dem XIAO schiebt Strom in das Netzteil zurück. Ein Labornetzteil
kann keinen Strom aufnehmen, also läuft seine Ausgangsspannung hoch — bis
etwas kaputtgeht.

**Ein echter LiPo zusammen mit USB ist dagegen normal.** Genau dafür ist der
Lade-IC da.

---

## Wasser

**Dieses Projekt ist nicht für Trinkwasser geeignet.**

Die Sonde hängt dauerhaft im Wasser. Das Gehäuse ist lackiert, die
Verschraubungen haben Dichtringe, das Sondenkabel hat einen Mantel. Für **keines**
dieser Teile gibt es hier eine Freigabe für Trinkwasser oder Lebensmittelkontakt.

Wenn deine Zisterne Wasser für Toilette, Waschmaschine oder Garten liefert:
Prüfe selbst, ob die Materialien zu deiner Nutzung passen. Wenn sie Trinkwasser
liefert: **Bau das nicht ein.**

---

## Strom im Freien

Das Gerät läuft mit 3,7 V aus dem Akku und erzeugt intern 15 V für die Sonde.
Beides ist ungefährlich für Menschen.

**Gefährlich wird es dort, wo Netzspannung dazukommt** — etwa an der
Zisternenpumpe oder an einer Steckdose im Schacht. Daran arbeitet, wer dafür
ausgebildet ist. Nicht im Nassen, nicht nebenbei.

---

## Haftungsausschluss

Dieses Projekt ist eine **private Bastelei**, kein Produkt.

- Alle Angaben ohne Gewähr. Die Messwerte stammen von **einem einzigen Aufbau**
  und sind nicht unabhängig geprüft.
- Bauteile, Bezugsquellen und Preise ändern sich. Was hier steht, war zum
  Zeitpunkt des Baus richtig.
- Der Nachbau geschieht **auf eigene Gefahr und eigene Verantwortung**.
- Es gibt keine Gewährleistung, keine Zusicherung von Eigenschaften und keine
  Haftung für Schäden an Personen, Sachen oder Daten.
- Prüfe selbst, ob dein Aufbau den Vorschriften entspricht, die bei dir gelten.

Wenn du dir bei einem Schritt nicht sicher bist: frag jemanden, der es weiß.
Das gilt besonders für den Akku und für den Schacht.
