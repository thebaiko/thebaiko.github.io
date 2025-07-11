---
title: "Abstract aircraft sculpture"
excerpt: ""
toc_label: "Contents:"
header:
  teaser: /assets/images/p3_01.jpg
gallery:
  - url: /assets/images/p3_01.jpg
    image_path: /assets/images/p3_01.jpg
  - url: /assets/images/p3_02.jpg
    image_path: /assets/images/p3_02.jpg
  - url: /assets/images/p3_vid.gif
    image_path: /assets/images/p3_vid.gif
gallery2:
  - url: /assets/images/p3_guide.png
    image_path: /assets/images/p3_guide.png
  - url: /assets/images/p3_sch.png
    image_path: /assets/images/p3_sch.png
  - url: /assets/images/p3_ftp.png
    image_path: /assets/images/p3_ftp.png

---

## Description
Ultra low power ATtiny85-based 'glass PCB' style electronics sculpture, featuring a pair of position and strobe lights that activate automatically at night. An abstract representation of an aircraft, quite useless if you ask me.  
I need to get a power profiler to find the real power consumption but it's been running for weeks now on a 30mAh coin cell.

## Materials

| Item         | Quantity | Description                                                  |
| --------         | ------   | ------------------------------------------------------------ |
| **Soldering required** |    |                                                          |
| Copper tape   | 1   |                                                        |
| Glass      | 1       |                                                       |
| 0805 SMD LEDs    | 3        | Red, red, green  |
| 0805 Resistors       | 4        |  220, 2x 22K, 47K ohm                                        |
| LDR | 1 | GL5516 Photoresistor |
| 0805 Capacitor | 1 | 0.1uF |
| 8-DIP ATtiny85 | 1 | Microcontrolller |
| Programmer or Arduino | 1 | To program the ATtiny85 |
| CR927 coin cell | 1 | 3V |
| 90º pin headers | 6 | To make a dirty surface-mount battery holder |
| Epoxy | | Optional but desirable to secure everything in place |

## Schematics
{% include gallery id="gallery2" caption=""%}

## Preparation
- Program the ATtiny85, I used Arduino as ISP.

- Print the circuit guide, place it beneath the glass and replicate it over the glass with thin copper tape. If the adhesive side is non-conductive, solder the tape joints. Use flux and low temperature or you'll burn the tape.

- Use the schematic and footprint diagram as a guide to place and solder the components. Start with the pin headers (bend them inwards a little with pliers to give them enough tension to hold the coin cell) and work your way out to the edges.

## Program

This project uses the [TinySnore library][snore]. The program is uploaded using Arduino as ISP.
[snore]: {{ "https://github.com/connornishijima/TinySnore" | relative_url }}

```cpp
#include <tinysnore.h>        // Low power library; not needed, but makes it simpler
 
const byte ledsPin = 1;       // Low If LED-resistor combination allows to stay well under the 20mA output limit
const byte beaconPin = 0;
const byte ldrpwrPin = 2;     // Pin to 'power up' the LDR only while we read the voltage
const byte ldrdataPin = 3;    // Voltage read 
 
void setup() {
  pinMode(ledsPin, OUTPUT);
  pinMode(ldrpwrPin, OUTPUT);
  pinMode(beaconPin, OUTPUT);
  pinMode (4, INPUT_PULLUP);  // Save some power, probably redundant
}
 
void loop() {
  digitalWrite(ldrpwrPin, HIGH);              // Power up the LDR
  delay(5);                                   // Stabilize
  int sensorValue = analogRead(ldrdataPin);   // Read LDR at voltage divider
  digitalWrite(ldrpwrPin, LOW);               // Power down LDR
  if (sensorValue <= 300) {                   // Modify value to make it more or less sensitive to light (0-1023)
    digitalWrite(beaconPin, HIGH);
    digitalWrite(ledsPin, HIGH);
    delay(15);                                // The beacon is flashing
    digitalWrite(beaconPin, LOW);
    snore(1000);                              // Go to sleep with the position lights ON; change value to
  }                                           // change flashing interval
  else {
    digitalWrite(ledsPin, LOW);
    snore(3000);                              // Go to sleep with the position lights OFF; increase
  }                                           // value to save power but it'll be less reactive
  delay(10);
}
```






## Links
Check out the [TinySnore library][snore].

[snore]: {{ "https://github.com/connornishijima/TinySnore" | relative_url }}

## Gallery
{% include gallery caption=""%}
