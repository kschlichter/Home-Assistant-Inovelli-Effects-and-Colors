# Home-Assistant-Inovelli-Effects-and-Colors
  
  [Inovelli Calculations](https://docs.google.com/spreadsheets/d/14wTP4OL4hkDK3Et5kYL4fyxPIK_R9JR3cgFxSa6dhyw/edit?usp=sharing)
  
  
  [Blueprint Exchange post](https://community.home-assistant.io/t/control-leds-and-led-effects-on-inovelli-black-red-and-blue-devices-by-area-group-device-id-or-entity/681056) / [Older Home Assistant forum post with more history](https://community.home-assistant.io/t/control-leds-and-led-effects-on-inovelli-dimmers-switches-and-combo-fan-lights-by-area-device-or-entity/421862)


  Supported Inovelli devices:
  * Z-Wave:
    * [LZW30 Black 500 Series Switch](https://help.inovelli.com/en/articles/8454923-black-series-on-off-switch-manual)
    * [LZW31 Black 500 series Dimmer](https://inovelli.com/products/z-wave-black-series-smart-dimmer-switch)
    * [LZW30-SN Red 500 Series Switch](https://help.inovelli.com/en/articles/8453781-red-series-on-off-switch-manual)
    * [LZW31-SN Red 500 Series Dimmer](https://help.inovelli.com/en/articles/8319390-red-series-dimmer-switch-manual)
    * [LZW36 Red 500 Series Fan / Light Combo](https://help.inovelli.com/en/articles/8483467-red-series-fan-light-switch-manual)
    * [VZW31-SN Red 800 Series 2-in-1 Dimmer](https://inovelli.com/products/z-wave-800-red-series-smart-2-1-on-off-dimmer-switch)
    * [VZM32-SN Red 800 Series mmWave Presence Dimmer](https://inovelli.com/collections/z-wave-light-switches-red-series/products/zwave-800-red-series-mmwave-presence-sensor-smart-dimmer-switch)
  * Zigbee2MQTT or ZHA:
    * [VZM30-SN Blue Series On/Off Switch](https://inovelli.com/collections/inovelli-blue-series/products/blue-series-smart-on-off-switch-w-humidity-sensor)
    * [VZM31-SN Blue Series 2-in-1 Dimmer](https://inovelli.com/en-ca/products/zigbee-matter-blue-series-smart-2-1-on-off-dimmer-switch)
    * [VZM32-SN Blue Series mmWave Presence Dimmer Switch](https://inovelli.com/collections/inovelli-blue-series/products/zigbee-blue-series-mmwave-presence-sensor-smart-dimmer-switch)
    * [VZM35-SN Blue Series 3-Speed Fan Switch](https://inovelli.com/products/blue-series-fan-switch-zigbee-3-0)
  * Matter:
    * [VTM31-SN White Series Smart 2-1 Switch](https://inovelli.com/collections/inovelli-white-series/products/thread-matter-white-series-smart-2-1-on-off-dimmer-switch)
    * [VTM35-SN White Series Smart Fan Switch](https://inovelli.com/collections/inovelli-white-series/products/thread-matter-white-series-smart-fan-switch-3-speed-ceiling-on-off-exhaust)

  This is a complete rewrite of the work done by BrianHanifin on post: https://community.home-assistant.io/t/inovelli-z-wave-red-series-notification-led/165483


## Features
  
  This blueprint and script can set and clear effects as well as configure the LED or LED strip on Inovelli dimmers, switches, and fan / light combo dimmers from the "Black", "Red 500", "Red 800", "Blue", and "White" series. Devices of different types can be called simultaneously. **It will accept entities, the device ID, groups, labels, , device type (e.g.** `fan` **), floors,** `floor: 'all'`, **areas, or** `area: 'all'` **and find all Inovelli devices in the house.** This blueprint and script can set everything at once—even if the devices are different series, different sources (e.g. 2 areas, and 3 entities), and different integrations (Z-Wave JS and Zigbee2MQTT, or ZHA and Matter) all at once.

  The  LED indicator can be set alongside an effect in a single call so that an indicator color change doesn't clear the effect (this does restart the duration timer in some versions of Inovelli's firmware, however). For example, setting "chase" for 1 second with a red indicator LED is a nice notification that something changed without being overly obnoxious and distracting (like setting "fast blink" with an infinite duration).  The effect wears off after one second, but the indicator stays red afterwards.
  
  Effects that have been set to "forever" can be cleared by just passing the entity, device, group, label, area, or floor.  There's no need to remember the effect, color, and brightness parameter or set them to 0 in a template.  It's easier to remember when you get to my age, and less to type.  Also, it's a fairly safe way to "fail" if we don't have all the right parameters, since (most of the time) clearing the effect will have no visible result on the physical device.


## Blueprint Installation Instructions
  
  After importing the blueprint:
  * In Home Assistant:
    * Click on "Settings" in the left-hand navigation pane.
    * "Automations & Scenes"
    * "Blueprints" near the top in a horizontal menu.
    * "Inovelli LED Settings and Effects Blueprint" to open a new screen with the configuration options.
    * Use the UI to set static parameters or call it like the script version with dynamic or templated inputs.
    * Click the blue "Save Script" button in the lower-right corner of the screen.
    * Give your script a name (e.g. bedroom_night) and click "rename"
    * Call it from an automation with `service: script.bedroom_night`

<a href="https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fkschlichter%2FHome-Assistant-Inovelli-Effects-and-Colors%2Fblob%2Fmaster%2Fblueprints%2Fscript%2Fkschlichter%2Finovelli_led_blueprint.yaml" target="_blank" rel="noreferrer noopener"><img src="https://my.home-assistant.io/badges/blueprint_import.svg" alt="Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled." /></a>


## Script Installation Instructions

The easiest way to install is via blueprint.  If you'd rather use the script I recommend adding a directory called `scripts` and [including it in your configuration.yaml file](https://www.home-assistant.io/docs/configuration/splitting_configuration/).
```
script: !include scripts.yaml
script folder: !include_dir_named scripts
```
As a quick start you can follow these steps:

* In Home Assistant:
  * Click on “Settings” in the left-hand navigation pane.
  * “Automations & Scenes”
  * “Scripts” near the top in a horizontal menu / tab.
  * Blue, “+ Add Script” button in the lower-right.
  * “Create new script”
  * In the upper-right click the vertical ellipsis (three dot) menu and then “edit in Yaml”.
  * Delete the pre-populated lines.
* Open a new tab
* In Github (https://github.com/kschlichter/Home-Assistant-Inovelli-Effects-and-Colors/blob/master/inovelli_led.yaml)
  * On the right side, click the “copy raw file” in between the “raw” and “download” buttons.
* In Home Assistant:
  * Paste the contents into the yaml editor.
  * In the vertical ellipsis select “Edit in Visual Editor”
  * Click the blue, “Save Script” button in the lower right.
  * Click "Developer tools" in the left-hand navigation pane.
  * Click "YAML" at the top of the page.
  * Click "Check configuration" and then, under YAML configuration reloading, click "Scripts".
  * Click "Actions" in the blue top bar, and select the script from the drop-down menu there.  
  * Call it from automations by passing the parameters explained below. 

## Setting the LED indicator

  These parameters are all optional and can be configured together or individually (to change the brightness at sunrise but not the color, for example).

    - LEDnumber: (Blue Series only; LED 1 .. LED 7 or All) Set to `all` to configure the whole LED bar (default if the parameter is left out) or configure a single LED.
        If specific LEDs have been set, they'll need to be unset before the LED bar will show anything.  The easiest way is to call LEDnumber 'all' with LEDcolor 'all clear'.
    - LEDcolor: (int or string) Sets color of the status LED.  If LEDcolor_off is defined and supported by the device, this is only used for "on" status.
        If a "color set" is used like "all unicorn", LEDnumber cannot be set to "LED {n}"; "all" LEDs must be configured or none will be.
    - LEDcolor_off: (Red 800 and Blue Series only; int or string) Sets color of status LED when off, for devices that support this feature.
        Note that the Blue Series and Red 800 Series support separate colors for on and off while the Black 500, Red 500, and White Series devices do not and will ignore the variable.
        If a "color set" is used like "all unicorn", LEDnumber cannot be set to "LED {n}"; "all" LEDs must be configured or none will be.
    - LEDbrightness: (float 0.0 – 10.0) Sets the brightness of the status LED.  If LEDbrightness_off is defined and supported by the device, this is only used for "on" status.
        LEDbrightness is multiplied by 10 or rounded off for each device, depending on whether they support 0 – 100 or 0 – 10 brightness levels.  This was done for backwards compatibility with old automations.
    - LEDbrightness_off: (Red 800 and Blue Series only; float 0.0 – 10.0; see note above) Sets the brightness of the status LED when off, for devices that support this feature.

## Required for setting LED effects

  All four parameters must be passed in order to change the effect.

    - duration: (string) Either "Off", or a whole integer followed by "Seconds", "Minutes", "Hours", "Indefinitely", or "Forever".
    - effect: (string) Where older devices and individual LEDs don't support a given effect, that effect has been mapped to something that is supported.
    - brightness: (integer 1 – 10) Sets the brightness of the LED's effect
    - color: (int or string) Sets color of LED effect and must be one of: "Off", Red, Orange, Lemon, Yellow, Lime, Green, Cyan, Teal, Blue, Purple, Magenta, Light Pink, Pink, Hot Pink, White
        Color sets like "all usa" cannot be used with effects.  I can't think of a way to get all 7 LEDs synchronized for effects like "pulse" or "chase".


## Selector Mode:
  Select devices that meet *ANY* requirements (logical OR) or *ALL* requirements (logical AND).   Since the default for `selector_mode:` is `any`, you can leave this field out / unselected and get the same result.  Blueprints and script calls written before this feature was added do not need to be updated, and will have the same behavior as before.  
  **Turning off the LED bar for everything that's upstairs, OR ANY fans:**
  This service call will turn off the LED bar for all Inovelli devices that are upstairs, and also all fans in the house (e.g. upstairs, downstairs, in the garage, or outside).

```
service: script.inovelli_led
data:
  selector_mode: any
  domain: 'fan'
  floor: 'upstairs'
  LEDcolor: 'Off'
  LEDcolor_off: 'Off'
``` 

  **Turning off the LED bar only for fans that are upstairs:**
  By using `selector_mode: 'all'`, we can now limit the entities to upstairs fans.  Fans that are downstairs, or lights that are upstairs will not be changed by this call.

```
service: script.inovelli_led
data:
  selector_mode: all
  domain: 'fan'
  floor: 'upstairs'
  LEDcolor: "Off"
  LEDcolor_off: "Off"
```

## Z2M Topic
  Topic (string) used to identify and route messages within the MQTT protocol.  Default: 'zigbee2mqtt'
  
  **Turn off LEDs on all devices reporting through the topic "zigbee2mqttupstairs":**
```
service: script.inovelli_led
data:
  selector_mode: all
  domain: 'fan'
  z2m_topic: 'zigbee2mqttupstairs'
  LEDcolor: "Off"
  LEDcolor_off: "Off"
```

## Allowed Domains:
  Only allow entities of these types.  The default will allow all three types.
  **Set the LED bar on all fans on the first floor to purple (while leaving lights and switches blue):**
```
service: script.inovelli_led
data:
  allowed_domains: 'fan'
  floor: 'first floor'
  LEDcolor: 'purple'
  LEDcolor_off: 'purple'
```
  
## Domain:
  Select and include all entities with domain(s) light.*, fan.*, or switch.* or deselect for all.  
  **Set the LED bar on all fans in the house to purple:**
```
service: script.inovelli_led
data:
  domain: 'fan'
  LEDcolor: 'purple'
  LEDcolor_off: 'purple'
```

**Set the LED bar on all lights and switches in the house to blue:**
```
service: script.inovelli_led
data:
  domain: 
    - light
    - switch
  LEDcolor: 'blue'
  LEDcolor_off: 'blue'
```


## Label:
  Labels on Inovelli devices and entities, or areas containing Inovelli devices.  Labels can be on the entity, device, or area.

  **Set all bedroom LEDs to a dim red in the evening:**
```  
action: script.inovelli_led
data:
  label: `bedroom`
  LEDcolor: 'RED'
  LEDcolor_off: 'Red'
  LEDbrightness: 2
  LEDbrightness_off: 0.1
```


## Floor:
  Floor names or IDs containing areas with Inovelli devices.
  
  **Setting an an effect on all devices upstairs:**
  
```
service: script.inovelli_led
data:
  floor: 'upstairs'
  duration: 'Forever'
  effect: 'CHASE'
  brightness: 8
  color: 'Teal'
```


## Area:
  Area names or IDs containing Inovelli devices.
  
  **'area: all' will find any compatible Inovelli devices in Home Assistant 2023.04 or newer.**
  **Setting an effect on every Inovelli device in the house**
```
service: script.inovelli_led
data:
  area: 'all'
  duration: 'Forever'
  effect: 'Fast Blink'
  brightness: 8.7
  color: 'light pink'
```

   **String of comma-separated values (improper list format still works)**
   **Setting an effect on all devices in two areas:**
```
service: script.inovelli_led
data:
  area: 'Family Room, 7d7a44fe4d0f4bee947c430d2714e45c' 
  duration: 'Forever'
  effect: 'CHASE'
  brightness: 8
  color: 'Teal'
```


## Group:
  Group names or IDs for groups containing Inovelli devices. Mix and match types as you like.
  
  **Proper list format**
  **Setting a 2min effect on all devices in two groups:**
```
service: script.inovelli_led
data:
  group:
    - group.lights_and_switches
    - 0249abdc634c12cbf6cdc06d7a507495
  duration: '2 minutes'
  effect: 'pulse'
  brightness: 8
  color: 'red'
```


## Device: 
  Device IDs of Inovelli devices. Mix and match types as you like.  Devices will only accept the device ID, making them harder to use unless it's a templated device, like 

  **Mix and match fields:**
  **Clearing an effect on all devices upstairs, in the office, the device that triggered the event, and the front porch:**
  Since the default behavior for the script is to clear any effects, there's no need to call `effect: 'Clear Effect'`.  
  
```
service: script.inovelli_led
data:
  floor: 'upstairs'
  area: 'office'
  device: '{{ trigger.event.data.device_id }}'
  entity: 'fan.front_porch'
```

  **Full LED configuration, using device ID**

```
service: script.inovelli_led
data:
  device:
    - 531d79e9270d72d9cab44a4f295967d4
    - ef82d0eb91499feadf45e257c0e5eda1
  LEDcolor: 'blue'
  LEDcolor_off: 'hOt PiNk'
  LEDbrightness: 7
  LEDbrightness_off: 2.5
  LEDnumber: 'all'
```


## Entity:
  The light.*, switch.*, or fan.* entity for the LED we're setting. 
  
  **A 30 sec effect to signal an event, followed by LED 7 (at the top) turning green as an on-going notification:**
  
```
service: script.inovelli_led
data:
  entity: 'light.office'
  LEDcolor: 'green'
  LEDnumber: 'led 7'
  LEDbrightness: 7.5
  LEDbrightness_off: 2.3
  duration: '30 second'
  effect: 'fast blink'
  color: 'green'
  brightness: 7
```
 

## LEDnumber:
  Sets the full LED bar by default or `'all'`, or specific LEDs (1 – 7) starting at the bottom.
  
  **Configuring an LED color set (color sets start with 'all' and must be set with all LEDs)**
  Once an invidivual LED or an LED color set has been configured, it must be cleared.  The full LED bar settings will be overridden by the individual LED settings.
```
service: script.inovelli_led
data:
  device:
    - 88f56168bdb28f3ac764fc3d7e3d407b
  LEDnumber: 'LED 1'
  LEDcolor: 'All Unicorn'
  LEDcolor_off: 'All USA'
  LEDbrightness: 8.6
  LEDbrightness_off: 0.4
```
      
 **Configuring an LED color for one LED**

```
service: script.inovelli_led
data:
  entity: 'light.office'
  LEDnumber: 'led 4'
  LEDcolor: 'red'
```

  **Clearing all individual LED color settings in the house**
  This will reset the LED bars to red.  

```
service: script.inovelli_led
data:
  area: 'all'
  LEDnumber: 'all'
  LEDcolor: 'all clear'
  LEDcolor_off: 'all clear'
```


## LEDcolor:
  Sets the color of the LED status bar, which indicates brightness levels of the light.
  * This is not for effects.
  * Color sets:
    * Blue Series only.
    * Pre-defined sets, like `all unicorn` and `all usa`, can be cleared with `all clear`.
    * Setting `LEDcolor` will not override the individual LED colors.

```
action: script.inovelli_led
data:
  entity:
    - light.office
  LEDnumber: All
  LEDcolor: All Clear
```

## LEDcolor_custom:
  Sets the color of the LED status bar to a custom dictionary, which indicates brightness levels of the light.
  * example: `{'led 1':255,'led 2':255,'led 3':255,'led 4':255,'led 5':255,'led 6':255,'led 7':255,'all':0}`
    * example above clears settings for all individual LEDs and sets the LED bar to red.
  * Needs `LEDcolor: all custom on` and `LEDnumber: all` (or left out)
  * Custom color sets:
    * Blue Series only.
    * Setting `LEDcolor` will not override the individual LED colors.
```
action: script.inovelli_led
data:
  LEDnumber: All
  LEDcolor_custom: >-
    {'led 1':25,'led 2':75,'led 3':125,'led 4':150,'led 5':175,'led 6':200,'led 7':250,'all':0}
  entity:
    - light.office
  LEDcolor: All Custom On
```

## LEDcolor_off:
  Sets the color of the LED status bar, which indicates brightness levels of the light.
  * Red 800 and Blue Series only.  No support on Black, Red 500, or White devices.
  * This is not for effects.
  * Custom color sets:
    * Blue Series only.
    * Pre-defined sets, like `all unicorn` and `all usa`, can be cleared with `all clear`.
    * Setting `LEDcolor` will not override the individual LED colors.


## LEDbrightness:
  Sets the brightness of the LED status when on. 0 means off.
  * This is not for effects.


## LEDbrightness_off:
  Sets the brightness of the LED status when off. 0 means off.
  * This is not for effects.
  * Red 800 and Blue Series only.


## LEDnumber_effect:
  Sets the effect on the full LED bar by default, or specific LEDs (1 – 7) starting at the bottom.
  * Red800 and Blue Series only.  No support on Black, Red 500, or White series devices.
  * The full LED bar will override individual LEDs (e.g. you won't see LED 3 through an effect for the full bar)
    * Once the full bar effect is cleared, the individual LED effects will still be running.
    * Individual LED effects need to be cleared 
  * When using `duration: forever` each LED's effect will need to be cleared with a separate command.  

```
action: script.inovelli_led
data:
  entity:
    - light.office
  LEDnumber_effect: LED 4
  effect: Pulse
  brightness: 7.5
  color: Lime
  duration: Forever
```

## effect:
#### To set an effect, all four of the parameters below must be defined ####
  Type of effect. (Red and Blue Series only.  Black and White Series devices do not support effects.)
action: script.inovelli_led
data:
  entity:
    - light.office
  effect: chase
  brightness: 7
  color: red
  duration: Forever

## brightness:
  Sets the brightness of the LED's effect.  0 means off.


## color:
  Color of LED for the effect


## duration:
  How long the effect will last.

## Clearing an effect: ##
Clearing an effect can be done by simply calling the entity (or floor, area, label, etc):
```
action: script.inovelli_led
data:
  entity:
    - light.office
```

To clear one LED, add the LEDnumber_effect parameter:
```
action: script.inovelli_led
data:
  entity:
    - light.office
  LEDnumber_effect: LED 4
```
