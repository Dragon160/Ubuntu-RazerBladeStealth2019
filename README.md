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

## Enable suspend-then-hibernate

One thing I loved on Windows was the default behavior to sleep and than after 1-2 hours to hibernate. That will save you a lot of power for e.g. over the night.

Luckily with Ubuntu this feature just needs to be enabled. First, you need to enable the delay for the suspend-then-hibernate mode:

    /etc/systemd/sleep.conf
        --> HibernateDelaySec=180min is the default. Change it, if needed

Next, go to

    /etc/systemd/logind.conf

    Change the content of these to options:
    HandleLidSwitch=suspend-then-hibernate
    HandleLidSwitchExternalPower=suspend-then-hibernate

Thats it! Do not forget to restart the system or the systemd-logind service.

### Enable hibernate at all

If hibernate is not working for you at all (see test below) you need to do serveral things.

    // test if hibernate is working - after power on again, it should not be a reboot
    sudo systemctl hibernate

This blog describes it very good:
https://notes.palsch.de/2019/07/ubuntu-18-04-lts-hibernate-to-swapfile/

Basically its all about increasing your swapfile, and edit the grub to use the swapfile as an resume point.


## Power saving with TLP

For getting the best battery life on the notebook I installed _TLP_ (https://github.com/linrunner/TLP) to configure CPU power state and more. The latest version (1.3.x) also supports the latest Intel Ice Lake CPUs which is important for the Razer Stealth.
(BTW: I did not performed any battery test so far - it seems that the battery is also without TLP quite good.)

Installation:

    sudo add-apt-repository ppa:linrunner/tlp
    sudo apt-get update
    sudo apt-get install tlp tlp-rdw

To start the service:

    sudo tlp start

__Be aware__ that now the system sleep mode is __broken__. The reason is as simple as stupid - the default TLP configuration sets maximum CPU performance on battery mode. This also prevents the CPU to go into a deeper sleeping mode (>S0).

To avoid that I created an TLP config file which fix this problem.
Just copy the file (https://github.com/Dragon160/Ubuntu-RazerBladeStealth2019/blob/master/src/tlp-razer.conf) to this location:

    /etc/tlp.d/tlp-razer.conf

It will just override the given settings from the defaults one.
After coping you need to start the service again.

    sudo tlp start

Now the sleeping problem should be solved!

## Touchpad multitouch / swipe

Out of the box unfortuntely only 2-finger scrolling is supported. To recognize swipe and pinch gestures you can use a great app called _Fusuma_

    https://github.com/iberianpig/fusuma

It works out of the box - do not forget to start it with the command:

    fusuma

You can find a demo configuration file here https://github.com/Dragon160/Ubuntu-RazerBladeStealth2019/blob/master/src/fusuma-razer.yml)



## Tips and Tricks

### Gnome Dock 

If you want to minimize your application by clicking on the dock you need to enable that via gnomesettings.

    gsettings set org.gnome.shell.extensions.dash-to-dock click-action minimize

(Remove 'minimize' to disable it again.)  


### Dark mode

You can use gnome tweak tools to adjust several ui related settings.

    sudo apt-get install gnome-tweaks 

This will install an application named 'Tweaks' which is listed under your application list. Within here you can enable the dark mode: 

Appearance -> Applications -> Yaru-dark

### macOs like Dock

This can easily been done by installing an gnome tweak extension. For the nice dock I use this one

    https://extensions.gnome.org/extension/307/dash-to-dock/

## Troubleshooting

### Sleep mode is broken again

Sometimes, it seems that the sleep mode is again broken after installing some gnome extensions or similar things.
I discovered that in this cases it seeems that _TLP_ is the bad guy.
Unfortunately the only way to fix is is to reinstall _TLP_

    sudo apt remove tlp
    sudo apt install tlp

Luckily your settings will be kept.
__If this is not working for you__ I would recommend to do not use _TLP_. Uninstall it with

    sudo apt remove tlp

and reboot. Battery life is still quite good :-)