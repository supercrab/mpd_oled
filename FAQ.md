#FAQ for mpd_oled#

### How can I change the layout?

There are no configuration options to change the layout If you
wish to change the layout then you will need to change the code.

### How can I remove the spectrum analiser?

There are no configuration options to remove the spectrum If you
wish to change the layout then you will need to change the code.

### What code do I change to customise the display

In main.cpp there are two simple functions that draw the layout

   draw_clock():         draws the stop screen
   draw_spect_display(): draws the play/pause screen

If you remove the spectrum analyser component then there is no need
to change any other code, just use a nonexistant file for the FIFO
and this should avoid any CPU usage, e.g.

   mpd_oled -c fifo,/tmp/dummy

### The spectrum analyser isn't working, what do I do

Check that you followed *all* the instructions to set up the audio
copy. Check that cava is installed, e.g. run ```which cava```


