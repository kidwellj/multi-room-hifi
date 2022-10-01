# Howto Multi-room HiFi

Following are (work in progress!) build details for my DIY multi-room hifi system. I've tried a fair number of possible builds and settled on the following:

# Software:
- Logitech Media Server [via docker](https://hub.docker.com/r/lmscommunity/logitechmediaserver)
- [Jellyfin](https://jellyfin.org/docs/general/server/media/music.html) media server / with [Finamp](https://github.com/jmshrv/finamp) iPhone client - Note: my original music server for mobile devices was airsonic. Based on UX issues and inefficient memory use, I swapped to the much prettier [Navidrome](https://www.navidrome.org/), however navidrome lacks multiple libraries as a feature. Meanwhile, music support on jellyfin has improved dramatically, and now with a few good mobile clients in beta, I was happy to drop this all in favour of jellyfin.
- [Audiobookshelf](https://github.com/advplyr/audiobookshelf) for podcasts and audiobooks
- Home Assistant using the [Squeezebox integration](https://www.home-assistant.io/integrations/squeezebox/)
- [esp32-squeezelite](https://github.com/sle118/squeezelite-esp32) and [piCorePlayer](https://docs.picoreplayer.org/getting-started/) clients

To get a sense of the broader network and hardware setup I'm running in the house, you can read about it [here](https://github.com/kidwellj/victorian-smarthome).

It's worth noting that this isn't my final server configuration. I'm very happy with the improvements offered by audiobookshelf and finamp, but I do dream of switching to Roon when I can afford to purchase the software. I'm aware that many other people use Plex, but I think Jellyfin, which is properly open source, now has feature parity on the things that are important to me.

# Devices:

## Server

Music library and server-side applications are on a debian server running inside a Proxmox hypervisor

## Kitchen (total cost = £20)

Music is playing on a [Roberts Radio](https://www.robertsradio.com/en-gb/retro) via an a1s model [esp32-audio-kit](https://amzn.to/3fB4JsA) (£16 via aliexpress, £25-30 on amazon/ebay UK) board which is running [esp32-squeezelite](https://ale.cx/ALEX/2021/01/esp32-audio-kit-with-squeezelite-esp32/) which provides digital to analog conversion and output to the Roberts radio using a 3.5mm stereo plug and gets power from the Roberts radio via the hidden USB port inside. This device supports direct streaming via airplay, bluetooth or as an LMS client. 

Roberts now sells units which support bluetooth streaming, but this is fairly limited functionality and unnecessarily expensive. Rather than getting a new unit, I "upgraded" this old radio and it works great! If you're interested, you can easily get an old Roberts radio on ebay for £20-30.

### Installation process:

h/t to [Damian Darfdas for writing up a howto](https://screenzone.eu/esp32-squeezelite-on-esp32-audio-kit/) that I followed for this process.

Possible prerequisite steps (for Mac laptop, in my case):
1. Install [python3](https://www.python.org/downloads/)
2. Install esptool
3. Erase the flash memory on your device: `esptool.py erase_flash` with a h/t for tip here: (https://esp32.com/viewtopic.php?t=11439).

*Note: the manufacturer seems to shift hardware on these boards without notice, so you may need to experiment with flashing different images. I had to switch from 32 to 16 bit image after much frustration. You can find details of my initial failure here: [https://www.reddit.com/r/esp32/comments/wh0ash/esp32audiokit/].*

Now for the main event
1. Download binary for ESP32-audio-kit device. I used 
2.[`I2S-4MFlash.16.1023.master-cmak`](https://github.com/sle118/squeezelite-esp32/releases/tag/I2S-4MFlash.16.1023.master-cmake)
2. Flash your esp32 device with the Squeezelite binary. You can use a command line tool like [esptool](https://github.com/espressif/esptool/releases) or a GUI tool like [esphome-flasher](https://github.com/esphome/esphome-flasher/releases) or [https://raspiaudio.github.io/] (selecting 'i2s' as the product).


## Dining Room (total cost = £110)

Music plays on a [Raspberry Pi](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/) which cost £36 and an [IQAudio DigiAmp+ board](https://www.raspberrypi.com/products/iqaudio-digiamp-plus/) which cost £30. This device runs [piCorePlayer](https://docs.picoreplayer.org/getting-started/) which runs as an LMS client. The device also has a rotary encoder (KY-040), 128X64 Pixel OLED display (SSD1306) and an IR receiver (TSOP38238) which I use with an old apple remote. This is a pretty nice little Class D amplifier, running full-HD 192kHz / 24bit audio at 2x35w, which powers a set of Wharfedale 9.1 speakers I picked up used for £50.

- You can find a useful pinout reference for this board [here](https://pinout.xyz/pinout/pi_digiamp#).
- You can find instructions on adding in an IR receiver via [piCorePlayer documentation](https://docs.picoreplayer.org/projects/add-an-ir-receiver/) and also a useful blog post [here](https://smarthome.exposed/infrared-remote-for-hifiberry/). If you're using an apple remote like I am, you'll need to upload the custom `lircd.conf-A1156` file which I've included in this repository.
- Note: I've tried running this device using [Volumio 3](https://volumio.com/en/volumio-3) and [Mopidy](https://mopidy.com/) (in June-July 2022). I found both of these platforms to be overly complex and unstable as music players so eventually settled on LMS friendly piCorePlayer. I have never experienced any performance problems with this platform, which streams FLAC files over wifi without any difficulties.

## Living Room (total cost = £26.50 + case)

For our front room, I've built a DIY sonos-style player which streams via wifi to a single Cambridge Audio S20 speaker which I got used for £10 running in mono (for obvious reasons). 

I've gone with a Raspberry Pi Zero 2W board (£15) paired with the same IQAudio DigiAmp+ board I used in the Dining Room above. Flashed with piCorePlayer, it JustWorks(TM). While this board takes quite a lot longer to boot than my Pi4B, closer to 30 seconds, once it's up and running performance is seamless. I've pushed full FLAC streaming with no delays, jitter, or gaps in playback.

- You can find a useful pinout reference for this board [here](https://pinout.xyz/pinout/pi_digiamp#).
- 
Note: I'm planning to get a set of Wharfedale Diamond 10 speakers later next year and will power this system using a used Denon AVR-2312 (these run £50-150) which is compatible with Home Asssitant and Logitech Media Server as an endpoint for audio streaming. When this upgrade is complete, I'll be moving this little streamer to the library.

*In case you're interested, here's the details of my failed first try:*

my original goal was to try my hand at a DIY DAC/Amp configuration using a 35W TDA8932 BTL Mono Amplifier Board (this cost £5 via aliexpress) and a £4 (also aliexpress) PCM5102 DAC. The reason I went for separate DAC/Amp boards in this setup was because there are very few amplifier boards with a build-in DAC chip, and this really limits your overall options. Esp32-squeezelite software requires an ESP32 WROVER Development Board, as it needs at least 4MB PRAM to run and WROOM boards aren't enough. In my case, I went with a Lilygo TTGO T8 v1.8 board.  I'm aware that there are many projects out there using a board like the 3W MAX98357 ([like here](https://circuitdigest.com/microcontroller-projects/esp32-based-internet-radio-using-max98357a-i2s-amplifier-board)), but I still think that going with separate boards is the best approach. I've drawn on the terrific [SqueezeAMP project](https://github.com/philippe44/SqueezeAMP) for some elements of my design. For this build, I found @schreibfaul1's [repository quite helpful](https://github.com/schreibfaul1/ESP32-audioI2S/wiki). There are a fair few youtube videos out there detailing the build as well. However, try as I might, I couldn't get squeezelite-esp32 to flash on the board. I tried a second esp32 WROVER board as well to no avail. I gather that there are a number of different components to the bootloader so flashing needs to be done in steps sometimes, but had to hit pause on this attempt as I was getting nowhere. Details below in case anyone else wants to try and succeed where I failed to get it running!

### Wiring the device

Connect the following pins between the TTGO T8 ESP32 board and your I2S DAC:

| ESP pin |	PCM5102 I2S signal |
| ------------- | ------------- |
| GPIO25 | LCK |
| GPIO26 | DATA IN (DIN) |
| GPIO27 | BCK |
| GND | SCK, GND |
| 5V | VIN |

![Schematic for wiring](https://github.com/schreibfaul1/ESP32-audioI2S/raw/master/additional_info/ESP32_I2S_PCM5102A_ONLY.JPG)

Next wire up the amplifier (TDA8932). This is a bit simpler:

1. The power pins can go to a DC converter. I re-used one from an old laptop. Optimum is 24V at 2amp. See note below re: DC power.
2. I wired input via (just) two short wires to the PCM5102 board which has spaces for wired out labelled "G R G L" (or sometimes just R G L with a shared Neutral wire). Note I'm not using the 3.5mm adapter here because I need mono for this amplifier board, this also means that I've skipped the G/R connection points. *
3. Then I soldered on two wires which connect to speaker terminals from the amplifier audio out points.

Note: I've drawn a lot of this information from a really helpful discussion amidst experts on [diyaudio forums](https://www.diyaudio.com/community/threads/should-i-use-a-laptop-ac-adapter-as-psu-for-my-first-diy-class-d.389171/#post-7094448).*

### Adding DC Power for the amplifier

Sourcing, hacking and making power supplies (PSU) for audio devices is a whole art unto itself. I usually keep a box full of discarded AC adapters from old electronics around the house. This is sifted by voltage, there are low voltage 3.3 or 5V adapters for cell phones and the like with similarly low amperage/wattage ratings. Then there are the bigger "bricks" taken from laptop power adapters, which range up to 90V or even 110V in some cases. There is a whole community around repurposing [old computer power supplies](https://www.instructables.com/Encyclopedia-of-ATX-to-Bench-Power-Supply-Conversi/) as well. This amplifier is small, but it does require some power, so I went with the laptop power brick. This has more power than the amp needs, with the Lenovo PSU I found supplying 90W at 20V or about 4.5A. I only need 2A at 20V, so was wondering if I could bridge over another power wire to USB micro for my ESP32 and save myself a cord. This isn't current sharing, strictly speaking, because I want two different voltages: 3.3V for the ESP32 and as close to 24V as I can get for the amp. So I've aquired a
couple DC-DC converters to connect to this PSU (via a barrel adapter). There are two ways to wire up this setup:

(1) Simply wire your 20-24V laptop PSU straight to the amplifier AND run a separate power lead via a USB charger to the ESP32

(2) Split the power coming in from your PSU to both. 

This second option is more elegant, but also a lot more complicated. You need to reduce the voltage for your ESP32 carefully so you don't fry it. I haven't run this solution yet, but you can view the conversation on DIY Audio if you want to get a sense of how it will probably go.



### Software Installation:

Flash your ESP32 using the same process detailed above, and you're all set!

# Notes:

## Flashing ESP32

It's worth noting that while flashing ESP32 devices can be really easy, sometimes it can be quite tricky. The ESP32 actually has its storage divided into several partitions and goes through a series of boot loading, first a simple partition and then subsequent boot processes are loaded. Some software flashing only replaces later Boot process stages. It's also the case that certain builds of the ESP32 can be more difficult to get into a ROM flashing mode. Usually if your ESP32 has a reset and a boot button (wired to pins GPIO0 and GPIO2) then you can hold these down to enter a rom flashing state. But some devices (like the lilygo T8 board I use above) only has a boot button, so you will need to manually bridge the pins with a resistor to "pull low" at 10k ohms or less which will also trigger a rom flashing state.

It is also a bit annoying to find the serial device for USB on MacOS. For reference, the command `ls -lha /dev/tty*|grep modem` will give you the device ID once it is plugged in, and you can use this with esptool.py.

# Future plans:

- Buy and deploy a Roon server
- Deploy [HQPlayer](https://audiobacon.net/2021/03/17/hqplayer-better-than-a-5000-upscaler/)
- Get a used Denon receiver with Ethernet connectivity to swap in on the Living Room

# Resources:

As I've been going along, I've found some pretty helpful resources on the internet. So if you're also planning on diving into DIY Audio, here are the sources I'd recommend:

- The DIYAudio Forum
- [Slimdevices Forum](https://forums.slimdevices.com/showthread.php?112697-ANNOUNCE-Squeezelite-ESP32-(dedicated-thread)) (for Logitech Squeezebox originally, but now with lots of DIY and home to Squeezelite-ESP32 developers)
- Github - run a search for your part names and discover previous builds like this one!
- [Hackaday](https://hackaday.io) - same as above. Lots of interesting builds documented on here
- [AVForums](https://www.avforums.com) - not as much hacking on here as DIYAudio, but still lots of interesting notes about components and quality
- [AudioScience Review Forums](https://www.audiosciencereview.com) - seriously exacting standards in terms of audio quality, good discussion on the underlying factors in device performance
- [DIY Budget Audio blog](https://diybudgetaudio.com)
- [R/Selfhosted reddit feed](https://www.reddit.com/r/selfhosted) - discussion of software and home servers
- [Raspiaudio Forums](https://forum.raspiaudio.com/) a bit like the slimdevices forum, but more folks coming from RPi and ESP to LMS.