# sber-E27-wb2l-sm2135-libretuya
**Прошивка лампочки под esphome.**

```
substitutions:
  board_name: sb-e27-1

esphome:
  name: $board_name
  project:
    name: "SBDV-0019.WB2L"
    version: "BK7231T"
  comment: "детская, люстра"

bk72xx:
  board: wb2l
  framework:
    version: dev
preferences:
  flash_write_interval: 1min

api:
  encryption:
    key: !secret keyapi 

ota:
  password: !secret passwordota


logger:
  baud_rate: 0

captive_portal:
wifi:
  ssid: !secret wifi1
  password: !secret password1

  ap:
    ssid: "$board_name Hotspot"
    password: !secret password1


button:
  - platform: restart
    name: Reset.$board_name

sm2135:
  data_pin: P7
  clock_pin: P8
  cw_current: 35mA
  rgb_current: 20mA


output:
  - platform: sm2135
    id: output_red
    channel: 2
    max_power: 1.0
  - platform: sm2135
    id: output_green
    channel: 0
    max_power: 1.0
  - platform: sm2135
    id: output_blue
    channel: 1
    max_power: 1.0
  - platform: sm2135
    id: output_cold_white
    channel: 3
    max_power: 1.0
  - platform: sm2135
    id: output_warm_white
    channel: 4
    max_power: 1.0

light:
  - platform: rgbww
    name:  $board_name
    id: light1
    red: output_red
    green: output_green
    blue: output_blue
    cold_white: output_cold_white
    warm_white: output_warm_white
    cold_white_color_temperature: 6500 K
    warm_white_color_temperature: 2700 K
    color_interlock: true
    constant_brightness: true
    restore_mode: RESTORE_AND_ON
    gamma_correct: false
sensor:
  - platform: wifi_signal
    name: WiFi_Signal.$board_name
  - platform: uptime
    name: uptime_sensor.$board_name
text_sensor:
  - platform: version
    name: Version.$board_name
```
