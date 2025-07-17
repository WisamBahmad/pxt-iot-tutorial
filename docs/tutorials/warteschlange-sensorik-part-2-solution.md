```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
neopixel=github:microsoft/pxt-neopixel#v0.7.6
```
### @explicitHints false

# Warteschlangen-Sensorik Teil 2
## Lösung

* Unten findest du die komplette Lösung.
* Drücke 📥`|Download|` und teste das Programm.

```template

function schreibeInfosAufDisplay (position: number, wert: number, _symbol: string) {
    zeile = "P" + position + ": " + _symbol + " :" + wert
    smartfeldAktoren.oledWriteStrNewLine(zeile)
}
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
function initialisiereLoRaVerbindung () {
    smartfeldAktoren.oledClear()
    smartfeldAktoren.oledWriteStr("Verbinde")
    IoTCube.LoRa_Join(
    eBool.enable,
    eBool.enable,
    10,
    8
    )
    while (!(IoTCube.getStatus(eSTATUS_MASK.JOINED))) {
        basic.pause(1000)
        smartfeldAktoren.oledWriteStr(".")
    }
    smartfeldAktoren.oledClear()
    smartfeldAktoren.oledWriteStr("Verbunden!")
    basic.pause(2000)
    smartfeldAktoren.oledClear()
}
function sendeUndZeigePersonenanzahl () {
    IoTCube.addUnsignedInteger(eIDs.ID_0, anzahlPersonenInWarteschlange)
    IoTCube.SendBufferSimple()
    basic.showNumber(anzahlPersonenInWarteschlange)
    warte5SekundenUndZeigeFortschritt()
}
function warte5SekundenUndZeigeFortschritt () {
    smartfeldAktoren.oledClear()
    for (let fortschritt = 0; fortschritt <= 100; fortschritt++) {
        smartfeldAktoren.oledLoadingBar(fortschritt)
        basic.pause(10)
    }
    smartfeldAktoren.oledClear()
}
let h_unterschied = 0
let h_mitLED = 0
let h_umgebung = 0
let anzahlPersonenInWarteschlange = 0
let maximum = 0
let ANZAHL_MESSUNGEN = 0
let zeile = ""
smartfeldSensoren.initSunlight()
smartfeldAktoren.oledInit(128, 64)
let strip = neopixel.create(DigitalPin.P1, 16, NeoPixelMode.RGB)
strip.setBrightness(255)
initialisiereLoRaVerbindung()
let anzahlPersonenVorher = -1
basic.forever(function () {
    anzahlPersonenInWarteschlange = 0
    smartfeldAktoren.oledClear()
    for (let Index = 0; Index <= 8; Index++) {
        strip.setPixelColor(Index + 2, neopixel.colors(NeoPixelColors.Black))
        strip.show()
        h_umgebung = messeMax()
        strip.setPixelColor(Index + 2, neopixel.colors(NeoPixelColors.White))
        strip.show()
        h_mitLED = messeMax()
        h_unterschied = h_mitLED - h_umgebung
        if (h_unterschied < 100) {
            schreibeInfosAufDisplay(Index, h_unterschied, "X")
            anzahlPersonenInWarteschlange += 1
        } else {
            schreibeInfosAufDisplay(Index, h_unterschied, "-")
        }
        strip.clear()
    }
    if (anzahlPersonenInWarteschlange != anzahlPersonenVorher) {
        sendeUndZeigePersonenanzahl()
        anzahlPersonenVorher = anzahlPersonenInWarteschlange
    }
})

```