# Moode, Volumio, RuneAudio and MPD OLED Spectrum Display for Raspberry Pi

The mpd_oled program displays an information screen including a music
frequency spectrum on an OLED screen connected to a Raspberry Pi (or similar)
running MPD, this includes Moode, Volumio and rAudio (RuneAudio fork).
The program supports I2C and SPI 128x64 OLED displays with an SSD1306,
SSD1309, SH1106 or SSH1106 controller.
![OLED with mpd_oled](mpd_oled.jpg)

## Build and install

The instructions depend on the player

* [Build and install on Volumio](doc/INSTALL_VOLUMIO.md)
* [Build and install on Moode](doc/INSTALL_MOODE.md)
* [Build and install on rAudio](doc/INSTALL_RAUDIO.md)
* Build and install on Debian-based OS running MPD: follow the instructions
  for [Build and install on Volumio](doc/INSTALL_VOLUMIO.md) but configure
  a copy of the audio by editing /etc/mpd.conf directly and appending
  the contents of `/usr/local/share/mp_oled/mpd_oled_fifo.conf`.

Please check the [FAQ](FAQ.md)

## Credits

C.A.V.A. is a bar spectrum audio visualizer: <https://github.com/karlstav/cava>

OLED interface based on ArduiPI_OLED: <https://github.com/hallard/ArduiPi_OLED>
(which is based on the Adafruit_SSD1306, Adafruit_GFX, and bcm2835 library
code).

C library for Broadcom BCM 2835: <https://www.airspayce.com/mikem/bcm2835/>
