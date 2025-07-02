```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```
### @explicitHints false

# IoT Tutorial Toilette Teil 1


## 📗 Einführung,  Teil 1

**Voraussetzungen**
* Micro:Bit Basics: 
    * Du kannst Programme erstellen und herunterladen.
    * Du kennst die Einstiegspunkte "Beim Start" und "Dauerhaft".
    * Dir ist klar, dass Programme in der Regel schrittweise (von oben nach unten) abgearbeitet werden.
    Zudem kannst Du Schleifen und Verzweigungen einsetzen.
    * es ist bekannt, dass Kategorien Blöcke (z.B. ``||basic:Grundlagen||``) beinhalten, welche in Programmen genutzt werden können
    * Variablen können erstellt, verwendet und verändert werden

**Lernergebnis**

In diesem Tutorial entwickelst du Schritt für Schritt ein Programm, das den Belegungsstatus einer Toilette 
simuliert und die Daten über 🛜 LoRa ins Internet sendet. Am Ende hast 
du ein funktionsfähiges Programm, das...

* den Belegungsstatus anzeigt.
* per ``||Input:Knopfdruck||`` den Seifenstand reduziert oder wieder auffüllt:
    * ``||Input:Knopf A ist geklickt||``: Seifenstand 🧼 wird durch Knopf A um 20% reduziert.
    * ``||Input:Knopf B ist geklickt||``: Seifenstand 🧼 wird durch Knopf B wieder auf 100% aufgefüllt.

**Schwierigkeitsgrad:** 🔥🔥⚪⚪

Klicke auf das 💡- Symbol, falls Du zusätzliche Hilfe brauchst und um deinen Code zu überprüfen.

## 👁️ Voraussetzungen @showdialog
* Für Teil 1 brauchst Du grundsätzlich nur einen Micro:Bit. 
* Falls du lieber gleich den IoT- Cube nehmen möchtest, kannst du ihn so anschliessen:
![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/iot-cube-anschliessen-klein.png)
* Stelle die Schalter vorerst so ein:
    * Battery Switch: **off**
    * LoRa Module: **on**
![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/iot-cube-power-switches-klein.png)

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
* 🧼 Ändere den Bereich von **seifenstandInProzent** bis 100. 
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
* 🟥 Zeichne erneut das Säulendiagramm. Dupliziere diesen Teil aus ``beim Start``
* Verzögere die Dauerhaftschleife um 100 ms mit ``||basic:pausiere (ms)||``.

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
    basic.pause(100)
}
```

## 🧼 Füllstand kleiner 0 verhindern
* 🧼 Versuche mit dem Block ``||Logic:wenn wahr dann||`` Füllstände kleiner als 0 zu verhindern. 
* 🧼 Setze den Füllstand auf 0 sollte der Füllstand die 0 unterschreiten.
[Hier findest du weitere Informationen zu logischen Operatoren](https://makecode.microbit.org/blocks/logic/boolean)
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

## Weiter gehts mit Teil 2!

[Teil 2](https://makecode.microbit.org/#tutorial:github:reifab/pxt-iot-tutorial/docs/tutorials/seifenspender-part-2-de)
