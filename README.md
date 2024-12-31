```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```
### @explicitHints false
### @diffs true

# IoT Tutorial


## 📗 Einführung  

Vorraussetzungen: 🌱 IoT Basics abgeschlossen  
Schwierigkeitsgrad: 🔥🔥⚪⚪

In diesem Tutorial baust du Schritt für Schritt ein Programm auf, 
das einen Seifenstand simuliert über 🛜 LoRa ins Internet sendet. Am Ende hast 
du ein funktionsfähiges Programm, das:

* 🖥️ Ein OLED-Display initialisiert und Statusmeldungen ausgibt.  
* 🛜 Eine LoRa-Verbindung aufbaut.  
* 🧼 Den Seifenstand anzeigt und über 🛜 LoRa sendet.  
* 🔘 Per Knopfdruck den Seifenstand reduziert oder wieder auffüllt:  
    * ➖ Knopf A geklickt -> Seifenstand wird reduziert  
    * ➕ Knopf B geklickt -> Seifenspender wird aufgefüllt  
* ⏳ Eine Ladebalken-Animation für Wartezeiten darstellt.

## 👁️ Vorraussetzungen
* 🖥️ Du benötigst einen IoT Cube dessen OLED Display an J5 angeschlossen ist.
* 🛜 Ein LoRa- Gateway muss in Reichweite sein, welches mit TTN (The Things Network) verbunden ist.

## 🧼 Variable für den Seifenstand
Um den Füllstand des Seifenspenders zu speichern, setzen wir eine Variable ein.
* 🧼 ``||variables:Erstelle eine Variable...||`` und benenne sie mit **seifenstandInProzent**.
* 🧼 **beim Start** soll der Seifenstand auf 100 % gesetzt werden. ``||variables:setze seifenstandInProzent auf 100||``

```blocks
let seifenstandInProzent = 100
```

## 🧼 Seifenstand anzeigen
* 🟥 Hol dir den Block ``||led:Zeichne Säulendiagramm|`` und ziehe diesen in den Block **beim Start** direkt unter der Variablendeklaration.
* 🧼 Hol die Variable ``||variables:seifenstandInProzent||`` um sie mit dem Säulendiagramm darzustellen. 
* 🧼 Ändere den Bereich von ``seifenstandInProzent`` bis 100. 
* 📥 Drücke `|Download|` und kontrolliere die LED- Anzeige:  
🟥🟥🟥🟥🟥  
🟥🟥🟥🟥🟥  
🟥🟥🟥🟥🟥  
🟥🟥🟥🟥🟥  
🟥🟥🟥🟥🟥  
Leuchten alle LEDs?

```blocks
let seifenstandInProzent = 100
// @highlight
led.plotBarGraph(
seifenstandInProzent,
100
)
```

## ➖ Füllstand reduzieren mit  Knopf A 
* Hol dir den Block ``||Logic:wenn wahr dann||`` und ziehe ihn in die ``dauerhaft`` Schleife
* Schiebe den Block ``||Input:Knopf A ist geklickt||`` auf das Feld ``wahr``
* 🧼 Reduziere die Variable ``||variables:seifenstandInProzent||`` um die Zahl 20. Benutze dazu ``||Math:Mathematik||``
* 🟥 Zeichne erneut das Säulendiagramm. Kopiere diesen Teil aus ``beim Start``

```blocks
basic.forever(function () {
    // @highlight
    if (input.buttonIsPressed(Button.A)) {
        seifenstandInProzent = seifenstandInProzent - 20
        led.plotBarGraph(
            seifenstandInProzent,
            100
        )
    }
}
```

## 🧼 Flüllstand kleiner 0 verhindern
* 🧼 Versuche mit dem Block ``||Logic:wenn wahr dann||`` Füllstände kleiner als 0 zu verhindern. 
* 🧼 Setze den Füllstand auf 0 sollte der Füllstand die 0 unterschreiten.
[Hier findest du weitere Informationen zu logischen Operatoren]
(https://makecode.microbit.org/blocks/logic/boolean)
* 📥 Drücke `|Download|` und kontrolliere die 🟥 LEDs:  
⬛⬛🟥⬛⬛  
🟥🟥🟥🟥🟥  
🟥🟥🟥🟥🟥  
🟥🟥🟥🟥🟥  
🟥🟥🟥🟥🟥  
Reduziert sich die Anzeige?

```blocks
basic.forever(function () {
    if (input.buttonIsPressed(Button.A)) {
        seifenstandInProzent = seifenstandInProzent - 20
        // @highlight
        if(seifenstandInProzent<0){
            // @highlight
            seifenstandInProzent = 0
        }
        led.plotBarGraph(
            seifenstandInProzent,
            100
        )
    }
}
```

## ➕ Seifenspender auffüllen mit Knopf B
* Hol dir den Block ``||Logic:wenn wahr dann||`` und ziehe ihn in zuunterst in
die ``dauerhaft`` Schleife
* Schiebe den Block ``||Input:Knopf A ist geklickt||`` auf das Feld ``wahr``
und ändere Knopf A zu Knopf **B**
* 🧼 Setze die Variable ``||variables:seifenstandInProzent||`` auf die Zahl 100, wenn B gedrückt wurde.
* Zeichne erneut das Säulendiagramm. Kopiere diesen Teil aus ``beim Start``
* 📥 Drücke `|Download|` und kontrolliere die 🟥 LED-  Anzeige... 
Funktioniert alles wie gewünscht?

```blocks
basic.forever(function () {
    if (input.buttonIsPressed(Button.A)) {
        seifenstandInProzent = seifenstandInProzent - 20
        
        if(seifenstandInProzent<0){
           
            seifenstandInProzent = 0
        }
        led.plotBarGraph(
            seifenstandInProzent,
            100
        )
    }
    // @highlight
    if (input.buttonIsPressed(Button.B)) {
        // @highlight
        seifenstandInProzent = 100
    }
}
```

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
* Ziehe darunter den Block ``||Schleifen:während flasch mache||`` hinein. Weil
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

## 🧼 Seifenstand senden 🛜

Zu Beginn ist der Seifenstand 100 %.
Diesen wollen wir nach dem Verbindungsaufbau senden.

* ``||basic:pausiere (ms)||`` für 2000 ms, nachdem der Text "Vebunden!" ausgegeben wurde,
damit dieser Text mindestens für diese Zeit auf dem Display steht.
* Lösche danach den Text mit ``||SmartfeldAktoren:Lösche Displayinhalt||`` 
um Energie zu sparen.
* Darunter setzt du den Block ``||IoTCube:Ganzzahl mit ID_0 = 0 hinzufügen||`` ein.
* Die 0 ersetzt du nun mit der Variable ``||variables:seifenstandInProzent||``.
* Nun schickst du diese Zahl mit ``||IoTCube:Sende Daten||`` in die ☁️ Cloud!

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
* Melde dich an (Login- Informationen kriegst Du vom Smartfeld)
* Gehe zu Dashboards  
![Tutorialbild 1](https://github.com/reifab/pxt-iot-tutorial/blob/development/docs/static/tutorials/1_Tutorial_Add_Group.png?raw=true)
* Klicke die Gruppen an (Groups):
![Tutorialbild 2](https://github.com/reifab/pxt-iot-tutorial/blob/development/docs/static/tutorials/2_Tutorial_Add_Group.png?raw=true)
* Klicke auf **Add entity group** oder wähle eine bestende Gruppe aus:
![Tutorialbild 3](https://github.com/reifab/pxt-iot-tutorial/blob/development/docs/static/tutorials/3_Tutorial_Add_Group.png?raw=true)
*  Gib einen passenden Namen und Beschreibung für deine Gruppe und klicke auf **hinzufügen**:
![Tutorialbild 4](https://github.com/reifab/pxt-iot-tutorial/blob/development/docs/static/tutorials/4_Tutorial_Add_Group.png?raw=true)

## Clavis Cloud ☁️, Dashboard importieren
* [Klicke hier, um eine Vorlage zu laden](https://github.com/reifab/pxt-iot-tutorial/blob/development/docs/static/tutorials/seifenspender_dashboard.json?raw=true)
* Drücke die Tastenkombination "CTRL+S" um dies als Datei zu speichern.
* Klicke auf der Clavis Cloud **🡅Dashboard importieren**.
* Suche auf dem Dateisystem nach der heruntergeladenen Datei wähle sie aus.

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

## Step 3

```blockconfig.global
randint(1, 3)
```
