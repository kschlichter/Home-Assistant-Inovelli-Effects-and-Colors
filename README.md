# Home-Assistant-Inovelli-Red-Dimmer-Switch
# [Z-Wave JS to MQTT](https://hub.docker.com/r/zwavejs/zwavejs2mqtt) Container

  Requires container 2.4.0 https://hub.docker.com/r/zwavejs/zwavejs2mqtt
  
  This is a complete rewrite of the original work which was done by BrianHanifin on post: https://community.home-assistant.io/t/inovelli-z-wave-red-series-notification-led/165483
    
  **Features**
  This script can set effects, the LED indicator strip, any single aspect of the LED strip, or clear an effect.  A nighttime automation could simply dim the LED indicator without changing the color; separate automations could handle the indicator brightness while another sets colors for morning, night, and holidays.  Changes to the LED brightness at night or during the day would be easy to manage and wouldn't have to be tracked down through multiple automations.  
  
  The  LED indicator can now be set alongside an effect in a single call so that an indicator color change doesn't clear the effect and using the switch (which clears the effect in old firmware versions like mine) will still have a red "warning" LED indicator.  For example, setting "chase" for 1 second with a red indicator LED is a nice "hey, there's a door open" notification without being overly obnoxious and distracting.
  
  Effects that have been set to "forever" can be cleared by just passing the entity and model.  There's no need to remember the effect, color, and brightness parameter or set them to 0.  It's easier to remember when you get to my age.  Also, it's a fairly safe way to "fail" if we don't have all the right parameters since (most of the time) clearing the effect will have no visible result on the switch.
  
  Z-Wave JS can take multiple entities in a comma separate list, which makes setting the entire house easy (and copying the yaml into a new automation, too).
  
  I'm using lower case throughout the script since it's able to handle capitol letters in the middle of mistyped and camelcase words like "LightPink".
  
  [Calculations](https://docs.google.com/spreadsheets/d/14wTP4OL4hkDK3Et5kYL4fyxPIK_R9JR3cgFxSa6dhyw/edit?usp=sharing) are no longer needed, since the dimmer takes effect parameters individually, which also means "pulse" works as an effect again and the "choose" code is a little cleaner than in the 2.2.0 container.
    
  **Required parameters**
    entity: (string) The entity ID from "Developer Tools" -> "States".  The device to be modified (e.g. light.office_lights_level)
            Can also be a comma separated list, if all entities are the same model.  For example, "light.office, light.family_room"
    model: (string) One of: dimmer (LZW31), switch (LZW30), combo_light (LZW36), combo_fan (LZW36)

  **Required for setting the LED indicator**
    LEDcolor: (int or string) Sets color of LED status and must be one of: Off, Red, Orange, Yellow, Green, Cyan, Teal, Blue, Purple, Light Pink, Pink, White
            NOTE: The Red Series switch not take any digit 1 – 255 as a color.  Red must be 0, and it cannot receive the teal or light pink settings.  
    LEDintensity: (whole integer percentage e.g. 60%) Sets the brightness of the LED status when on.
    LEDintensity_off: ((whole integer percentage e.g. 60%) Sets the brightness of the LED status when off.

  **Required for setting LED effects**
    duration: (string or whole integer of seconds) Either "Off", an integer of seconds, or a whole integer followed by "Second", "Seconds", "Minute", or "Minutes", "Hour", "Hours", or "Indefinitely".
    effect: (string) One of: "Off", "Solid", "Chase" (not available on switches), "Fast Blink", "Slow Blink", "Blink", "Pulse", "Breath"
            NOTE: The Red Series dimmer won't "pulse".  Home assistant is limiting the value for the effect register to 83823359 and the math for pulse works out to more than that.  
    intensity: (int 1 – 10) Sets the brightness of the LED's effect
    color: (int or string) Sets color of LED effect and must be one of: Off, Red, Orange, Yellow, Green, Cyan, Teal, Blue, Purple, Light Pink, Pink, White
            NOTE: The Red Series switch, combo_fan and combo_light will not take any digit 1 – 255.  Red must be 0, and it cannot receive the teal or light pink settings.  

  **Notification effect examples:**
	
    service: script.inovelli_led_zwavejs
    data:
      entity: light.office, light.family_room_2x, light.family_room_6x, light.guest_room
      model: dimmer
      duration: Forever
      effect: FAST BLINK
      intensity: 8
      color: red
      
    service: script.inovelli_led_zwavejs
    data:
      entity: light.ceiling_FAN
      model: combo_fan      
      effect: FAST BLINK
      intensity: 8
      color: red
      
    service: script.inovelli_led_zwavejs
    data:
      entity: light.ceiling_LIGHT
      model: combo_light     
      effect: FAST BLINK
      intensity: 8
      color: red
            
    service: script.inovelli_led_zwavejs
    data:
      entity: switch.front_porch_lights
      model: switch
      effect: FAST BLINK
      intensity: 8
      color: red
      
  **Clearing an effect**
  
    service: script.inovelli_led_zwavejs
    data:
      entity: switch.front_porch_lights
      model: switch
  
  **LED color example:**

    service: script.inovelli_led_zwavejs
    data:
      entity: light.office
      model: dimmer   
      LEDcolor: blue
      LEDintensity: 7
      LEDintensity_off: 3

  **LED intensity_off example: (maybe part of a nighttime routine?)**

    service: script.inovelli_led_zwavejs
    data:
      entity: light.office
      model: dimmer   
      LEDintensity_off: 2
            
  **Effect to signal LED indicator transition**
  
    service: script.inovelli_led_zwavejs
    data:
      entity: light.office
      model: dimmer
      LEDcolor: green
      LEDintensity: 7
      LEDintensity_off: 3
      duration: 1 second
      effect: Chase
      color: green
      intensity: 7




# Open Z-Wave 1.4 (Deprecated)

Original work by BrianHanifin on post: https://community.home-assistant.io/t/inovelli-z-wave-red-series-notification-led/165483
  I've corrected the calculations for switch notification effects, and added functionality for configuring the LED color and intensities.

Calculation References:

	https://nathanfiscus.github.io/inovelli-notification-calc/
	https://community.inovelli.com/t/home-assistant-2nd-gen-switch-rgb-working/168/62
	https://docs.google.com/spreadsheets/d/14wTP4OL4hkDK3Et5kYL4fyxPIK_R9JR3cgFxSa6dhyw/edit?usp=sharing (updated with dimmer and switch math)

**Using the script:**

  Notification effects default to "Off", and LED colors require parameters to be passed to the script.  This allows a user or automation to set one independantly 
  of the other.  For example, turning the LEDs red at sunset and blue at sunrise doesn't require a notification effect.  Someone arriving home sets a "chase" 
  notification effect, but then returns the LEDs to their previous state (blue or red) after 10 seconds.
  
  Notification effects are set by passing a calculated number to the "LED Strip Effect" configuration parameter.  Since the numbers are multiplied and then added, 
  it's possible to get the LED (switch) or LED strip (dimmer) to still do something if defaults are left out, but it may not be the expected effect, color, or 
  intensity.  
  
  The Inovelli LZW36 fan / light combo switch has two, independently controlled, LEDs.  In order to determine the parameter value, the "Type" variable must be
  passed to the script with a value of either "Fan" or "Light".  For consistency, the "Type" can be passed to the script when using other Inovelli switches, or
  omitted so that old automations don't need to be updated.
    
  **Notification effect examples:**

    color: Teal
    duration: 10 Minutes
    effect: Chase
    entity_id: zwave.office_lights
    level: 6
    service: script.inovelli_led

    color: Light Pink
    duration: 30 Seconds
    effect: Pulse
    entity_id: zwave.entryway_lights
    level: 10
    service: script.inovelli_led
    
    color: Green
    duration: Indefinitely
    effect: Chase
    Type: Fan
    entity_id: zwave.guest_bedroom_fan_light
    level: 10
    service: script.inovelli_led
    
    color: Green
    duration: Indefinitely
    effect: Chase
    Type: Light
    entity_id: zwave.guest_bedroom_fan_light
    level: 10
    service: script.inovelli_led


  LED settings will modify the default LED color when a notification effect is not active (i.e. normal use).  On the dimmer, the strip will fade up and down as 
  the light is dimmed.  The switch LED is either on or off.  Intensity for the LED can be set independantly for 'on' and 'off' states.  Defaults are not set for 
  the LEDs, and all four parameters must be passed for the LED settings to be modified.
  
  **LED color examples:**

    LEDcolor: Red
    LEDintensity: 50%
    LEDintensity_off: 10%
    entity_id: zwave.office_lights
    service: script.inovelli_led

    LEDcolor: Blue
    LEDintensity: 100%
    LEDintensity_off: 30%
    entity_id: zwave.office_lights
    service: script.inovelli_led
    
  Setting an LED color immediately overrides a notification effect.  If you tend to use long notifications, but want to change the LED colors at regular intervals
  (like sunrise and sunset), you'll have to handle this conflict.  One possible solution is a binary sensor that's set and unset when the notification begins and 
  ends.  Also, calling the script with something like the example below, will result in the LED color being set, but the notification effect is overriden before 
  a user would notice it.
  
    color: Light Pink
    duration: 30 Seconds
    effect: Pulse
    level: 10
    LEDcolor: Blue
    LEDintensity: 100%
    LEDintensity_off: 50%
    entity_id: zwave.entryway_lights
    service: script.inovelli_led
    
**Component Notes:**

Inovelli LZW30-SN Switch Red Series:

	github XML Config file: https://github.com/InovelliUSA/OpenZWave/blob/master/config/inovelli/lzw30-sn.xml
	LED Indicator Color: 5 size: 2
	LED Indicator Intensity: 6 size: 1
	LED Indicator Intensity (when off): 7 size: 1
	LED Strip Effect: 8 size: 4

Inovelli LZW31-SN Dimmer Red Series:

	github XML Config file: https://github.com/InovelliUSA/OpenZWave/blob/master/config/inovelli/lzw31-sn.xml
	LED Indicator Color: 13 size: 2
	LED Indicator Intensity: 14 size: 1
	LED Indicator Intensity (when off): 15 size: 1
	LED Strip Effect: 16 size: 4

Inovelli LZW36 Fan / Light Switch:

	github XML Config file: https://github.com/InovelliUSA/OpenZWave/blob/master/config/inovelli/lzw36.xml
	Light Indicator Color: 18 size: 2
	Light LED Indicator Intensity: 19 size: 1
	Light LED Indicator Intensity (when off): 22 size: 1
	Light LED Strip Effect: 16 size: 24
	Fan Indicator Color: 13 size: 20
	Fan LED Indicator Intensity: 21 size: 1
	Fan LED Indicator Intensity (when off): 23 size: 1
	Fan LED Strip Effect: 16 size: 25

		
