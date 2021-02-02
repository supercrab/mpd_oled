# Install instructions for Moode

These instructions are compatible with Moode 7 and 6 (and Moode 5 later versions)
using a 32-bit kernel architecture.

## Base system

Install [Moode](http://moodeaudio.org/). Ensure a command line prompt is
available for entering the commands below (e.g. use SSH, enable in the Moode UI
at **Configure / System / Local Services / SSH term server**, log in with
default username 'pi', default password 'moodeaudio').

## Install all dependencies
```
sudo apt update
sudo apt install autoconf make libtool libfftw3-dev libmpdclient-dev libi2c-dev i2c-tools lm-sensors
```

## Build and install cava

mpd_oled uses Cava, a bar spectrum audio visualizer, to calculate the spectrum
   
   <https://github.com/karlstav/cava>

If you have Cava installed (try running `cava -h`), there is no need
to install Cava again, but to use the installled version you must use
`mpd_oled -k ...`.

Download, build and install Cava. These commands build a reduced
feature-set executable called `mpd_oled_cava` (to avoid overwriting
an existing Cava install)
```
git clone https://github.com/karlstav/cava
cd cava
./autogen.sh
./configure --disable-input-portaudio --disable-input-sndio --disable-output-ncurses --disable-input-pulse --program-prefix=mpd_oled_
make
sudo make install-strip
```

## Build and install mpd_oled

Download, build and install mpd_oled.
```
cd ..   # if you are still in the cava source directory
git clone https://github.com/antiprism/mpd_oled
cd mpd_oled
./bootstrap
CPPFLAGS="-W -Wall -Wno-psabi" ./configure
make
sudo make install-strip
```

## System settings

Configure your system to enable I2C or SPI, depending on how your OLED
is connected.

### I2C
I use a cheap 4 pin I2C SSH1106 display with a Raspberry Pi Zero. It is
[wired like this](wiring_i2c.png).

In /etc/modules I have the line `i2c-dev`
```
sudo nano /etc/modules
```

In /boot/config.txt I have the line `dtparam=i2c_arm=on`.
The I2C bus speed on your system may be too slow for a reasonable screen
refresh. Set a higher bus speed by adding
the following line `dtparam=i2c_arm_baudrate=400000` to
/boot/config.txt, or try a higher value for a higher screen
refresh (I use 800000 with a 25 FPS screen refresh)
```
sudo nano /boot/config.txt
```

Restart the Pi after making any system configuration changes.

### SPI
I use a cheap 7 pin SPI SSH1106 display with a Raspberry Pi Zero. It is
[wired like this](wiring_spi.png).
In /boot/config.txt I have the line `dtparam=spi=on`.
```
sudo nano /boot/config.txt
```

Restart the Pi after making any system configuration changes.

## Configure a copy of the playing audio

You may wish to [test the display](#test-the-display) before
following the next instructions.

*The next instructions configure MPD to make a*
*copy of its output to a named pipe.*
*This works reliably, but has two disadvantages: the configuration*
*involves patching Moode, which may inhibit Moode upgrades; the spectrum*
*only works when the audio is played through MPD, like music files,*
*web radio and DLNA streaming. Creating a copy of the audio for all*
*audio sources is harder, and may be unreliable -- see the thread on*
*[using mpd_oled with Spotify and Airplay](https://github.com/antiprism/mpd_oled/issues/4)*

The MPD audio output will be copied to a named pipe, where Cava can
read it and calculate the spectrum. This is configured in /etc/mpd.conf.
However, Moode regenerates this file, and also disables all but a single MPD
output, in response to various events, and so the Moode code must be changed.

**Moode 6 only:** Moode 6 includes technical measures to disallow
code changes; run the following commands to disable them
```
sqlite3 /var/local/www/db/moode-sqlite3.db "DROP TRIGGER ro_columns"
sqlite3 /var/local/www/db/moode-sqlite3.db "UPDATE cfg_hash SET ACTION = 'warning' WHERE PARAM = '/var/www/command/worker.php'"
sqlite3 /var/local/www/db/moode-sqlite3.db "UPDATE cfg_hash SET ACTION = 'warning' WHERE PARAM = '/var/www/inc/playerlib.php'"
```

Carrying on for Moode 6 and 7, copy the FIFO configuration file to
/usr/local/etc/mpd_oled_fifo.conf
```
sudo cp mpd_oled_fifo.conf /usr/local/etc/
```

Patch the Moode source code. (Note 1:
a Moode system update may overwrite the patched code, in which case, repeat
the next instructions, and possibly also the previous instructions.
Note 2: if, for any reason, regeneration of
/etc/mpd.conf has been disabled (for example, if it has been set immutable)
then edit the file directly and append the contents of mpd_oled_fifo.conf.)

Run **just one** of the following three patch commands, depending on your
Moode version.

Patch Moode 7 (do not run this on Moode 6)
```
sudo patch -d/ -p0 -N < moode7_mpd_fifo.patch  # Patch Moode 7
```

Patch Moode 6.5 and later (do not run this on Moode 7)
```
sudo patch -d/ -p0 -N < moode6_mpd_fifo.patch  # Patch Moode 6.5 and later
```

Patch Moode 6.4 and earlier (may work on Moode 5, later versions)
```
sudo patch -d/ -p0 -N < moode_old_mpd_fifo.patch  # Patch Moode 6.4 and earlier
```
If you ever want to make any changes to the FIFO configuration,
then modify /usr/local/etc/mpd_oled_fifo.conf and restart MPD,
by going to the Moode UI Audio Config page and clicking on
"RESTART" in the MPD section (for Moode 5, restart MPD now).

Now, enable the Moode metadata file
```
sqlite3 /var/local/www/db/moode-sqlite3.db "update cfg_system set value=1 where param='extmeta'" && mpc add ""

```
Go to the Moode UI and set your timezone at **Moode / Configure / System**.

**Essential: reboot the machine**.

## Test the display

Check the program works correctly by running a test command and checking
the display while the player is stopped, paused and playing music.

The program can be tested without the audio copy enabled, in which
case the spectrum analyser are will be blank.

The OLED type MUST be specified with -o from the following list:
    1 - Adafruit SPI 128x64,
    3 - Adafruit I2C 128x64,
    4 - Seeed I2C 128x64,
    6 - SH1106 I2C 128x64.
    7 - SH1106 SPI 128x64.

E.g. the command for a generic I2C SH1106 display (OLED type 6) with
a display of 10 bars and a gap of 1 pixel between bars and a framerate
of 20Hz is
```
sudo mpd_oled -o 6 -b 10 -g 1 -f 20
```
The program can be stopped by pressing Control-C.

For I2C OLEDs (mpd_oled -o 3, 4 or 6) you may need to specify
the I2C address, find this by running,
e.g. `sudo i2cdetect -y 1` and then specify the address with mpd_oled -a,
e.g. `mpd_oled -o6 -a 3d ...`.
If you have a reset pin connected, specify
the GPIO number with mpd_oled -r, e.g. `mpd_oled -o6 -r 24 ...`.
Specify the I2C bus number, if not 1,
with mpd_oled -B, e.g. `mpd_oled -o6 -B 0 ...`

For, SPI OLEDs (mpd_oled -o 1 or 7), you may need to specify your reset pin
GPIO number (mpd_oled -r, default 25), DC pin GPIO number (mpd_oled -D,
default 24) or CS value (mpd_oled -S, default 0).

If your display is upside down, you can rotate it 180 degrees with option '-R'.

Once the display is working, play some music and check the spectrum display
is working and is synchronised with the music. If there are no bars then the
audio copy may not have been configured correctly. If the bars seem jerky
or not synchronized with the music then reduce the values of -b and/or -f.

## Install the mpd_oled service

When you have chosen some suitable options, install and configure
an mpd_oled service file so that mpd_oled will run at boot.

Install a service file. This will not overwrite an existing mpd_oled
service file.
```
sudo mpd_oled_install.sh
```

Edit the service file to include your chosen options. Rerun
this command any time to change the options. You *must* include a
valid -o parameter for your OLED. If the command appears to hang,
allow it some time to complete. If the included mpd_oled options are
valid then mpd_oled will start running on the display when the
command completes.

Either, run the command with no options, which will open an editor, then
add your options (from a successful mpd_oled test command) on the line
starting `ExecStart` and after `mpd_oled`.

```
sudo mpd_oled_edit.sh     # edit mpd_oled options with editor
```

Or, append all your options (from a successful mpd_oled test command)
to the command and the service file will be updated to use these
optiond for mpd_oled, e.g. the following will cause the service to
run `mpd_oled -o 6 -b 10`
```
sudo mpd_oled_edit.sh -o 6 -b 10
```

Commands from the following list can be run to control the service
(they do not need to be run from the mpd_oled directory)
```
sudo systemctl enable mpd_oled    # start mpd_oled at boot
sudo systemctl disable mpd_oled   # don't start mpd_oled at boot
sudo systemctl start mpd_oled     # start mpd_oled now
sudo systemctl stop mpd_oled      # stop mpd_oled now
sudo systemctl status mpd_oled    # report the status of the service
```

If you wish to uninstall the mpd_oled service (just the service,
the command does not uninstall the mpd_oled or cava binaries)
```
sudo mpd_oled_uninstall.sh
```

