# Home-Assistant-Inovelli-Red-Dimmer-Switch

Original work by BrianHanifin on post: https://community.home-assistant.io/t/inovelli-z-wave-red-series-notification-led/165483
  I've corrected the calculations for switch notification effects, and added functionality for configuring the LED color and intensities.

Calculation References:
   https://nathanfiscus.github.io/inovelli-notification-calc/
   https://community.inovelli.com/t/home-assistant-2nd-gen-switch-rgb-working/168/62
   https://docs.google.com/spreadsheets/u/1/d/1SGJrJHCUtz8AzznWL_mLCTJjjr2U0IpltcUkRr7N_6M/edit?usp=sharing (dimmer only)

Using the script:
  Notification effects default to "Off", and LED colors require parameters to be passed to the script.  This allows a user or automation to set one independantly 
  of the other.  For example, turning the LEDs red at sunset and blue at sunrise doesn't require a notification effect.  Someone arriving home sets a "chase" 
  notification effect, but then returns the LEDs to their previous state (blue or red) after 10 seconds.
  
  Notification effects are set by passing a calculated number to the "LED Strip Effect" configuration parameter.  Since the numbers are multiplied and then added, 
  it's possible to get the LED (switch) or LED strip (dimmer) to still do something if defaults are left out, but it may not be the expected effect, color, or 
  intensity.  
    
  Notification effect examples:

    color: Teal
    duration: 10 Minutes
    effect: Chase
    entity_id: zwave.office_lights
    level: 6
    service: script.inovelli_switch_leds

    color: Light Pink
    duration: 30 Seconds
    effect: Pulse
    entity_id: zwave.entryway_lights
    level: 10
    service: script.inovelli_switch_leds


  LED settings will modify the default LED color when a notification effect is not active (i.e. normal use).  On the dimmer, the strip will fade up and down as 
  the light is dimmed.  The switch LED is either on or off.  Intensity for the LED can be set independantly for 'on' and 'off' states.  Defaults are not set for 
  the LEDs, and all four parameters must be passed for the LED settings to be modified.
  
  LED color examples: 

    LEDcolor: Red
    LEDintensity: 50%
    LEDintensity_off: 10%
    entity_id: zwave.office_lights
    service: script.inovelli_switch_leds

    LEDcolor: Blue
    LEDintensity: 100%
    LEDintensity_off: 30%
    entity_id: zwave.office_lights
    service: script.inovelli_switch_leds
    
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
    service: script.inovelli_switch_leds
    
Component Notes: 
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
