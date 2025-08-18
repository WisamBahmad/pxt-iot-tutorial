```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```
### @explicitHints false

# Smart Toilet Tutorial mit Sensor

## 📗 Einführung

**Voraussetzungen**

🌱 IoT Basics abgeschlossen und Smart Toilet Tutorial [Teil 2 - mit Internetverbindung](https://makecode.microbit.org/#tutorial:github:fave-smartfeld/pxt-smart-toilet-tutorial/docs/tutorials/smart-toilet-part2) abgeschlossen.

Schwierigkeitsgrad: 🔥🔥⚪⚪

## 👁️ Voraussetzungen @showdialog
* Schliesse den Cube so an, falls noch nicht gemacht:
![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/iot-cube-anschliessen-klein.png)
* Stelle die Schalter vorerst so ein:
    * Battery Switch: **off**
    * LoRa Module: **on**
![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/iot-cube-power-switches-klein.png)
* Ein LoRa-Gateway🛜 muss in Reichweite sein, welches mit TTN (The Things Network) verbunden ist.
Dies ist im Klassensatz einmal vorhanden und kann hunderte von IoT- Cubes bedienen.
![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/gateway-klein.png)

## Lernergebnis

Aus den vorherigen Tutorials kennst du bereits, wie man den Status der Toilette 🚽 ("Besetzt" | "Frei") per LoRa🛜 ins Internet sendet. Bisher hast du den Status durch Tastendruck ausgelöst. In einer echten Anwendung übernimmt das natürlich ein Sensor – und genau darum geht es in diesem Tutorial.

* Du baust dein kleines Toilettenhäuschen-Modell jetzt mit einem Taster (Micro Switch) aus.

Am Ende hast du ein Programm, das …

* eine LoRa-Verbindung🛜 aufbaut
* den Status der Toilette 🚽 über den Taster erkennt
* den Status 🚽 über LoRa🛜 ins Internet sendet

Das vollständige Programm aus Teil 2 ist schon integriert. Falls dir etwas unklar ist, lohnt es sich, diesen Teil noch einmal durchzuarbeiten.

## Hardware vorbereiten
* Du bekommst dieses WC- Häuschen- Modell: ![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/wc-haus.png)
* Du brauchst zusätzlich dieses Kunststoffteil: [Taster- Aufnahme](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/3dModel/taster-halterung.html)
* Falls du das Teil selbst 3D- Drucken möchtest, lade das STL- File hier herunter: [🌍STL-3D-Modell](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/3dModel/taster-halterung.stl)
* Schliesse den Grove Micro Switch an **JX** an.

## Gratuliere 🏆 - du hast das Tutorial erfolgreich bearbeitet 🚀

* Verbinde deine Smarte Toilette mit dem Toiletten Widget der [Claviscloud](https://iot.claviscloud.ch/)! 
* Teste, ob die Daten korrekt angezeigt werden!
* Falls irgendwas noch nicht richtig läuft, hier hast Du eine funktionierende Version zum Testen: [Lösung](https://makecode.microbit.org/#tutorial:github:reifab/pxt-smart-toilet-tutorial/docs/tutorials/smart-toilet-part3-solution)

```template
function macheFrei () {
    statusFreiOderBesetzt = 1
    basic.showLeds(`
        . . # . .
        . # # # .
        # . # . #
        . . # . .
        . . # . .
        `)
    sendeDaten(statusFreiOderBesetzt)
}
function macheBesetzt () {
    statusFreiOderBesetzt = 0
    basic.showLeds(`
        . . . . #
        . . . . #
        . . . . #
        # # # # #
        . # # # .
        `)
    sendeDaten(statusFreiOderBesetzt)
}
input.onButtonPressed(Button.A, function () {
    macheBesetzt()
})
function sendeDaten (status: number) {
    if (control.millis() > msBeiLetztemSenden + 6000) {
        IoTCube.addBinary(eIDs.ID_0, status)
        IoTCube.SendBufferSimple()
        music.play(music.tonePlayable(262, music.beat(BeatFraction.Whole)), music.PlaybackMode.UntilDone)
        spaeterSenden = false
        msBeiLetztemSenden = control.millis()
    } else {
        spaeterSenden = true
    }
}
input.onButtonPressed(Button.B, function () {
    macheFrei()
})
let statusFreiOderBesetzt = 0
let spaeterSenden = false
let msBeiLetztemSenden = 0
IoTCube.LoRa_Join(
eBool.enable,
eBool.enable,
10,
8
)
while (!(IoTCube.getStatus(eSTATUS_MASK.JOINED))) {
    basic.showLeds(`
        # . # . #
        # . # . #
        # # # # #
        . . # . .
        . . # . .
        `)
    basic.pause(1000)
}
basic.showIcon(IconNames.Yes)
basic.pause(5000)
msBeiLetztemSenden = control.millis()
spaeterSenden = false
macheFrei()
loops.everyInterval(500, function () {
    if (spaeterSenden) {
        sendeDaten(statusFreiOderBesetzt)
    }
})
```