# Gameboy Zero

Checklist for building a custom Gameboy Zero.

## Disk setup

* Install [Retropie](https://retropie.org.uk/download/) using [Etcher](https://etcher.io/)
* Add blank ssh file to root of disk
* Add wpa_supplicant.conf with wifi credentials to root of disk

### Example wpa_supplicant.conf

```
country=US
update_config=1
ctrl_interface=/var/run/wpa_supplicant

network={
	ssid="YOUR SSID"
	psk="YOUR PASSWORD"
	scan_ssid=1
}
```

## Configure

First, connect to the pi using ssh and run:

```bash
sudo raspi-config
```

* 1 > Change password
* 2 > Change hostname
* 4 > Setup localization
* 7 > Advanced Options > Expand Filesystem
* Reboot

Next, change the font for the console to be legible for a smaller screen.

```bash
sudo dpkg-reconfigure console-setup
```

Select UTF-8 > Guess optimal character set > Terminus > 10x20 (framebuffer only)

## Setup GPIO for buttons

Thanks to [prerunnerseth](http://www.sudomod.com/forum/viewtopic.php?t=57) for explaining how to set this up.

![Raspberry Pinout](https://raw.githubusercontent.com/mrdink/gameboy-zero-setup/master/gpio-pinout.png "Raspberry Pinout")

First, create the following directory in pi user home directory.

```bash
cd ~
mkdir Adafruit-Retrogame
cd Adafruit-Retrogame
```

Next, download `retrogame.c` to this directory/

```bash
wget https://raw.githubusercontent.com/mrdink/gameboy-zero-setup/master/retrogame.c
```

Next, run the following to make the executable retrogame utility.

```bash
make retrogame
```

Next, create a custom udev rule which will allow retrogame input events to be visible to applications built using the latest SDL2 library.

```bash
sudo nano /etc/udev/rules.d/10-retrogame.rules
```

Copy the following into the console:

```bash
SUBSYSTEM=="input", ATTRS{name}=="retrogame", ENV{ID_INPUT_KEYBOARD}="1"
```

Save with Control+O, Enter, Control+X.

Reboot to make sure the configuration has saved. `sudo reboot now`

Now, let's run the utility.

```bash
cd Adafruit-Retrogame
sudo +x retrogame
sudo ./retrogame
```

If this seems to be working, cancel the process with Control+C.

Next, setup the system to automatically load this process at startup.

```bash
sudo nano /etc/rc.local
```

Add the following before `exit 0` at the bottom of the file.

```
/home/pi/Adafruit-Retrogame/retrogame &
```

Finally, reboot and test to make sure buttons are working as expected.

