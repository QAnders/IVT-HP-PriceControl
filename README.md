# IVT-HP-PriceControl

With todays electrical prices I figured I want to be able to halt my heat-pump from running during peak prices over the day. This should save me a few bucks as I can prevent it from running when the price is high...

My heat-pump, an IVT HEE11, has something called "External Control" which is a on/off contact where "on" means the contact is closed and "off" means the contact is open.
You can use "Day Average" price as well for the cut off, in hat case the heat-pump will be allowed to run only when the price is below the days average.
The contact will be a relay-board controlled by a RaspberryPI running `node-red` and using the UI.

![image](https://user-images.githubusercontent.com/12028885/188300575-ad7203d8-191f-4f44-9dc9-e862a3d95c25.png)


In the IVT control settings I can choose what the "External Control" does, and in my case I'm going to set it to block heating and warm water, thus make the heat-pump just sit there waiting until the contact is opened again.

![image](https://user-images.githubusercontent.com/12028885/187434016-c21886fc-9069-4c46-9978-3bc267740a55.png)



## Requirements

- RaspberryPi that can run `node-red` and have GPIO (a "B" or Zero model basically)
	-  BananaPI, Beaglebone or similar will also work as long as you can add node-red and some "control" to open/close the relay
- Relay card for GPIO (I am using this from [Amazon.com](https://www.amazon.com/SunFounder-Channel-Shield-Arduino-Raspberry/dp/B00E0NSORY/ref=sr_1_9))
- Temperature sensor that works with node-red, e.g. `mqtt` or `api` based, e.g. Shelly
- Some simple cable to connect to the heat-pump terminals

> *You can of course also build something similar using Home Assistant, Homey or similar "home automation" controller with a simple RF relay switch (Shelly, Nexa, or similar) so don't go invest in all this stuff if you have other stuff at home already!*

- Your heat-pump manual (possibly instructors manual). IVT HE can be found here:
	- < 2013 year models: https://cdn.jseducation.se/files/pages/6720641855-im-greenline-he-201003-sv-1.pdf
	- \> 2013 year models: https://vpkollen.se/uploaded/manualer/IVT/Installationsm_HE.pdf
- Some technical know-how of getting into the heat-pump (showing the `PEL` card where my connectors are on at terminal `10-C`)

![image](https://user-images.githubusercontent.com/12028885/187435226-d5ee3a16-1da2-4497-8117-be1acb3a223d.png)


## Electrical pricing API
We have to use the Entsoe API which is public and free (at least for now). See separate instructions in [Entsoe-API-Setup.md](Entsoe-API-Setup.md).


## Software
I've built the solution on `node-red` which is installed on all RaspberryPI's from start but you can of course use any other platform that supports node.js and node-red.
The node-red flow is added as a separate file here and you'll need to set an environment variables by using, e.g:

`export SECURITY_TOKEN=u-u-i-d` (SECURITY_TOKEN, the security token for Entsoe API)

> Add the export to the `.bashrc` of the user running the node-red process (normally you, so located in your home directory).
> To add it system-wide (NB! then available to all users) add it in /etc/environment instead.

The flow will run on every hour (on the hour) and request the current price for the hour and then, based on the given cut-off price, pull or release the relay (to block or allow heating).

The `node-red` ui (e.g. http://localhost:1880/ui/) will show the status and you can set the cut-off price as well as a "main switch".

### node-red modules
You'll need to install a few extra modules for node-red. In the "Manage palette" menu (from the three lines (hamburger) in the top right corner).

- node-red-dashboard
- node-red-contrib-ui-led
- node-red-node-pi-gpio

(I'm using a Xiaomi Bluethooth temperature sensor and will need `node-red-contrib-xiaomi-ble` as well)

Once you've installed those you can import my flow from the same menu (chose "Import" obviously)


## Version 2...

I'm working on implementing the temperature control so I can force the heat-pump to run if the room temperature goes below a certain point and I am also pondering on adding a sensor on the warm water tank to see if the water goes too cold and then also override and start, even if the price is high...
