# Inbetriebnahme

> **Hier steht:** Alles, was genau einmal gemacht wird — erster Flash, Home
> Assistant einrichten, kalibrieren.
> **Hier steht nicht:** Der laufende Betrieb. Der steht in
> [`betrieb.md`](betrieb.md).

Diese Seite liest man einmal von oben nach unten.

---

## Voraussetzung: ESPHome ab 2026.8.0

```bash
esphome version
```

**Geh nicht auf eine ältere Version zurück.** Sonst bleibt kein einziges Update
über Funk auf dem Gerät. Warum, steht im [FAQ](faq.md). Entwickelt wurde mit
2026.9.0.

---

## 1. Zugangsdaten anlegen

```bash
cp secrets.yaml.example secrets.yaml
```

Dann ausfüllen. Den API-Schlüssel erzeugst du mit:

```bash
openssl rand -base64 32
```

`secrets.yaml` steht in `.gitignore` und darf dort auch bleiben.

---

## 2. Die drei Werte anpassen

In `zisterne.yaml` ganz oben unter `substitutions:`

| Wert | Bedeutung |
|---|---|
| `flaeche_m2` | Grundfläche der Zisterne in Quadratmetern. **Nachmessen.** |
| `max_hoehe_m` | Maximale Wasserhöhe in Metern. Muss zum Messbereich der Sonde passen. |
| `sonden_offset_m` | Wie hoch die Sonde über dem Boden hängt. **Beim Testen auf 0 lassen.** |

Der Offset ist die häufigste Stolperfalle — er wird auf den Messwert addiert.
Steht dort schon die geplante Aufhängehöhe, während die Sonde noch im Eimer
liegt, zeigt sie genau diesen Wert zu viel. Siehe [FAQ](faq.md).

**Optional, spart Wachzeit:** die feste IP unter `wifi: manual_ip:` eintragen.
Das spart bei jedem Aufwachen zwei bis drei Sekunden und damit Akku.

---

## 3. Der erste Flash

Der erste Flash muss über USB laufen — vorher gibt es das Gerät im Netz noch
nicht. Und im Tiefschlaf schaltet der XIAO seinen USB-Anschluss ab.

Also:

1. USB-C anstecken
2. **BOOT** halten, **RESET** kurz tippen, **BOOT** loslassen
3. Port suchen: `ls /dev/cu.*`
4. Flashen:

```bash
esphome run zisterne.yaml --device /dev/cu.usbmodemXXXX --no-logs
```

`--device` zwingt ESPHome auf USB statt auf Funk. `--no-logs` spart dir ein
Log-Fenster, das beim nativen USB nach jedem Reset ohnehin abbricht.

> **„Das Board bootet nicht mehr"** ist fast immer ein Irrtum: Es **schläft**.
> Im Tiefschlaf sind LED und USB-Port aus. Mehr dazu in
> [`fehlersuche.md`](fehlersuche.md).

---

## 4. Den Helfer in Home Assistant anlegen

Einstellungen → Geräte & Dienste → **Helfer** → Helfer erstellen →
**Umschalter** → Name `Zisterne wach halten`.

Daraus wird die Entität, die in `zisterne.yaml` steht. Sie ist der einzige Weg,
das Gerät wach zu halten.

> Gibt es den Helfer nicht, bleibt der Zustand „aus" und das Gerät schläft
> normal weiter. Das ist die sichere Richtung — ein fehlender Helfer kann den
> Akku nicht leeren.

---

## 5. Das Gerät in Home Assistant einrichten

Hier gibt es ein Henne-Ei-Problem: Das Gerät ist nur rund **neun Sekunden pro
Stunde** online. In diesem Fenster findet Home Assistant es weder automatisch,
noch lässt es sich einrichten.

**Lösung: einmal eine Firmware flashen, die gar nicht schlafen kann.**

1. In `zisterne.yaml` auskommentieren: der ganze `deep_sleep:`-Block und die
   beiden Schlaf-Aufrufe im Skript `schlafen_gehen`.
2. Flashen. Das Gerät bleibt jetzt dauerhaft online.
3. In Home Assistant: Integration hinzufügen, IP und Port `6053`, API-Schlüssel
   aus `secrets.yaml`.
4. **Helfer `Zisterne wach halten` auf AN.** Der Zustand liegt in Home
   Assistant, nicht auf dem Gerät — ein Flash kann ihn nicht verlieren.
5. Alles wieder einkommentieren, per Funk flashen. Das Gerät liest den Helfer
   beim Hochfahren und bleibt wach.
6. Helfer auf **AUS**, dann den Knopf **Messen und syncen** drücken.

> **Es reicht nicht, nur die Schlafsperre zu setzen.** Es gibt zwei Wege in den
> Tiefschlaf, und die Sperre blockiert nur einen davon. Deshalb muss der Block
> wirklich auskommentiert werden. Details im [FAQ](faq.md).

---

## 6. Pegel kalibrieren

Erst wenn die Sonde eingebaut und aufgehängt ist:

1. Echte Wasserhöhe mit dem Zollstock messen.
2. Wert von `Zisterne Pegel` in Home Assistant ablesen.
3. Differenz in `sonden_offset_m` eintragen.
4. **Volumen prüfen:** 100 Liter entnehmen, der Wert muss um 100 Liter fallen.
   Wenn nicht, `flaeche_m2` korrigieren.

---

## 7. Akkuspannung kalibrieren

Der Spannungsteiler wird mit einem festen Faktor umgerechnet. Der Nennwert
stimmt nicht: Zwei Widerstände mit je 1 % Toleranz können zusammen 2 %
danebenliegen — bei 4,1 V sind das 80 mV.

**Ablauf:**

1. **USB abziehen.** Sonst misst du die Ladespannung.
2. Helfer `Zisterne wach halten` auf **AN**, dann das nächste Aufwachen
   abwarten.
3. Knopf **Messen und syncen** drücken. Der misst auch den Akku, damit Sensor
   und Multimeter zur selben Minute gehören. Helfer auf AN lassen, sonst
   schläft das Gerät danach.
4. Multimeter an die Akkupads, Gleichspannung, drei Nachkommastellen.
5. Rechnen:

```
neuer Faktor = Multimeter / (Sensorwert / alter Faktor)
```

6. Den Wert in `zisterne.yaml` eintragen und per Funk flashen.

**Mach eine Gegenprobe** mit einem zweiten Wertepaar. Liegen beide Ergebnisse
dicht beieinander, ist es ein echter systematischer Fehler und kein
Zufallstreffer. Ein Beispiel mit Zahlen steht in [`messungen.md`](messungen.md).

> **Der Faktor verschiebt auch die Akkuwarnung.** Zeigte der Sensor vorher zu
> wenig an, schlug die Warnschwelle zu früh an — nach der Korrektur schlägt sie
> beim richtigen Wert an. Wer mehr Reserve will, stellt die Schwelle höher.

**Nach jedem Tausch der Teilerwiderstände neu kalibrieren.**

---

## Danach

Alles Weitere — Messintervall, Nachtruhe, Updates über Funk, Warnungen — steht
in [`betrieb.md`](betrieb.md).

Was noch zu prüfen ist, steht in [`pruefplan.md`](pruefplan.md).
