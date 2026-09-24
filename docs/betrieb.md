# Betrieb

> **Hier steht:** Was du am laufenden Gerät machst — Intervall ändern, wach
> halten, Updates fahren, Warnungen einrichten.
> **Hier steht nicht:** Die einmalige Einrichtung. Die steht in
> [`inbetriebnahme.md`](inbetriebnahme.md).

Diese Seite schlägt man nach.

---

## Die zwei Bedienelemente

Mehr gibt es nicht, und mehr braucht es nicht.

| | Was es ist | Wann es wirkt |
|---|---|---|
| Helfer **`Zisterne wach halten`** | ein **Zustand** in Home Assistant | beim nächsten Aufwachen |
| Knopf **`Messen und syncen`** | eine **Handlung** | sofort |

**Der Helfer sagt, was gelten soll. Der Knopf wendet es jetzt an.**

Das Gerät ist rund **neun Sekunden pro Stunde** erreichbar. Etwas *auf* dem
Gerät umzulegen hieße, genau dieses Fenster zu treffen — deshalb liegt der
Zustand in Home Assistant. Du legst ihn um, wann du willst; beim nächsten
Aufwachen liest der ESP ihn aus.

### Was der Knopf tut

| Helfer | Was passiert |
|---|---|
| **AN** | misst, bleibt wach, das 30-Minuten-Fenster startet **neu** |
| **AUS** | misst, schickt die Werte raus, **geht schlafen** |

Er führt denselben Ablauf aus wie ein Aufwachen: messen, Werte rausschicken,
dann nach dem Helfer richten.

> **Bei Helfer AUS ist das ein Einwegknopf.** Das Gerät ist danach weg, und der
> nächste Druck geht erst beim nächsten Aufwachen. Das ist Absicht — so kommst
> du ohne zweiten Knopf sofort zurück in den Stundentakt.

### Sicherheitsnetz

Bleibt der Helfer versehentlich auf AN, schläft das Gerät nach **30 Minuten**
trotzdem ein. Ohne das wäre der Akku in wenigen Tagen leer.

---

## Messintervall ändern

Der Regler **`Zisterne Messintervall`** legt fest, wie lange das Gerät zwischen
zwei Messungen schläft. Einstellbar von **5 bis 720 Minuten** in Fünferschritten.

Gedacht für den Jahresverlauf: im Sommer häufiger messen, weil sich der
Füllstand schnell ändert, im Winter seltener.

**Der neue Wert greift ab dem nächsten Einschlafen.**

### Was das kostet

Der Schlafstrom läuft immer mit, ist aber klein. Jeder **Weckvorgang** kostet
rund 0,2 mAh. Bei einem Akku mit 2000 mAh ergibt das:

| Intervall | Weckvorgänge pro Tag | Verbrauch pro Tag | Laufzeit |
|---|---|---|---|
| 15 min | 96 | 19,2 mAh | ~3,5 Monate |
| 30 min | 48 | 9,8 mAh | ~7 Monate |
| **60 min** | 24 | **5,1 mAh** | **~13 Monate** |
| **60 min + Nachtruhe** | **16** | **3,6 mAh** | **~18 Monate** |
| 120 min | 12 | 2,7 mAh | ~2 Jahre |
| 360 min | 4 | 1,2 mAh | ~4,5 Jahre |

**Die Wachzeit dominiert alles.** Der Schlaf frisst nur rund 8 % — wer Laufzeit
braucht, dreht am Intervall, nicht am Schlafstrom.

### Einstellen ist fummelig

Das Gerät ist nur während seiner Wachzeit erreichbar. Also:

1. Helfer `Zisterne wach halten` auf **AN**, warten bis es aufwacht
2. Intervall setzen
3. Helfer auf **AUS**, dann Knopf **Messen und syncen**

Oder eine Automation, die beim Verfügbarwerden des Geräts feuert und den Wert
setzt — zum Beispiel saisonal am 1. April und 1. Oktober.

---

## Nachtruhe

Zwischen **21 und 6 Uhr** wird nicht gemessen. Das spart acht der
vierundzwanzig Weckvorgänge und bringt rund **fünf Monate** Laufzeit.

Eingestellt wird es oben in `zisterne.yaml`:

```yaml
nacht_von: "21"            # ab dieser vollen Stunde ist Ruhe
nacht_weckzeit: "06:00:00" # dann wird wieder gemessen
```

**Abschalten:** `nacht_von: "24"`. Dann ist die Bedingung nie wahr und das
Gerät läuft rund um die Uhr.

Die Weckzeit ist die **einzige** Quelle für das Ende des Fensters — das Skript
liest die Stunde aus derselben Zeichenkette, die auch an die Weckfunktion geht.
Es gibt nichts doppelt zu pflegen.

> Das Gerät misst **zuerst** und entscheidet **danach**. Die letzte Messung des
> Tages kommt also kurz *nach* 21 Uhr, beim ersten Aufwachen im Fenster.

### Nebeneffekt: der Rhythmus wird jede Nacht nachgestellt

Der Tiefschlaf ist eine Stoppuhr, keine Uhr. Ein Zyklus dauert die eingestellte
Zeit **plus** die Wachzeit, und der interne Oszillator geht rund 1 % falsch.
Die Weckzeit wandert dadurch täglich ein paar Minuten nach hinten.

Die Nachtruhe rechnet dagegen gegen die **echte Uhrzeit**. Um 6 Uhr steht der
Rhythmus also wieder exakt:

```
06:00:00  Anker, exakt
07:00:09
08:00:18
...
21:02:15  letzte Messung, dann Nachtruhe
06:00:00  Anker, exakt
```

Ohne Nachtruhe würde die Abweichung immer weiterwachsen. Zahlen in
[`messungen.md`](messungen.md).

---

## Firmware aktualisieren

Das Gerät schläft und ist fast immer offline. Der Ablauf:

1. Helfer **`Zisterne wach halten`** auf AN
2. Warten, bis das Gerät das nächste Mal aufwacht (höchstens ein Intervall).
   Es bleibt dann bis zu 30 Minuten wach.
3. `esphome run zisterne.yaml`
4. Helfer wieder auf **AUS**, dann Knopf **Messen und syncen**

**Kein Handanlegen, kein USB, kein Deckel öffnen.** Ein Update dauert rund fünf
Sekunden.

**Findet ESPHome das Gerät nicht?** Läuft auf dem Board gerade eine andere
Konfiguration, sucht es unter dem falschen Namen. Dann die IP direkt angeben:

```bash
esphome run zisterne.yaml --device 192.168.x.y
```

### Während eines Updates kann nichts einschlafen

Ein halb geschriebener Flash wäre der schlimmste Fall — nach dem Einbau gibt es
keinen Knopf mehr am Gerät. Deshalb sind **beide** Wege in den Tiefschlaf
gesperrt, solange geschrieben wird. Nachgewiesen in
[`messungen.md`](messungen.md).

### Die zwei Wege in den Tiefschlaf

Wichtig für jeden, der an der Konfiguration schraubt:

| Weg | Wird ausgelöst durch | Sperre wirkt? |
|---|---|---|
| automatisch | die Wachzeit-Notbremse läuft ab | **ja** |
| manuell | ein ausdrückliches „jetzt schlafen" | **nein** |

Der Grund steht in einer einzigen Zeile in ESPHomes Quelltext:

```cpp
void DeepSleepComponent::begin_sleep(bool manual) {
  if (this->prevent_ && !manual) { ... return; }
```

Die Sperre wird also ausdrücklich nur beim automatischen Weg geprüft.

**Wer das Gerät zuverlässig wach halten will, muss beide Wege schließen.** Am
sichersten ist, die Schlafkomponente ganz auszukommentieren. Genau daran ist
im Projekt schon zweimal etwas gescheitert.

---

## Warnungen einrichten

Zwei Automationen:

| Sensor | Schwelle | Benachrichtigung |
|---|---|---|
| Akkuspannung | unter 3,5 V | „Zisternen-Akku laden" |
| Box-Feuchte | über 60 % | „Silica-Gel wechseln" |

Den genauen Entitätsnamen zeigt Home Assistant an. Er setzt sich aus dem
**Gerätenamen in HA** und dem kurzen Namen aus der Konfiguration zusammen —
siehe [FAQ](faq.md), falls dort etwas doppelt steht.

**Die Feuchte-Warnung ist die wichtigere von beiden.** Ein leerer Akku ist
ärgerlich, eine nasse Platine ist kaputt.

### Was die Box-Feuchte bedeutet

| Feuchte | Bedeutung |
|---|---|
| unter 40 % | alles gut |
| 40 bis 60 % | Gel arbeitet, im Auge behalten |
| über 60 % | **Gel wechseln**, Dichtungen prüfen |

---

## Jahreswartung

Einmal im Jahr:

1. **Akku laden.** Über USB-C am XIAO. **Drinnen laden**, nicht im Schacht —
   unter 0 °C nimmt die Zelle dauerhaft Schaden. Siehe
   [`../SICHERHEIT.md`](../SICHERHEIT.md).
2. **Silica-Gel wechseln.** Spätestens, wenn die Feuchtewarnung kommt.
3. **Dichtungen anschauen**, solange die Box offen ist.
