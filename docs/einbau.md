# Einbau

> **Hier steht:** Wo die fertige Box hinkommt und wie die Sonde hängt.
> **Hier steht nicht:** Was in die Box kommt und wie es darin befestigt wird —
> das ist [`bauanleitung.html`](bauanleitung.html), Schritt 7 und 8.

**Lies vorher [`../SICHERHEIT.md`](../SICHERHEIT.md).** Der Schacht ist das
Gefährlichste an diesem Projekt, nicht die Elektronik.

---

## Wo die Box hinkommt

Oben auf dem Schacht sitzt meist ein Teleskop aus Kunststoff, darunter beginnt
der Betonkonus.

**Der Beton schirmt WLAN vollständig ab.** Gemessen: einen Meter tief kein
Empfang mehr. Die Box muss also in den Kunststoffbereich.

**Sie wird an die senkrechte Innenwand geschraubt, so hoch wie möglich.**

Warum so hoch, obwohl unten mehr Platz wäre: Außen um den Schacht liegt feuchte
Erde, und die dämpft 2,4 GHz stark. Je höher die Box sitzt, desto kürzer ist
der Weg durch die Erde.

**Ziel: Oberkante Box etwa 40 mm unter der tiefsten Deckelrippe.**

```
   ┌───── Kunststoffdeckel ─────┐   ← nichts dran, nicht bohren
   │  (Rippen ragen nach unten) │
   │                            │
   │  [Winkel]═[BOX]═[Winkel]   │   ← hier, im Kunststoffbereich
   │   Antenne INNEN, senkrecht │
   ╞════════════════════════════╡   ← ab hier Beton, Funk tot
   │         │                  │
   │         │ Sondenkabel      │
   │      [Dübel = Zugentlastung]
   │                            │
```

Drei Vorteile dieser Stelle: bestes Signal, kein Verlängerungskabel für die
Antenne nötig, und du kommst zum jährlichen Laden bequem heran.

---

## Befestigen ohne Loch im Gehäuse

Nimm **Außenbefestigungslaschen**, die außen an den Gehäuseecken einrasten.
Damit bleibt die Dichtheit der Box unangetastet — kein Loch, keine zusätzliche
Dichtstelle.

Die Laschen mit M4-Schrauben, Scheibe und Mutter **durch** die Kunststoffwand.
Keine Blechschrauben, die Wand ist zu dünn.

**Zwei Details beim Bohren:**

- **Nicht im Überlappungsbereich bohren.** Ein Teleskopschacht ist ein
  Schiebeprofil. Voll ausgezogen sitzt die Überlappung ganz unten — oben
  bohren ist also sicher.
- **EPDM-Dichtscheiben unter die Schraubenköpfe.** Außen liegt Erde, sonst
  sickert dort Wasser ein.

---

## Die Antenne bleibt in der Box

Die Box ist aus Kunststoff, da geht 2,4 GHz problemlos durch. Kein Durchbruch
für eine Außenantenne, keine zusätzliche Dichtstelle.

**Senkrecht an die obere Innenwand legen, mindestens 10 mm Abstand zu Platine
und Akku.**

Senkrecht ist nicht Geschmackssache: Accesspoints senden fast immer senkrecht
polarisiert. Eine schräg liegende Antenne verliert spürbar.

Welche Anschlüsse die Antenne umschalten, steht in
[`verdrahtung.md`](verdrahtung.md). Was das bringt — 11 dB — steht in
[`messungen.md`](messungen.md).

---

## Fünf Regeln beim Montieren

1. **Verschraubungen zeigen nach unten.** Unter dem Deckel tropft
   Kondenswasser.
2. **Luft zum Deckel lassen.** Ein befahrbarer Deckel federt unter Last durch.
   Die Box darf ihn nicht berühren.
3. **Vor dem Bohren nochmal messen.** Halte den fertigen Aufbau genau an die
   geplante Stelle, Deckel zu, und schau auf den WLAN-Wert. Erst wenn der
   passt, wird gebohrt. Das ist Test 1 aus [`pruefplan.md`](pruefplan.md).
4. **Sondenkabel separat zugentlasten.** Ein Dübel im Beton, Kabel mit einer
   Schelle daran. Die Box soll nur sich selbst tragen, nicht das Kabel.
5. **Nicht dort montieren, wo du beim Arbeiten hineingreifst**, und nicht im
   Weg von Schlauch und Pumpenseil.

---

## Die Sonde aufhängen

1. **Etwa 10 cm über dem Boden**, nicht in den Schlamm.
2. **Nicht in den Zulaufstrahl** und nicht direkt neben die Pumpe.
3. Optional ein senkrechtes Rohr als Beruhigungsrohr, unten gelocht.
4. **Die Sonde darf nicht am Kabel hängen.** Zug quetscht die Luft-Kapillare
   im Kabel, und dann misst sie falsch. Häng sie an ein Edelstahlseil, so wie
   eine Pumpe am Seil hängt.

> **Die Kapillare ist der empfindlichste Teil des ganzen Aufbaus.** Sie darf
> nicht abgeschnitten, geknickt, verklebt oder vergossen werden. Sie sorgt
> dafür, dass die Sonde gegen den aktuellen Luftdruck misst — ohne sie
> verschiebt jeder Wetterwechsel die Anzeige um bis zu 30 cm.

---

## Nach dem Einbau

**Kalibrieren.** Echte Wasserhöhe mit dem Zollstock messen und den Offset
setzen — Ablauf in [`inbetriebnahme.md`](inbetriebnahme.md).

**Und dann ist Schluss mit USB.** Ab jetzt läuft jedes Update über Funk. Wenn
Test 10 aus [`pruefplan.md`](pruefplan.md) nicht dreimal sauber durchgelaufen
ist, mach das **vor** dem Zuschrauben.
