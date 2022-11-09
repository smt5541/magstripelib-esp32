What is it?
===========

**MagStripe** is an [Arduino](http://arduino.cc/) library for [ESP32](https://docs.espressif.com/projects/arduino-esp32/en/latest/) to interface with TTL (raw) magnetic card readers.
It supports reading any of the cards' possible three tracks, but not simultaneously. Most cards only contain
data on tracks 1 and 2 though.

![MagStripe-KDE.jpg](https://raw.githubusercontent.com/smt5541/magstripelib-esp32/master/MagStripe-KDE.jpg)

Card Readers
============

There are many brands of TTL magnetic card readers on the market, some with a fixed reading head and others
with screws allowing the head to be manually positioned to choose one of the tracks. The connector comes with
varying colors for each wire, but usually there are five wires with the following order and function:

  * Ground (black wire)
  * RDT/data
  * RCL/clock
  * CLS/card present
  * Power/+5V (red wire)

For more information, you can check the
[KDE KDR-1000 datasheet](https://github.com/smt5541/magstripelib-esp32/blob/docs/docs/KDR1000.pdf) (pictured above),
which can read all three tracks by repositioning the head, or the
[Panasonic ZU-M1121 datasheet](https://github.com/smt5541/magstripelib-esp32/blob/docs/docs/ZU-M1121S1.pdf) for an
example of another reader which can only read track 2 and has a different pin arrangement.
[]

**Note:** This library is designed for **swipe-type** readers (like the one pictured above) meaning it requires the CLS pin to be active _low_ while the card is being swiped and _high_ otherwise. **Insert-type** readers usually have the CLS pin connected to a switch instead (active _high_ when the card is fully inserted and _low_ otherwise). This behavior is not supported by this library. Check your reader's datasheet if you're not sure how your reader behaves.

Installation
============

Download the latest zip file from the [releases](https://github.com/smt5541/magstripelib-esp32/releases) section. Then open it from the `Sketch > Include Library > Add .ZIP Library...` menu inside the Arduino IDE and a new "MagStripe" entry should appear in the `Sketch > Include Library` and `File > Examples` menus.

![Arduino IDE](https://raw.githubusercontent.com/smt5541/magstripelib-esp32/master/screenshot-01.png)

How it Works
============

**Note:** This library has only been tested on the **ESP32-C3-MINI-1** chip on the **ESP32-C3-DevKitM-1**, performance on other ESP32 boards is not guaranteed. If you have another ESP32 and confirm that this works, please submit a PR to update this README. Conversely, if you have an issue on another ESP32, please [submit an issue](https://github.com/smt5541/magstripelib-esp32/issues/new).

Connect your card reader to the following digital pins of the **ESP32**:

ESP32 Pin   | Card Reader Pin
--------------|-----------------
Digital Pin 2 | RDT/data
Digital Pin 3 | RCL/clock
Digital Pin 4 | CLS/card present

If your reader can read multiple tracks at a time, and thus has extra sets of data and clock wires, check its
datasheet and use the ones appropriate for the track you want to read.

The library has a thin [interface](MagStripe.h) and is very straightforward to use, just check if there is a
card present before trying to read. The included [`MagStripeReader.ino`](examples/MagStripeReader/MagStripeReader.ino)
example shows how to do it.

The `read()` method does the necessary validation checks and only returns data if it has been read correctly
from the card. The data returned is a string with the (ASCII) full contents of the track, including the control
characters.

To know about the format of data returned for each track, check the
[magnetic card standards](https://github.com/smt5541/magstripelib-esp32/blob/docs/docs/layoutstd.pdf)
reference.
