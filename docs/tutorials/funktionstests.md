```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```

# IoT-Cube Test-Tutorial

## Vollständigkeit des Boxes überprüfen

Siehe Bild im Box

* 1: Ultrasonic Distance
* 2: Gesture
* 3: VOC and eCO2
* 4: Infrared Reflective
* 5: Magnetic Switch
* 6: UV Sensor
* 7: Sunlight Sensor
* 8: Sliding Potentiometer
* 9: Micro Switch
* 10: 12 Hub 6 Ports
* 11: Relay
* 12: Electromagnet
* Grove Kabel (5 Stk.)
* Y-USB Kabel
* Capacitive Moisture (inkl. Kabel)
* IoT-Würfel
* LED Strip
* Haftmagnet Neodym
* Servo (inkl. Adapterkabel)

Here is some text.

## Step 2

Testprogramm

```template
input.onButtonPressed(Button.A, function () {
    IoTCube.OTAA_Setup(
    "0000000000000007",
    "AC1F09FFFE0531A9",
    "A4ADC71EC8FD44B4C8C5EBB078947DC6",
    eBands.EU868,
    "A",
    eBool.enable
    )
    IoTCube.LoRa_Join(
    eBool.enable,
    eBool.enable,
    10,
    8
    )
})
input.onButtonPressed(Button.B, function () {
    if (IoTCube.getStatus(eSTATUS_MASK.JOINED) == true) {
        IoTCube.addTemperature(20.5, 1)
        IoTCube.SendBuffer(IoTCube.getCayenne())
    }
})
basic.pause(2000)
IoTCube.LoRa_Join(
eBool.disable,
eBool.disable,
10,
8
)
OLED.init(128, 64)
OLED.clear()
OLED.writeStringNewLine(IoTCube.getParameter(eRUI3_PARAM.VERSION))
```

