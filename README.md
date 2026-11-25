# PaperMP3
Low-power e-ink (ipod like) MP3 player with an STM32U575, CS43131 hi-fi DAC, swappable battery, SD card and 35 hours of battery life. This project has also been uploaded to JLCspark2026 and still waiting for approval. https://oshwlab.com/p19lgod/project_dcujfckz (dead link)
# Project introduction
A compact, low-power portable E-INK music player built around a "hi-fi" DAC. It is designed for long battery life (aprox 35 hours of playtime), clean audio and an ultra low power display that stays readable. It runs on a user-replaceable camera battery, so a spare battery can be swapped in and a worn one replaced without tools. 

MCU -	STM32U575RGT6 (Cortex-M33), running at 1.8 V
Audio - CS43131 DAC + Class-H headphone amp, 3.5 mm jack
Display - 1.54" 200x200 e-paper
Power - nPM1300 PMIC (charger, fuel gauge, two bucks) + TPS7A20 low-noise LDO
Storage - Tool-free microSD
Battery - Sony NP-BJ1 "J type", 700 mAh
USB-C - Charging, mass storage, or USB DAC
Runtime - ~35 h estimated (see below)

# Project function
**Audio.** The STM32U575 feeds the CS43131 over I2S. The only crystal (that will be replaced with an oscilator) in the system is for the DAC and is really annoying to source (22.5792 MHz), but it allows the audio clock to be set by the DAC so there is no MCU PLL jitter on the signal path. Both the 44.1 kHz and 48 kHz families are generated natively, so there's no resampling for most files. (macro shots make my soldering look terrible)
![stm32.jpg](https://image.easyeda.com/oshwhub/pullImage/396b406154bb4bfd9db1fc60ce3c397c.jpg)

**Display.** The e-ink screen display firmware is forked from inkboy (https://github.com/kuslota/InkBoy). It shows album art pulled from the embedded metadata of your song files (JPEG art, dithered down to 200x200 black and white). I got this display from alixpress (Goodisplay). Song content looks like this.
![24_now_playing_no_headphones.png](https://image.easyeda.com/oshwhub/pullImage/a744e596374e4423a876894e3fb59082.png)

**Controls.** Five buttons: skip, back, play/pause, Vol+ and Vol-. Double-tapping play/pause acts as "back" (I couldent find a nice place to put a seperate back button). A long press on play/pause resumes whatever you were last playing.

**Storage and settings.** Music, playlists and settings all live on the microSD card, under the battery. Settings can be changed in the on-device menu or by editing a plain settings.txt, so updating firmware or moving to a new device means songs, preferences and playlists stay.
![render_11_settings_page1.png](https://image.easyeda.com/oshwhub/pullImage/aed83f8118874fe6b0e0b690de6162e7.png)
**Battery.** The 700 mAh NP-BJ1 should give an estimated 35 hours of playback. The figure is Will be tested soon, with the dac being the majority of the system power draw. 
![Sony J type battery.jpg](https://image.easyeda.com/oshwhub/pullImage/fcb1e8d5b3054fc581736d4f08ce5950.jpg)
**USB-C.** Plug in and the screen asks what you want: storage, USB DAC (the player becomes an external DAC), or charge only. If nothing enumerates within a few seconds, it assumes a wall charger and carries on charging.
![render_19_usb_prompt.png](https://image.easyeda.com/oshwhub/pullImage/63178cb9e0df4c2eaa75c1a33af549ab.png)

# Hardware

Power goes through the nPM1300 (PMIC) from nordic, and it does a lot of the heavy lifting, making my life "easy": charging, fuel gauge, both bucks and the USB charging. There is however a seperate low noise LDO for the CS43131 (AMP) from cirrius. This IC enables all the high quality audio features in the device.
Controlling this all is the STM32U575 (MCU) running at 1.8V for the AMP. The logic voltage forces me to use level shifters TXB0106 for the display, TXS02612 for the SD card. They work, but they cost board space and add to the BOM, and finding a way to remove them is one of the goals for the next revision.
The case has G3 curves. Its a pain to model but feels (in my optionion) so much nicer. 
The battery and MicroSD card can be accessed (tooless) with a push corner to remove the back panel held in by magnets.
The volume buttons are stolen from an Iphone 4 (Iphone 5's do work, see assembly instructions). 

# Software - More details in Software.md
**Code written with AI (Opus 5.5 and Fable 5.2).** Im working with a friend to get human written firmware that is written indipendently (chinese wall technique), but for now for testing hardware functionality and new features, its been working really well.

**Formats** - Built to play DSD (DSD64 and maybe DSD128 as DoP), FLAC, WAV, Opus, MP3 and AAC/M4A, so most files should work. Only M4A and FLAC have been tested, but I'll update this post as I test more.



![dont steal music.JPG](https://image.easyeda.com/oshwhub/pullImage/e4c863eb08114d4ab05d75fe75eaa7b1.jpg)
**Quick settings** - This is accessed when playing a song by either pressing both skip and reverse at the same time or holding the play button (see controls below). Here you can set a *sleep timer*, loop the current song and see what is up next to skip forwards to play it. 
**Display** - The GUI is designed specifically around the capabilities of an EINK display, the progress bar and battery icon only redraw the specific pixel that changes from one state to another. The elapsed-time text updates on those same ticks instead of every second but is intentionally removed because of how it ghosts. When playing songs the display does a full refresh to prioritise album art quality.
The work done by inkboy (link above) has really with the usability of the eink display. There are a few settings to mess with to suit the way you would use the device. (display settings explaination)
*Highlight* sets how the selected row in a list is marked. *Dark mode* (underwhelming in my opinion), three *text sizes* and *album art* toggle are self explanitory. *Display drive* (Fast/ Classic) Fast is inkboys custom waveform, are faster but may be more unstable, classic is the fallback factory waveform. *Full refresh* (Often/normal/rarely) depends how tollerant you are to ghosting. *Ink strength* (1-5) strength of a refresh, 5 being the strongest and most bold. *Edge clean-up*, it can clean up the halo or smear at the edges of changed areas. 
**Library and playback** - Music, Artists and Albums views are built from the file metadata in the background. There's a 10-band *EQ* (±12 dB, presets plus custom), *gapless playback* (between songs), *Repeat* (in quick settings), *Headphone detect* use this setting if the device is having trouble with detecting headphones, though it will play music with headphones unplugged and *Podcast speed*, also accessable through quick settings. 
**Sleep timer** - Found in quick settings. *Fade out* how long the volume ramps down before stoping playback. *Finish the song* pauses after the track finishes.
**Controls** - *Quick menu* how to bring up quick menu when playing a song or podcast. *Double-press back* make a double press of the play/pause key go back, now the only way to go back is a long press. Make sure that *Quick menu* and *Double-press back* settings dont conflict. *Keys act on* decides whether buttons act when pressed or released. *Long press* determines the point at which a press is a long press.
**Power & USB**
*Sleep after* is the idle time before the device sleeps. *USB* sets the behaviour when USB is plugged in. *Charge rate* leave default unless you change the battery to somthing that can tollerate the faster charging. *Force charge* allows charging when there is no thermistor. *charger* shows the current charging state. *Battery %* enables the fuel gauge for an accurate state of charge at the expense of more power usage.
**System**
*Rebuild library* rescans SD card and recompiles metadata. *Firmware* shows the current firmware version and allows you to update the firmware through the SD card. To do this put a .bin firmware file in the firmware folder of the SD card, and in settings click install. The new image is written to the other flash bank, verified, then booted. If it restarts three times in its first 20 seconds, the player rolls back to the previous version automatically. *About* shows the device state and useful for troubleshooting.
With a blank STM32U575 the bootloader is not able to update through USB so the very first install still has to go over SWD, and subsiquent versions go through a file on the SD card. **LOGIC VOLTAGE IS 1.8V USE A LEVEL SHIFTER OR AN ORIGINAL STLINK.** SWD pins are under the display. To flash over SWD use a .hex with STM32CubeProgrammer. 

# Mistakes and learning outcomes
- **V1 burned the PMIC (probably).** I had the 1.8 V and 3.3 V rails connected to the mosfet output, and fried the PMIC on that board. For the second revision connected those lines to the correct place and added more decoupling capacitors.
- **The 3 ohm pull-up.** On the V0.5 board I soldered a 3 ohm resistor where a pull-up should go for the centre switch, and got away without killing the PMIC. Replacing it with the correct 47 k fixed things.
- **USB DFU never worked.** I'd planned to flash over USB but getting into the bootloader is fiddly. So SWD and the SD card firmware updates are my solution.
- **Native DSD got dropped.** V0.5 gave up the DAC's dedicated DSD pins to free up space on the board. DSD now goes out as DoP over the normal I2S link, which covers DSD64 and maybe DSD128, but looses support for DSD256.
# Assembling processes
The MP3 player has the following parts, the board must have already been flashed:
![parts layed out.JPG](https://image.easyeda.com/oshwhub/pullImage/38632730dc604d7f80f451e8d49237cb.jpg)
1. Outside shell (3.2x5mm M2 heatset inserts x4)
2. Inner subframe (5x1mm Magnets x3, 3.2x5mm M2 heatset inserts x4)
3. PCB
4. Front face buttons x3
5. Battery board
6. Volume rocker from an iphone 4
7. SD card
8. 1.54 inch eink display from GOODISPLAY 
9. 5mm M2 brass standoff x2
10. Flat thinhead screw M2 4mm x6
11. Countersunk M2 5mm x4
12. Back cover (5x1mm Magnets x7)

Start by melting all 8 heatset inserts and supergluing all 10 magnets into their respective holes. Ensure that the magnets on the inner subframe and back panel stick together.

![Midplate topside.JPG](https://image.easyeda.com/oshwhub/pullImage/6dbf0800db6f4eb0bcc206a1ecb7ea32.jpg)
![Midplate left side.JPG](https://image.easyeda.com/oshwhub/pullImage/ce5b608a92324a3889d66bb3daadd084.jpg)
![Midplate right side.JPG](https://image.easyeda.com/oshwhub/pullImage/684b72d2621048ed9c258630ccf16249.jpg)

![Shell.JPG](https://image.easyeda.com/oshwhub/pullImage/fdbd026f846f440eaf82713ab8030327.jpg)

Place the outershell face down on your workspace and put in all of the buttons. The side mounted volume rocker has a small dimple to hold the rocker in place, Iphone 5 volume rocker should work but you will need to grind out this dimple for them to fit.
![Shell with buttons.JPG](https://image.easyeda.com/oshwhub/pullImage/6a1ab9ba1e2d4107a7238ce23eb9204e.jpg)

Carefully connect your display to the PCB. 
![PCB.JPG](https://image.easyeda.com/oshwhub/pullImage/d330b44b9d2149f0b306ee499fb4b670.jpg)

While slightly angling the housing down towards the volume rocker carefully put in the PCB assembly. Be incredibly careful with the display, I would look from under and wiggle it with my finger to make sure its seated correctly. Lightly screw 2 Flat thinhead screws in the top two holes and two brass standoffs in the bottom two holes. Make sure to test all the buttons before fully torquing down the screws, the volume buttons might have some issues if they are not seated correctly, reflow them accordingly.
![Half assembled.JPG](https://image.easyeda.com/oshwhub/pullImage/9c3882cc2c744555b84386ec2b716c86.jpg)

Put the main assembly aside and pick up the inner subframe. At the bottom, slide in the battery board, it should slide in and not forced. Screw in two countersunk asthetic screws:)
![Midplate with battery fully out.JPG](https://image.easyeda.com/oshwhub/pullImage/6b28b97ac8d14e64806f26e170f77f64.jpg)

![Midplate with battery half out.JPG](https://image.easyeda.com/oshwhub/pullImage/5e156cb536ff44a9b5cb2b17368680c3.jpg)
![Midplate with battery board.JPG](https://image.easyeda.com/oshwhub/pullImage/47fe5b18161842dd8403a4f656c32d79.jpg)


Ensuring the Battery board is flush to the bottom edge of the inner subframe slide it ontop of the PCB in the main assembly. Do not force anything, ensure that its pushed in as flat as possible. If you catch any snags 1. Stop what you are doing and take out the inner subframe. 2. once its out make sure you push the volume rocker's flush to the outside wall. 3. Make sure that the microsd card slot isnt populated. 4. Check if any heatset inserts have snagged, this includes the heatset inserts under the PCB. 

Verify that the inner subframe is fully seated by checking that you can see the brass heatset inserts from the side. Start with the 2 countersunk screws on the inside*. Starting from the top of the unit (by the usb c and 3.5mm jack) going down, screw in the 4 flat thinhead screws.
![Back no batt.JPG](https://image.easyeda.com/oshwhub/pullImage/c746b8e97bc0438da2f95bfe9cd3ee84.jpg)
![assembled without top screw 2.JPG](https://image.easyeda.com/oshwhub/pullImage/4d4ef88cc79546e7a424f9a02df74e18.jpg)

Put in an SD card, the battery and backcover. Enjoy!
![Back w batt.JPG](https://image.easyeda.com/oshwhub/pullImage/d8b8ee6932714684a31df99b11911460.jpg)

# Finished product display
![Finished.JPG](https://image.easyeda.com/oshwhub/pullImage/d34f54a020f5469f91808c0146f3c1f7.jpg)

# Stuff left to do
- Longer screws (flat coutersunk M2 12mm) with more holes in the pcb that are closer to the edge.
- Finish V2.5
- Need 2Mb varient to test grayscale without loosing firmware backup. 
- This post will keep on getting updated as I finish up this project, Im curious to hear your feedback on my ramblings.
