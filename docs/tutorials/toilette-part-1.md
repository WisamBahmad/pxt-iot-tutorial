```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```
### @explicitHints false

# IoT Tutorial Toilette Teil 1 (In Arbeit)


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