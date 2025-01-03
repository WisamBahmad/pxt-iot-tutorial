```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```
### @explicitHints false

# IoT Tutorial Teil 2


## 📗 Einführung, Teil 2

Vorraussetzungen: 🌱 IoT Basics abgeschlossen und IoT Tutorial Teil 1 abgeschlossen.
Schwierigkeitsgrad: 🔥🔥⚪⚪

Aus dem Tutorial Teil 1 hast du bereits ein Programm, das den Seifenstand siumuliert. 
Nun wollen wir über 🛜 LoRa den Seifenstand ins Internet sendet. Am Ende hast du ein
funktionsfähiges Programm,das:

* 🛜 Eine LoRa-Verbindung aufbaut. 
* 🧼 Den Seifenstand über 🛜 LoRa sendet. 
* ⏳ Eine Ladebalken-Animation für Wartezeiten darstellt.

## 👁️ Vorraussetzungen
* 🖥️ Du benötigst einen IoT Cube dessen OLED Display an J5 angeschlossen ist.
* 🛜 Ein LoRa- Gateway muss in Reichweite sein, welches mit TTN (The Things Network) verbunden ist.

## 🖥️ Statusmeldung anzeigen auf OLED
Auf dem kleinen Display auf dem IoT Cube wollen wir den Text "Verbinde" anzeigen. 

* 🖥️ Hol dir einen Block ``||SmartfeldAktoren:init OLED Breite 128 Höhe 64||``
und ergänze in unten im Block ``||basic:beim Start||``. 
* 🖥️ Darunter setzt du den Block ``||SmartfeldAktoren:Lösche Displayinhalt||``
ein.
Damit löschst du bestehende Inhalte auf dem Display.
* 🖥️ Jetzt verwendest du den Block ``||SmartfeldAktoren:schreibe String||``.
Damit schreibst du den Text "Verbinde" auf das Display.
```blocks
let seifenstandInProzent = 100
led.plotBarGraph(
seifenstandInProzent,
100
)
// @highlight
smartfeldAktoren.oledInit(128, 64)
// @highlight
smartfeldAktoren.oledClear()
// @highlight
smartfeldAktoren.oledWriteStr("Verbinde")
```

## 🛜 Verbindung mit Internet aufbauen
Nun bauen wir eine Verbindung zum Internet auf.
Auf dem 🖼️ OLED- Display wollen wir den Status **Verbunden!** anzeigen,
sobald die Verbindung steht. 
* 🛜 Ziehe den Block ``||IoTCube:LoRa Netzwerk-Verbindung||`` zuunterst in 
**beimStart** hinein.
* Ziehe darunter den Block ``||loops:während flasch mache||`` hinein. Weil
das Verbinden je nach Umstände 5 is 30 Sekunden dauert, wollen in dieser
Schleife verbleiben, solange die Verbindung noch **nicht** besteht. 
* Ziehe dazu den Block ``||Logic:nicht||`` auf die Schleife,
um den Wahrheitswert zu negieren.
* 🛜 Füge in den **nicht** Block nun ``||IoTCube:Lese Gerätestatus-Bit||`` ein.
Ändere darin das Bit auf "Verbunden". Der Code ist dann zu lesen als:
"Wähend das Gerät nicht verbunden ist." 
* Warte in der Schleife jeweils 1000 ms. Nutze ``||basic:pausiere (ms)||``.
* 🖥️ Schreibe mit dem Block 
``||SmartfeldAktoren:schreibe String||`` bei jedem Schleifendurchlauf ein "."
auf das Display.
* 🖥️ Lösche unter der Schleife den Displayinhalt mittels ``||SmartfeldAktoren:Lösche Displayinhalt||``.
* 🖥️ Schreibe nach der Schleife ein "Verbunden!" auf das OLED- Display.
* 📥 Drücke `|Download|` und teste, ob der Verbindungsaubau klappt.

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
while (!(IoTCube.getStatus(eSTATUS_MASK.JOINED))) {
    smartfeldAktoren.oledWriteStr(".")
    basic.pause(1000)
}
// @highlight
smartfeldAktoren.oledClear()
// @highlight
smartfeldAktoren.oledWriteStr("Verbunden!")
```

## 🧼 Seifenstand senden beim Start

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

## ☁️ Dashboard erstellen auf Clavis Cloud 

Nun geht es an die Visualisierung der Daten auf der  Clavis Cloud ☁️. 
* Rufe die Website [🌍iot.claviscloud.ch](https://iot.claviscloud.ch/home) auf.
* Melde dich an (Login- Informationen kriegst Du vom Smartfeld).
* Schau dir dieses [📹 Video](https://wiki.smartfeld.ch/lib/exe/fetch.php?media=dashboard_erstellen_seifenspender.mp4) an. Es beinhaltet folgende Schritte
 * Dashboard- Gruppe erstellen (falls noch nicht vorhanden)
 * Dashboard erstellen
 * Widget erstellen für die Anzeige des 🧼 Seifenstandes

## 🧼 Seifenstand senden bei Tastendruck

Immer wenn eine Taste gedrückt wird, sollen die Daten an die Cloud geschickt 
werden. Lass uns die nötigen Schritte durchführen.

* Wenn Knopf A geklickt ist und der Seifenstand grösser als 0 ist, schickst Du 
  den aktuellen Seifenstand mithilfe der Blöcke ``||IoTCube:Ganzzahl mit ID_0 = 0 hinzufügen||``, 
  und ``||IoTCube:Sende Daten||`` ins Internet. Falls du unsicher bist, klicke
  auf die 💡 Glühbirne links unten.
* Dasselbe machst du, wenn Knopf B geklickt wurde. Falls du unsicher bist, klicke
  auf die 💡 Glühbirne links unten.

```blocks
basic.forever(function () {
    if (input.buttonIsPressed(Button.A)) {
        if (seifenstandInProzent > 0) {
            seifenstandInProzent = seifenstandInProzent - 20
            // @highlight
            IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
            // @highlight
            IoTCube.SendBufferSimple()
            led.plotBarGraph(
            seifenstandInProzent,
            100
            )
        } else {
            seifenstandInProzent = 0
        }
    }
    if (input.buttonIsPressed(Button.B)) {
        seifenstandInProzent = 100
        // @highlight
        IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
        // @highlight
        IoTCube.SendBufferSimple()
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
    }
    basic.clearScreen()
})
```

## ⏱️ Funktion für Wartezeit erstellen

Nach dem Versenden von Daten über LoRa ist eine Wartezeit von mindestens 
5 Sekunden erforderlich, bevor erneut Daten übertragen werden können. 
Hintergrund: Während dieser 5 Sekunden steht ein Empfangsfenster zur Verfügung, 
über das Daten von der Cloud zum Cube übertragen werden können.

Baue dir mit folgenden Blöcken die Wartefunktion nach, welche im Tootip
(die 💡 Glühbirne links unten) angezeigt wird.

* ``||function:Erstelle eine Funktion||``
    * Benenne die Funktion mit "warte_5_Sekunden_mit_Anzeige" 
    * Lösche darin den Displayinhalt mit ``||SmartfeldAktoren:Lösche Displayinhalt||`` 
    * ``||loops:für Index von 0 bis 4||`` 
        * klicke auf Index --> Neue Variable, nenne diese **fortschritt**
        * ersetze die 4 mit 100.
    * ``||SmartfeldAktoren:zeichne Ladebalken bei 0 Prozent||`` 
        * ersetze die 0 mit der Variable **fortschritt**
    * Warte in der Schleife mit ``||basic:pausiere (ms)||`` jeweils für 50 ms
     (100x 50ms = 5 Sekunden)
    * Lösche den Displayinhalt erneut nach der Schleife.

```blocks
// @highlight
function warte_5_Sekunden_mit_Anzeige () {
    smartfeldAktoren.oledClear()
    for (let fortschritt = 0; fortschritt <= 100; fortschritt++) {
        smartfeldAktoren.oledLoadingBar(fortschritt)
        basic.pause(50)
    }
    smartfeldAktoren.oledClear()
}
```

## ⏱️ Wartezeit einbauen

* Nachdem du die Funktion nachgebaut hast, fügst du sie nach jedem Senden ein.
* 📥 Drücke `|Download|` und teste Dein fertiges Programm!
    * Reagiert das Dashboard auf [iot.claviscloud.ch](https://iot.claviscloud.ch/dashboards/)

```blocks
basic.forever(function () {
    if (input.buttonIsPressed(Button.A)) {
        if (seifenstandInProzent > 0) {
            seifenstandInProzent = seifenstandInProzent - 20
            IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
            IoTCube.SendBufferSimple()
            // @highlight
            warte_5_Sekunden_mit_Anzeige ()
            led.plotBarGraph(
            seifenstandInProzent,
            100
            )
        } else {
            seifenstandInProzent = 0
        }
    }
    if (input.buttonIsPressed(Button.B)) {
        seifenstandInProzent = 100
        IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
       
        IoTCube.SendBufferSimple()
        // @highlight
        warte_5_Sekunden_mit_Anzeige ()
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
    }
    basic.clearScreen()
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

```template
let seifenstandInProzent = 100
led.plotBarGraph(
seifenstandInProzent,
100
)

basic.forever(function () {
    if (input.buttonIsPressed(Button.A)) {
        if (seifenstandInProzent > 0) {
            seifenstandInProzent = seifenstandInProzent - 20
            led.plotBarGraph(
            seifenstandInProzent,
            100
            )
        } else {
            seifenstandInProzent = 0
        }
    }
    if (input.buttonIsPressed(Button.B)) {
        seifenstandInProzent = 100
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
    }
    basic.clearScreen()
    basic.pause(100)
})

```
