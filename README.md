```package
cube=github:Smartfeld/pxt-iot-cube
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```
# IoT Tutorial

## 1 Einführung

Wir wollen im Rahmen unserer Firma einen Seifenspender smarter machen. Dazu nutzen wir den IoT Cube. 

Auf dem Microbit gibt es zwei Knöpfe, mit welchen wir die Hardware simulieren wollen:

1. Knopf A geklickt -> Seifenspender wird benutzt
2. Knppf B geklickt -> Seifendspender wurde aufgefüllt

Wenn der Seifenspender leer ist, soll dies auf dem Dashboard angezeigt werden.

## 2 Knöpfe vorbereiten

Hol dir einen Block ``||input:wenn Knopf A geklickt||``. Kopiere diesen Block mit **rechtsklick Duplizieren** und ändere beim kopierten Block **A zu B**.

## 3 Variable für den Füllstand

Um den Füllstand des Seifenspenders zu speichern, setzen wir eine Variable ein.
 ``||variables:Erstelle eine Variable...||`` und benenne sie zum Beispiel mit **seifenStand**.

Danach  ``||variables:setze seifenStand auf 10||`` und kopiere diese Anweisung in den Block ``||basic:beim Start||``.

## 4 Füllstand anzeigen

Hol dir den Block ``||led:Zeichne Säulendiagramm|`` und ziehe diesen in den Block ``beim Start`` direkt unter der Variablendeklaration.

Hol die Variable ``||variables:seifenStand||`` um sie mit dem Säulendiagramm darzustellen. Ändere den Bereich von 0 auf 10. 

## 5 Füllstand reduzieren beim Drücken von A

Wenn Knopf A gedrückt wird, wird der Seifenspender benutzt und der Füllstand soll sich reduzieren.

Reduziere nun den Füllstand um 1, immer wenn A gedrückt wird. Du kannst dies mit ``||variables:ändere seifenStand um 1||`` tun, wobei du das 1 durch -1 ersetzt. 

Für die erneute Anzeige dupliziere den Block ``||led:Zeichne Säulendiagramm|`` und zeige das Säulendiagramm bei jedem Tastendruck an.

## 6 Testen

Teste die bisherige Funktion. Was geht schief, wenn der Füllstand 0 unterschitten wird?
