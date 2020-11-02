# Home-Assistant-Inovelli-Red-Dimmer-Switch

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

		
