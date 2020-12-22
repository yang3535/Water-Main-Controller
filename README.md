# Water-Main-Controller
Home Automation water controller - pressure monitoring, valve control,  &amp; leak detection, MQTT support, Arcuino ESP8266

Functions (using default behaviors):

1. Reads water pressure sensor every 500ms
2. Publishes pressure every 5 mins if no significant pressure changes
  1. Significant pressure changes are .3 PSI or more
  2. If significant pressure changes are detected, the publishing rate is increased to 5 sec until pressure changes subside
3. Serial port messages are robust – use serial monitor &amp; MQTT Explorer for debugging
  1. Errors and status reports are published to MQTT
4. The following MQTT commands via MQTT are supported:

Commands are prefixed with RECV\_COMMAND\_TOPIC

Valid commands:

idlePublishInterval/\&lt;new value\&gt; - assigns a \&lt;new value\&gt;, but does not save to NVM

minPublishInterval/\&lt;new value\&gt; - assigns a \&lt;new value\&gt;, but does not save to NVM

sensorReadInterval/\&lt;new value\&gt; - assigns a \&lt;new value\&gt;, but does not save to NVM

reportParams - publishes parameters to REPORT\_TOPIC

defaultParams - sets parameters to default firmware values, but does not save to NVM

readParams - reads parameters from NVM storage, but does not save to NVM

writeParams - saves current active parameters to NVM storage

deleteParams - deletes parameters stored in NVM storage to force creation at next boot

valveState - 1 = OPEN, 0 = CLOSE

reboot - reboots device

help - shows list of valid commands via serial &amp; MQTT

1. Valve protection functions are incorporated
  1. The valve changes position by inverting polarity of the two control wires. This can be done with a manual switch or via ESP8266 control. Protection is necessary because the manual switch controls the valve directly in case of ESP8266 failure, so it is possible to get out of sync.
  2. Internal valveState synchronized every 30sec with actual valve position as read from the valve position switches in case manual control is used.
  3. Valve is automatically closed if valve switches indicate neither open or closed position for more than 30 secs.
  4. When activated, relays controlling valve activation are engaged for 10sec. They are intentionally open loop due to the potential of manual control interference.
2. GPIO assignment for a potential two-wire pulse output flowmeter are included, but no other programming has been done.
3. NTP, local timezone, &amp; DST are supported
4. OTA is supported

GPIO map:

// i2c pins are usually D1 &amp; D2 - see Valve Control Settings below for explanation

#define I2C\_ADDR 0x28

#define MAX\_PRESSURE 100

#define PIN\_SDA D6 // (GPIO12) Pins where i2c

#define PIN\_SDL D7 // (GPIO13) SDA &amp; SDL are attached

// Valve control settings

// On the ESP8266 pins D1 &amp; D2 are the only two that do not glitch HIGH at startup/reset.

// Since this application cannot tolerate the glitch, we must use D1 &amp; D2 here.

#define PIN\_VALVE\_ON D1 // (GPIO5) Valve works by reversing voltage on a set of two wires

#define PIN\_VALVE\_OFF D2 // (GPIO4) DO NOT SET VALVE\_ON &amp; VALVE\_OFF high at the same time!! It will short out the power supply!!

#define PIN\_VALVE\_ON\_INDICATOR D0 // (GPIO16) Confirms valve is in ON position when signal high

#define PIN\_VALVE\_OFF\_INDICATOR D5 // (GPIO14) Confirms valve is in OFF position when signal high

// Flow meter

#define PIN\_FLOW\_SIGNAL D8

Essential Hardware:

- [824-M32JM-000105-100](http://www.mouser.com/ProductDetail/te-connectivity/m32jm-000105-100pg/?qs=lc2O%252bfHJPVYobfuHIj4Lyg%3D%3D&amp;countrycode=US&amp;currencycode=USD) (Stocked at Mouser Electronics)
 M32JM-000105-100PG
 TE Connectivity Industrial Pressure Sensors
 US HTS:8532290040 ECCN:EAR99 COO:CN
- Motorized Ball Valve- 1&quot; Stainless Steel Electrical Ball Valve with Full Port, 9-24V DC and 5 Wire Setup, can be used with Indicator Lights, [Indicate Open or Closed Position] by U.S. Solid
 https://smile.amazon.com/dp/B06XCN8V6W/ref=cm\_sw\_em\_r\_mt\_dp\_zI.YFbWGFTD1B?\_encoding=UTF8&amp;psc=1
- mxuteuk 3pcs Momentary Rocker Switch Toggle Power Button (ON)/Off/(ON) 6 Pin 250V/10A 125V/15A, Use for Car Auto Boat Household Appliances KCD2-223-JT by mxuteuk
[https://smile.amazon.com/dp/B0885W19KL/ref=cm\_sw\_em\_r\_mt\_dp\_.A.YFbV7YQZZY?\_encoding=UTF8&amp;psc=1](https://smile.amazon.com/dp/B0885W19KL/ref=cm_sw_em_r_mt_dp_.A.YFbV7YQZZY?_encoding=UTF8&amp;psc=1)
- Electronics-Salon 2 DPDT Signal Relay Module Board, DC 5V Version, for Arduino Raspberry-Pi 8051 PIC. by Electronics-Salon.
[https://smile.amazon.com/dp/B00SKG6OM4/ref=cm\_sw\_em\_r\_mt\_dp\_5B.YFbPCCPKMZ?\_encoding=UTF8&amp;psc=1](https://smile.amazon.com/dp/B00SKG6OM4/ref=cm_sw_em_r_mt_dp_5B.YFbPCCPKMZ?_encoding=UTF8&amp;psc=1)
- MCIGICM LM2596 Buck Converter, DC to DC 3.0-40V to 1.5-35V Step Down Power Supply High Efficiency Voltage Regulator Module by McIgIcM
[https://smile.amazon.com/dp/B06XZ1DKF2/ref=cm\_sw\_em\_r\_mt\_dp\_cG.YFbN4SJQHS](https://smile.amazon.com/dp/B06XZ1DKF2/ref=cm_sw_em_r_mt_dp_cG.YFbN4SJQHS)