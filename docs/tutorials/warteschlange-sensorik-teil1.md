```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```
### @explicitHints false

# Warteschlange Sensorik Teil 1

## 📗 Einführung

Voraussetzungen: 🌱 IoT Basics abgeschlossen
Schwierigkeitsgrad: 🔥🔥🔥🔥

Stell dir vor, du möchtest herausfinden, wie viele Personen vor einer smarten Toilette warten – ganz ohne Kamera oder komplexe Technik.
In diesem Projekt baust du eine 🚧 Lichtschranke, die mit einfacher Sensorik auskommt: einem 💡 RGB-LED-Streifen und einem 👁️ Lichtsensor.

🔍 Das Messprinzip:
Wir beleuchten nacheinander bestimmte Positionen mit einer 💡 LED und messen mit dem 👁️ Lichtsensor, wie viel Licht jeweils reflektiert wird.
Dann schalten wir die 💡 LED kurz aus und messen erneut. Der Unterschied zeigt uns, wie stark das Licht zurückgeworfen wird. Steht etwas davor – zum Beispiel eine Lego-Figur – verringert sich die reflektierte Helligkeit.
So erkennen wir, ob an einer bestimmten Position etwas im Lichtstrahl steht.


**Was du in diesem Tutorial machst**
* Du baust eine 🚧 Lichtschranke mit 💡 RGB-LEDs und einem 👁️ Lichtsensor, richtest das System mit einem 3D-gedruckten Modell ein und 
* entwickelst ein Programm, das wartende Figuren zählt.


## 👁️ Hardware- Voraussetzungen @showdialog
* [🌍Wartebereich als Kunststoffteil:](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/3dModel/warteschlange3dViewer.html)
* Falls du das Teil 3D- Drucken möchtest, lade das STL- File hier herunter: [🌍STL-3D-Modell:](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/3dModel/Wartebereich.stl)
* Schliesse den RGB-LED-Streifen an J7 an.
* Verbinde den Lichtsensor an J0 mit dem IoT Cube.

## 🧱 LEDs initialisieren
* Lege eine Variable **ANZAHL_LEDS** mit dem Wert 9 an.
* Erstelle eine Variable **ERSTE_LED_POS** mit dem Wert 2. Damit beginnen wir bei LED 2 zu messen.
* Initialisiere den LED-Streifen mit ``||neopixel:create||`` an Pin P1 und 16 LEDs.
* Setze die Helligkeit mit ``||neopixel:set brightness||`` auf 255.

## 🔍 Helligkeit messen
* Schreibe eine Funktion **messeMax**, welche 10 Mal den Lichtsensor liest und den grössten Wert zurückgibt.
* Baue eine Funktion **messeHelligkeitsUnterschied(ledNr)**, welche die Umgebung misst, danach die LED einschaltet, erneut misst und die Differenz zurückgibt.
* In einer Funktion **messen** wird für jede verwendete LED der Unterschied gemessen und in eine Liste geschrieben.

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
