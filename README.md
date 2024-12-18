```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```
# IoT Tutorial

## Einführung

In diesem Tutorial baust du Schritt für Schritt ein Programm auf, das einen Seifenstand simuliert über LoRa sendet. Am Ende hast du ein funktionsfähiges Programm, das:

* Ein OLED-Display initialisiert und Statusmeldungen ausgibt.
* Eine LoRa-Verbindung aufbaut.
* Den Seifenstand anzeigt und über LoRa sendet.
* Per Knopfdruck den Seifenstand reduziert oder wieder auffüllt.
    * Knopf A geklickt -> Seifenstand wird reduziert 
    * Knopf B geklickt -> Seifendspender wird aufgefüllt
* Eine Ladebalken-Animation für Wartezeiten darstellt.

## Vorraussetzungen

* Du benötigst einen IoT Cube dessen OLED Display an J5 angeschlossen ist.
* Ein LoRa- Gateway muss in Reichweite sein, welches mit TTN (The Things Network) verbunden ist.

## Variable für den Seifenstand

Um den Füllstand des Seifenspenders zu speichern, setzen wir eine Variable ein.
* ``||variables:Erstelle eine Variable...||`` und benenne sie mit **seifenstandInProzent**.
* **beim Start** soll der Seifenstand auf 100 % gesetzt werden. ``||variables:setze seifenstandInProzent auf 100||``

```blocks
let seifenstandInProzent = 100
```

## Füllstand anzeigen

* Hol dir den Block ``||led:Zeichne Säulendiagramm|`` und ziehe diesen in den Block **beim Start** direkt unter der Variablendeklaration.
* Hol die Variable ``||variables:seifenstandInProzent||`` um sie mit dem Säulendiagramm darzustellen. Ändere den Bereich von ``seifenstandInProzent`` bis 100. 
* Drücke **Herunderladen** und kontrolliere die LED-  Anzeige... Leuchten alle LEDs?

```blocks
let seifenstandInProzent = 100
// @highlight
led.plotBarGraph(
seifenstandInProzent,
100
)
```

## Füllstand reduzieren mit Taste A

* Hol dir den Block ``||Logic:wenn wahr dann||`` und ziehe ihn in die ``dauerhaft`` Schleife
* Schiebe den Block ``||Input:Knopf A ist geklickt||`` auf das Feld ``wahr``
* Reduziere die Variable ``||variables:seifenstandInProzent||`` um die Zahl 20. Benutze dazu ``||Math:Mathematik||``
* Zeichne erneut das Säulendiagramm. Kopiere diesen Teil aus ``beim Start``

```blocks
basic.forever(function () {
    if (input.buttonIsPressed(Button.A)) {
        seifenstandInProzent = seifenstandInProzent - 20
        led.plotBarGraph(
            seifenstandInProzent,
            100
        )
    }
}
```

## Flüllstand kleiner 0 verhindern

* Versuche mit dem Block ``||Logic:wenn wahr dann||`` Füllstände kleiner als 0 zu verhindern. 
* Setze den Füllstand auf 0 sollte der Füllstand die 0 unterschreiten.
* Drücke **Herunderladen** und kontrolliere die LED-  Anzeige... Reduziert sich die Anzeige bei Tastendruck auf A?

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

## Seifenspender auffüllen mit Taste B

* Hol dir den Block ``||Logic:wenn wahr dann||`` und ziehe ihn in zuunterst in die ``dauerhaft`` Schleife
* Schiebe den Block ``||Input:Knopf B ist geklickt||`` auf das Feld ``wahr``
* Setze die Variable ``||variables:seifenstandInProzent||`` auf die Zahl 100, wenn B gedrückt wurde.
* Zeichne erneut das Säulendiagramm. Kopiere diesen Teil aus ``beim Start``
* Drücke **Herunderladen** und kontrolliere die LED-  Anzeige... Funktioniert alles wie gewünscht?


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

## Statusmeldung anzeigen auf OLED

Auf dem kleinen Display auf dem IoT Cube wollen wir den Text "Verbinde" anzeigen. 

* Hol dir einen Block ``||SmartfeldAktoren:init OLED Breite 128 Höhe 64||`` und setze ihn in den Block ``||basic:beim Start||`` ein. 
* Darunter setzt du den Block ``||SmartfeldAktoren:Lösche Displayinhalt||`` (in der Subkategorie Display). Damit löschst du bestehende Inhalte auf dem Display.
* Jetzt verwendest du den Block ``||SmartfeldAktoren:schreibe String||`` (in der Subkategorie Display). Damit schreibst du den Text "Verbinde" auf das Display.
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
## Verbinden mit LoRa