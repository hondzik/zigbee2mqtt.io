---
title: "Xiaomi WSDCGQ01LM control via MQTT"
description: "Integrate your Xiaomi WSDCGQ01LM via Zigbee2mqtt with whatever smart home
 infrastructure you are using without the vendors bridge or gateway."
---

*To contribute to this page, edit the following
[file](https://github.com/Koenkk/zigbee2mqtt.io/blob/master/docs/devices/WSDCGQ01LM.md)*

# Xiaomi WSDCGQ01LM

| Model | WSDCGQ01LM  |
| Vendor  | Xiaomi  |
| Description | MiJia temperature & humidity sensor |
| Supports | temperature and humidity |
| Picture | ![Xiaomi WSDCGQ01LM](../images/devices/WSDCGQ01LM.jpg) |

## Notes


### Pairing
Press and hold the reset button on the device for +- 5 seconds (until the blue light starts blinking). The reset button is the small button on the 'top' of the device. After this the device will automatically join.


### Device type specific configuration
*[How to use device type specific configuration](../information/configuration.md)*


* `temperature_precision`: Controls the precision of `temperature` values,
e.g. `0`, `1` or `2`; default `2`.
To control the precision based on the temperature value set it to e.g. `{30: 0, 10: 1}`,
when temperature >= 30 precision will be 0, when temperature >= 10 precision will be 1.
* `temperature_calibration`: Allows to manually calibrate temperature values,
e.g. `1` would add 1 degree to the temperature reported by the device; default `0`.


* `humidity_precision`: Controls the precision of `humidity` values, e.g. `0`, `1` or `2`; default `2`.
To control the precision based on the humidity value set it to e.g. `{80: 0, 10: 1}`,
when humidity >= 80 precision will be 0, when humidity >= 10 precision will be 1.


## OpenHAB integration and configuration
In OpenHAB you need the MQTT Binding to be installed. It is possible to add this sensor as a generic mqtt thing, but here it is described how to add the sensor manually via an editor.

To make the following configuration work, it is neccessary to enable the experimental attribute output in the configuration.yaml file:
```yaml
experimental:
    output: attribute
```

If you want to know, when the sensor has been last seen, add following block to your configuration.yaml file:
```yaml
advanced:
    last_seen: ISO_8601_local
```

### Things
To add this Xiaomi WSDCGQ01LM MiJia temperature & humidity sensor as Thing, it is necessary to embed the Thing into a bridge definition of your mqtt broker.

```yaml
Bridge mqtt:broker:zigbeeBroker [ host="YourHostname", secure=false, username="your_username", password="your_password" ]
{
    Thing topic TempSensor "MiJia temperature & humidity sensor"  @ "Your room"
    {
        Channels:
            Type number   : temperature "temperature"  [ stateTopic="zigbee2mqtt/<FRIENDLY_NAME>/temperature" ]
            Type number   : humidity    "humidity"     [ stateTopic="zigbee2mqtt/<FRIENDLY_NAME>/humidity" ]
            Type number   : voltage     "voltage"      [ stateTopic="zigbee2mqtt/<FRIENDLY_NAME>/voltage" ]
            Type number   : battery     "battery"      [ stateTopic="zigbee2mqtt/<FRIENDLY_NAME>/battery" ]
            Type number   : linkquality "link quality" [ stateTopic="zigbee2mqtt/<FRIENDLY_NAME>/linkquality" ]
			/* If last_seen is enabled */
            Type datetime : last_seen   "last seen"    [ stateTopic ="zigbee2mqtt/<FRIENDLY_NAME>/last_seen" ]
    }
}
```

### Items
```yaml
Number   temp_sensor_temp "temperature [%.1f °C]"               <temperature> {channel="mqtt:topic:zigbeeBroker:TempSensor:temperature"}
Number   temp_sensor_hum  "humidity [%.1f %%]"                  <humidity>    {channel="mqtt:topic:zigbeeBroker:TempSensor:humidity"}
Number   temp_sensor_volt "voltage [%d mV]"                     <energy>      {channel="mqtt:topic:zigbeeBroker:TempSensor:voltage"}
Number   temp_sensor_batt "battery [%.1f %%]"                   <battery>     {channel="mqtt:topic:zigbeeBroker:TempSensor:battery"}
Number   temp_sensor_lqi  "link qualitiy [%d]"                  <network>     {channel="mqtt:topic:zigbeeBroker:TempSensor:linkquality"}
/* If last_seen is enabled */
DateTime temp_sensor_ls   "last seen [%1$td.%1$tm.%1$tY %1$tR]" <string>      {channel="mqtt:topic:zigbeeBroker:TempSensor:last_seen"}
```


## Manual Home Assistant configuration
Although Home Assistant integration through [MQTT discovery](../integration/home_assistant) is preferred,
manual integration is possible with the following configuration:


{% raw %}
```yaml
sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    unit_of_measurement: "°C"
    device_class: "temperature"
    value_template: "{{ value_json.temperature }}"

sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    unit_of_measurement: "%"
    device_class: "humidity"
    value_template: "{{ value_json.humidity }}"

sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    unit_of_measurement: "%"
    device_class: "battery"
    value_template: "{{ value_json.battery }}"

sensor:
  - platform: "mqtt"
    state_topic: "zigbee2mqtt/<FRIENDLY_NAME>"
    availability_topic: "zigbee2mqtt/bridge/state"
    icon: "mdi:signal"
    unit_of_measurement: "lqi"
    value_template: "{{ value_json.linkquality }}"
```
{% endraw %}


