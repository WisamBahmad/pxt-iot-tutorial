```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```
### @explicitHints false

# IoT Tutorial Teil 2


## 📗 Einführung  

Vorraussetzungen: 🌱 IoT Basics abgeschlossen  
Schwierigkeitsgrad: 🔥🔥⚪⚪

In diesem Tutorial baust du Schritt für Schritt ein Programm auf, 
das einen Seifenstand simuliert über 🛜 LoRa ins Internet sendet. Am Ende hast 
du ein funktionsfähiges Programm, das:

* 🖥️ Ein OLED-Display initialisiert und Statusmeldungen ausgibt.  (Teil 1)
* 🧼 Den Seifenstand anzeigt (Teil 1)
* 🔘 Per Knopfdruck den Seifenstand reduziert oder wieder auffüllt: (Teil 1)
    * ➖ Knopf A geklickt -> Seifenstand wird reduziert  
    * ➕ Knopf B geklickt -> Seifenspender wird aufgefüllt  
* 🛜 Eine LoRa-Verbindung aufbaut.  (Teil 2)
* 🧼 Den Seifenstand über 🛜 LoRa sendet. (Teil 2)
* ⏳ Eine Ladebalken-Animation für Wartezeiten darstellt. (Teil 2)

## 👁️ Vorraussetzungen
* 🖥️ Du benötigst einen IoT Cube dessen OLED Display an J5 angeschlossen ist.
* 🛜 Ein LoRa- Gateway muss in Reichweite sein, welches mit TTN (The Things Network) verbunden ist.


## 🧼 Seifenstand senden 🛜

Zu Beginn ist der Seifenstand 100 %.
Diesen wollen wir nach dem Verbindungsaufbau senden.

* ``||basic:pausiere (ms)||`` für 2000 ms, nachdem der Text "Vebunden!" ausgegeben wurde,
damit dieser Text mindestens für diese Zeit auf dem Display steht.
* Lösche danach den Text mit ``||SmartfeldAktoren:Lösche Displayinhalt||`` 
um Energie zu sparen.
* Darunter setzt du den Block ``||IoTCube:Ganzzahl mit ID_0 = 0 hinzufügen||`` ein.
* Die 0 ersetzt du nun mit der Variable ``||variables:seifenstandInProzent||``.
* 📥 Drücke `|Download|`.
* Mit etwas Glück schickst du diese Zahl mit ``||IoTCube:Sende Daten||`` in die ☁️ Cloud!

```blocks
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

// @highlight
basic.pause(2000)
// @highlight
smartfeldAktoren.oledClear()
// @highlight
basic.clearScreen()
// @highlight
IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
// @highlight
IoTCube.SendBufferSimple()
```

## Clavis Cloud ☁️

Nun geht es an die Visualisierung der Daten auf der  Clavis Cloud ☁️. 
* Rufe die Website [🌍iot.claviscloud.ch](https://iot.claviscloud.ch/home) auf.
* Melde dich an (Login- Informationen kriegst Du vom Smartfeld).
* Schau dir dieses [📹 Video](https://wiki.smartfeld.ch/lib/exe/fetch.php?media=dashboard_erstellen_seifenspender.mp4) an. Es beinhaltet folgende Schritte
 * Dashboard- Gruppe erstellen (falls noch nicht vorhanden)
 * Dashboard erstellen
 * Widget erstellen für die Anzeige des 🧼 Seifenstandes

## Step 3

```blockconfig.global
randint(1, 3)
```

```template
function warte_5_Sekunden_mit_Anzeige () {
    smartfeldAktoren.oledClear()
    for (let fortschritt = 0; fortschritt <= 100; fortschritt++) {
        smartfeldAktoren.oledLoadingBar(fortschritt)
        basic.pause(50)
    }
    smartfeldAktoren.oledClear()
}
music.setVolume(50)
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
    if (input.buttonIsPressed(Button.A)) {
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
