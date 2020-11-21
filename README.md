# SX1302_UDP_Packet_Forwarder
SX1302 Modified UDP Packet Forwarder 

This modified version of the Semtech SX1302 UDP Packet Forwarder addresses two issues

1) On TTN the inclusion of the "temp" data element in the stats datagram prevents the datagram being received by TTN
2) Not all SX1302 gateway modules include a stts751 temperature sensor.  To overcome the need to install an external I2C temperature sensor a preset temperature value can be set

The following commands have been added and are included in the global_config.json file


Issue 1 - to control the "temp" data element

  To next elements enables or disables sending "temp" data element in "stat" datagram" */
  If following line is missing the default value is true and the temp variable is included in the stat datagram */
  To send temperature data element set the following to true, to prevent sending the "temp" set the following to false  */

  "temperature_active":false,


Issue 2 - a lack of temperature sensor

  Some SX1302 gateway modules do not have a stts751 temperature sensor installed.  As a result the packet forwarder will fail to start.
  Where the sensor is installed place "true" in the next line of code and the sensor will be used
  Where there is no sensor, place "false" in the next line of code
  The default value if the following line is not used is true

  "temperature_sensor": true,


  When there is no sensor, use the next command to set the expected temperature which is used in the calculation of rssi
  If the previous line of code was set to "true" the following temperature setting is ignore and the sensor is used to measure actual temperature
  If the following line is missing but required, the default value is 20.0C

  "temperature_value": 25.0
  
  
  
```
More Detail about the "temp" data element in the stat datagram
  
SX1301 UDP Packet Forwarder follows what I think is the original gateway-to-server protocol whereas the more recent SX1302 UDP Packet Forwarder follows version v1.3 of the gateway-to-server protocol.

The observation described here relates to the behaviour with the stat datagram
 json
{
	"stat":{...}
}

Example
V1.3 gateway-to-server protocol				Original gateway-to-server protocol
(SX1302 stat datagram)						(SX1301 stat datagram)
{"stat":{									{"stat":{					
    "time":"2014-01-12 08:59:28 GMT",		    "time":"2014-01-12 08:59:28 GMT",
    "lati":46.24000,						            "lati":46.24000,
    "long":3.25230,							    "long":3.25230,
    "alti":145,								    "alti":145,
    "rxnb":2,								    "rxnb":2,
    "rxok":2,								    "rxok":2,
    "rxfw":2,								    "rxfw":2,
    "ackr":100.0,							    "ackr":100.0,
    "dwnb":2,								    "dwnb":2,
    "txnb":2,								    "txnb":2
    "temp": 23.2							}}
}}
Observation
When a stat datagram using V1.3 of the gateway-to-server protocol is sent the TTN server ignores the datagram all together
This can be observed on the console as the gateway is "Not Seen".
The only time the gateway "Last Seen" is updated is when a Lora radio packet is received and forwarded to the server.
By patching the SX1302 UDP packet forwarder and removing the temp data element bot the V2 and V3 versions of the server operate correctly.

