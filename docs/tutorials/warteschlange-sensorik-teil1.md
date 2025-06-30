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


## 💡 LEDs initialisieren
* ``||variables:Erstelle eine Variable...||`` und benenne sie mit **ANZAHL_LEDS**. 
Setze diese auf den Wert 9 (weil wir neun Austrittslöcher im 3D- Modell haben).
* ``||variables:Erstelle eine Variable...||`` **ERSTE_LED_POS** und initialisiere
sie mit dem Wert 2 (wir brauchen die LEDs konstruktionsbedingt 
erst ab der dritten LED).
* Beim Start initialisierst Du den LED-Streifen mit ``||neopixel:setze strip auf ...||`` an Pin P1 und 9 LEDs.
* Setze die Helligkeit mit ``||neopixel:setzeHelligkeit||`` (unter mehr) auf 255.

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

## 🔍 Helligkeit messen

Künstliches Licht (z. B. LED oder Neonröhren) kann stark Flackern, 
auch wenn wir dies nicht wahrnehmen.

Damit die Messung auch bei flackerndem Licht konstanter wird, 
hilft folgender Trick:
Miss die Helligkeit mehrmals (z. B. 10-mal) und nimm den höchsten Wert.

Baue dir mit folgenden Blöcken die Wartefunktion nach, welche im Tooltip
(die 💡Glühbirne links unten) angezeigt wird.

* ``||function:Erstelle eine Funktion||`` (im Bereich Fortgeschritten zu finden)
    * Benenne die Funktion mit "messeMax" und klicke auf **Fertig**.
    * ``||variables:Erstelle eine Variable...||`` und benenne sie mit **ANZAHL_MESSUNGEN**. 
    Initialisiere die Variable mit 10. 
    * ``||variables:Erstelle eine Variable...||`` und benenne sie mit **maximum**.
    Setze das Maximum vorerst auf 0. 
    * ``||loops:für Index von 0 bis 4||`` 
      * ersetze die 4 mit **ANZAHL_MESSUNGEN**.
    * Setze in der Schleife das **maximum** auf den Sensorwert ``||SmartfeldSensoren:gib sichtbares Licht[lm] ||``,
    aber nur dann, wenn er grösser wie das bisherige maximum ist.
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

## 👥 Figuren zählen
* Miss beim Start die Werte ohne Figuren und speichere sie in **list_leermessungen**.
* In der ``||basic:forever||``-Schleife misst du erneut. Ziehe die aktuellen Werte von den Leerwerten ab.
* Ist der Unterschied grösser als 70, wird eine Figur gezählt.
* Gib die Werte auf dem OLED-Display aus und zeige die Anzahl Figuren an.

## Gratuliere 🏆 – du hast Teil 1 abgeschlossen 🚀

[Zur Lösung](https://makecode.microbit.org/#tutorial:github:reifab/pxt-iot-tutorial/docs/tutorials/warteschlange-sensorik-teil1-solution)
