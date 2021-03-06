
```

### Relay

Start a relay server on port 2081, it acts as an man in the middle passing messages between the appliance and
the clients like the Smarter app or another iBrew instances. It can be configured to block and/or modify/patch certain messages. And as there is only one connection needed to the appliances. Good riddense of the annoying beep when the wireless network disconnects due to overload of connections. Also statistics are now possible.


__Advantages__
 * No annoying beep. Only one connection to the appliance needed
 * A lot faster!
 * History and statistics possible!
 * Works with Smarter app!
 * Works with iBrew as client!?
 * Message blocking
 * Message patching

```ibrew relay 10.0.0.99```

#### Advance relay options

```ibrew dump relay in:DEBUG,out:DEBUG 10.0.0.99 127.0.0.1:3081```

Bind relay server to localhost on a different port and uses the firewall to block all debug messages

To change the firewall rules of a relay server already running, use

```ibrew remote unblock in:ADMIN,out:ADMIN 127.0.0.1:3081```

```ibrew remote block in:DEBUG,out:DEBUG 127.0.0.1:3081```

To see the rules use

```ibrew remote rules 127.0.0.1:3081```


### Firewall 

You can block messages either in (from the appliance) or out (from the relay), this can be usefull to disable calibration or other options like wifi and is internally used to speed things up :-)

```ibrew rules 10.0.0.99```

```ibrew unblock in:ADMIN,out:ADMIN 10.0.0.99```

```ibrew block in:DEBUG,out:DEBUG 10.0.0.99```


### Simulation

* Simulates iKettle 2.0 ```ibrew coffee simulate```
* Simulates Smarter Coffee machine ```ibrew kettle simulate```

You can use to dump action to see more info what is going on.

_Note that message blocking is disabled!_



You can add triggers by replacing the host (ip) with the word simulation and start it with the evens system enabled, if you want to try out triggers...

```
ibrew trigger add Domoticz Temperature "http://127.0.0.1:8080/json.htm?type=command&param=udevice&idx=155&nvalue=0&svalue=§N" simulation
ibrew events kettle simulate
```

### Web (WIP)

This is a build in progress, please contribute!

Start the web interface 

```
ibrew web
```

and surf to:

```
http://ip:port/
```

#### Replacement for the Smarter app

Yeah I know the start and stop icons look terrible... and it is partly functional but you get no visual indicator it worked. Work in progress!

![devices](https://raw.githubusercontent.com/Tristan79/iBrew/master/source/distro/images/devices.png)

#### Build in JSON Rest API

For bridging smarthome controllers or use it to integrate your appliance in your own website!

![rest](https://raw.githubusercontent.com/Tristan79/iBrew/master/source/distro/images/api.png)

#### Setup

You can setup your appliance without the Smarter app. Default settings, calibration, descaling or wifi setup... it does it all! No internet required. This means you can use and setup the kettle and the smarter coffee even if smarters servers disappears (chance of no smarter servers in 5 years 97%, I seen it before :-/)

![settings](https://raw.githubusercontent.com/Tristan79/iBrew/master/source/distro/images/settings.png)

#### Statistics

It keeps stats for you, and you only! It does NOT send them (or anything else) to Smarter like the Smarter app does and definitly not to me...
Keep your appliance usage to yourself, will you!

![stats](https://raw.githubusercontent.com/Tristan79/iBrew/master/source/distro/images/stats.png)

#### Protocol description is fully interactive!

Cuz my code... :-) 

![protocol](https://raw.githubusercontent.com/Tristan79/iBrew/master/source/distro/images/protocol.png)


### JSON REST API

You can find information on the iBrew JSON REST API in the Web Interface under:

```
http://ip:port/info/api
```

The default port is 2080

#### Example JSON return

http://localhost:2080/api/10.0.0.99/status

```
{"status": "ready", "sensors": {"base": "On", "temperature": {"raw": {"fahrenheid": 75, "celsius": 24}, "stable": {"fahrenheid": 75, "celsius": 24}}, "waterlevel": {"raw": 2174, "base": 1110, "stable": 2173}}, "appliance": {"model": "iKettle 2.0", "firmware": {"version": 19, "certified": "iBrew certified firmware"}, "mode": "normal", "network": {"connection": {"directmode": false, "host": "10.0.0.99", "connected": true, "port": 2081, "relay": false}, "relay": {"active": false, "bind": "", "port": 2081}}}, "settings": {"default": {"formula": {"use": false, "temperature": {"fahrenheid": 33, "celsius": 1}}, "keepwarm": 5, "temperature": {"fahrenheid": 212, "celsius": 100, "prefered": "celsius"}}}}
```

### JavaScript (WIP)

Work in progress! Help would be nice!

JavaScript for use with iBrew JSON REST API [Javascript iBrew interface](https://github.com/Tristan79/iBrew/blob/master/source/resources/ibrew.js) 
 
### Python

Python 2.7 only!

The [Python Smarter Interface](https://github.com/Tristan79/iBrew/blob/master/source/smarter/) to the iKettle 2.0 and the Smarter Coffee is located in the Smarter folder. Use __pydoc__ or any other python doc app to see the help on [SmarterInterface.py](https://github.com/Tristan79/iBrew/blob/master/source/smarter/SmarterInterface.py) and [SmarterProtocol.py](https://github.com/Tristan79/iBrew/blob/master/smarter/source/SmarterProtocol.py). There are a lot of options and functions you can use!

Yes, code could be a lot better, you rewrite it,... I suggest making classes of blocking, triggers and patches. Classes of the protocol arguments and messages. Arrghh. This is just one big prototype hack! For me it was about testing IT stuff, not the appliance. The appliances made testing stuff more fun! I mean, events trigger system, emulation, simulation of a _Kettle_?! Wiresharking protocols and figure out what it all does. Well for me this is great fun while I am sick.

#### Basic example

```
from smarter.SmarterInterface import *
from smarter.SmarterProtocol import *

appliance = SmarterClient()
appliance.setHost("10.0.0.99")
appliance.connect()
if appliance.isKettle() and not appliance.heaterOn:
    appliance.kettle_heat_default()
appliance.disconnect()

```

### Smarthome controller push

You can pull values and states with the JSON REST api with it also possible to push values and states with the trigger events system. Exmaple adding triggers, where Domoticz is the group (one action per trigger per group) and §N is the new value and §O is the old value.
:

```
ibrew trigger add Domoticz Temperature "http://127.0.0.1:8080/json.htm?type=command&param=udevice&idx=155&nvalue=0&svalue=§N" 10.0.0.99
ibrew trigger add Scripts KettleBusy "C:\SCRIPTS\SENSOR.BAT §N" 10.0.0.99
ibrew trigger add Scripts KettleBusy "/home/pi/iBrew/scripts/sensor.sh §O §N" 10.0.0.99
```

To see all triggers `ibrew triggers`. To see all active triggers `ibrew trigger 10.0.0.99`. Monitor the trigger event system `ibrew dump events 10.0.0.99` or use the web server with auto re-connect :-) `ibrew dump events web`. It is possible to set switch types to various formats (on/off, 1/0, enabled/disabled,...) `ibrew trigger Domoticz switch On 10.0.0.99`. See  `ibrew switches` for an overview. Enable disable entire groups `ibrew trigger Domoticz off 10.0.0.99` See for group overview `ibrew trigger groups 10.0.0.99`. If you run the webserver as daemon/service add sudo to all your ibrew commands such that the right config file gets updated! So instead of `ibrew trigger Domoticz switch On 10.0.0.99` run `sudo ibrew trigger Domoticz switch On 10.0.0.99`

Alpha!

## Guides



### [Domoticz](http://www.domoticz.com/)


Setting up iBrew for domoticz is very simple! 

```
ibrew domoticz (prefix (name)) [connection]
```
 
This command will set up all sensors in domoticz automatically.

Name is the optional hardware name, and prefix is a prefix string to the sensor names. 
Use as prefix "" to select none if you want to choose a hardware name and no prefix.

Domoticz connection string format

```
<username:password@>domoticz-ip<:port>
```

where 

```
<username:password@> = the username and password to access Domoticz, this is optional.
domoticz-ip = the IP-address or hostname of your Domoticz installation.
<:port> = the port number of your Domoticz installation, this is optional.                           
```

examples 
 
```
ibrew domoticz user:password@127.0.0.1:8080 10.0.0.99
```

or

```
ibrew domoticz 192.168.0.30:8080 10.0.0.98
```

Below is the guide to do it manually!

#### Manually

[iBrew Forum Thread](http://domoticz.com/forum/viewtopic.php?f=56&t=12985)

Lets set up a kettle temperature sensor and a on base sensor!

For this example the base url of domoticz is 

`http://127.0.0.1:8080/`

The IP of the kettle is `10.0.0.99`

Lets start!

Go to `Setup -> Hardware` and create a new dummy hardware called `Smarter`

![hardware](https://raw.githubusercontent.com/Tristan79/iBrew/master/source/distro/images/domoticz/hardware.png)

Click on _Create Virtual Sensors_

![sensor](https://raw.githubusercontent.com/Tristan79/iBrew/master/source/distro/images/domoticz/sensor.png)

Create a temperature sensor

![temperature](https://raw.githubusercontent.com/Tristan79/iBrew/master/source/distro/images/domoticz/temperature.png)

Go to `Setup -> Devices` and look up your new sensor.

![devices](https://raw.githubusercontent.com/Tristan79/iBrew/master/source/distro/images/domoticz/devices.png)

Use the _idx_ of the sensor to add a trigger

```
ibrew trigger add Domoticz Temperature "http://127.0.0.1:8080/json.htm?type=command&param=udevice&idx=149&nvalue=0&svalue=§N" 10.0.0.99
```

Now we also add an off base motion sensor 

![switch](https://raw.githubusercontent.com/Tristan79/iBrew/master/source/distro/images/domoticz/switch.png)

Go to `Switches` and look up your new sensor.

![switches](https://raw.githubusercontent.com/Tristan79/iBrew/master/source/distro/images/domoticz/switches.png)

Edit it!

![motion](https://raw.githubusercontent.com/Tristan79/iBrew/master/source/distro/images/domoticz/motion.png)

Select motion 

![edit](https://raw.githubusercontent.com/Tristan79/iBrew/master/source/distro/images/domoticz/edit.png)

Look up the idx in `Setup -> Devices`

Use the _idx_ of the sensor to add a trigger

```
ibrew trigger add Domoticz OffBase "http://127.0.0.1:8080/json.htm?type=command&param=switchlight&idx=99&switchcmd=§N" 10.0.0.99
```

We need to set up the right switch state type, domoticz uses the format _On_ or _Off_

`ibrew trigger Domoticz switch On 10.0.0.99`

start any server with the events enables like

`ibrew dump events web`

If you run the webserver as daemon/service add sudo to all your ibrew commands such that the right config file gets updated!

So instead of `ibrew trigger Domoticz switch On 10.0.0.99` run `sudo ibrew trigger Domoticz switch On 10.0.0.99`

To see it in action!

For more information on [JSON used by domoticz!](https://www.domoticz.com/wiki/Domoticz_API/JSON_URL's)

Next step is creating and filling in buttons actions using either script:// or iBrew JSON Rest API!...

#### User Variables

Adding user variables

![menu](https://raw.githubusercontent.com/Tristan79/iBrew/master/source/distro/images/domoticz/menuvariables.png)

![edit](https://raw.githubusercontent.com/Tristan79/iBrew/master/source/distro/images/domoticz/editvariable.png)

![vars](https://raw.githubusercontent.com/Tristan79/iBrew/master/source/distro/images/domoticz/variables.png)

for numbers

```
sudo ibrew trigger add DomoticzUser DEFAULTTEMPERATURE "http://127.0.0.1:8080/json.htm?type=command&param=updateuservariable&vname=Kettle Default Temperature&vtype=0&vvalue=§N" 10.0.0.99
```

or for text

```
sudo ibrew trigger add DomoticzUser KETTLESTATUS "http://127.0.0.1:8080/json.htm?type=command&param=updateuservariable&vname=Kettle Status&vtype=2&vvalue=§N" 10.0.0.98
```

####  Domoticz Technical Stuff

[Domoticz JSON API](https://www.domoticz.com/wiki/Domoticz_API/JSON_URL's)

__Switch based triggers__
 * uservariable _string_ ("1",0")
 * virtual sensor _switch_ (set to motion)

__Watersensor based triggers__
 * virtual sensor _custom sensor_
 * uservariable _integer_

__Temperature based triggers__
 * virtual sensor _temperature_
 * uservariable _integer_

__Text based triggers__
 * virtual sensor _text_
 * uservariable _string_

Make sure you change the switch state type for the sensor group of domoticz (Domoticz), you can leave the user variable group (DomoticzUser) as it is!
 
```
sudo ibrew trigger Domoticz switch On 10.0.0.99
```

### HomeKit ~ [HomeBridge](https://github.com/nfarina/homebridge)

Yes, you can! Connect your iKettle or Smarter Coffee to HomeKit... just follow the following steps!

Alternativly you could use https://github.com/nferro/homebridge-http-json

#### Software

Install the following software to bridge iBrew with HomeKit

 * [Homebridge](https://github.com/nfarina/homebridge)
 * [cmdSwitch2](https://github.com/luisiam/homebridge-cmdswitch2)

#### Setup

Part of config file relevant to iKettle, iKettle 2.0 or Smarter Coffee

Fill in your own device host (either IP address or hostname) and location to iBrew, 

 * If you use an IP address: PLEASE use a static IP address! (assign in your router)
 * If you are the lucky owner of a router that assigns dynamic IP addresses with hostnames attached (usually in the form of device.local or device.lan) you can use that. If you are lucky, else use a static IP.
 * If you only have ONE device: you can use autodetection (and leave out the ip or hostname) but it adds a 2 seconds time penalty.


#### HomeBridge example config iKettle 

```
	"platforms": [{
		"platform": "cmdSwitch2",
		"switches": [{
			"name": "iKettle",
			"on_cmd": "/Users/Tristan/Coding/iBrew/ibrew legacy heat 10.0.0.3",
			"off_cmd": "/Users/Tristan/Coding/iBrew/ibrew legacy stop 10.0.0.3",
			"state_cmd": "/Users/Tristan/Coding/iBrew/ibrew legacy status 10.0.0.3 | grep 'Heating'"
            "manufacturer": "iBrew",
            "model": "iBrew iKettle",
            "serial": "44DE1AD79BC",
            "polling": true,
            "interval": 1,
		}]

```

#### HomeBridge example config iKettle 2.0
  
```
	"platforms": [{
		"platform": "cmdSwitch2",
		"switches": [{
			"name": "iKettle 2.0",
			"on_cmd": "/Users/Tristan/Coding/iBrew/ibrew start 10.0.0.99",
			"off_cmd": "/Users/Tristan/Coding/iBrew/ibrew stop 10.0.0.99",
			"state_cmd": "/Users/Tristan/Coding/iBrew/ibrew status 10.0.0.99 | grep 'busy'",
            "manufacturer": "iBrew",
            "model": "iBrew iKettle 2.0",
            "serial": "44DE2AD79BC",
            "polling": true,
            "interval": 1
		}]
```

#### HomeBridge example config Smarter Coffee

```
	"platforms": [{
		"platform": "cmdSwitch2",
		"switches": [{
			"name": "Smarter Coffee",
			"on_cmd": "/Users/Tristan/Coding/iBrew/ibrew start 10.0.0.98",
			"off_cmd": "/Users/Tristan/Coding/iBrew/ibrew stop 10.0.0.98",
			"state_cmd": "/Users/Tristan/Coding/iBrew/ibrew status 10.0.0.98 | grep 'busy'",
            "manufacturer": "iBrew",
            "model": "iBrew Smarter Coffee",
            "serial": "44DE3AD79BC",
            "polling": true,
            "interval": 1
		}]
```




#### Example HomeBridge config file

If you do not use any other HomeBridge devices, you can use and alter the following 
example config file for iKettle 2.0 and Smarter Coffee. 

```
{
	"bridge": {
		"name": "Homebridge",
		"username": "CC:22:3D:E3:CE:30",
		"port": 51826,
		"pin": "031-45-154"
	},

	"description": "Homebridge",

	"platforms": [{
		"platform": "cmdSwitch2",
		"switches": [{
			"name": "iKettle 2.0",
			"on_cmd": "/Users/Tristan/Coding/iBrew/ibrew start 10.0.0.99",
			"off_cmd": "/Users/Tristan/Coding/iBrew/ibrew stop 10.0.0.99",
			"state_cmd": "/Users/Tristan/Coding/iBrew/ibrew status 10.0.0.99 | grep 'busy'",
            "manufacturer": "iBrew",
            "model": "iKettle 2.0 Intermezzo",
            "serial": "90DE2AD79BC",
            "polling": true,
            "interval": 1
		},
        {
			"name": "Smarter Coffee",
			"on_cmd": "/Users/Tristan/Coding/iBrew/ibrew start 10.0.0.98",
			"off_cmd": "/Users/Tristan/Coding/iBrew/ibrew stop 10.0.0.98",
			"state_cmd": "/Users/Tristan/Coding/iBrew/ibrew status 10.0.0.98 | grep 'busy'",
            "manufacturer": "iBrew",
            "model": "iBrew Smarter Coffee",
            "serial": "42DE3AD79BC",
            "polling": true,
            "interval": 1
		}]

	}]
}
```

### [Home Assistant](https://home-assistant.io)
[iBrew Forum Thread](https://community.home-assistant.io/t/smarter-coffee-ikettle/1870)

configuration.yaml:

```
switch:
  - platform: command_line
    switches:
      my_kettle:
        command_on: "/home/hass/ibrewcontrol.sh start"
        command_off: "/home/hass/ibrewcontrol.sh stop"
        command_state: "/home/hass/ibrewstatus.sh"
        friendly_name: Kettle
```

ibrewcontrol.sh
```
#!/bin/bash
COMMAND=$1
ibrew $COMMAND <your Kettle IP>
```

ibrewstatus.sh

```
#!/bin/bash
CMD='ibrew status <your kettle IP> |grep busy'
if [ -z "$CMD" ];then
        exit 1
else
        exit 0
fi
```

### [OpenHAB](http://www.openhab.org) 
[iBrew Forum Thread](https://community.openhab.org/t/smarter-coffee-machine-control-with-tcp-binding/12831)

### [Smartthings](https://www.smartthings.com) 
[iBrew Forum Thread](https://community.smartthings.com/t/smarter-coffee/22776/11)

## iBrew Mentioned
 * [knx](https://knx-user-forum.de/forum/supportforen/smarthome-py/1019085-logik-trigger-via-seitenaufruf)
 * [reddit](https://www.reddit.com/r/amazonecho/comments/54vpum/echo_with_kettle/)
 * [micasaverde](http://forum.micasaverde.com/index.php?topic=30336.30)

### Other

Have any links, info or help on other Smarthome controller software, please post in the issues!

## Versions
 * PHASE 1: [BRAINSTORMING] v0.0 Bean Grinder Pack 
 * PHASE 2: [PROTOTYPE]     v0.1 White Tealeaf Edition 
 * PHASE 3: [PROTOCOL]      v0.2 Tea Noire Sweet 
 * PHASE 4: [CONSOLE]       v0.3 Kettle Rattle 
 * PHASE 5: [WEB]           v0.4 Brewing on the 7th day 
 *          [CORE]          v0.4.4 Intermezzo!
 * PHASE 6: [PRERELEASE]    v0.5 The conundrum struggle
 * PHASE 7: [FINALCUT]      v1.0 Out of order!
 * PHASE 8: [BUGS]          v1.1 Ant trail!
 
### Upcoming for the last 3 versions  

Protocol
 * PROTOCOL: History message is not finished
 * PROTOCOL: Modifiers/patches!!! 
 * PROCOCOL: Time arguments (have not figured that out)
 * IKETTLE20: Fahrenheid not finished, please to not use.
 * IKETTLE20: Watersensor to something usefull (like the stupid left or right side handle, cuz the kettle weight balance is off, its inaccurate as fuck even in the smarter app :-/)
 * SMARTER COFFEE: Have not looked at single cup... needs a remote coffee machine session ;-)
 * SMARTER COFFEE: Did I accidently switch carafe required bit?
 * SMARTER COFFEE: Cups from the status and cups from the display setting is different 
 * SMARTER COFFEE: Timers protocol
 * SMARTER COFFEE: v0.5 Missing Coffee Smarter codes (working bit only?)
 * SMARTER COFFEE: Descaling data bit? (the smarter app has it...)
 * HELP: Add the missing pieces
 * SIMULATOR: Add carafe removal and water filling
 * SIMULATOR: Fix waterlevel and fix cups?

Interfaces
 * PYTHON: Better error handling
 * PYTHON: Make the print stuff more general
 * PYTHON: Strip zero from ip
 * PYTHON: fix wireless with the same name
 * PYTHON: filter out wrong responses... of know commands??? or atleast acknowledge them, (03 responses)
 * PYTHON: Beverages should be able to override keepwarm time... xCLI/REST
 * WEB: Web interface 
 * WEB: Auto relay when in web mode
 * WEB: API key, login
 * WEB: Settings iBrew (like blocking, patches, other stuff)
 * WEB: All REST api has no error check...
 * JAVASCRIPT: JSON Rest API
 * ME: hugs!

## LICENSE

The author has no contact with or support from Smarter, and is not affiliated in any way with the company that produces the appliances.

