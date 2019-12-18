## Manual Firmware Installation {#manual-firmware-installation}


Updating your Omega is really easy and can be done in a number of ways. You can use our built in upgrade tool `oupgrade` for a streamlined approach to updating your Omega.

>For more on `oupgrade` you can read our article on [upgrading your Omega's Firmware](#updating-the-omega)

This guide will show you how to manually install a firmware of your choice. This firmware can be one of the builds published by Onion or a custom firmware image.


### Step 1: Downloading the Firmware Image

Firmware images for the Omega2 family can be found on the Onion repo: http://repo.onioniot.com/omega2/images/

Let's begin by downloading the latest firmware image from [the Onion repo](http://repo.onioniot.com/omega2/images/). This repo is where we keep all the builds of the various Onion Firmware.

On your Omega, start by changing directories to `/tmp`:

```
cd /tmp
```

Now we're going to need the link address of the firmware we want to download. Right click on the link, and select the `copy link address` option:

![copy link address](https://raw.githubusercontent.com/OnionIoT/Onion-Docs/master/Omega2/Documentation/Doing-Stuff/img/command-line-updating-omega-pic-download-link.png)

**Note: There are two versions of the firmware; one for the Omega2, and one for the Omega2+. You'll need to make sure you're copying the link to the correct firmware.**

* Omega2 firmwares start with "omega2-..."
* Omega2+ firmwares start with "omega2**p**-..."
* Omega2 Pro firmwares start with "omega2**pro**-..."


Next, enter the following into your terminal:

```bash
wget <LINK ADDRESS>
```

where `<LINK ADDRESS>` is the link you copied from the repo. Your command-line should look something like this:

```
root@Omega-2757:/tmp# wget http://repo.onioniot.com/omega2/images/omega2p-v0.3.2-b218.bin
```

Once you've verified that link is to the correct binary, hit Enter and your firmware will begin downloading.

```
root@Omega-2757:/tmp# wget http://repo.onioniot.com/omega2/images/omega2p-v0.3.2-b218.bin
--2016-12-07 21:10:02--  http://repo.onioniot.com/omega2/images/omega2p-v0.3.2-b218.bin
Resolving repo.onioniot.com... 52.89.44.24, 54.149.140.66
Connecting to repo.onioniot.com|52.89.44.24|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 6815910 (6.5M) [application/octet-stream]
Saving to: 'omega2p-v0.3.2-b218.bin'

omega2p-v0.3.2-b218 100%[===================>]   6.50M  1.57MB/s    in 4.8s

2016-12-07 21:10:07 (1.34 MB/s) - 'omega2p-v0.3.2-b218.bin' saved [6815910/6815910]
```

Take note of the name of the firmware you just downloaded for the next step. In this example, the name is `omega2p-v0.3.2-b218.bin`.

#### Alternative Step 1: Downloading Firmware onto a USB Storage Device

If your Omega no longer has enough room to download the Firmware, you can download the firmware onto a USB Storage Device and work from that USB's directory on your Omega.

>For more on using a USB storage device on your Omega, check out our [guide to using a USB storage device on your Omega](#usb-storage)


### Step 2: Upgrading your Omega to the Downloaded Firmware

The command to upgrade your Omega's firmware is `sysupgrade`. You can run the following command to upgrade your firmware:

```
sysupgrade <FIRMWARE FILE NAME>
```

From the above example, the command would look like:

```
sysupgrade omega2p-v0.3.2-b218.bin
```

and you'll see output similar to the following upon entering the command:

```
root@Omega-2757:/tmp# sysupgrade omega2p-v0.3.2-b218.bin
Saving config files...
killall: watchdog: no process killed
Sending TERM to remaining processes ... uhttpd device-client avahi-daemon onion-helper udhcpc mountd ntpd shellinaboxd udhcpc dnsmasq ubusd logd rpcd netifd odhcpd crond
Sending KILL to remaining processes ...
Switching to ramdisk...
Performing system upgrade...
Unlocking firmware ...

Writing from <stdin> to firmware ...  [e]
```

### Additional `sysupgrade` Options

The `sysupgrade` command comes with a number of additional options that you can explore by entering:

```
sysupgrade -h
```

The options that might prove most useful to you are `-n` or `-F`.

* The `-n` flag tells `sysupgrade` not to save configuration files after upgrading. 
    * This can be useful if you would like to reset the Omega to the default settings included in the firmware image. 
    * This will **erase everything on the Omega** so make sure to back up your custom data and settings.
* The `-F` flag forces `sysupgrade` to upgrade the firmware.
    * This is useful in the event that `sysupgrade` refuses to upgrade the firmware. 
    * This flag tells `sysupgrade` to ignore several critical checks that ensure the correct firmware is flashed.
    * **This flag is not recommended under normal circumstances. Use with caution!**
