# Zendure SolarFlow

**Please note this is work in progress. If you have more or better information feel free to create a pull request! Thanks!**

The Zendure SolarFlow ([https://eu.zendure.com/](https://eu.zendure.com/)) is a controller and microinverter that can connect up to 2 solar panels (800W total) and output up to 1200W.

Manual: [https://cdn.shopify.com/s/files/1/0720/4379/0616/files/SolarFlow20230525_V8.pdf](https://cdn.shopify.com/s/files/1/0720/4379/0616/files/SolarFlow20230525_V8.pdf)

#### Devices:

Name  : Smart PV Hub 1200 Controller

Model : ZDSPVH1200

Name: Add-on Battery AB1000

Battery model : 	ZDAB1000

In this posting I will detail how to communicate with the Zendure SolarFlow using Bluetooth (get a bluetooth enabled device and start gathering data). (Without a local account, mobile app or cloud account) "Some" privacy is ensured.   

### DISCLAIMER (progress at own risk! I can't be liable for any damage you do to the device!)

### Zendure SolarFlow and Bluetooth

**WARNING**: This system includes no security. It is possible to read and write data to the system without any authentication. (It is probably safer to keep a constant connection to avoid someone else connecting to it!)

The Zendure SolarFlow has 2 services, one Notify and one to Write. 

```
SERVICE          = "0000A002-0000-1000-8000-00805F9B34FB"
UUID_READ_NOTIFY = "0000C305-0000-1000-8000-00805F9B34FB"
UUID_WRITE       = "0000C304-0000-1000-8000-00805F9B34FB"
```

The following is a sequence of requests made by the Zandure Application. 

### Data structures used by Zendure SolarFLow


JSON | Property | Description
---- | ---  | ---
root |      |    
| | messageId | STRING: This can be random; Used with method: error, getInfo, getInfo-rsp, read, write, BLESPP_OK, BLEGetVersion, otareq, otareq-rsp; Device always uses 123; App uses random 128bit hex-string, 1006 (BLEGetVersion) or 1009 (BLESPP_OK)
| | method | STRING: This can be: error, report, getInfo, getInfo-rsp, read, read_reply, write, write_reply, BLESPP, BLESPP_OK, BLEGetVersion, firmware, otareq, otareq-rsp
| | success | INT: Zero(0) is False and one(1) is True; Used with method: read_reply (getAll), write_reply
| | deviceId | STRING: Unique device id used to identify the replies when you have multiple SolarFlow devices; Used with method: error, report, getInfo, getInfo-rsp, read, read_reply, write, write_reply, BLESPP, firmware, otareq, otareq-rsp
| | timestamp | LONG: A timestamp (the device is not connected to the internet so it has no time tracking); Used with method: error, getInfo, getInfo-rsp, read, write, write_reply, firmware, otareq, otareq-rsp; Device uses seconds; App uses milliseconds
| | deviceSn | STRING: Device serial number; Used with method: getInfo-rsp, firmware 
| | offData | INT: Used with method: error
| | data | unknown[]: Used with method: error
| | result | INT: Used with method: otareq-rsp
| | reason | INT: Used with method: otareq-rsp
| | packetCount | INT: Used with method: otareq-rsp
| | payloadSize | INT: Used with method: otareq-rsp
| | fileRange | INT: Used with method: otareq-rsp
| | properties | See properties tables 
| | packData | See packData table 
| | firmwares | See firmwares table 
| | modules | See modules table 
| | firmware | See firmware table 
 

JSON | Property |Description
---- | ---  | ---
properties  |  | This is used with method: report|
| | ["getAll"] | It is used to get all the settings from the system. |


JSON | Property |Description
---- | ---  | ---
properties  |  | These are used with method: report |
| | packNum | INT: Number of battery packs, 1,2,3,4|
| | masterSwitch | INT |
| | electricLevel | INT: Overall battery power status as a percentage (%) (e.g., If two packs then it will be ((A+B)/2), where A is the battery power from pack 1 and B is the battery power from pack 2. |
| | wifiState | INT: If WiFi is enabled (1) or disabled (0)|
| | buzzerSwitch | INT: This is the audible buzzer, enabled (1) or disabled (0)|
| | socSet | INT : Maximum battery charge set by the user (%), 90% shown as 900 (value/10); App allows setting: 70%-100% |
| | solarInputPower | INT: Input from Solar Panels (W) |
| | solarPower1 | INT: Input from first Solar Panel (W) |
| | solarPower1Cycle | INT |
| | solarPower2 | INT: Input from second Solar Panel (W) |
| | solarPower2Cycle | INT |
| | packInputPower | INT: How much power is discharging from the batteries (W) |
| | packInputPowerCylce | INT |
| | outputPackPower | INT: The amount of power sent to the batteries in total (W)|
| | outputPackPowerCycle | INT |
| | outputHomePower | INT: Output from SolarFlow going to Home(microinverter) (W)|
| | outputHomePowerCycle | INT |
| | outputLimit | INT: Limit set on SolarFlow on how much to send to Home(microinverter) (W); App allows setting: 0, 30, 60, 90, 100-1200 |
| | inputLimit | INT |
| | remainOutTime | INT: How much time is left until the batteries discharge to 0% at the current rate of discharge. (59940 seems to be a default value when not discharging) |
| | remainInputTime | INT: (59940 seems to be a default status)  |
| | packState | INT : Status of the batteries. If zero(0) is not doing anything, one(1) is charging batteries, two(2) is discharging batteries |
| | hubState | INT : If automatic shutdown is enabled (1) or disabled (0) |
| | masterSoftVersion | INT: Software version| 
| | masterhaerVersion | INT |
| | inputMode | INT |
| | blueOta | INT |
| | pvBrand | INT : Brand of microinverter, Other (0), Hoymiles (1), Enphase (2), APsystems (3), Anker (4), Deye (5), BossWerk (6), Tsun (7) |
| | pass | INT : If the battery is bypassed (1) or not (0) |
| | passMode | INT : Control mode for `pass`, Automatic (0), Always Off (1), Always On (2) |
| | autoRecover | INT : If `passMode` gets reset to Automatic after a day (1) or not (0) |
| | minSoc | INT (value/10): Minimum charge level that the batteries will go to. (They will discharge up to this amount) This is used to maintain the batteries in good health. App allows setting: 0%-50% |
| | inverseMaxPower | INT: Maximum power that the microinverter supports; App allows setting: 100, 200, 300, ..., 1200 |
| | autoModel | INT |
| | gridPower | INT |
| | smartMode | INT |
| | smartPower | INT |
| | heatState | INT |
 

JSON | Property |Description
---- | ---  | ---
packData  |  | These are used with method: report  |
| | sn | STRING: Battery Serial Number (can be found on the outside)|
| | power | INT: Current |
| | socLevel | INT : Current battery level (%)|
| | state | INT: A zero (0) means it is doing nothing, and a one(1) means it is charging, two (2) means it is discharging|
| | maxTemp | INT: The maximum temprature is calculated as follows ((maxTemp/10)-273.15) e.g. (2841/10)-273.15=10.95C. A better algorithm is ((maxTemp-2731)/10) |
| | maxVol | INT |
| | minVol | INT |
| | totalVol | INT |
| | softVersion | INT : Software version|
| | soh | INT : Battery health? |


JSON | Property |Description
---- | ---  | ---
firmwares  |  | These are used with method: getInfo-rsp |
| | type | STRING: Type of device, e.g. MASTER, BMS, BMS_AB2000
| | version | INT: Software version or -1


JSON | Property |Description
---- | ---  | ---
modules  |  | These are used with method: firmware |
| | module | STRING: Type of device, e.g. MASTER, BMS, BMS_AB2000
| | version | INT: Software version or -1

JSON | Property |Description
---- | ---  | ---
firmware  |  | These are used with method: otareq |
| | CRC16 | INT: Checksum of payload ([CRC-Type](https://crccalc.com/?method=CRC-16/MODBUS); high and low byte of result swapped)
| | size | INT: Payload size (bytes)
| | type | STRING: Type of device, e.g. MASTER, BMS_AB2000
| | version | INT: Software version

Key: INT=an integer number, STRING=a long string of characters, LONG=a long number

#### Example communication of reading settings/values

I have anonymised a lot of the data in here. 
DEVICE_ID, UNIX_TIMESTAMP, DEVICE_SERIAL, BATTERY1_SN, BATTERY2_SN

If you receive this in Notify:
```
{"deviceId":"DEVICE_ID","method":"BLESPP"}
```

Then you can reply with the following:
```
{"messageId":"UNIX_TIMESTAMP","method":"BLESPP_OK"}
```
Once BLESSP_OK is sent you can then start sending requests.

We can now send getInfo:
```
{"messageId": "UNIX_TIMESTAMP","method":"getInfo","timestamp": UNIX_TIMESTAMP}
```

And get a reply getInfo-rsp: 

```
{"messageId":"123","method":"getInfo-rsp","deviceId":"DEVICE_ID","timestamp":0000000,"deviceSn":"DEVICE_SERIAL","firmwares":[{"type":"MASTER","version":XXXX},{"type":"BMS","version":XXXX}]}
```

Next we can send a request to getAll information. (This will generate a number of notifications, so be ready to get lots of data):

```
{"messageId":"11","deviceId":"DEVICE_ID","timestamp":UNIX_TIMESTAMP,"properties":["getAll"],"method":"read"}
```

First reply to getAll:

```
{"method":"read_reply","deviceId":"DEVICE_ID","success":1,"properties":{"getAll":1}}
```

Next reply with the number of batteries and their serial numbers:

```
{"method":"report","deviceId":"DEVICE_ID","properties":{"packNum":2},"packData":[{"sn":"BATTERY1_SN"},{"sn":"BATTERY2_SN"}]}
```

Master Switch, electric level and Wifi State:

```
{"method":"report","deviceId":"DEVICE_ID","properties":{"masterSwitch":1,"electricLevel":90,"wifiState":0}}
```

If the buzzer is on, max battery level and energy from solar panels

```
{"method":"report","deviceId":"DEVICE_ID","properties":{"buzzerSwitch":0,"socSet":111,"solarInputPower":111}}
```

Some energy values:

```
{"method":"report","deviceId":"DEVICE_ID","properties":{"packInputPower":0,"outputPackPower":111,"outputHomePower":111}}
```

What an error looks like:

```
{"messageId":"123","method":"error","deviceId":"DEVICE_ID","timestamp":3034829,"offData":1,"data":[]}
```

Output limit to send to the home, input limit and remaing OutTime

```
{"method":"report","deviceId":"DEVICE_ID","properties":{"outputLimit":112,"inputLimit":0,"remainOutTime":480}}
```

More examples:

```
{"method":"report","deviceId":"DEVICE_ID","properties":{"remainInputTime":59940,"packState":1,"hubState":0}}
```

```
{"method":"report","deviceId":"DEVICE_ID","properties":{"masterSoftVersion":0000,"masterhaerVersion":0,"inputMode":0}}
```

```
{"method":"report","deviceId":"DEVICE_ID","properties":{"blueOta":1,"pvBrand":1,"pass":0}}
```

```
{"method":"report","deviceId":"DEVICE_ID","properties":{"minSoc":113,"inverseMaxPower":112,"autoModel":0}}
```

```
{"method":"report","deviceId":"DEVICE_ID","properties":{"gridPower":0,"smartMode":0,"smartPower":0}}
```

```
{"method":"report","deviceId":"DEVICE_ID","properties":{},"packData":[{"power":111,"socLevel":11,"state":1,"sn":"BATTERY1_SN"}]}
```

```
{"method":"report","deviceId":"DEVICE_ID","properties":{"outputPackPower":000,"outputHomePower":000},"packData":[{"maxTemp":2800,"sn":"BATTERY1_SN"}]}
```

```
{"method":"report","deviceId":"DEVICE_ID","properties":{},"packData":[{"totalVol": 1000,"maxVol":111,"minVol":111,"sn":"BATTERY1_SN"}]}
```

```
{"method":"report","deviceId":"DEVICE_ID","properties":{},"packData":[{"softVersion":0000,"sn":"BATTERY1_SN"},{"power":111,"sn":"BATTERY2_SN"}]}
```

```
{"method":"report","deviceId":"DEVICE_ID","properties":{},"packData":[{"socLevel":11,"state":1,"maxTemp":2800,"sn":"BATTERY1_SN"}]}
```

```
{"method":"report","deviceId":"DEVICE_ID","properties":{"outputPackPower":111,"outputHomePower":111},"packData":[{"totalVol":1000,"sn":"BATTERY1_SN"}]}
```

```
{"method":"report","deviceId":"DEVICE_ID","properties":{},"packData":[{"maxVol":111,"minVol":111,"softVersion":0000,"sn":"BATTERY1_SN"}]}
```

```
{"method":"report","deviceId":"DEVICE_ID","properties":{"outputPackPower":111,"outputHomePower":111}}
```

#### Example communication of writing settings

I have anonymised a lot of the data in here. 
DEVICE_ID, UNIX_TIMESTAMP


Set output power limit to 30W:

```
{"method": "write", "timestamp": UNIX_TIMESTAMP, "messageId": "d9f644656a4346c8a459aadb8dcfd92c", "deviceId": "DEVICE_ID", "properties": {"outputLimit": 30}}
```

```
{"method": "write_reply", "deviceId": "DEVICE_ID", "timestamp": UNIX_TIMESTAMP, "success": 1, "properties": {"outputLimit": 30}}
```

Set minimum charge level to 50%:

```
{"method": "write", "timestamp": UNIX_TIMESTAMP, "messageId": "a8037350118b40f28ba2aecc9649c305", "deviceId": "DEVICE_ID", "properties": {"minSoc": 500}}
```

```
{"method": "write_reply", "deviceId": "DEVICE_ID", "timestamp": UNIX_TIMESTAMP, "success": 1, "properties": {"minSoc": 500}}
```

Enable automatic shutdown:

```
{"method": "write", "timestamp": UNIX_TIMESTAMP, "messageId": "30cc48981dd14a56bb2aa8512f34b39f", "deviceId": "DEVICE_ID", "properties": {"hubState": 1}}
```

```
{"method": "write_reply", "deviceId": "DEVICE_ID", "timestamp": UNIX_TIMESTAMP, "success": 1, "properties": {"hubState": 1}}
```

Set microinverter power to 400W and other brand:

```
{"method": "write", "timestamp": UNIX_TIMESTAMP, "messageId": "b3337c1f052f4c3783e3bb3678adddba", "deviceId": "DEVICE_ID", "properties": {"inverseMaxPower": 400, "pvBrand": 0}}
```

```
{"method": "write_reply", "deviceId": "DEVICE_ID", "timestamp": UNIX_TIMESTAMP, "success": 1, "properties": {"inverseMaxPower": 400, "pvBrand": 2}}
```

Set maxium charge level to 70%:

```
{"method": "write", "timestamp": UNIX_TIMESTAMP, "messageId": "222497e9c3764d22b2b65e15b99fdfcd", "deviceId": "DEVICE_ID", "properties": {"socSet": 700}}
```

```
{"method": "write_reply", "deviceId": "DEVICE_ID", "timestamp": UNIX_TIMESTAMP, "success": 1, "properties": {"socSet": 700}}
```

Passthrough:

```
{"method": "write", "timestamp": UNIX_TIMESTAMP, "messageId": "fe2c5da7f4464ba7af80675e63eafbf8", "deviceId": "DEVICE_ID", "properties": {"passMode": 2}}
```

```
{"method": "write_reply", "deviceId": "DEVICE_ID", "timestamp": UNIX_TIMESTAMP, "success": 1, "properties": {"passMode": 2}}
```

Don't reset passMode:

```
{"method": "write", "timestamp": UNIX_TIMESTAMP, "messageId": "67d9d1bc2ca946e2b4c57e47e7c3d75f", "deviceId": "DEVICE_ID", "properties": {"autoRecover": 0}}
```

```
{"method": "write_reply", "deviceId": "DEVICE_ID", "timestamp": UNIX_TIMESTAMP, "success": 1, "properties": {"autoRecover": 0}}
```

Disable buzzer sound:

```
{"method": "write", "timestamp": UNIX_TIMESTAMP, "messageId": "c48542da7f55414d8e97fc717fd862fa", "deviceId": "DEVICE_ID", "properties": {"buzzerSwitch": 0}}
```

```
{"method": "write_reply", "deviceId": "DEVICE_ID", "timestamp": UNIX_TIMESTAMP, "success": 1, "properties": {"buzzerSwitch": 0}}
```

#### Example communication of a firmware update

I have anonymised a lot of the data in here. 
DEVICE_ID, UNIX_TIMESTAMP

Start the update:

```
{"firmware": {"CRC16": 61491, "size": 76260, "type": "BMS_AB2000", "version": 4113}, "deviceId": "DEVICE_ID", "messageId": "883e4ea9bf0b45a09906d9e573d3160e", "method": "otareq", "timestamp": UNIX_TIMESTAMP}
```

The device replies:

```
{"method": "otareq-rsp", "timestamp": UNIX_TIMESTAMP, "messageId": "123", "deviceId": "DEVICE_ID", "result": 1, "reason": 0, "packetCount": 15, "payloadSize": 128, "fileRange": 0}
```

Next send a block of 16 packets (`packetCount + 1`?) with 5 byte header and 128 byte of the payload (payloadSize):

```
Header        | Payload
55:2f:00:f0:80:e8:46:...
55:2f:00:f1:80:d8:81:...
55:2f:00:f2:80:9d:a1:...
...
55:2f:00:ff:80:63:5c:...
```

Header-Format: 55:2f:XX:YZ:NN  
XX represents the block number, starting with 0 and counting up.  
Y represents the number of the last packet of the block.  
Z represents the number of the packet, starting with 0 and counting up.  
NN represents the payload length.  

The device replies with:

```
55:24:00:00:05:20:00:00:08:00
```

Format: 55:24:XX:00:05:20:00:0M:MM:MM  
XX represents the block number, starting with 0 and counting up.  
MMMMM represents the total received number of bytes. In the above example this is 128 × 16 = 2048 = 0x800  

Then send the next block:

```
Header        | Payload
55:2f:01:f0:80:56:52:...
55:2f:01:f1:80:04:d0:...
55:2f:01:f2:80:08:40:...
...
```

And wait for the device reply:

```
55:24:01:00:05:20:00:00:10:00
```

The last data block may contain less than 16 packets and the last packet may contain a smaller payload.

After the reply for the last block the device sends the following packets:

```
55:25:25:00:01:01
55:26:00:00:02:03:00
55:26:00:00:02:03:02
...
55:26:00:00:02:03:60
55:26:00:00:02:01:64
55:25:00:00:01:03
```

Format: 55:25:XX:00:01:01  
XX represents the number of the last block.  

Format: 55:26:00:00:02:03:VV  
VV represents the progress 0 to 100.  
