# Ubuntu @ Razer Blade Stealth (late 2019) Mercury White

## This repository holds all info for running Ubuntu 19.10 on the Blade
In general these changes should be also valid for newer versions of Ubuntu - be aware that the settings are only tested with the following system setup.

- Ubuntu 19.10 using Xorg
- Gnome 3.34.2
- Grub2
- Razer Blade Stealth (Core i7-1065G7, Intel Iris Plus Graphics, 16GB Ram, 256GB NMVe SSD)


## Razer Keyboard lightning

You can use the famous _openrazer_ (https://openrazer.github.io/) repository to get the necessary drivers for almost all razer systems.

Installation:

    sudo add-apt-repository ppa:openrazer/stable
    sudo apt update
    sudo apt install openrazer-meta

For modifying the keyboard lightning / effects I use _Polychromatic_ (https://github.com/polychromatic/polychromatic).

Use it with these commands:

    sudo add-apt-repository ppa:polychromatic/stable
    sudo apt update
    sudo apt install polychromatic

## Display will not turn on after sleep

Edit the '/etc/default/grub' file and add this line:

    GRUB_CMDLINE_LINUX_DEFAULT="quiet splash button.lid_init_state=open"

to apply the settings:

    sudo update-grub
    sudo shutdown -r now

This will tell the system that the initial state of the display is open - and turned on.

## Power saving with TLP

For getting the best battery life on the notebook I installed _TLP_ (https://github.com/linrunner/TLP) to configure CPU power state and more. The latest version (1.3.x) also supports the latest Intel Ice Lake CPUs which is important for the Razer Stealth.

Installation:

    sudo add-apt-repository ppa:linrunner/tlp
    sudo apt-get update
    sudo apt-get install tlp tlp-rdw

To start the service:
    
    sudo tlp start

__Be aware__ that know the system sleep mode is __broken__. The reason is as simple as stupid - the default TLP configuration sets maximum CPU performance on battery mode. This also prevents the CPU to go into a deeper sleeping mode (>S0).

To avoid that I created an TLP config file which fix this problem.
Just copy the file (https://github.com/Dragon160/Ubuntu-RazerBladeStealth2019/blob/master/src/tlp-razer.conf) to this location:

    /etc/tlp.d/tlp-razer.conf

It will just override the given settings from the defaults one.
After coping you need to start the service again.
    
    sudo tlp start

Now the sleeping problem should be solved!

## Improve Touchpad drivers (nope - no swipe gestures)

IMHO the synaptics driver are performing better than the build-in libinput ones.

To verify which driver are currently active you need to perform the following

    xinput --list
        -> will return devices and there IDs (touchpad is for me ID 14)
    xinput --list-props 14
        -> will list all properties and the used driver

To switch from libinput to synaptics you just need to install the synaptics driver (__do not delete libinput__ as it will be used by the keyboard as well).

Installation and reboot:

    sudo apt install xserver-xorg-input-synaptics
    sudo systemctl shutdown -r now

Done.

A note to the swipe gestures:
_I was not able_ to determine how to enable 3-gestures swipes or similar. It seems that this is not supported by the synaptics driver right now. The supported features can be displayed by using:

    synclient

Please give a not if you have found a working solution! :-)

