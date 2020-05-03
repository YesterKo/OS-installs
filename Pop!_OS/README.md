# Pop!_OS

## Installation

So, first up, has a very nice and straight-forward installer, and got installed with no hiccups.

## Post-installation

### Trackpad
One thing I noticed very quickly is that the touchpad's right- and middle-click didn't work.

For that I installed GNOME Tweaks using `sudo apt install gnome-tweaks` and changed "mouse click emulation" under Keyboard & Mouse. [source](https://support.system76.com/articles/touchpad/)

### Terminal
For the terminal I made it transparent by going into it's preferences, selecting the profile you want to change and under colours disable 'use transparency from system theme'  and enable 'use transparent backround'.

Oh and also I changed the keybind for opening a terminal to ctrl-alt-t.

### Battery

For battery management I installed TLP.

I installed it using `sudo apt install tlp tlp-rdw --no-install-recommends` gotten from the [System76 page](https://support.system76.com/articles/battery/)
