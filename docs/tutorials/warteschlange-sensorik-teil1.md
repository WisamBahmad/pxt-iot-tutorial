```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
neopixel=github:microsoft/pxt-neopixel#v0.7.6
```
### @explicitHints false

# Warteschlange Sensorik Teil 1

## 📗 Einführung

Stell dir vor, du möchtest automatisiert herausfinden, wie viele Personen vor 
einer smarten Toilette warten. In diesem Projekt baust du eine neunkanalige 
Lichtschranke💡👁️, die mit relativ einfacher Technik auskommt: 
einem RGB-LED-Streifen💡 und einem Lichtsensor 👁️.

* Voraussetzungen: 🌱 IoT Basics abgeschlossen
* Schwierigkeitsgrad: 🔥🔥🔥🔥


## 🎬 Messprinzip 

Wir beleuchten nacheinander bestimmte Positionen mit einer LED💡und messen 
mit dem Lichtsensor👁️, wie viel Licht jeweils ankommt.
Steht etwas zwischen Sensor👁️ und LED💡, zum Beispiel eine Lego-Figur🦹‍♂️, 
verringert sich die Helligkeit.
So erkennen wir, ob an einer bestimmten Position etwas im Lichtstrahl steht.
Die Messungen werden an neun Positionen in der Warteschlange nacheinander 
vorgenommen, sodass dieL ego-Figuren🦹‍♂️gezählt werden können.

Schau Dir dieses Video an, welches das Messprinzip illustriert:
* [Video🎬 ansehen: Warteschlange Sensorik](https://wiki.smartfeld.ch/lib/exe/fetch.php?media=warteschlange_sensorik.mp4)

## 👁️ Hardware- Voraussetzungen @showdialog
* [Wartebereich als Kunststoffteil:](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/3dModel/warteschlange3dViewer.html)
* Falls du das Teil 3D- Drucken möchtest, lade das STL- File hier herunter: [🌍STL-3D-Modell:](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/3dModel/Wartebereich.stl)
* Schliesse den RGB-LED-Streifen💡 an **J7** an.
* Verbinde den Lichtsensor👁️ an **J0** mit dem IoT Cube.
* Schliesse das OLED- Display 🖥️  an **J5** an. 


## 💡 LEDs in Betrieb nehmen

Wir wollen zu Beginn die erste LED, welche im 3D- Modell hinter einem Loch
hervorscheint blinken lassen. Auf dem 16er- LED streifen wäre dies die LED
mit Index **2**.

* ``||basic:beim Start||`` initialisierst Du den LED-Streifen mit ``||neopixel:setze strip auf NeoPixels an Pin||`` **P1** mit **16** Pixeln und im Modus RGB.
* Unter **...mehr** findest du den Block ``||neopixel:setzeHelligkeit||``, womit du die Helligkeit auf 255 setzt.
* In der ``dauerhaft``- Schleife verwendest Du den Block ``||neopixel:strip setze Farbe von Neopixel||`` **2** auf **schwarz**  (unter **...mehr**)
* darunter verwendest Du den Block ``||neopixel:strip anzeigen||``
* ``||basic:pausiere (ms)||`` nach dem Anzeigen für 100 ms
* danach den Block ``||neopixel:strip setze Farbe von Neopixel||`` **2** auf **weiss**  (unter **...mehr**)
* darunter verwendest Du den Block ``||neopixel:strip anzeigen||``
* ``||basic:pausiere (ms)||`` nach dem Anzeigen für 200 ms
* 📥 Drücke `|Download|` und kontrolliere, ob die LED 2 (erste LED in der Warteschlange) 
blinkt.

```blocks
let strip = neopixel.create(DigitalPin.P1, 16, NeoPixelMode.RGB)
strip.setBrightness(255)
basic.forever(function () {
    strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Black))
    strip.show()
    basic.pause(200)
    strip.setPixelColor(2, neopixel.colors(NeoPixelColors.White))
    strip.show()
    basic.pause(200)
})
```

## 🔍 Helligkeit messen und anzeigen auf OLED- Display 

Nun messen wir mit dem Sonnenlichtsensor 👁️ zweimal die Helligkeit,
einmal mit LED- Beleuchtung und einmal ohne (nur Fremdlicht) und zeigen
die Werte auf dem Display an.

* ``||basic:beim Start||`` initialisierst du das Display mit 🖥️ ``||SmartfeldAktoren:init OLED Breite 128 Höhe 64||`` 
*  ``||basic:beim Start||`` initialisierst Du den Sonnenlichtsensor mit ``||SmartfeldSensoren:init Sonnenlicht Sensor||``
* Setze den Block 🖥️ ``||SmartfeldAktoren:Lösche Displayinhalt||``
zuoberst in der Dauerhaft-Schleife ein.
*  ``||variables:Erstelle eine Variable...||`` und benenne sie mit **h_umgebung**.
* In der ``dauerhaft`` - Schleife verwendest du den Block ``||variables:setze lichtmessung auf 0||``
* ersetze die 0 durch eine Messung mit ``||SmartfeldSensoren:gib sichtbares Licht [lm]||`` und
führe diese Messung direkt nach dem ersten **strip anzeigen** durch.
* Unter der Messung setzt du den Block ``||SmartfeldAktoren:schreibe Nummer||``
ein. 
* Ersetze die 0 mit der Variable ``||variables:h_umgebung||``
* Setze den Block  ``||SmartfeldAktoren:schreibe String "-" ||``ein, um einen Bindestrich
(als Trennzeichen zum nächsten Messwert) auf dem 🖥️ Display auszugeben.
* ``||variables:Erstelle eine Variable...||`` und benenne sie mit **h_mitLED**.
* Wiederhole die Messung sowie die Anzeige unter Verwendung der Variable **h_mitLED**. Die Messung kannst Du nach dem zweiten **strip anzeigen** einfügen. 
* 📥 Drücke `|Download|` und beobachte die Werte auf dem Display. Beantworte
für dich folgende Fragen:
  * Wie gross ist der Unterschied der Messwerte (Umgebungungslicht - Licht mit LED)
  * Wie stark lassen sich die Werte von Fremdlicht beeinflussen?
  * Wie stark streuen die Messwerte bei scheinbar konstantem Fremdlicht?

```blocks
let h_umgebung = 0
let h_mitLED = 0
// @highlight
smartfeldSensoren.initSunlight()
// @highlight
smartfeldAktoren.oledInit(128, 64)
let strip = neopixel.create(DigitalPin.P1, 16, NeoPixelMode.RGB)
strip.setBrightness(255)
basic.forever(function () {
    // @highlight
    smartfeldAktoren.oledClear()
    strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Black))
    strip.show()
    // @highlight
    h_umgebung = smartfeldSensoren.getHalfWord_Visible()
    // @highlight
    smartfeldAktoren.oledWriteNum(h_umgebung)
    // @highlight
    smartfeldAktoren.oledWriteStr("-")
    basic.pause(200)
    strip.setPixelColor(2, neopixel.colors(NeoPixelColors.White))
    strip.show()
     // @highlight
    h_mitLED= smartfeldSensoren.getHalfWord_Visible()
    // @highlight
    smartfeldAktoren.oledWriteNum(h_mitLED)
    basic.pause(200)
})
```

## 🔍 Messfunktion (messeMax) für Mehrfachmessung erstellen

Nach diesen Fragestellungen hast du wohlmöglich bemerkt, dass
  * die Einzelmessungen auch unter gleichen Bedingungen 
  relativ stark variieren, ca. +/- 40 Lumen
  * Erhöht man das Fremdlich, erhöht sich der Hell- sowie der Dunkelwert inetwa gleich

Gründe für die Unterschiede: Künstliches Licht (z. B. LEDs oder Neonröhren) 
kann stark Flackern, auch wenn wir dies nicht wahrnehmen.

Damit die Messung auch bei flackerndem Licht konstanter wird, 
hilft folgender Trick:
* Helligkeit mehrmals messen (z. B. 10-mal) und höchsten Wert bestimmen.

Du baust jetzt diese Messfunktion nach, welche Dir im Tooltip
(die 💡Glühbirne links unten) angezeigt wird.

* ``||functions:Erstelle eine Funktion...||`` (im Bereich Fortgeschritten zu finden)
    * Benenne die Funktion mit **messeMax** und klicke auf **Fertig**.
    * ``||variables:Erstelle eine Variable...||`` und benenne sie mit **ANZAHL_MESSUNGEN**. 
    Initialisiere die Variable mit 10. 
    * ``||variables:Erstelle eine Variable...||`` und benenne sie mit **maximum**.
    Setze das Maximum vorerst auf 0. 
    * ``||loops:für Index von 0 bis 4||`` 
      * ersetze die 4 mit **ANZAHL_MESSUNGEN**.
    * Bestimme mit dem Block ``||math:maximal||`` den grösseren der beiden 
    Werte **maximum** oder dem Sensorwert 
    ``||SmartfeldSensoren:gib sichtbares Licht[lm] ||``
    und überschreibe damit die Variable **maximum**. Schau Dir den Tooltip 💡
    dafür an.
    * gib am Schluss das gerundete **maximum** zurück mithilfe den Blöcken ``||functions:0 zurückgeben||`` und ``||math:runden||``

```blocks
function messeMax () {
    ANZAHL_MESSUNGEN = 10
    maximum = 0
    for (let index = 0; index < ANZAHL_MESSUNGEN; index++) {
        maximum = Math.max(maximum, smartfeldSensoren.getHalfWord_Visible())
    }
    return Math.round(maximum)
}
```

## 🔍 Mehrfachmessung (Funktion messeMax) einsetzen und testen

* Ersetze in der ``dauerhaft`` - Schleife die zwei Blöcke ``||SmartfeldSensoren:gib sichtbares Licht [lm]||``
  druch je einen Fuktionsaufruf ``||functions:Aufruf messeMax||``
* 📥 Drücke `|Download|` und beobachte die Werte auf dem Display. Beantworte
für dich folgende Fragen:
  * Sind die Messwerte gegenüber vorher konstanter?
  * Wie stark variieren die Werte noch bei gleichen Bedingungen?

```blocks
// @hide
function messeMax () {
    ANZAHL_MESSUNGEN = 10
    maximum = 0
    for (let index = 0; index < ANZAHL_MESSUNGEN; index++) {
        if (smartfeldSensoren.getHalfWord_Visible() > maximum) {
            maximum = Math.max(maximum, smartfeldSensoren.getHalfWord_Visible())
        }
    }
    return Math.round(maximum)
}

let h_mitLED = 0
let h_umgebung = 0
let maximum = 0
let ANZAHL_MESSUNGEN = 0
smartfeldSensoren.initSunlight()
smartfeldAktoren.oledInit(128, 64)
let strip = neopixel.create(DigitalPin.P1, 16, NeoPixelMode.RGB)
strip.setBrightness(255)

basic.forever(function () {
    smartfeldAktoren.oledClear()
    strip.setPixelColor(2, neopixel.colors(NeoPixelColors.Black))
    strip.show()
    // @highlight
    h_umgebung = messeMax()
    smartfeldAktoren.oledWriteNum(h_umgebung)
    smartfeldAktoren.oledWriteStr("-")
    basic.pause(200)
    strip.setPixelColor(2, neopixel.colors(NeoPixelColors.White))
    strip.show()
    // @highlight
    h_mitLED = messeMax()
    smartfeldAktoren.oledWriteNum(h_mitLED)
    basic.pause(200)
})
```

## Elemente für Später
* ``||variables:Erstelle eine Variable...||`` und benenne sie mit **ANZAHL_LEDS**. 
Setze diese auf den Wert 9 (weil wir neun Austrittslöcher im 3D- Modell haben).
* ``||variables:Erstelle eine Variable...||`` **ERSTE_LED_POS** und initialisiere
sie mit dem Wert 2 (wir brauchen die LEDs konstruktionsbedingt 
erst ab der dritten LED).

```blocks
{
let strip: neopixel.Strip = null
let ERSTE_LED_POS = 0
let ANZAHL_LEDS = 0
ANZAHL_LEDS = 9
ERSTE_LED_POS = 2
strip = neopixel.create(DigitalPin.P1, 16, NeoPixelMode.RGB_RGB)
strip.setBrightness(255)
}
```

## 👥 Figuren zählen
* Miss beim Start die Werte ohne Figuren und speichere sie in **list_leermessungen**.
* In der ``||basic:forever||``-Schleife misst du erneut. Ziehe die aktuellen Werte von den Leerwerten ab.
* Ist der Unterschied grösser als 70, wird eine Figur gezählt.
* Gib die Werte auf dem OLED-Display aus und zeige die Anzahl Figuren an.

## Gratuliere 🏆 – du hast Teil 1 abgeschlossen 🚀

[Zur Lösung](https://makecode.microbit.org/#tutorial:github:reifab/pxt-iot-tutorial/docs/tutorials/warteschlange-sensorik-teil1-solution)


