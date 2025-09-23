```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```
### @explicitHints false

# Smart Toilet Tutorial mit Sensor

## 📗 Einführung

**Voraussetzungen**

🌱 IoT Basics abgeschlossen und das Smart-Toilet-Tutorial [Teil 2 – mit Internetverbindung](https://makecode.microbit.org/#tutorial:github:fave-smartfeld/pxt-smart-toilet-tutorial/docs/tutorials/smart-toilet-part2) erfolgreich beendet.

Schwierigkeitsgrad: 🔥🔥⚪⚪

## 👁️ Voraussetzungen @showdialog
* Schliesse den Cube so an, falls du das noch nicht erledigt hast:
![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/iot-cube-anschliessen-klein.png)
* Stelle die Schalter vorerst so ein:
    * Battery Switch: **off**
    * LoRa Module: **on**
![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/iot-cube-power-switches-klein.png)
* Ein LoRa-Gateway🛜 muss in Reichweite und mit TTN (The Things Network) verbunden sein.
  In jedem Klassensatz ist mindestens eines vorhanden, das Hunderte von IoT-Cubes bedienen kann.
![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/gateway-klein.png)

## Lernergebnis

Aus den vorherigen Tutorials kennst du bereits, wie man den Status der Toilette 🚽 ("Besetzt" oder "Frei") per LoRa🛜 ins Internet sendet. Bisher hast du den Status durch Tastendruck ausgelöst. In einer echten Anwendung übernimmt das ein Sensor – und genau darum geht es in diesem Tutorial.

* Du baust dein Toilettenhäuschen-Modell jetzt mit einem Taster (Magnetic Switch) aus.

Am Ende hast du ein Programm, das …

* eine LoRa-Verbindung🛜 aufbaut
* den Status der Toilette 🚽 über den Taster erkennt
* den Status 🚽 über LoRa🛜 ins Internet sendet

Brauchbare Funktionen aus Teil 2 sind schon integriert; das Auswerten der Tasten A und B
wurde hingegen entfernt.

Falls dir am bestehenden Code etwas unklar ist, lohnt es sich,
diesen Teil noch einmal in Ruhe durchzugehen.

## Hardware vorbereiten
* Du bekommst dieses WC-Häuschen-Modell: ![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/wc-haus.png)
* Du brauchst zusätzlich dieses Kunststoffteil: [Taster-Halterung](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/3dModel/taster-halterung.html)
* Falls du das Teil selbst 3D-drucken möchtest, lade das STL-File hier herunter: [🌍 STL-3D-Modell](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/3dModel/taster-halterung.stl)
* Schliesse den Grove Magnetic Switch an **J3** an.

## Magnet-Schalter🧲 auslesen
Der Magnet-Schalter (Magnetic Switch) liefert ein digitales Signal,
das wir einfach auswerten können:

0 → kein Magnet in der Nähe → Tür offen

1 → Magnet 🧲 in der Nähe → Tür geschlossen 

Damit wir den Zustand der Tür laufend erkennen, lesen wir den Sensor in der
bestehenden ``||basic:dauerhaft||``-Schleife regelmässig
aus und übersetzen das Signal in "offen" oder "geschlossen".

* ``||variables:Erstelle eine Variable...||`` und benenne sie **zustandTür**.
* Setze die Variable **zustandTür** zuoberst in der
``||basic:dauerhaft||``-Schleife auf den Zustand, der am Pin P2 gemessen wird. Verwende
  dazu die Blöcke ``||variables:setze zustandTür auf 0||`` sowie
  ``||pins:digitale Werte von Pin P0||`` und ersetze die 0 durch den Pins-Block.
* Stelle im Pins-Block P0 auf P2 um.

```blocks
//@hide
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
//@hide
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
//@hide
function sendeDaten (status: number) {
    if (control.millis() > msBeiLetztemSenden + 5000) {
        IoTCube.addBinary(eIDs.ID_0, status)
        IoTCube.SendBufferSimple()
        music.play(music.tonePlayable(262, music.beat(BeatFraction.Whole)), music.PlaybackMode.UntilDone)
        spaeterSenden = false
        msBeiLetztemSenden = control.millis()
    } else {
        spaeterSenden = true
    }
}
let statusFreiOderBesetzt = 0
let spaeterSenden = false
let msBeiLetztemSenden = 0
IoTCube.LoRa_Join(
eBool.enable,
eBool.enable,
10,
8
)
//@hide
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
let zustandTür = 0
//@highlight
let zustandTürDavor = -1
macheFrei()
basic.forever(function () {
    //@highlight
    zustandTür = pins.digitalReadPin(DigitalPin.P2)
    if (spaeterSenden) {
        sendeDaten(statusFreiOderBesetzt)
    }
})
```

## Logik zum Senden

Wenn die Tür geschlossen ist, nehmen wir an, das WC sei **Besetzt**. Andernfalls
nehmen wir an, das WC sei **Frei**. Diese Zustände wollen wir einerseits
anzeigen, andererseits über LoRa🛜 in die Claviscloud schicken, was 
zum Glück beides schon vorbereitet ist.

* Setze unter das Auslesen des Magnetic Switch einen
  ``||logic:wenn wahr dann||``-Block.
* Prüfe mit ``||logic:Vergleich 0 = 0||``, ob ``||variables:zustandTür||``
  den Wert 1 hat (Tür geschlossen).
* Wenn dies der Fall ist, rufe die Funktion ``||function:Aufruf macheBesetzt||`` auf,
  andernfalls ``||function:Aufruf macheFrei||``. (Die Funktionen findest du im Bereich
  **Fortgeschritten** – klappe ihn bei Bedarf zuerst auf.)
* Klicke auf 📥 `|Download|`.
* Prüfe, ob in der Cloud die Änderung des Zustands (frei oder besetzt) angezeigt wird:
  [iot.claviscloud.ch](https://iot.claviscloud.ch/dashboards/)
* Behebe gegebenenfalls aufgetretene Fehler. Klicke auf das 💡-Symbol bei Schwierigkeiten.
* Fällt dir sonst noch etwas auf? Gibt es Dinge, die du optimieren könntest?


```blocks
//@hide
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
//@hide
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
//@hide
function sendeDaten (status: number) {
    if (control.millis() > msBeiLetztemSenden + 5000) {
        IoTCube.addBinary(eIDs.ID_0, status)
        IoTCube.SendBufferSimple()
        music.play(music.tonePlayable(262, music.beat(BeatFraction.Whole)), music.PlaybackMode.UntilDone)
        spaeterSenden = false
        msBeiLetztemSenden = control.millis()
    } else {
        spaeterSenden = true
    }
}
let statusFreiOderBesetzt = 0
let spaeterSenden = false
let msBeiLetztemSenden = 0
IoTCube.LoRa_Join(
eBool.enable,
eBool.enable,
10,
8
)
//@hide
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
let zustandTür = 0
//@highlight
let zustandTürDavor = -1
macheFrei()
basic.forever(function () {
    zustandTür = pins.digitalReadPin(DigitalPin.P2)
    //@highlight
    if (zustandTür == 1) {
        macheBesetzt()
    } else {
        macheFrei()
    }
    if (spaeterSenden) {
        sendeDaten(statusFreiOderBesetzt)
    }
})
```

## Optimieren

Im Moment wird alle 5 Sekunden gesendet, auch ohne Zustandswechsel. 
Das kostet unnötig Energie. Sinnvoller ist es, nur bei einer Änderung 
des Türzustands zu senden.

* ``||variables:Erstelle eine Variable...||`` und benenne sie **zustandTürDavor**.
* Setze ganz am Ende im Block ``beim Start`` die Variable
  ``||variables:zustandTürDavor||`` auf -1, damit sie sich beim ersten Durchlauf garantiert
  vom gemessenen Wert unterscheidet: ``||variables:setze zustandTürDavor auf -1||``
* Prüfe in der ``||basic:dauerhaft||``-Schleife, ob sich die Variablen ``||variables:zustandTür||`` und ``||variables:zustandTürDavor||``
  unterscheiden (≠-Vergleich). Wenn ja, aktualisiere **zustandTürDavor**
  und führe nur dann die bestehende Logik aus. Verwende dafür diese Blöcke:
  * ``||logic:wenn wahr dann||`` sowie ``||logic:0 ≠ 0||``
  * ``||variables:setze zustandTürDavor auf zustandTür||``
  * Verschiebe nun deine bisherige Abfrage (Tür = 1) in diesen Wenn-Block.
    (Dadurch werden die Funktionen nur noch ausgeführt, wenn sich der Türzustand
    ändert.)

```blocks
//@hide
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
//@hide
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
//@hide
function sendeDaten (status: number) {
    if (control.millis() > msBeiLetztemSenden + 5000) {
        IoTCube.addBinary(eIDs.ID_0, status)
        IoTCube.SendBufferSimple()
        music.play(music.tonePlayable(262, music.beat(BeatFraction.Whole)), music.PlaybackMode.UntilDone)
        spaeterSenden = false
        msBeiLetztemSenden = control.millis()
    } else {
        spaeterSenden = true
    }
}
let statusFreiOderBesetzt = 0
let spaeterSenden = false
let msBeiLetztemSenden = 0
IoTCube.LoRa_Join(
eBool.enable,
eBool.enable,
10,
8
)
//@hide
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
let zustandTür = 0
//@highlight
let zustandTürDavor = -1
macheFrei()
basic.forever(function () {
    zustandTür = pins.digitalReadPin(DigitalPin.P2)
    //@highlight
    if (zustandTür != zustandTürDavor) {
        zustandTürDavor = zustandTür
        if (zustandTür == 1) {
            macheBesetzt()
        } else {
            macheFrei()
        }
    }
    if (spaeterSenden) {
        sendeDaten(statusFreiOderBesetzt)
    }
})
```

## Gratuliere 🏆 – du hast das Tutorial erfolgreich bearbeitet 🚀

* Verbinde deine smarte Toilette mit dem Toiletten-Widget der [Claviscloud](https://iot.claviscloud.ch/).
* Teste, ob die Daten korrekt angezeigt werden.
* Falls etwas noch nicht richtig läuft, findest du hier eine funktionierende Version zum Testen: [Lösung](https://makecode.microbit.org/#tutorial:github:reifab/pxt-smart-toilet-tutorial/docs/tutorials/smart-toilet-part3-solution)

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
function sendeDaten (status: number) {
    if (control.millis() > msBeiLetztemSenden + 5000) {
        IoTCube.addBinary(eIDs.ID_0, status)
        IoTCube.SendBufferSimple()
        music.play(music.tonePlayable(262, music.beat(BeatFraction.Whole)), music.PlaybackMode.UntilDone)
        spaeterSenden = false
        msBeiLetztemSenden = control.millis()
    } else {
        spaeterSenden = true
    }
}
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

basic.forever(function () {
    if (spaeterSenden) {
        sendeDaten(statusFreiOderBesetzt)
    }
})

```
