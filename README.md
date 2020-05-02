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