### Description
In this repository, i made a doorbell using ESP8266-01 as a controller. This doorbell will play local mp3 file as notification sound through 3.5mm audio jack in my laptop, since i install home assistant server on docker in Ubuntu laptop. The audio jack in my laptop is connected to external speaker that will boost notification sound volume. I will cover up the details of this project in 2 parts. First is setting up doorbell hardware. Second is configuring home assistant server.

### First part, Doorbell Hardware

#### Tools and Equipments
Here is the parts we need to make physical doorbell button.
* ESP8266-01 Module
* Push Button
* BD140 Transistor
* 2x 18650 Li-Ion Battery
* 2x 5mm LED Indicator

#### Wiring Diagram
This section will cover wiring diagram from doorbell button. The detailed schematic and board file can be downloaded in the controller board folder. <br>
<p align="center">
<img src="https://github.com/falithurrahman/DoorBell_Project/blob/master/Controller%20Board/Schematic.JPG" alt="drawing" width="600"/>
</p>
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

### Second Part, Home Assistant Configuration

#### Software Requirement
I am intended to make customized mp3 file played as notification sound through 3.5mm audio jack. This file is stored in local hard drive inside home assistant server. An add-ons is need to be installed to fulfill this purpose. To install the add-ons, follow these steps.
* In your home assisstant, go to supervisor -> Add-ons Store.
* Under repositories part, where we configure which add-on repositories to fetch data from, copy-paste this link. Then click 'Add'. It might take a bit, be patient. <br>
https://github.com/bestlibre/hassio-addons
* When the add-ons successfully installed, start the service.
* Add these line to your configuration.yaml file 
````yaml
# Mopidy addon for hass.io
media_player:
  platform: mpd
  host: YOUR.HOST.IP
````
* Restart home assistant server. Now we have Mopidy add-ons running in home assistant server.

After installing the add-ons, we need to configure it. As we can see at mopidy add-ons page, there are several variables that we can configure. Since our purpose is to play local mp3 file as notification sound, we need to enable local file scan and set the directory where the mp3 is stored. It can be done by writing these line
````yaml
local_scan: true
options:
  - name: local/media_dir
    value: /share/mopidy/media
````
In the example above, i place my mp3 file inside /share/mopidy/media. Then, restart home assistant server.

Next step, we will play the audio file stored in local drive through home assistant. To play the audio, follow the steps below.
* Go to Mopidy add-ons store page, and open web user interface. 
* We will see there are three options: image, iris, moped.Click on iris. <br>
<p align="center">
<img src="https://github.com/falithurrahman/DoorBell_Project/blob/master/MopidyUI_1.JPG" alt="drawing" width="600"/>
</p>
* At iris homepage, go to browse -> local media -> tracks
<p align="center">
<img src="https://github.com/falithurrahman/DoorBell_Project/blob/master/MopidyUI_2.JPG" alt="drawing" width="600"/> <br>
<img src="https://github.com/falithurrahman/DoorBell_Project/blob/master/MopidyUI_3.JPG" alt="drawing" width="600"/> <br>
<img src="https://github.com/falithurrahman/DoorBell_Project/blob/master/MopidyUI_4.JPG" alt="drawing" width="600"/> <br>
</p>
Or in short, you can access the link below <br>
http://"home_assistant_server_IP":6680/iris/library/browse/local%3Adirectory%3Ftype%3Dtrack <br>
Replace home_assistant_server_IP with the IP of home assistant without "" mark. <br>
* Find audio file we need, click on the three dots (...) and click on "Copy URI". Now we have the internal ID of this file in our clipboard. <br>
<p align="center">
<img src="https://github.com/falithurrahman/DoorBell_Project/blob/master/MopidyUI_5.JPG" alt="drawing" width="600"/> <br>
<img src="https://github.com/falithurrahman/DoorBell_Project/blob/master/MopidyUI_6.JPG" alt="drawing" width="600"/> <br>
</p>
* Go to developer tools -> service tab. Choose 'media_player.play_media' for service and 'media_player.mpd' for entity. For service data, fill it with line below
    ````yaml
    {
    "entity_id": "media_player.mpd",
    "media_content_type": "music",
    "media_content_id": "..."
    }
    ````
    Replace the media_content_id value with the URI file we copy. <br>
    <p align="center">
    <img src="https://github.com/falithurrahman/DoorBell_Project/blob/master/MopidyUI_7.JPG" alt="drawing" width="600"/> <br>
    </p>
* Click "Call Service". The file should now be played via the 3.5mm headphone jack. Now we can use it in any automation.