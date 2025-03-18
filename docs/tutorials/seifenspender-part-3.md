```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```
### @explicitHints false

# IoT Tutorial Teil 3


## 📗 Einführung, Teil 3

Vorraussetzungen: 🌱 IoT Basics abgeschlossen und IoT Tutorial [Teil 2](https://makecode.microbit.org/#tutorial:github:reifab/pxt-iot-tutorial/docs/tutorials/seifenspender-part-2) abgeschlossen.
Schwierigkeitsgrad: 🔥🔥⚪⚪

Aus dem Tutorial Teil 2 hast du bereits ein Programm, das den Seifenstand simuliert
und über LoRa🛜 ins Internet sendet. Um der realen Anwendung näher zu kommen, 
erweiterst Du den IoT Cube um einen Ultraschallsensor, der den Seifenstand misst.

Am Schuluss hast du ein Programm, welches...

* Den Seifenstand mit einem Ultraschallsensor🦇 misst.
* Eine LoRa-Verbindung🛜 aufbaut. 
* Den Seifenstand🧼 über LoRa🛜 sendet. 
* Eine Ladebalken-Animation⏳ für Wartezeiten darstellt.

## 👁️ Vorraussetzungen @showdialog
* Du benötigst einen IoT Cube dessen OLED Display 🖥️ an J5 angeschlossen ist.
* Schliesse den Cube so an, falls noch nicht gemacht:
![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/iot-cube-anschliessen-klein.png)
* Stelle die Schalter vorerst so ein:
    * Battery Switch: **off**
    * LoRa Module: **on**
![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/iot-cube-power-switches-klein.png)

* Schliesse den Ultraschallsensor🦇 an J1 an.
![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/14_Tutorial_Seifenspender_Ultraschallsensor.png)

* Ein LoRa- Gateway🛜 muss in Reichweite sein, welches mit TTN (The Things Network) verbunden ist.
Dies ist im Klassensatz einmal vorhanden und kann hunderte von IoT- Cubes bedienen.
![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/gateway-klein.png)


## ☁️ Dashboard kontrollieren auf Clavis Cloud 

Teste, ob die Daten immer noch auf der Clavis Cloud ☁️ ankommen.
* Rufe die Website [🌍iot.claviscloud.ch](https://iot.claviscloud.ch/home) auf.
* Navigiere zu Deinem Dashboard. 

Wird der gemessene 🧼 Seifenstand auf dem Dashboard angezeigt?

```template
function warte_5_Sekunden_mit_Anzeige () {
    smartfeldAktoren.oledClear()
    for (let fortschritt = 0; fortschritt <= 100; fortschritt++) {
        smartfeldAktoren.oledLoadingBar(fortschritt)
        basic.pause(50)
    }
    smartfeldAktoren.oledClear()
}
smartfeldAktoren.oledInit(128, 64)
smartfeldAktoren.oledClear()
smartfeldAktoren.oledWriteStr("Verbinde")
music.setVolume(50)
IoTCube.LoRa_Join(
eBool.enable,
eBool.enable,
10,
8
)
while (!(IoTCube.getStatus(eSTATUS_MASK.JOINED))) {
    smartfeldAktoren.oledWriteStr(".")
    basic.pause(1000)
}
smartfeldAktoren.oledClear()
smartfeldAktoren.oledWriteStr("Verbunden!")
let seifenstandInProzent = 100
led.plotBarGraph(
seifenstandInProzent,
100
)
basic.pause(2000)
smartfeldAktoren.oledClear()
basic.clearScreen()
IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
IoTCube.SendBufferSimple()
basic.forever(function () {
    if (input.buttonIsPressed(Button.A)) {
        if (seifenstandInProzent > 0) {
            seifenstandInProzent = seifenstandInProzent - 20
            IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
            IoTCube.SendBufferSimple()
            led.plotBarGraph(
            seifenstandInProzent,
            100
            )
            warte_5_Sekunden_mit_Anzeige()
        } else {
            music.play(music.builtinPlayableSoundEffect(soundExpression.sad), music.PlaybackMode.UntilDone)
            seifenstandInProzent = 0
        }
    }
    if (input.buttonIsPressed(Button.B)) {
        music.play(music.builtinPlayableSoundEffect(soundExpression.giggle), music.PlaybackMode.InBackground)
        seifenstandInProzent = 100
        IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
        IoTCube.SendBufferSimple()
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
        warte_5_Sekunden_mit_Anzeige()
    }
    basic.clearScreen()
})
```
