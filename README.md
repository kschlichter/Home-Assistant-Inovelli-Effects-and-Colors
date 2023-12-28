# Home-Assistant-Inovelli-Red-Dimmer-Switch
  
  [Inovelli Calculations](https://docs.google.com/spreadsheets/d/14wTP4OL4hkDK3Et5kYL4fyxPIK_R9JR3cgFxSa6dhyw/edit?usp=sharing)
  
  [Z-Wave JS to MQTT](https://hub.docker.com/r/zwavejs/zwavejs2mqtt) Container
  
  [Home Assistant Forum Post](https://community.home-assistant.io/t/control-leds-and-led-effects-on-inovelli-dimmers-switches-and-combo-fan-lights-by-area-device-or-entity/421862)

  Requires container 2.4.0 or greater.
  
  This is a complete rewrite of the original work which was done by BrianHanifin on post: https://community.home-assistant.io/t/inovelli-z-wave-red-series-notification-led/165483
    
  **Features**
  This script can set and clear effects as well as configure the LED or LED strip on Inovelli dimmers, switches, and fan / light combo dimmers.  Devices of different types can be set in a single call.  It will accept entities of Inovelli devices, the device itself in the form of the device ID, or an area ID to set all Inovelli devices in that area.

  The  LED indicator can be set alongside an effect in a single call so that an indicator color change doesn't clear the effect (this does restart the duration timer, however). For example, setting "chase" for 1 second with a red indicator LED is a nice notification that something changed without being overly obnoxious and distracting (like setting "fast blink" with an infinite duration).  The effect wears off after one second, but the indicator stays red afterwards.
  
  Effects that have been set to "forever" can be cleared by just passing the entity, device, or area the entity belongs to.  There's no need to remember the effect, color, and brightness parameter or set them to 0.  It's easier to remember when you get to my age, and less to type.  Also, it's a fairly safe way to "fail" if we don't have all the right parameters since (most of the time) clearing the effect will have no visible result on the physical device.
   
  **Required for setting the LED indicator**

    - LEDcolor: (int or string) Sets color of LED status and must be one of: Off, Red, Orange, Yellow, Green, Cyan, Teal, Blue, Purple, Light Pink, Pink, White
    - LEDbrightness: (whole integer 1 – 10) Sets the brightness of the LED status when on.
    - LEDbrightness_off: (whole integer 1 – 10) Sets the brightness of the LED status when off.

  **Required for setting LED effects**
  
    - duration: (string) Either "Off", or a whole integer followed by "Seconds", "Minutes", "Hours", "Indefinitely", or "Forever".
    - effect: (string) One of: "Off", "Solid", "Chase" (switches don't support chase and will fast blink instead), "Fast Blink", "Slow Blink", "Blink", or "Pulse".
    - brightness: (integer 1 – 10) Sets the brightness of the LED's effect
    - color: (int or string) Sets color of LED effect and must be one of: Off, Red, Orange, Yellow, Green, Cyan, Teal, Blue, Purple, Light Pink, Pink, White


## Notification effect examples:
  Area and group values can be IDs or names, mixed as a proper list or a string of comma-separated values.

   **String of comma-separated values**
   
    service: script.inovelli_led_zwavejs
    data:
      area: 'Family Room, 7d7a44fe4d0f4bee947c430d2714e45c' 
      duration: Forever
      effect: CHASE
      brightness: 8
      color: Teal

  **'area: all' will find any compatible Inovelli devices in Home Assistant 2023.04 or newer.**
  
    service: script.inovelli_led_zwavejs
    data:
      area: all
      duration: Forever
      effect: 'Fast Blink'
      brightness: 8
      color: 'light pink'

  **Proper list format**
  
    service: script.inovelli_led_zwavejs
    data:
      group:
        - group.lights_and_switches
        - 0249abdc634c12cbf6cdc06d7a507495
      effect: pulse
      brightness: 8
      color: red
      
## Clearing an effect
  
    service: script.inovelli_led_zwavejs
    data:
      area: 'Family Room'
      entity: fan.front_porch
  
## LED color example:

    service: script.inovelli_led_zwavejs
    data:
      entity: light.office
      LEDcolor: blue
      LEDbrightness: 7
      LEDbrightness_off: 3

    service: script.inovelli_led
    data:
      group: group.outside_front_lights
      LEDcolor: Green  
      
## LEDbrightness_off example: (maybe part of a nighttime routine?)

    service: script.inovelli_led_zwavejs
    data:
      entity: light.office
      LEDbrightness_off: 2
            
## Effect to signal LED indicator transition
  
    service: script.inovelli_led_zwavejs
    data:
      entity: light.office
      LEDcolor: green
      LEDbrightness: 7
      LEDbrightness_off: 3
      duration: 1 second
      effect: Chase
      color: green
      brightness: 7

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
             - service: script.inovelli_led_zwavejs
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
                - service: script.inovelli_led_zwavejs
                data:
                  area: >
                    {% set area = namespace(id=[]) %} 
		    {% set area.id = area.id + [area_id(trigger.event.data.device_id) | string] %} 
		    {{ area.id|lower }}
        default: []
    mode: single
