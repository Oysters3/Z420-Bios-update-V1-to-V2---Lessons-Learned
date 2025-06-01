#Issues and lessons learned from completing a V1 to V2 upgrade.

### TLDR
Upgrading a V1 board to V2 using a usb drive and the bios "Flash System ROM" utilities will brick you machine, even if you step from an early version to 1.23 to 3.96.

This is becasue it DOES NOT update the ME (Management Engine) section of the bios.

Update from early version such as 1.03 to 1.23 is fine. Then update ME FIRST before updating to later version such as 3.96.

Ram: V1 board upgraded to V2, with a V1 processor wont boot with 1866Mhz ram. I had to change back to 1600Mhz sticks. A V2 chip such as E5-2697 will boot with both. 


### Learnings

There is alot of good and bad information out there. Some of which is not written clearly on what and why a bios upgrade will work or fail. 
Here is my own account and findings, some of which are basics I wish I had figured out before bricking my server, with trial and error however was able to re-instate it, fully working and upgraded to V2.

Alot of great work and information is already available which helped me immensely. Highly recommend reading through he below. Shout out to these two in particular:

[https://github.com/SuperThunder/HP_Z420_Z620_Z820_BootBlock_Upgrade](https://github.com/SuperThunder/HP_Z420_Z620_Z820_BootBlock_Upgrade) 

[https://github.com/bibikalka1/HP_Z420_Z620_Z820_BOOTBLOCK2013_BIOS_mod](https://github.com/bibikalka1/HP_Z420_Z620_Z820_BOOTBLOCK2013_BIOS_mod)

I was new to this, so apologies to veterans for stating the obvious (and probably basic knowledge I did not have) this may help the next person. 

### Things to Consider:
- The BIOS (.bin file) for the z420 that is stored on the chip on the motherboard and is made up of not just the bios but also Boot Block and ME (Management Engine) code. It also stores MAC address, serial number and other information for that particular motherboard. 

- Upgrading Bios can be done in several ways (also dependent on what OS you have). Plenty of information out there on how to do this. 
	- ME firmware should be upgraded **before** BIOS updates to avoid compatibility issues
	- Tools like HP's `MEBLAST` utility or modded `fpt` (Flash Programming Tool) are used for ME/Boot Block updates.

- Another option (I used and started this whole rabbit hole) is to download a bios file from HP support site, extract it, copy the .bin file onto a small prepared usb drive and use the flashing utility in the servers bios. 

- Information on the web also states you need to step to bios 1.23 before going to a later one, such as 3.96. **HOWEVER**: updating this way to 1.23 will work, further updating this way to 3.96 will brick your machine, it will not post, fans will ramp up and you will get 5 or more beeps on a cycle. I was unable to use any of the methods regarding crisis recovery to work. 
- Some people have had issues it seems finding/downloading bios 1.23. That and 3.96 are attached in this repository. Your welcome. 

- **WHY**? after much research discovered I had misunderstood the fact that the ME version in earlier bios is 7, and will work up to about bios 2.07. Bios 3.xx needs ME version 8, which is NOT updated when flashing a bios with a bin file in the bios utility. If updating bios this way you need to update ME to 8 first after changing to 1.23.

- I was able to confirmed this once I downloaded the bios (3.96 I had just flashed) directly from the chip and inspected with a hex editor. See SuperThunders walkthrough on how to do this. 

- This single point was not written plainly in any of the documentation I read before completing the update. 

- If using a USB to flash via bios utility, it is recommended to use an older/smaller stick, partition to 512Mb and Fat32 and have nothing else on it. 

- If using a raspberry Pi to read/write to Bios chip:
	- minimise connections through a breadboard. Get Male-Female DuPont wires and connect SOIC cable directly to Raspberry Pi pins. I only used a breadboard for some of the power. Less connections that wiggle around the better especially with data transfer. 
	- During read/writes I lost connection to the chip, it was not moved or nudged. The clips are finicky. 
	- Good power supply for the Pi, minimum 2.5A @ 5V. mine failed to write and verify using a 2.5A power source, changed to 3A and it worked. 
	- There is some draw/absorption through the motherboard, hence needing a slightly bigger supply I think, other option is to de-solder chip or one of its legs...

#### Boot Block
- The Boot Block is a minimal BIOS recovery subsystem stored in a protected region of the firmware chip.
-  Activates if the main BIOS is corrupted, allowing reflashing via USB or network
- Original systems (V1 boards) used **2011 Boot Block**, but modded BIOS files enable **2013 Boot Block** (enables use of V2 chips) for enhanced compatibility with newer hardware.

- Upgrading requires triggering "Manufacturing Mode" via jumper settings (e.g., "E14 BB") to unlock write access to the BIOS region, and using specialized utilities (such as Intel’s FPT tool or HP’s MEBLAST) under DOS, not just the standard BIOS update utility.

**OR** manually copying sections of the bios over in a hex editor and then reflashing the chip (if you have bricked it, is about your only option). It is easier than it sounds. 

#### Management Engine (ME)
- The ME is a dedicated co-processor embedded in Intel chipsets that operates independently from the main CPU. 
- In Z420 systems it manages power states, hardware monitoring, and remote management features like Active Management Technology (AMT)
-  Early Z420 BIOS versions used **ME 7.1.xx.xx while later updates introduced **ME 8.1.xx.xx.
- ME firmware must match the BIOS version; mismatches can cause errors like "Cannot locate ME device driver" during updates, or failure to boot and giving 5 or more beeps before fans ramp up.
- For instance as explained above, flashing a 3.96 bios via usb and still have the original boot block makes for an unhappy machine. 
- Corrupted ME firmware (e.g., version 0.0.0.0) can disable hardware features and require specialized recovery tools like `fptw -rewrite -me` or physical SPI reprogramming

### Bios ME Boot block Reference

| BIOS Version         | Boot Block Required | ME Firmware Supported | Notes                                                                                   |
|----------------------|--------------------|----------------------|-----------------------------------------------------------------------------------------|
| v2.07 and below      | 2011               | ME7                  | Last BIOS versions fully compatible with ME7.                                           |
| v3.15 – v3.51        | 2011 or 2013       | ME7 (if not yet upgraded) / ME8 (if upgraded) | Transitional versions; some may support ME8, but ME7 still works if not yet upgraded.   |
| v3.60 and above      | 2013               | ME8                  | Requires ME8. Updating to these BIOS versions before upgrading ME to ME8 can brick system.|
| v3.91, v3.96, etc.   | 2013               | ME8                  | Only supports ME8. Must upgrade ME to ME8 before flashing these BIOS versions.          |

### RAM
The Original V1 board was upgraded to use 128Gb of 18866 Mhz ECC ram.
After upgrading to bios 3.96 and ME8, it would not boot with this ram. After changing back to original 1600Mhz ECC ram it would boot. 
From there after several checks, the V2 processor was installed and found it would boot with either. 


### Other information to be found
Serial number can be found in the UEFI tool or HxD if you didnt note it down before bricking your machine. Or if you cant recover a good bios and want to start with another from somewhere else. 
- setup and read the chip to get what is currently on the bios chip.
- Open UEFI tool and the downloaded .bin file
- opened hex editor for the padding section under BIOS region, the serial number is against 1DE0 line (offset `0x1DE0`), it is there, just need to look for it.
  
![image](https://github.com/user-attachments/assets/2621d22b-d56a-4754-a8ab-aac4b514cecf)

![image](https://github.com/user-attachments/assets/3d4a93db-41f4-4f4c-a5d7-c44625e98eb1)

- MAC address is in similar spot (right above) in the Bios padding at line 1DD0
  
![image](https://github.com/user-attachments/assets/9bdad568-34f2-428c-a768-ea5af49ba26f)

Or if needing to change them you can open HxD, search and change the relevant bits if needed. 
- Serial number is at line 00511DF0
  
![image](https://github.com/user-attachments/assets/56d90fe7-3627-4b9b-987c-eedcf4a212f3)

- MAC is at line 00511DDE0
  
![image](https://github.com/user-attachments/assets/09d362a7-c28f-4c07-a585-159f76686b9e)

See "Bios update with HxD" for more info. 

### Utilities Used
Hex editor HxD. 
UEFITool.
Rasperry Pi with Pi OS and FlashRom installed + SOIC clip and DuPont wires as described in walk though linked above by SuperThunder. 


