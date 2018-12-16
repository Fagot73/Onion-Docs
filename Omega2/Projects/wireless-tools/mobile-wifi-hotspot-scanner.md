## Mobile WiFi Network Scanner {#wifi-network-scanner}

The Omega can scan nearby WiFi networks and report information such as their SSID, encryption type, and signal strength. In this project, we'll be using the Omega to scan local WiFi networks, record the GPS coordinates where they're found, display the networks with the strongest signal on the OLED Expansion, and save the rest of the data to a spreadsheet file.


![wifi scanner outside](./img/mobile-wifi-hotspot-scanner-outside.jpg)

### Overview

**Skill Level:** Intermediate

**Time Required:** 10 minutes

The WiFi scanner will:

* Scan for any WiFi networks in range using a `ubus` call
* Retrieve location data from the GPS Expansion, again using the `ubus`
* Sort the scanned networks by signal strength and display the six networks with the strongest signal on the OLED Expansion

It will then save the following data for each network into a comma separated value (CSV) file that can be imported into a spreadsheet program:

* Date & time scanned
* Latitude and longitude
* SSID
* BSSID
* Encryption type
* Signal strength

Using the Power Dock, you will be able to use your scanner out in the world without needing a USB power supply.


### Ingredients

* Onion [Omega2](https://onion.io/store/omega2/) or [Omega2+](https://onion.io/store/omega2p/)
* Onion [Power Dock](https://onion.io/store/power-dock/)
	* The [Expansion Dock](https://onion.io/store/expansion-dock/) and [Arduino Dock 2](https://onion.io/store/arduino-dock-r2/) will work as well, they just won't be mobile
* Onion [OLED Expansion](https://onion.io/store/oled-expansion/)
* Onion [GPS Expansion](https://onion.io/store/gps-expansion/)
* [External GPS Antenna](https://www.amazon.com/gp/product/B00LXRQY9A/ref=as_li_tl?ie=UTF8&tag=onion0e-20&camp=1789&creative=9325&linkCode=as2&creativeASIN=B00LXRQY9A&linkId=66164544d399bf466485e8881a8f2df8) with a [u.Fl connector](https://www.amazon.com/gp/product/B005UWD0EG/ref=as_li_tl?ie=UTF8&tag=onion0e-20&camp=1789&creative=9325&linkCode=as2&creativeASIN=B005UWD0EG&linkId=1ad0d1bd7b949a15414af21e5f595090) **(optional, but gets better reception indoors)**
* A [3.7V LiPo battery](https://www.amazon.com/gp/product/B01MYY9J78/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&tag=onion0e-20&camp=1789&creative=9325&linkCode=as2&creativeASIN=B01MYY9J78&linkId=c74126e601f388e237102887a744e778)
	* We found 1200 mAh to be good for several hours of use

![ingredients](./img/mobile-wifi-hotspot-scanner-ingredients.jpg)

### Step-by-Step

Here's how to turn your Omega into a WiFi scanner!

#### 1. Prepare

You'll need to have an Omega2 ready to go, complete the [First Time Setup Guide](https://docs.onion.io/omega2-docs/first-time-setup.html) to connect your Omega to WiFi and update to the latest firmware.

#### 2. Setup the Hardware

Connect your Omega to the Power Dock, then plug in the OLED Expansion into the Expansion Header. Then plug in the GPS Expansion into the USB host port as shown below.

![wifi scanner outside](./img/mobile-wifi-hotspot-scanner-assembled.jpg)

The GPS Expansion's antenna is connected via a Hirose U.FL connector. If you have your own antenna with the appropriate connector that you would like to use, you can gently unplug the included antenna (the large square piece with a wire) and replace it with your own.

![wifi scanner outside](./img/mobile-wifi-hotspot-scanner-external-antenna.jpg)

<!--# 2 -->

#### 3. Install Packages

[Connect to the Omega's command line](https://docs.onion.io/omega2-docs/connecting-to-the-omega-terminal.html#connecting-to-the-omega-terminal-ssh) and install Python as well as some of the packages we need:

```
opkg update
opkg install python-light pyOledExp ogps git git-http ca-bundle
```

The `pyOledExp` package gives us control of the OLED Expansion, while the `ogps` package will provide a `ubus` service that lets us easily get data from the GPS Expansion. The `git`, `git-http`, and `ca-bundle` packages will allows us to download the project code form GitHub.

After installing `ogps`, check that the `ubus` `gps` service is listed by running `ubus list`:

![ubus list](./img/using-gps-expansion-4-ubus-list.png)

If you don't see `gps` listed, you'll need to restart your `rpcd` service in order to refresh the list:

```
/etc/init.d/rpcd restart
```

If this doesn't work, try reinstalling the `ogps` package by running the following commands:

```
opkg remove ogps
opkg update
opkg install ogps
```

#### 4. Download and Install the Project Software

The code for this project is all done and can be found in Onion's [`wifi-hotspot-scanner` repo](https://github.com/OnionIoT/wifi-hotspot-scanner) on GitHub. Use [`git` to download the code to your Omega](https://docs.onion.io/omega2-docs/installing-and-using-git.html): navigate to the `/root` directory, and clone the GitHub repo:

```
cd /root
git clone https://github.com/OnionIoT/wifi-hotspot-scanner.git
```

#### 5. Running the Project on Boot

Next we'll setup the Omega to automatically run the scanner when it turns on. Edit the `/etc/rc.local` file and add the following line above `exit 0`:

```sh
python /root/wifi-hotspot-scanner/main.py &
```

This way, when you flip the power switch, the Omega will run the code in the background after it completes the initialization process.

#### 6. Using the WiFi Scanner

Here's the fun part! Press the reset button and the Omega will run the program.

If the GPS Expansion is able to lock onto a satellite signal, you'll see the time, the GPS coordinates, and the 6 WiFi networks with the strongest signal available nearby.

![oled closeup](./img/mobile-wifi-hotspot-scanner-oled-closeup.jpg)

The Omega will then save data about all of the discovered networks to a file called `wifiData.csv` in the project directory. You can then import this into a spreadsheet or navigation program for mapping later!

![wifi scanner outside](./img/mobile-wifi-hotspot-scanner-outside.jpg)

##### Unable to Lock Signal

If the GPS Expansion cannot lock onto a satellite, you'll see an error message on the OLED. The program will try again in a few seconds.

##### Saved Data

Assuming the project code was downloaded to the `/root` directory, the collected wifi data will be saved to: `/root/wifi-hotspot-scanner/wifiData.csv`. It is a Comma Separated Value (CSV) file and can be opened with any spreadsheet program. It stores data about the surrounding networks for every single scan:

![csv file output](./img/mobile-wifi-hotspot-scanner-csv.png)

This data can be used in a variety of creative ways: creating a map of your neighbourhood that shows the strength of the local WiFi networks, creating a database of open networks around the city, the sky is the limit.

### Code Highlight

The `ubus` system utility is a key part of the firmware on which the Omega is based. It allows you to call services and functions on the Omega as if you were sending data to a web API. The basic syntax goes like this:

```
ubus call (service) (function) '{(JSON parameters)}'
```

The WiFi and GPS scanning functions are available as `ubus` functions so that they can be called by any program.

You can see how they work in the `ubusHelper.py` module:

```python
# basics of running a command
# returns a dict as ubus functions return json objects
def runCommand(command):
    output, err = shellHelper.runCommand(command)
    responseDict = json.loads(output)
    return responseDict

# often used commands
# add more if you need
def call(args):
    command = ["ubus", "call"]
    command.extend(args)
    return runCommand(command)
```

and the `helpers.py` module:

```python
# scan wifi networks in range
# returns a list of wifi dictionaries
def scanWifi():
    device = json.dumps({"device": "ra0"})
    args = ["onion", "wifi-scan", device]
    return ubus.call(args)["results"]

# read the GPS expansion
# returns a dictionary with gps info
def readGps():
    args =["gps", "info"]
    response = ubus.call(args)

    # check if the GPS is locked
    if "signal" in response and response["signal"] == False:
        return False
    # else return the data
    return response
```

In essence, the `scanWifi()` function above runs the following command:

```
ubus call onion wifi-scan '{"device":"ra0"}'
```

And the `readGps()` function runs this command:

```
ubus call gps info
```

Try running these two commands on your Omega's command line by hand and take note of the output.
