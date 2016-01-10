---
layout: page
title: Kiosk mode rasberry pi
---
# Use a rasberry pi as a kiosk

I want to display a static copy of a webiste on a rasberry pi which does not 
have a network connection.  These are my notes on getting everything to work.

## Chromium

I need Chromium because I need Flash.  I need flash because the website is
basically one huge, painful flash animation

It was a little hard to track down an ARM build of chromium.  Unfortunetly
`apt-get chromium-browser` doesn't just work.

Taken from <http://conoroneill.net/running-the-latest-chromium-45-on-debian-jessie-on-your-raspberry-pi-2/>

    wget http://ftp.us.debian.org/debian/pool/main/libg/libgcrypt11/libgcrypt11_1.5.0-5+deb7u3_armhf.deb
    wget http://launchpadlibrarian.net/218525709/chromium-browser_45.0.2454.85-0ubuntu0.14.04.1.1097_armhf.deb
    wget http://launchpadlibrarian.net/218525711/chromium-codecs-ffmpeg-extra_45.0.2454.85-0ubuntu0.14.04.1.1097_armhf.deb
    sudo dpkg -i libgcrypt11_1.5.0-5+deb7u3_armhf.deb
    sudo dpkg -i chromium-codecs-ffmpeg-extra_45.0.2454.85-0ubuntu0.14.04.1.1097_armhf.deb
    sudo dpkg -i chromium-browser_45.0.2454.85-0ubuntu0.14.04.1.1097_armhf.deb

## PepperFlash
PepperFlash has no hardware acceleration so it is slow, but it is good enough for what I needed.

Instructions are adapted from <https://www.raspberrypi.org/forums/viewtopic.php?t=99202>:

    wget http://os.archlinuxarm.org/armv7h/alarm/chromium-pepper-flash-12.0.0.77-1-armv7h.pkg.tar.xz
    tar -xf PepperFlash-12.0.0.77-armv7h.tar.gz
    sudo cp lib/PepperFlash/lib/* /usr/lib/chromium-browser/plugins
    sudo nano /etc/chromium-browser/default

Edit the chromium config::

    vim /etc/chromium/default
    CHROMIUM_FLAGS="--ppapi-flash-path=/usr/lib/chromium-browser/plugins/libpepflashplayer.so --ppapi-flash-version=12.0.0.77 -password-store=detect -user-data-dir"

It looks like it's a good thing the Raspberry Pi 2 Model B has a quad-core CPI.

    $ top
    ... snip ...
     PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
      986 pi        20   0  252448  65724  40588 S 141.9  6.9  17:16.15 chromiu+
      839 pi        20   0  462472 119356  79216 R  82.8 12.6  10:18.33 chromiu+
      955 pi        20   0  284936  70168  58028 R  30.6  7.4   3:42.61 chromiu+

## Start Chromium on Start

By default Raspian logs in the user `pi` and runs `startx`.  ALl that is left
is to open chromium automatically on login:

    pi@raspberrypi:~/.config/autostart $ more chromium.desktop
    [Desktop Entry]
    Name=Chromium
    Type=Application
    Comment=Start up chromium
    Exec=/usr/bin/chromium-browser

## Load site by default in chromium

We can use the chromium settings to open a page on startup.

We can also use the `--kiosk` option to start chromium in full-screen mode.
Or use the chromium command line options in the `autostart` file.  Change the
last line to:

    Exec=/usr/bin/chromium-browser --kiosk --disable-session-crashed-bubble file:///path/to/file.html

I found the detailed instructions here <http://superuser.com/questions/461035/disable-google-chrome-session-restore-functionality>

## Testing with remote desktop

The `autostart` folder is repsected on remote desktop sessions.  To install RDP
on the pi:

    sudo apt-get install xrdp

To kill a remote desktop session so you can test `autostart` changes use
something like:

    pkill Xvnc

The RDP session has a different display than the session started on power-up,
so you may be trying to start two instances of chromium which will not work.  
You might need to kill chromium:

    pkill chromium-b+ 

## Automatically connect on WiFi 

Sometime the pi does not connect to wifi network.  I am not sure why because 
usually it does automatically connect.

You might try this guide <http://weworkweplay.com/play/automatically-connect-a-raspberry-pi-to-a-wifi-network/>

## Power options

Does the pi go into power saving mode.

## Shutdown

It's bad to shutdown the pi by removing power.  Eventually the filesystem may
get corrupted.  

Even if I setup a power switch to run the shutdown command, the end-user in
will never bother with shutting it off correctly.

