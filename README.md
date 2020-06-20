# Jetson SuperCap (Super Capacitor) RTC Details
NVIDIA Jetson Nano &amp; Jetson Xavier NX SuperCap (Super Capacitor) RTC Instructions

This is a basic set of details for connecting a SuperCapacitor to the Jetson Nano and Jetson Xavier NX platforms for use as an RTC backup source.  Use at your own risk.  This isn't meant to be a complete tutorial and using these directions will likely void any warranties and you could blow something up if you aren't careful.  I'm not your mom so do what you want.

If you are reading this and wondering "why" then I can't help you but suffice it to say, reasons exist and I wanted to put the information out there for others.

This is meant to be used with the developer kits from NVIDIA:
- https://developer.nvidia.com/embedded/jetson-nano-developer-kit
- https://developer.nvidia.com/embedded/jetson-xavier-nx-devkit

The following SuperCapacitors from Maxwell were used:
- https://www.digikey.com/product-detail/en/maxwell-technologies-inc/BCAP0005-P300-X11/1182-1057-ND/9948923
- Datasheet for those is here: https://media.digikey.com/pdf/Data%20Sheets/Maxwell%20Tech%20PDFs/BCAP0005_P300_X11.pdf

These capacitors have a maximum voltage of 3V (3.1V surge) and are 5Farads in capacity.  Other alternative SuperCaps could be used but results are based on these and they seem more than adequate for the use case.

***
# Super Capacitor Prep
This is pretty simple.  The negative and positive sides are both marked.  
- Cut the SuperCap leads shorter (unless you want them long).
- Attach wire leads with solder.
- Apply some heatshrink.

![SuperCap Prep Annotated](CapPrepAnnotated.png)CapPrepAnnotated.png

***

# Jetson Nano
Details below apply to the NVIDIA Jetson Nano.

## Circuit Diagram:
![Jetson Nano RTC Circuit Diagram](JetsonNanoRTCCircuitDiagram.png)

## Changes:
 - The diode (D64) is not present on the Nano and can instead just be jumpered.
 - The location for the supercap on the board can be ignored and we can solder leads directly to the RTC pads.

## Before:
![Jetson Nano Before](NanoBefore.png)

## After:
![Jetson Nano After](NanoAfter.png)

***

# Jetson Xavier NX
Details below apply to the NVIDIA Jetson Xavier NX.

## Circuit Diagram:
![Jetson Xavier NX RTC Circuit Diagram](JetsonXavierNXRTCCircuitDiagram.png)

## Changes:
 - The diode (D4) is present on the Xavier NX and should be removed to reduce leakage current.
 - The resistor (R222) is not present but can be jumpered with a small amount of solder (Alternative: jumper the pads for D4 after removing it).
 - There is no location available for a supercap so the RTC battery pads will be used.

## Before:
![Jetson Xavier NX Before](XavierNXBefore.png)

## After:
![Jetson Xavier NX After](XavierNXAfter.png)

***

# Testing Results
    Xavier NX - 5F/3V Maxwell Cap (Diode Removed)
    Fully Charged: 2.999V
    (Connected to the network.)
    >ubuntu@xavierNX:~$ timedatectl 
    >Local time: Tue 2020-06-16 08:17:42 EDT
    >Universal time: Tue 2020-06-16 12:17:42 UTC
    >RTC time: Tue 2020-06-16 12:17:43
    >Time zone: America/New_York (EDT, -0400)
    >System clock synchronized: yes
    >systemd-timesyncd.service active: yes
    >RTC in local TZ: no

    Testing results (the voltages come from probing with a Fluke meter):
    (Disconnected from the network and power)
    8:19am - 2.999V
    12:13pm - 2.965V
    24 hours
    9:33am - 2.875V
    48 hours
    10:18am - 2.795V
    3:55pm - 2.781V
    72 hours
    10:29am - 2.734V
    96 hours
    8:35am - 2.681V
    Plugging back into power and getting time locally (no network)
    Local time is currently 8:37am (this is correct).
    Timedatectl command shows everything to be accurate and in sync as before.

# Links and quoted details
- https://forums.developer.nvidia.com/t/battery-on-j45-and-software-for-rtc/76613
- https://forums.developer.nvidia.com/t/battery-on-j45-and-software-for-rtc/76613
- https://www.digikey.com/product-detail/en/mpd-memory-protection-devices/BK-885-TR/BK-885-CT-ND/755448
- https://forums.developer.nvidia.com/t/rtc-jetson-nano/109749
- https://custom-build-robots.com/top-story-en/nvidia-jetson-nano-real-time-clock-rtc-part-1/12774?lang=en
- https://custom-build-robots.com/top-story-en/nvidia-jetson-nano-real-time-clock-rtc-part-2/12776?lang=en
- https://developer.nvidia.com/jetson-xavier-nx-data-sheet-v13
- https://developer.nvidia.com/embedded/dlc/jetson-nano-system-module-datasheet
-        An optional back up battery can be attached to the PMIC_BBAT module input. 
         It is used to maintain the RTC voltage when VDD_IN is not present. 
         This pin is connected directly to the onboard PMIC. 
         When a backup cell is connected to the PMIC, the RTC will retain its contents 
         and can be configured to charge the backup cell.
         
         The following backupcells may be attached to the PMIC_BBAT pin:
         - SuperCapacitor (goldcap,doublelayerelectrolytic)
         - Standard capacitors (tantalum)
         - Rechargeab leLithium Manganese cells
         
         The backup cells MUST provide a voltage in the range 2.5V to 3.5V. 
         These is charged with a constant current (CC), constant voltage (CV) charger
         that can be configured between 2.5V and 3.5V CV output and 50μA to 800μA CC.
- https://pastebin.com/YJkTe3nF
-        maxim,backup-battery-charging-current = <0x64>;
         maxim,backup-battery-charging-voltage = <0x2dc6c0>;
         maxim,backup-battery-output-resister = <0x64>;
- 0x64 is 100uA (Default configured charge current)
- 0x2dc6c0 is 3000000uV or 3v (Default configured charge voltage)
