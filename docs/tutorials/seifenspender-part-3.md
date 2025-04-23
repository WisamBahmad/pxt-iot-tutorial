```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```
### @explicitHints false

# IoT Tutorial Teil 3


## 📗 Einführung, Teil 3

Vorraussetzungen: 🌱 IoT Basics abgeschlossen und IoT Tutorial [Teil 2](https://makecode.microbit.org/#tutorial:github:reifab/pxt-iot-tutorial/docs/tutorials/seifenspender-part-2) abgeschlossen.
Schwierigkeitsgrad: 🔥🔥🔥⚪

Aus dem Tutorial Teil 2 hast du bereits ein Programm, das den Seifenstand mit Knopf A und B 
simuliert und über LoRa🛜 ins Internet sendet. Um der realen Anwendung näher zu kommen, 
ersetzt Du die Knöpfe A und B durch einen Ultraschallsensor, der den Seifenstand misst.

Am Schuluss hast du ein Programm, welches...

* Den Seifenstand mit einem Ultraschallsensor🦇 misst.
* Eine LoRa-Verbindung🛜 aufbaut. 
* Den Seifenstand🧼 über LoRa🛜 sendet. 
* Eine Ladebalken-Animation⏳ für Wartezeiten darstellt.

## 👁️ Vorraussetzungen @showdialog

Du brauchst gegenüber Tutorial Teil 2 folgendes:
* Schliesse den Ultraschallsensor🦇 an J1 an.
![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/14_Tutorial_Seifenspender_Ultraschallsensor.png)

## Bisherige Funktion überprüfen

Teste, ob die Daten immer noch auf der Clavis Cloud ☁️ ankommen.
* Lade das bestehende Programm auf den IoT Cube
* Rufe die Website [🌍iot.claviscloud.ch](https://iot.claviscloud.ch/home) auf.
* Navigiere zu Deinem Dashboard. 

Wird der🧼 Seifenstand (100 %) auf dem Dashboard angezeigt? Reduziert
sich der Seifenstand beim Drücken von Knopf A?

## Nicht mehr benötigten Code entfernen

Die Knöpfe A und B sollen durch eine reale Messung mittels Ultraschallsensor 
ersetzt werden. Welcher Code wird neu nicht mehr benötigt? 
* Klicke auf die Glühbirne 💡, dann siehst du den Code, wie er ohne die Knöpfe aussieht.
Das Senden des Seifenstands wurde belassen. 
* Passe den Code ensprechend dem Beispiel (Glühbirne 💡) an.
```blocks
// @collapsed
let seifenstandInProzent = 100
led.plotBarGraph(
seifenstandInProzent,
100
)
smartfeldAktoren.oledInit(128, 64)
smartfeldAktoren.oledClear()
smartfeldAktoren.oledWriteStr("Verbinde")
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
basic.pause(3000)
smartfeldAktoren.oledClear()
IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
IoTCube.SendBufferSimple()
warte_5_Sekunden_mit_Anzeige ()

// @highlight
basic.forever(function () {  
    IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
    IoTCube.SendBufferSimple()
    warte_5_Sekunden_mit_Anzeige ()
    led.plotBarGraph(
    seifenstandInProzent,
    100
    )
    basic.pause(100)
})


function warte_5_Sekunden_mit_Anzeige () {
    smartfeldAktoren.oledClear()
    for (let fortschritt = 0; fortschritt <= 100; fortschritt++) {
        smartfeldAktoren.oledLoadingBar(fortschritt)
        basic.pause(50)
    }
    smartfeldAktoren.oledClear()
}
```

## Senden vorerst verhindern

Wir wollen vorerst noch nichts an die ☁️ Cloud senden.
* Modifiziere **dauerhaft** so, wie unter dem 💡angezeigt. 
Verwende dazu den Block ``||Logic:wenn wahr dann||``. 
Dieser verhindert das Senden sobald du wahr auf **falsch** stellst.
* Modiziere **beim Start** ebenfalls so, wie unter dem 💡angezeigt. 

```blocks
let seifenstandInProzent = 100
led.plotBarGraph(
seifenstandInProzent,
100
)
smartfeldAktoren.oledInit(128, 64)
smartfeldAktoren.oledClear()
// @highlight
if (false) {
    smartfeldAktoren.oledWriteStr("Verbinde")
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
    basic.pause(3000)
    smartfeldAktoren.oledClear()
    IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
    IoTCube.SendBufferSimple()
    warte_5_Sekunden_mit_Anzeige ()
}
basic.forever(function () {
   // @highlight
    if (false) {
        IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
        IoTCube.SendBufferSimple()
        warte_5_Sekunden_mit_Anzeige()
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
    }
    basic.pause(100)
})

function warte_5_Sekunden_mit_Anzeige () {
    smartfeldAktoren.oledClear()
    for (let fortschritt = 0; fortschritt <= 100; fortschritt++) {
        smartfeldAktoren.oledLoadingBar(fortschritt)
        basic.pause(50)
    }
    smartfeldAktoren.oledClear()
}
```

## Ultraschallsensor verwenden

* Schliesse den Ultraschallsensor🦇  an J1 an. 
* Um die gemessene Distanz 📏 zwischen Seife und Sensor zu speichern, benötigen wir eine Variable.
``||variables:Erstelle eine Variable...||`` und benenne sie mit **distanzSensorZuSeife** .
* Ziehe den Block ``||variables:setze distanzSensorZuSeife auf 0||`` zuoberst in die Dauerhaftschleife .
* Um der Variable den Messwert zuzuweisen, füge den Block ``||SmartfeldSensoren:Distanz in cm||``
anstelle der 0 ein. Belasse den Pin auf P0.

```blocks
basic.forever(function () {
    // @highlight
    distanzSensorZuSeife = smartfeldSensoren.measureInCentimetersV2(DigitalPin.P0)
    if (false) {
        IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
        IoTCube.SendBufferSimple()
        warte_5_Sekunden_mit_Anzeige()
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
    }
    basic.pause(100)
})
// @collapsed
function warte_5_Sekunden_mit_Anzeige () {
    smartfeldAktoren.oledClear()
    for (let fortschritt = 0; fortschritt <= 100; fortschritt++) {
        smartfeldAktoren.oledLoadingBar(fortschritt)
        basic.pause(50)
    }
    smartfeldAktoren.oledClear()
}
```

## Messwert anzeigen

Der Sensor liefert die Distanz in cm. Um die Funktion des Sensors zu überprüfen,
zeigen wir den Sensorwert auf dem OLED- Display an. 

* Setze den Block 🖥️ ``||SmartfeldAktoren:Lösche Displayinhalt||``
zuoberst in der Dauerhaftschleife ein.
Damit löschst du bestehende Inhalte auf dem Display.
* Unter der Variable setzt du den Block ``||SmartfeldAktoren:schreibe Nummer||``
ein. 
* Ersetze die 0 mit der Variable ``||variables:distanzSensorZuSeife||``
* Runde den Wert auf ganze Zahlen mit dem Block ``||math:runden||``
* Schreibe nach dem Messwert die Masseinheit ** cm** auf das Display mit ``||SmartfeldAktoren:schreibe String||``

```blocks
basic.forever(function () {
    // @highlight
    smartfeldAktoren.oledClear()
    // @highlight
    distanzSensorZuSeife = smartfeldSensoren.measureInCentimetersV2(DigitalPin.P0)
     // @highlight
    smartfeldAktoren.oledWriteNum(Math.round(distanzSensorZuSeife))
     // @highlight
    smartfeldAktoren.oledWriteStr(" cm")
    if (false) {
        IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
        IoTCube.SendBufferSimple()
        warte_5_Sekunden_mit_Anzeige()
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
    }
    basic.pause(100)
})
```

## Modell für Seifenspender bauen






## Füllstand berechnen 



## Füllstand anzeigen

```template
let seifenstandInProzent = 100
led.plotBarGraph(
seifenstandInProzent,
100
)
smartfeldAktoren.oledInit(128, 64)
smartfeldAktoren.oledClear()
smartfeldAktoren.oledWriteStr("Verbinde")
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
basic.pause(3000)
smartfeldAktoren.oledClear()
IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
IoTCube.SendBufferSimple()
warte_5_Sekunden_mit_Anzeige ()

basic.forever(function () {
    if (input.buttonIsPressed(Button.A)) {
        seifenstandInProzent = seifenstandInProzent - 20
        if (seifenstandInProzent < 0) {
            seifenstandInProzent = 0
        }
        IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
        IoTCube.SendBufferSimple()
        warte_5_Sekunden_mit_Anzeige ()
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
    }
    if (input.buttonIsPressed(Button.B)) {
        seifenstandInProzent = 100
        IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
        IoTCube.SendBufferSimple()
        warte_5_Sekunden_mit_Anzeige ()
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
    }
    basic.pause(100)
})

// @collapsed
function warte_5_Sekunden_mit_Anzeige () {
    smartfeldAktoren.oledClear()
    for (let fortschritt = 0; fortschritt <= 100; fortschritt++) {
        smartfeldAktoren.oledLoadingBar(fortschritt)
        basic.pause(50)
    }
    smartfeldAktoren.oledClear()
}
```
