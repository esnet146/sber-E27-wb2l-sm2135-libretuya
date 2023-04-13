# sber-E27-wb2l-sm2135-libretuya
Прошивка лампочки под esphome
Стандартный компонент с ошибками, не переключается на белый свет. вместо него использовал
https://git.kolosowscy.pl/homeassistant/esphome-hardware/-/tree/10d9f14fe8e9185eab9cc4ed9f239e3e4a0e0203/custom_components/sm2135
Ток драйвера прописан жестко в коде, исправить можно в sm2135.cpp:
прилагается[sm2135.zip](https://github.com/esnet146/sber-E27-wb2l-sm2135-libretuya/files/11011953/sm2135.zip) распаковать и каталог sm2135 со всеми файликами положить в каталог my_components в папке где хранятся *.YAML

```
  if (this->update_channel_ == 3 || this->update_channel_ == 4) {
    // No color so must be Cold/Warm
    data[0] = SM2135_ADDR_MC;
    data[1] = SM2135_45MA;
    data[2] = SM2135_CW;
    this->write_buffer_(data, 3);
    delay(1);
    data[0] = SM2135_ADDR_C;
    data[1] = this->pwm_amounts_[4];  //4  Warm
    data[2] = this->pwm_amounts_[3];  //3  Cold
    this->write_buffer_(data, 3);
	delay(2);
  } else {
    // Color
    data[0] = SM2135_ADDR_MC;
    data[1] = SM2135_20MA;
    data[2] = SM2135_RGB;
    data[3] = this->pwm_amounts_[1];  // Green
    data[4] = this->pwm_amounts_[0];  // Red
    data[5] = this->pwm_amounts_[2];  // Blue
    this->write_buffer_(data, 6);
	delay(2);
```

```
substitutions:
  board_name: sb-e27-1

esphome:
  name: $board_name
  project:
    name: "SberE27.LibreTuya"
    version: "WB2L"
  comment: "Sber E27"

libretuya:
  board: wb2l
#  board: generic-bk7231t-qfn32-tuya
  framework:
    version: dev

external_components:
  - source:
      type: local
      path: my_components


preferences:
  flash_write_interval: 1min

api:
  encryption:
    key: !secret keyapi 
  reboot_timeout: 0s

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


web_server:
  port: 80  


button:
  - platform: restart
    name: Reset.$board_name

sm2135:
  data_pin: P7
  clock_pin: P8

output:
  - platform: sm2135
    id: output_red
    channel: 2
    max_power: 0.4
  - platform: sm2135
    id: output_green
    channel: 0
    max_power: 0.4
  - platform: sm2135
    id: output_blue
    channel: 1
    max_power: 0.4
  - platform: sm2135
    id: output_cold_white
    channel: 3
    max_power: 0.6
  - platform: sm2135
    id: output_warm_white
    channel: 4
    max_power: 0.6

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
```
