# Howto Multi-room HiFi

Following are (work in progress!) build details for my DIY multi-room hifi system. I've tried a fair number of possible builds and settled on the following:

# Software:
- logitech media server [via docker](https://hub.docker.com/r/lmscommunity/logitechmediaserver)
- home asisstant using the [Squeezebox integration](https://www.home-assistant.io/integrations/squeezebox/)
- [esp32-squeezelite](https://github.com/sle118/squeezelite-esp32) and [piCorePlayer](https://docs.picoreplayer.org/getting-started/) clients

# Devices:

## Server

Music library and server-side applications are on a debian server running inside a Proxmox hypervisor

## Kitchen (total cost = £20)

Music is playing on a [Roberts Radio](https://www.robertsradio.com/en-gb/retro) via an a1s model esp32-audio-kit (£16) board which is running [esp32-squeezelite](https://ale.cx/ALEX/2021/01/esp32-audio-kit-with-squeezelite-esp32/) which provides digital to analog conversion and output to the Roberts radio using a 3.5mm stereo plug and gets power from the Roberts radio via the hidden USB port inside. This device supports direct streaming via airplay, bluetooth or as an LMS client. 

Roberts now sells units which support bluetooth streaming, but this is fairly limited functionality and unnecessarily expensive. Rather than getting a new unit, I "upgraded" this old radio and it works great!

### Installation process:

h/t to [Damian Darfdas for writing up a howto](https://screenzone.eu/esp32-squeezelite-on-esp32-audio-kit/) that I followed for this process.

Possible prerequisite steps:
1. Install [python3](https://www.python.org/downloads/)
2. Install esptool
3. Erase the flash memory on your device: `esptool.py erase_flash` with a h/t for tip here: (https://esp32.com/viewtopic.php?t=11439).

*Note: the manufacturer seems to shift hardware on these boards without notice, so you may need to experiment with flashing different images. I had to switch from 32 to 16 bit image after much frustration. You can find details of my initial failure here: [https://www.reddit.com/r/esp32/comments/wh0ash/esp32audiokit/].*

Now for the main event
1. Download binary for ESP32-audio-kit device. I used 
2.[`I2S-4MFlash.16.1023.master-cmak`](https://github.com/sle118/squeezelite-esp32/releases/tag/I2S-4MFlash.16.1023.master-cmake)
2. Flash your esp32 device with the Squeezelite binary. You can use a command line tool like [esptool](https://github.com/espressif/esptool/releases) or a GUI tool like [esphome-flasher](https://github.com/esphome/esphome-flasher/releases) or [https://raspiaudio.github.io/] (selecting 'i2s' as the product).


## Dining Room (total cost = £110)

Music plays on a [Raspberry Pi](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/) which cost £36 and an [IQAudio DigiAmp+ board](https://www.raspberrypi.com/products/iqaudio-digiamp-plus/) which cost £30. This device runs [piCorePlayer](https://docs.picoreplayer.org/getting-started/) which runs as an LMS client. The device also has a rotary encoder (KY-040), 128X64 Pixel OLED display (SSD1306) and an IR receiver which I use with an old apple remote. This is a pretty nice little Class D amplifier, running full-HD 192kHz / 24bit audio at 2x35w, which powers a set of Wharfedale 9.1 speakers I picked up used for £50

- I've tried running this device using [Volumio 3](https://volumio.com/en/volumio-3) and [Mopidy](https://mopidy.com/) (in June-July 2022). I found both of these platforms to be overly complex and unstable as music players so eventually settled on LMS friendly piCorePlayer.

## Living Room (total cost = £25.50)

For our front room, I've built a DIY sonos-style player. This uses a 35W TDA8932 BTL Mono Amplifier Board (this cost £5) which powers a single Cambridge Audio S20 speaker which I got used for £10 running in mono (for obvious reasons). 

We stream audio to this amplifier using a simple ESP32 WROVER Development Board (an Lilygo TTGO T8 v1.8 board) running esp32-squeezelite client software which connects to the amp using a £4 PCM5102 DAC. The reason I've gone for separate DAC/Amp boards in this setup is because there are very few amplifier boards with a build-in DAC chip, and this really limits your overall options. I'm aware that there are many projects out there using a board like the 3W MAX98357 ([like here](https://circuitdigest.com/microcontroller-projects/esp32-based-internet-radio-using-max98357a-i2s-amplifier-board) and here), but at this point I think going with separate boards is the best approach. I've drawn on the terrific [SqueezeAMP project](https://github.com/philippe44/SqueezeAMP) for this design.

For this build, I found @schreibfaul1's [repository quite helpful](https://github.com/schreibfaul1/ESP32-audioI2S/wiki). There are a fair few youtube videos out there detailing the build as well. 

Wiring is relatively simple:

Connect the following pins between the TTGO T8 ESP32 board and your I2S DAC:

| ESP pin |	PCM 5102 I2S signal |
| ------------- | ------------- |
| GPIO25 | LRCK |
| GPIO25 | DATA IN (DIN) |
| GPIO26 | BCK |

![Schematic for wiring](https://github.com/schreibfaul1/ESP32-audioI2S/raw/master/additional_info/ESP32_I2S_PCM5102A_ONLY.JPG)
