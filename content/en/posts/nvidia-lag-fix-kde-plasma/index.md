+++
title = 'Nvidia Lag Fix KDE Plasma 5/6 on X11'
date = 2024-05-03T18:19:28+02:00
draft = false
+++

Hello readers of this post!

Some of you that have a high refresh-rate with a second monitor at a different refresh rate, let's say 60hz may have seen some stutters or lags when launching apps like System Settings and may also have seen that the windows does not move at 144fps.

I managed to fix this issue by merging multiple solutions found on multiple reddit posts and on forums of KDE Plasma. Here are the things that you want to do to try to solve this:

## Disabling OpenGL Flipping

To do this you need to go to nvidia-settings -> OpenGL Settings -> Uncheck "Allow OpenGL Flipping"

{{< figure src="nvidia-settings.webp" title="Nvidia settings" >}}

You will normally see a change in mouse stutter and your cursor should not "skip" frames when launching apps.

To persist this change, you need to edit the XOrg configuration file in `/etc/X11/xorg.conf`

It is not created by default, nvidia-settings can generate it for you if you launch it with the root user with this command:

sudo nvidia-settings

To generate the XOrg file with nvidia-settings in root mode go to:

X Server Display Configuration -> Save to X Configuration File and save it into `/etc/X11/xorg.conf`:

OpenGL Menu

And then to make the OpenGL Flipping completely disabled add this to your xorg.conf file in the "Device" section of the config:

```shell
Option         "NoFlip" "true"
```
Save it and reboot your computer.

## Disabling KWIN Vsync and force full composition pipeline

To do this, you will need to add some line to your /etc/environment file.

This file needs to be edited by the root user so use your favorite editor in sudo mode and add these lines:

```shell
__GL_SYNC_DISPLAY_DEVICE=DP-0 
VDPAU_NVIDIA_SYNC_DISPLAY_DEVICE=DP-0
```

`__GL_SYNC_DISPLAY_DEVICE` => Indicate your monitor with high refresh rate

`VDPAU_NVIDIA_SYNC_DISPLAY_DEVICE` => Same thing

In order to obtain the display device ID (like DP-0) you can use nvidia-settings and go to the X Server Display Configuration.

Select your monitor in the layout and read the selection field, it should read "DP-X on GPU-0" where X is the ID of the DisplayPort port used:


Use this value (DP-0 for example) in the __GL_SYNC and VDPAU items in the environment file and save it.

Again in the same file add these three lines after the other two:

```shell
KWIN_X11_REFRESH_RATE=144000
KWIN_X11_NO_SYNC_TO_VBLANK=1
KWIN_X11_FORCE_SOFTWARE_VSYNC=1
```
Meaning of the values:

`KWIN_X11_REFRESH_RATE` : Refresh rate of your monitor multiplied by 1000

`KWIN_X11_NO_SYNC_TO_VBLANK` : Don't use vblank for kwin compositor

`KWIN_X11_FORCE_SOFTWARE_VSYNC` : Force the usage of the software Vsync of kwin

Go to nvidia-settings in sudo mode like before then go to the X Server Display Configuration and click on the "Advanced..." button on the bottom of the window.

Select your high refresh rate monitor and check "Force Composition Pipeline" and "Force Full Composition Pipeline".

Apply and Save your X configuration file like before in /etc/X11/xorg.conf.

Reboot your computer

I hope your experience is smoother now. For me it worked like a charm!

Sources:

- [My original post on reddit](https://www.reddit.com/r/EndeavourOS/comments/p8b2kf/for_those_that_have_stutterlags_problems_with_kde)
- [Some tutorial on reddit](https://www.reddit.com/r/linux_gaming/comments/ap781g/living_with_144hz_with_60hz_secondary_screens/)
- [KDE Issue](https://bugs.kde.org/show_bug.cgi?id=433094)
