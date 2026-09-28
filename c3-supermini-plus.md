## ESPHome config for ESP32 SuperMini Plus

```yaml
# ESPHome config for the ESP32-C3 SuperMini Plus / V2 (red PCB)
# Onboard WS2812 RGB LED on GPIO8, BOOT button on GPIO9.

substitutions:
  name: c3-supermini-plus
  friendly_name: C3 SuperMini Plus

esphome:
  name: ${name}
  friendly_name: ${friendly_name}

esp32:
  board: esp32-c3-devkitm-1
  variant: esp32c3
  framework:
    type: esp-idf

# Logs go out over the native USB port
logger:

# Home Assistant connection. Keep the key the ESPHome wizard generates for you.
api:
  encryption:
    key: !secret c3_supermini_plus__encryption_key
ota:
  - platform: esphome
    password: !secret ota_password

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
  # Fallback hotspot if Wi-Fi fails
  ap:
    ssid: "C3 SuperMini Plus Fallback"
    password: !secret fallback_ap_password

captive_portal:

# ---------------------------------------------------------------
# Onboard WS2812 RGB LED
# ---------------------------------------------------------------
light:
  - platform: esp32_rmt_led_strip
    id: onboard_rgb
    name: "Onboard RGB LED"
    pin:
      number: GPIO8
      ignore_strapping_warning: true   # GPIO8 is a strapping pin; fine for an LED
    num_leds: 1
    chipset: WS2812
    channel_colors: GRB                     # If red/green are swapped, try RGB
    restore_mode: RESTORE_DEFAULT_OFF
    default_transition_length: 0.5s
    effects:
      - pulse:
          name: "Slow Pulse"
          transition_length: 1s
          update_interval: 1s
          min_brightness: 15%          # This LED blanks out below roughly 11%
          max_brightness: 60%
      - pulse:
          name: "Fast Pulse"
          transition_length: 0.3s
          update_interval: 0.3s
          min_brightness: 15%
          max_brightness: 60%
      - strobe:
          name: "Strobe"
      - random:
          name: "Random Colours"
          transition_length: 2s
          update_interval: 3s
      - addressable_rainbow:
          name: "Rainbow"
          speed: 10

# ---------------------------------------------------------------
# BOOT button (GPIO9) as a bonus input: short press toggles the LED
# ---------------------------------------------------------------
binary_sensor:
  - platform: gpio
    name: "BOOT Button"
    pin:
      number: GPIO9
      mode: INPUT_PULLUP
      inverted: true
      ignore_strapping_warning: true
    filters:
      - delayed_on: 20ms
    on_click:
      then:
        - light.toggle: onboard_rgb

# ---------------------------------------------------------------
# Handy diagnostics
# ---------------------------------------------------------------
sensor:
  - platform: wifi_signal
    name: "Wi-Fi Signal"
    update_interval: 60s
  - platform: uptime
    name: "Uptime"

button:
  - platform: restart
    name: "Restart"
```
