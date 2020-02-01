# Ubuntu @ Razer Blade Stealth (late 2019) Mercury White

## This repository holds all info for running Ubuntu 19.10 on the Blade
In general these changes should be also valid for newer versions of Ubuntu - be aware that the settings are only tested with the following system setup.

- Ubuntu 19.10 using Xorg
- Gnome 3.34.2
- Grub2
- Razer Blade Stealth (Core i7-1065G7, Intel Iris Plus Graphics, 16GB Ram, 256GB NMVe SSD)


## Razer Keyboard lightning

tbd

## Display will not turned on after sleep


Edit the '/etc/default/grub' file and add this line:

    GRUB_CMDLINE_LINUX_DEFAULT="quiet splash button.lid_init_state=open"

to apply the settings:

    sudo update-grub
    sudo shutdown -r now

This will tell the system that the initial state of the display is open - and turned on.

## Power saving with TLP

tbd

## Improve Touchpad drivers

tbd