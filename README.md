# Home-Assistant-Inovelli-Effects-and-Colors
  
  [Inovelli Calculations](https://docs.google.com/spreadsheets/d/14wTP4OL4hkDK3Et5kYL4fyxPIK_R9JR3cgFxSa6dhyw/edit?usp=sharing)
  
  [Z-Wave JS UI](https://hub.docker.com/r/zwavejs/zwave-js-ui) Container
  
  [Home Assistant Forum Post](https://community.home-assistant.io/t/control-leds-and-led-effects-on-inovelli-dimmers-switches-and-combo-fan-lights-by-area-device-or-entity/421862)

  Supported Inovelli devices:
  * Z-Wave:
    * [LZW30 Black 500 Series Switch](https://help.inovelli.com/en/articles/8454923-black-series-on-off-switch-manual)
    * [LZW31 Black 500 series Dimmer](https://inovelli.com/products/z-wave-black-series-smart-dimmer-switch)
    * [LZW30-SN Red 500 Series Switch](https://help.inovelli.com/en/articles/8453781-red-series-on-off-switch-manual)
    * [LZW31-SN Red 500 Series Dimmer](https://help.inovelli.com/en/articles/8319390-red-series-dimmer-switch-manual)
    * [LZW36 Red 500 Series Fan / Light Combo](https://help.inovelli.com/en/articles/8483467-red-series-fan-light-switch-manual)
    * [VZW31-SN Red 800 Series 2-in-1 Dimmer](https://inovelli.com/products/z-wave-800-red-series-smart-2-1-on-off-dimmer-switch)
  * Zigbee:
    * [VZM31-SN Blue Series 2-in-1 Dimmer](https://inovelli.com/en-ca/products/zigbee-matter-blue-series-smart-2-1-on-off-dimmer-switch)
    * [VZM35-SN Blue Series 3-Speed Fan Switch](https://inovelli.com/products/blue-series-fan-switch-zigbee-3-0)

  This is a complete rewrite of the work done by BrianHanifin on post: https://community.home-assistant.io/t/inovelli-z-wave-red-series-notification-led/165483


## Features
  
  This script can set and clear effects as well as configure the LED or LED strip on Inovelli dimmers, switches, and fan / light combo dimmers from the "Black", "Red 500", "Red 800", and "Blue" series.  Devices of different types can be called simultaneously.  **It will accept entities of Inovelli devices, the device itself in the form of the device ID, groups of entities, or areas and find all Inovelli devices in that area.**

  The  LED indicator can be set alongside an effect in a single call so that an indicator color change doesn't clear the effect (this does restart the duration timer in some versions of Inovelli's firmware, however). For example, setting "chase" for 1 second with a red indicator LED is a nice notification that something changed without being overly obnoxious and distracting (like setting "fast blink" with an infinite duration).  The effect wears off after one second, but the indicator stays red afterwards.
  
  Effects that have been set to "forever" can be cleared by just passing the entity, device, group, or area.  There's no need to remember the effect, color, and brightness parameter or set them to 0 in a template.  It's easier to remember when you get to my age, and less to type.  Also, it's a fairly safe way to "fail" if we don't have all the right parameters since (most of the time) clearing the effect will have no visible result on the physical device.


## Blueprint
  
  The blueprint can be imported, saved as a script, and used in the same way as the script below.  Alternatively, use the UI to set static parameters and simply call it with a single line, like: `service: script.inovelli_led_night_leds`

  
## Setting the LED indicator

  These parameters are all optional and can be configured together or individually (to change the brightness at sunrise but not the color, for example).
  
    - LEDcolor: (int or string) Sets color of status LED.  If LEDcolor_off is defined and supported by the device, this is only used for "on" status.
    - LEDbrightness: (whole integer 1 – 10) Sets the brightness of the status LED.  If LEDbrightness_off is defined and supported by the device, this is only used for "on" status.
    - LEDbrightness_off: (whole integer 1 – 10) Sets the brightness of the status LED when off, for devices that support this feature.
    - LEDcolor_off: (int or string) Sets color of status LED when off, for devices that support this feature.
        Note that the Blue 2-in-1 switch/dimmer and Red 2-in-1 switch/dimmer support separate colors for on and off while the Black 500 and Red 500 Series devices do not.


## Required for setting LED effects

  All four parameters must be passed in order to cange the effect.
  
    - duration: (string) Either "Off", or a whole integer followed by "Seconds", "Minutes", "Hours", "Indefinitely", or "Forever".
    - effect: (string) Where older devices don't support a new effect, that effect has been mapped to something that is supported.
    - brightness: (integer 1 – 10) Sets the brightness of the LED's effect
    - color: (int or string) Sets color of LED effect and must be one of: "Off", Red, Orange, Lemon, Yellow, Lime, Green, Cyan, Teal, Blue, Purple, Magenta, Light Pink, Pink, Hot Pink, White


## Notification effect examples:
  Area and group values can be IDs or names, mixed as a proper list or a string of comma-separated values.  Entities must use the entity name, and devices must use the device ID.

  **'area: all' will find any compatible Inovelli devices in Home Assistant 2023.04 or newer.**
  
    service: script.inovelli_led
    data:
      area: all
      duration: Forever
      effect: 'Fast Blink'
      brightness: 8
      color: 'light pink'

   **String of comma-separated values (improper list format still works)**
   
    service: script.inovelli_led
    data:
      area: 'Family Room, 7d7a44fe4d0f4bee947c430d2714e45c' 
      duration: Forever
      effect: CHASE
      brightness: 8
      color: Teal

  **Proper list format**
  
    service: script.inovelli_led
    data:
      group:
        - group.lights_and_switches
        - 0249abdc634c12cbf6cdc06d7a507495
      effect: pulse
      brightness: 8
      color: red
            
  **Effect to signal LED transition to new color**
  
    service: script.inovelli_led
    data:
      entity: light.office
      LEDcolor: green
      LEDbrightness: 7
      LEDbrightness_off: 3
      duration: 1 second
      effect: Chase
      color: green
      brightness: 7
      
  **Clearing an effect**

  Mix and match areas, groups, devices, and entities
  
    service: script.inovelli_led
    data:
      area: 'Family Room'
      entity: fan.front_porch
  
## LED color examples:

  **Full LED configuration, using device ID**

    service: script.inovelli_led
    data:
      device:
        - 531d79e9270d72d9cab44a4f295967d4
        - ef82d0eb91499feadf45e257c0e5eda1
      LEDcolor: blue
      LEDbrightness: 7
      LEDbrightness_off: 3

  **Set by group domain**

    service: script.inovelli_led
    data:
      group: group.outside_front_lights
      LEDcolor: Green  
      
 **LEDbrightness_off example: (maybe part of a nighttime routine?)**

    service: script.inovelli_led
    data:
      entity: light.office
      LEDbrightness_off: 2

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
