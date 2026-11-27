**LOGIC VOLTAGE IS 1.8V for V2 PCB's USE A LEVEL SHIFTER OR AN STLINK THAT SUPPORTS 1.8V.** SWD pins are under the display. To flash over SWD use a .hex with STM32CubeProgrammer. 

**AI usage** Declaration that OPUS 5.5 and Fable 5.2 was used to write the firmware.
**DAC** I previously got the DAC working in a previous fork of the firmware, V2.7.1 doesnt have a working USB DAC in my testing. 

**Quick settings** - This is accessed when playing a song by either pressing both skip and reverse at the same time or holding the play button (see controls below). Here you can set a *sleep timer*, loop the current song and see what is up next to skip forwards to play it. 

**Display** - The GUI is designed specifically around the capabilities of an EINK display, the progress bar and battery icon only redraw the specific pixel that changes from one state to another. The elapsed-time text updates on those same ticks instead of every second but is intentionally removed because of how it ghosts. When playing songs the display does a full refresh to prioritise album art quality.
The work done by inkboy (link above) has really with the usability of the eink display. There are a few settings to mess with to suit the way you would use the device. (display settings explaination)*Highlight* sets how the selected row in a list is marked. *Dark mode* (underwhelming in my opinion), three *text sizes* and *album art* toggle are self explanitory. *Display drive* (Fast/ Classic) Fast is inkboys custom waveform, are faster but may be more unstable, classic is the fallback factory waveform. *Full refresh* (Often/normal/rarely) depends how tollerant you are to ghosting. *Ink strength* (1-5) strength of a refresh, 5 being the strongest and most bold. *Edge clean-up*, it can clean up the halo or smear at the edges of changed areas. 

**Library and playback** - Music, Artists and Albums views are built from the file metadata in the background. There's a 10-band *EQ* (±12 dB, presets plus custom), *gapless playback* (between songs), *Repeat* (in quick settings), *Headphone detect* use this setting if the device is having trouble with detecting headphones, though it will play music with headphones unplugged and *Podcast speed*, also accessable through quick settings. 

**Sleep timer** - Found in quick settings. *Fade out* how long the volume ramps down before stoping playback. *Finish the song* pauses after the track finishes.

**Controls** - *Quick menu* how to bring up quick menu when playing a song or podcast. *Double-press back* make a double press of the play/pause key go back, now the only way to go back is a long press. Make sure that *Quick menu* and *Double-press back* settings dont conflict. *Keys act on* decides whether buttons act when pressed or released. *Long press* determines the point at which a press is a long press.

**Power & USB**
*Sleep after* is the idle time before the device sleeps. *USB* sets the behaviour when USB is plugged in. *Charge rate* leave default unless you change the battery to somthing that can tollerate the faster charging. *Force charge* allows charging when there is no thermistor. *charger* shows the current charging state. *Battery %* enables the fuel gauge for an accurate state of charge at the expense of more power usage.

**System**
*Rebuild library* rescans SD card and recompiles metadata. *Firmware* shows the current firmware version and allows you to update the firmware through the SD card. To do this put a .bin firmware file in the firmware folder of the SD card, and in settings click install. The new image is written to the other flash bank, verified, then booted. If it restarts three times in its first 20 seconds, the player rolls back to the previous version automatically. *About* shows the device state and useful for troubleshooting.
With a blank STM32U575 the bootloader is not able to update through USB so the very first install still has to go over SWD, and subsiquent versions go through a file on the SD card. 




