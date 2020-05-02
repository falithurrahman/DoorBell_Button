### Description
In this repository, i made a doorbell using ESP8266-01 as a controller. This doorbell will play a notification sound through 3.5mm audio jack in my laptop, since i install home assistant server on docker in Ubuntu laptop. The audio jack in my laptop is connected to external speaker that will boost notification sound volume. I will cover up the details of this project in 2 parts. First is setting up doorbell hardware. Second is configuring home assistant server.

#### First part, Doorbell Hardware

#### Tools and Equipments
Here is the parts we need to make physical doorbell button.
* ESP8266-01 Module
* Push Button
* BD140 Transistor
* 2x 18650 Li-Ion Battery
* 2x 5mm LED Indicator

#### Wiring Diagram
This section will cover wiring diagram from doorbell button. The detailed schematic and board file can be downloaded in the controller board folder. <br>
<img src="https://github.com/falithurrahman/DoorBell_Project/blob/master/Controller%20Board/Schematic.JPG" alt="drawing" width="200"/> <br>
I used 2x 18650 Li-Ion battery to power this circuit. Those batteries will have parallel connection to increase the capacity (mAh). Higher capacity will make this battery more durable. I have tried this configuration, and the battery i use can last 4-5 days. So i have to replace the battery once every 4-5 days for charging.

#### Code
I use ESPHome firmware running on ESP8266-01 device. Since i use ESPHome, the code will be in yaml file. You can download the code inside Code folder with ESP01_BellControl.yaml filename. Pushbutton and indicator LED are attached to GPI02 and GPIO0 respectively. Button debounce filter is added to binary sensor in order to minimize misreading which is caused by debounce.
````yaml
switch:
  - platform: gpio
    id: indikator
    inverted: true
    name: LED Indikator
    pin: GPIO0

binary_sensor:
  - platform: gpio
    id: button
    name: Doorbell Button
    pin:
      number: GPIO2
      mode: INPUT_PULLUP
      inverted: true
    filters:
      - delayed_on: 25ms
      - delayed_off: 25ms
````