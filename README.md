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
  * Zigbee2MQTT or ZHA:
    * [VZM31-SN Blue Series 2-in-1 Dimmer](https://inovelli.com/en-ca/products/zigbee-matter-blue-series-smart-2-1-on-off-dimmer-switch)
    * [VZM35-SN Blue Series 3-Speed Fan Switch](https://inovelli.com/products/blue-series-fan-switch-zigbee-3-0)

  This is a complete rewrite of the work done by BrianHanifin on post: https://community.home-assistant.io/t/inovelli-z-wave-red-series-notification-led/165483


## Features
  
  This blueprint and script can set and clear effects as well as configure the LED or LED strip on Inovelli dimmers, switches, and fan / light combo dimmers from the "Black", "Red 500", "Red 800", and "Blue" series. Devices of different types can be called simultaneously. **It will accept entities, the device ID, groups, labels, floors,** `floor: 'all'`, **areas, or** `area: 'all'` **and find all Inovelli devices in the house.** This blueprint and script can set everything at once—even if the devices are different series, different sources (e.g. 2 areas, and 3 entities), and different integrations (Z-Wave JS and Zigbee2MQTT, or ZWave JS and ZHA) all at once.

  The  LED indicator can be set alongside an effect in a single call so that an indicator color change doesn't clear the effect (this does restart the duration timer in some versions of Inovelli's firmware, however). For example, setting "chase" for 1 second with a red indicator LED is a nice notification that something changed without being overly obnoxious and distracting (like setting "fast blink" with an infinite duration).  The effect wears off after one second, but the indicator stays red afterwards.
  
  Effects that have been set to "forever" can be cleared by just passing the entity, device, group, label, area, or floor.  There's no need to remember the effect, color, and brightness parameter or set them to 0 in a template.  It's easier to remember when you get to my age, and less to type.  Also, it's a fairly safe way to "fail" if we don't have all the right parameters, since (most of the time) clearing the effect will have no visible result on the physical device.


## Blueprint
  
  After importing the blueprint, use the UI to set static parameters and simply call it in automations with a single line, like: `service: script.inovelli_led_night_leds`.

<a href="https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fkschlichter%2FHome-Assistant-Inovelli-Effects-and-Colors%2Fblob%2Fmaster%2Fblueprints%2Fscript%2Fkschlichter%2Finovelli_led_blueprint.yaml" target="_blank" rel="noreferrer noopener"><img src="https://my.home-assistant.io/badges/blueprint_import.svg" alt="Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled." /></a>


## Installation Instructions

The easiest way to install is via blueprint.  If you'd rather use the script, which can accept templeted inputs, I recommend adding a directory called `scripts` and [including it in your configuration.yaml file](https://www.home-assistant.io/docs/configuration/splitting_configuration/).
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
  * Paste the contents into the Yaml editor.
  * In the vertical ellipsis select “Edit in Visual Editor”
  * Click the blue, “Save Script” button in the lower right.

## Setting the LED indicator

  These parameters are all optional and can be configured together or individually (to change the brightness at sunrise but not the color, for example).

    - LEDnumber: (Blue Series only; LED 1 .. LED 7 or All) Set to `all` to configure the whole LED bar (default if the parameter is left out) or configure a single LED.
        If specific LEDs have been set, they'll need to be unset before the LED bar will show anything.  The easiest way is to call LEDnumber 'all' with LEDcolor 'all clear'.
    - LEDcolor: (int or string) Sets color of the status LED.  If LEDcolor_off is defined and supported by the device, this is only used for "on" status.
        If a "color set" is used like "all unicorn", LEDnumber cannot be set to "LED {n}"; "all" LEDs must be configured or none will be.
    - LEDcolor_off: (Red 800 and Blue Series only; int or string) Sets color of status LED when off, for devices that support this feature.
        Note that the Blue Series and Red 800 Series support separate colors for on and off while the Black 500 and Red 500 Series devices do not and will ignore the variable.
        If a "color set" is used like "all unicorn", LEDnumber cannot be set to "LED {n}"; "all" LEDs must be configured or none will be.
    - LEDbrightness: (float 0.0 – 10.0) Sets the brightness of the status LED.  If LEDbrightness_off is defined and supported by the device, this is only used for "on" status.
        LEDbrightness is multiplied by 10 or rounded off for each device, depending on whether they support 0 – 100 or 0 – 10 brightness levels.  This was done for backwards compatibility with old automations.
    - LEDbrightness_off: (Red 800 and Blue Series only; float 0.0 – 10.0; see note above) Sets the brightness of the status LED when off, for devices that support this feature.

## Required for setting LED effects

  All four parameters must be passed in order to cange the effect.

    - LEDnumber: (Blue Series only; LED 1 .. LED 7 or All) Set to `all` to configure the whole LED bar (default if the parameter is left out) or configure a single LED.
        If specific LEDs have been set, they'll need to be unset before the LED bar will show anything.  The easiest way is to call LEDnumber 'all' with LEDcolor 'all clear'.
    - LEDcolor: (int or string) Sets color of the status LED.  If LEDcolor_off is defined and supported by the device, this is only used for "on" status.
        If a "color set" is used like "all unicorn", LEDnumber cannot be set to "LED {n}"; "all" LEDs must be configured or none will be.
    - LEDcolor_off: (Red 800 and Blue Series only; int or string) Sets color of status LED when off, for devices that support this feature.
        Note that the Blue Series and Red 800 Series support separate colors for on and off while the Black 500 and Red 500 Series devices do not and will ignore the variable.
        If a "color set" is used like "all unicorn", LEDnumber cannot be set to "LED {n}"; "all" LEDs must be configured or none will be.
    - LEDbrightness: (float 0.0 – 10.0) Sets the brightness of the status LED.  If LEDbrightness_off is defined and supported by the device, this is only used for "on" status.
        LEDbrightness is multiplied by 10 or rounded off for each device, depending on whether they support 0 – 100 or 0 – 10 brightness levels.  This was done for backwards compatibility with old automations.
    - LEDbrightness_off: (Red 800 and Blue Series only; float 0.0 – 10.0; see note above) Sets the brightness of the status LED when off, for devices that support this feature.

    - duration: (string) Either "Off", or a whole integer followed by "Seconds", "Minutes", "Hours", "Indefinitely", or "Forever".
    - effect: (string) Where older devices don't support a new effect, that effect has been mapped to something that is supported.
    - brightness: (integer 1 – 10) Sets the brightness of the LED's effect
    - color: (int or string) Sets color of LED effect and must be one of: "Off", Red, Orange, Lemon, Yellow, Lime, Green, Cyan, Teal, Blue, Purple, Magenta, Light Pink, Pink, Hot Pink, White
        Color sets like "all usa" cannot be used with effects.  I can't think of a way to get all 7 LEDs synchronized for effects like "pulse" or "chase".


## Notification effect examples:
  Area and group values can be IDs or names, mixed as a proper list or a string of comma-separated values.  Entities must use the entity name, and devices must use the device ID.

  **'area: all' will find any compatible Inovelli devices in Home Assistant 2023.04 or newer.**
  
    service: script.inovelli_led
    data:
      area: 'all'
      duration: 'Forever'
      effect: 'Fast Blink'
      brightness: 8.7
      color: 'light pink'

   **String of comma-separated values (improper list format still works)**
   
    service: script.inovelli_led
    data:
      area: 'Family Room, 7d7a44fe4d0f4bee947c430d2714e45c' 
      duration: 'Forever'
      effect: 'CHASE'
      brightness: 8
      color: 'Teal'

  **Proper list format**
  
    service: script.inovelli_led
    data:
      group:
        - group.lights_and_switches
        - 0249abdc634c12cbf6cdc06d7a507495
      duration: '2 minutes'
      effect: 'pulse'
      brightness: 8
      color: 'red'
            
  **Single LED (7, at the top) effect to signal LED transition to new color**
  
    service: script.inovelli_led
    data:
      entity: 'light.office'
      LEDcolor: 'green'
      LEDnumber: 'led 7'
      LEDbrightness: 7.5
      LEDbrightness_off: 2.3
      duration: '1 second'
      effect: 'Solid'
      color: 'green'
      brightness: 7
      
  **Clearing an effect**

  Mix and match areas, groups, devices, and entities
  
    service: script.inovelli_led
    data:
      floor: 'upstairs'
      entity: 'fan.front_porch'
  
## LED color examples:

  **Full LED configuration, using device ID**

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

  **Set by group domain**

    service: script.inovelli_led
    data:
      group: 'group.outside_front_lights'
      LEDcolor: 'Green'
      
 **LEDcolor_off and LEDbrightness_off example: (maybe part of a nighttime routine?)**

    service: script.inovelli_led
    data:
      entity: 'light.office'
      LEDcolor_off: 'RED'
      LEDbrightness_off: 2

 **Configuring an LED color set (color sets start with 'all' and must be set with all LEDs)**

    service: script.inovelli_led
    data:
      device:
        - 88f56168bdb28f3ac764fc3d7e3d407b
      LEDnumber: 'All'
      LEDcolor: 'All Unicorn'
      LEDcolor_off: 'All USA'
      LEDbrightness: 8.6
      LEDbrightness_off: 0.4
      
 **Configuring an LED color for one LED**

    service: script.inovelli_led
    data:
      entity: 'light.office'
      LEDnumber: 'led 4'
      LEDcolor: 'red'

**Clearing all individual LED color settings in the house**

    service: script.inovelli_led
    data:
      area: 'all'
      LEDnumber: 'all'
      LEDcolor: 'all clear'
      LEDcolor_off: 'all clear'



## Automation to listen for a config button press and set everything in the area to "pulse"
  
  This automation listens for a config / 3rd button press, checks in the condition stage that it's an Inovelli device, toggles an input_boolean for the area of the device (set this up separately), then sets or clears an effect on every Inovelli device in the area depending on the new state of the boolean.  I use that boolean in other automations to disable lighting controls (like motion timeouts).  
      
    alias: Light Locks
    description: ''
    trigger:
      - platform: event
        event_type: zwave_js_value_notification
        event_data:
          value: KeyPressed
          label: Scene 003
    condition:
      - condition: template
        value_template: >-
          {{ device_attr(trigger.event.data.device_id,'manufacturer') == 'Inovelli' }}
    action:
      - service: input_boolean.toggle
        data: {}
        target:
          entity_id: >-
            {{ 'input_boolean.' + area_name(trigger.event.data.device_id)|replace( ' ' , '_' )|string|lower + '_in_use' }}
      - choose:
          - conditions:
              - condition: template
                value_template: >-
                  {% set use_boolean = 'input_boolean.' + area_name(trigger.event.data.device_id)|replace( ' ' , '_' )|string|lower + '_in_use' %}
                  {{ is_state(use_boolean,'on') }}
            sequence:
             - service: script.inovelli_led
                data:
                  area: >
                    {% set area = namespace(id=[]) %} 
		    {% set area.id = area.id + [area_id(trigger.event.data.device_id) | string] %} 
		    {{ area.id|lower }}
                  duration: Forever
                  effect: Pulse
                  brightness: 4
                  color: Orange
          - conditions:
              - condition: template
                value_template: >-
                  {% set use_boolean = 'input_boolean.' + area_name(trigger.event.data.device_id)|replace( ' ' , '_' )|string|lower + '_in_use' %}
                  {{ is_state(use_boolean,'off') }}
            sequence:
                - service: script.inovelli_led
                data:
                  area: '{{ area_id(trigger.event.data.device_id) | string }}'
        default: []
    mode: single
