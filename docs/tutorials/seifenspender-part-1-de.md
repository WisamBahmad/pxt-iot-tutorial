```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```
### @explicitHints false

# IoT Tutorial Teil 1


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

## Weiter gehts mit Teil 2!

[Teil 2](https://makecode.microbit.org/#tutorial:github:reifab/pxt-iot-tutorial/docs/tutorials/seifenspender-part-2-de)
