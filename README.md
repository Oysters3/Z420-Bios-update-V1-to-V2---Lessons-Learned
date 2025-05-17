Issues and lessons learned from completing a V1 to V2 upgrade.

There is alot of good and bad information out there. Some of which is not written clearly on what and why a bios upgrade will work or fail. 
Here is my own account and findings, some of which are basics I wish I had figured out before bricking my server, with trial and error however was able to re-instate it, fully working and upgraded.

Alot of great work and information is already available which helped me immensely. Highly recommend reading through he below. Shout out to these two in particular:

[https://github.com/SuperThunder/HP_Z420_Z620_Z820_BootBlock_Upgrade](https://github.com/SuperThunder/HP_Z420_Z620_Z820_BootBlock_Upgrade) [https://github.com/bibikalka1/HP_Z420_Z620_Z820_BOOTBLOCK2013_BIOS_mod](https://github.com/bibikalka1/HP_Z420_Z620_Z820_BOOTBLOCK2013_BIOS_mod)

I was new to this, so apologies to veterans for stating the obvious (and probably basic knowledge I did not have) this may help the next person. 

### Things to Consider:
- The BIOS (.bin file) for the z420 that is stored on the chip is made up of not just the bios but also Boot Block and ME (Management Engine). It also stores MAC address, serial number and other information for that particular motherboard. 

- Upgrading Bios can be done in several ways (also dependent on what OS you have). Plenty of information out there on how to do this. 
	- ME firmware should be upgraded **before** BIOS updates to avoid compatibility issues
	- Tools like HP's `MEBLAST` utility or modded `fpt` (Flash Programming Tool) are used for ME/Boot Block updates.

- Another option (I used and started this whole rabbit hole) is to download a bios file from HP support site, extract it, copy the .bin file onto a small prepared usb drive and use the flashing utility in the servers bios. 

- Information on the web also states you need to step to bios 1.23 before going to a later one, such as 3.96. **HOWEVER**: updating this way to 1.23 will work, further updating this way to 3.96 will brick your machine, it will not post, fans will ramp up and you will get 5 or more beeps on a cycle. I was unable to use any of the methods regarding crisis recovery to work. 
- Some people have had issues it seems finding/downloading bios 1.23. That and 3.96 are attached in this repository. Your welcome. 

- **WHY**? after much research discovered the ME version in earlier bios is 7, and will work up to about bios 2.5ish. Bios 3.xx needs ME version 8, which is not updated when flashing a bios with a bin file in the bios utility. If updating bios this way you need to update ME to 8 first.

- I was able to confirmed this once I downloaded the bios (3.96 I had just flashed) directly from the chip and inspected with a hex editor. See SuperThunders walkthrough on how to do this. 

- This single point was not written plainly in any of the documentation I read before completing the update. 

- If using a USB to flash via bios utility, it is recommended to use an older/smaller stick, partition to 512Mb and Fat32 and have nothing else on it. 

- If using a raspberry Pi to read/write to Bios chip:
	- minimise connections through a breadboard. Get M-F wires and connect SOIC cable directly to Pi pins. I only used a breadboard for some of the power. Less connections that wiggle around the better especially with data transfer. 
	- During read/writes I lost connection to the chip, it was not moved or nudged. The clips are finicky. 
	- Good power supply for the Pi, minimum 2.5A @ 5V. mine failed to write and verify using a 2.5A power source, changed to 3A and it worked. 
	- There is some draw/absorption through the motherboard, hence needing a slightly bigger supply I think, other option is to de-solder chip or one of its legs...

#### Boot Block
- The Boot Block is a minimal BIOS recovery subsystem stored in a protected region of the firmware chip.
-  Activates if the main BIOS is corrupted, allowing reflashing via USB or network
- Original systems (V1 boards) used **2011 Boot Block**, but modded BIOS files enable **2013 Boot Block** (enables use of V2 chips) for enhanced compatibility with newer hardware.
- Upgrading requires triggering "Manufacturing Mode" via jumper settings (e.g., "E14 BB") to unlock write access to the BIOS region **OR** manually copying sections of the bios over in a hex editor and then reflashing the chip (if you have bricked it, is about your only option). It is easier than it sounds. 

#### Management Engine (ME)
- The ME is a dedicated co-processor embedded in Intel chipsets that operates independently from the main CPU. 
- In Z420 systems it manages power states, hardware monitoring, and remote management features like Active Management Technology (AMT)
-  Early Z420 BIOS versions used **ME 7.1.xx.xx while later updates introduced **ME 8.1.xx.xx.
- ME firmware must match the BIOS version; mismatches can cause errors like "Cannot locate ME device driver" during updates. 
- For instance as explained above, flashing a 3.96 bios via usb and still have the original boot block makes for an unhappy machine. 
- Corrupted ME firmware (e.g., version 0.0.0.0) can disable hardware features and require specialized recovery tools like `fptw -rewrite -me` or physical SPI reprogramming

### Utilities Used
Hex editor HxD. 
UEFITool.
Rasperry Pi with Pi OS and FlashRom installed + SOIC clip and wires as described in walk though linked above. 
