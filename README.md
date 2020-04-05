# Hackintosh on Thinkpad X230

I used to be a loyal Windows user until I got my first Macbook. The moment I got an extra SSD, I planned to install macOS on my Thinkpad X230. Tutorials are scattered around, some links no longer exist, some repositories uses different version of macOS from their description. So I decided to make this as a detailed reference for me. If you find it useful, I'm glad to help.

This repo has 2 parts: High Sierra and Catalina.

# Part 1. Install fresh High Sierra

_Please note that part 1 install fresh High Sierra. I've tried install fresh Catalina 10.15.4 on April 3, 2020. Please see part 2 below._

All tools and files were accessed and uploaded by March 18, 2020. To me, this is the latest update on the original source. It took me total of 6.5 hours to start from beginning, got stuck and solve everything.

**Target**: macOS High Sierra 10.13.6 (I'll try newer updates later)

**Source**: https://github.com/littlegtplr/Hackintosh-X230-macOS (please note some links and old version tools from this repo no longer exist)

**Available equipment:**
- Thinkpad X230 (Core i5-3230M, 16 GB RAM, 480 GB SSD)
- Macbook Air 11.6 (early 2015)
- 8 GB USB stick
- 1 GB USB stick
- Wifi dongle Edimax, model EW-7811UN

## 1. Prepare installer
- Download macOS: High Sierra (ref: https://hackintosher.com/guides/make-macos-flash-drive-installer/)
- Erase disk (to have Scheme, change View at top left to All drives)
- Create the installer to the 8 GB USB

## 2. Install Clover boot loader on 8 GB USB

Ref: https://medium.com/@salbito/installing-clover-on-a-macos-sierra-installer-130705b91bfa

- download Clover EFI at https://github.com/CloverHackyColor/CloverBootloader/releases. The latest version I used is r5105 (optional: you can try r3911 to have similar option to original tutorial).
- run installation as in tutorial (if package won’t work when you first try to run, just turn off Gatekeeper at System Preferences/Security & Privacy: click Open anyway)

In the new version, settings are not the same to the original repo, these are my settings:
- UEFI only
- EPS
- Rec. drivers
- Human.
- File: Apfs
- Memory: OsxAptioFixDrv
- Additional: EmuVariableUefi

## At this step:
You need to mount the EFI volume on the USB by following steps 3, 4a. Then continue from step 5.
However, if you already see the new EFI volume appears on the desktop, skip step 4a. In other words, follow step 3, then continue from step 5.

## 3. Install Clover Configurator

You will need this app to
- mount/unmount EFI volume
- configure the installaton configuration

Therefore let's download the latest Clover Configurator at https://mackie100projects.altervista.org/download-clover-configurator/. The latest version I used is 5.9.3.0.

When you run the app, if it doesn't work, just turn off Gatekeeper at System Preferences/Security & Privacy: click Open anyway.

## 4. Mount/Unmount EFI volume

In order to mount/unmount, I suggest to install Clover Configurator. You can use terminal to do this but you will have to use Clover Configurator at step 5 anyway. So go with it:

### 4a. Mount EFI volume:
- run Clover Configurator
- on the left panel, select Mount EFI
- on the right panel, look for EFI partition on your disk (at installation preparation, it is your 8 GB USB; at post-installation, it is your internal SSD)
- click on Mount Partition
- password may be required, provided if asked
Be patient, a new EFI volume will soon appear on your desktop

### 4b. Unmount EFI volume:
- run Clover Configurator
- on the left panel, select Mount EFI
- on the right panel, look for EFI partition on your 8 GB USB, click on unMount Partition
- password may be required, provided if asked
Be patient, the EFI volume will soon disappear from your desktop

## 5. Copy EFI folder:

At this step, the EFI volume must be mounted (either automatically after step 2 or manually at step 4a). In my case, it is mounted automatically after creating installer, no need to run 4a.

**Follow these steps carefully, most of my time was spent debugging at this step.**
- open a new Finder window
- go to the mounted EFI volume, there is a single EFI folder
- open this single EFI folder, there are 2 folders named BOOT and CLOVER, open the CLOVER folder and set it aside
- download CLOVER.zip from this repository
- open another new Finder window, go to the downloaded zip file, unzip file and go into the unzipped folder (named CLOVER), here you should see 8 folders (ACPI, doc...) and 3 files
- from this location, copy everything to the CLOVER folder on the other Finder window

**Please follow steps above carefully, when copying, there must be a dialogue telling you there are files with the same name and provides some options: Delete, Replace, Merge. Select Merge.**

## 6. Clover configurator

- run Clover Configurator
- open config.plist on mounted EFI volume: EFI/CLOVER/config.plist
- on the left, go to SMBIOS. here some boxes are filled, but not SmUUID. 
- on the right of Serial number, click on Generate new Serial Number a few times
- open terminal, generate uuid by running `uuidgen`, paste the result to SmUUID box in Clover Configurator
- look above, copy Board Serial number, move to the left, Rt Variables: paste to MLB box
- File/Save to config.plist in USB drive: EFI/CLOVER/ (replace old file)
- a notice telling the file won't be changed later, just accept
- Now unmount the EFI volume on the USB (using step 4b), then eject the other volume Install macOS High Sierra.

## 7. BIOS settings on Thinkpad

Press F1 during POST

- change Security/Virtualization/VT-d: Disable
- change Startup/Boot mode: Diagnostics
- change Config/Network/Wake on Lan: Disable
- change Config/Power/Intel Rapid Start technology: Disable
- change Security/SecurityChip/SecurityChip: Disable
- change Startup/UEFI-Legacy Boot: UEFI Only
- change Security/Fingerprint/Predesktop Authentication: Disable
- change CSM Support to No
- check: Security Theft: Disable, disable Computrace

## 8. Install (on thinkpad)
- plug to USB 2.0 port on the right hand side
- Press F12 during POST, select USB HDD: Kingston
- when Clover boot menu appears, use arrow key or mouse to select Boot from Install High Sierra...
- seems to work, loading…
- select English,
- Disk utility => Erase SSD drive similar to step 1 (select the whole drive, not the volume)
- install
- when restart, boot again from usb, boot from Install, edit minstallconfig.xml
- when restart, boot again from usb, now boot from High Sierra instead of Install… => 18 minutes
- when restart, boot again from usb, now boot from High Sierra, then Setup
- Select US
- Select Not connect to Internet, Don’t transfer info.
- Create account, Customize, don’t track, London time, Not share Mac Analytic, No Siri
- Follow instruction to detect keyboard

## 9. Configure EFI partition (on thinkpad)
- copy clover boot loader and configurator to usb to Thinkpad
- run clover configurator, mount EFI of usb installer
- copy EFI folder to desktop
- unmount EFI of usb installer
- mount EFI of Recovery HD
- copy EFI on Desktop to EFI volume (which has a EFI folder), do not delete, select Merge
- delete EFI/APPLE
- unmount 2 usb, then restart
Now the machine can start by itself

## 10 Wifi dongle driver (for thinkpad)
- On the old Mac, download wifi dongle driver (https://www.edimax.com/edimax/download/download/data/edimax/global/download/product/wireless_adapters/wireless_adapters_n150/ew-7811un/) version 1.0.1.8 for macOS 10.13
- plug wifi dongle and usb to new Mac
- unzip on usb, installer.pkg
- during installation, need Securety and Privacy approval.
- be patient, then Restart
- wifi dongle icon will appear at top bar

## 11. Power Management (on thinkpad)
- follow instruction on https://github.com/Piker-Alpha/ssdtPRGen.sh
- better save file
- run command: ~/ssdtPRGen.sh

## 12. Sound (on thinkpad)
- To have sound, go to System Preferences/Sound, tab Output, select Speaker (Analog)
- To have microphone, change to tab Input, select Microphone (Digital)
- done
- To fix low volume, change configure of VoodooHDA.kext as instructed [here](https://www.reddit.com/r/hackintosh/comments/dfdf3l/x230_from_2012_living_the_catalina_dream/). Basically, right click, Show package content and edit info.plist, enable 4 settings starting with VoodooHDAEnable_.

## 13. Trackpoint (optional)
- Go to BIOS, disable Trackpad

Please note that middle scrolling button doesn't work. That is a disappoint.

I prefer using an external mouse for more functions. It may happen that scrolling is not as smooth as the trackpad on macOS. I suggest using MOS to improve experience. From this [introduction](https://www.reddit.com/r/MacOS/comments/b3dakf/mojave_smooth_scrolling/), you can install MOS from the main [page](https://mos.caldis.me/) or visit the [github](https://github.com/Caldis/Mos/blob/master/README.enUS.md).

## Extra notes

### a. Connectivity
- Bluetooth will run natively after installation
- Bluetooth (and sometimes Wifi) may become instable after waking up from sleep. Suggestion: disconnect your bluetooth devices and turn off bluetooth before putting your Hackintosh to sleep. After waking up, reconnect bluetooth devices. For wifi, disable and enable is adequate.

### b. TRIM support on SSD
Initially after installation,TRIM is not activated on my SSD (SanDisk SDSSDA480G) - you can find if it's on or off by go to About This Mac > System Report > SATA.

To turn it on, go to terminal and run `sudo trimforce enable`.

### c. Updates
Regular app updates (iTunes, Safari...) run normally.

Security updates need some special treatments:
- Turn off SIP by mounting EFI volume, loading the config.plist on this volumn and edit Rt Variables/CsrActiveConfig from 0x0 to 0x67 (as instructed at https://hackintosher.com/forums/thread/enable-disable-system-integrity-protection-sip-on-a-hackintosh.53/).
- Save the config.plist, unmount the EFI and Restart the machine
- Go to App Store/Updates, when a security update is shown, click on Update
- When a dialogue Some updates need to finish downloading before they are installed appears, click on Not now (if you select Download and Restart, the machine will restart after downloading, but the update is not installed on Hackintosh).
- After the update installation is downloaded, go to a folder located at /Library/Updates/. Look for one that has several .pkg files. At the time of this Readme, I was trying to install 2020-001 Security update.
- Run the corresponding installation. At the time of this Readme, it is SecUpd2020-001HighSierra.pkg
- After the installation has been prepared, select Restart
- After all this, enable SIP by editing CsrActiveConfig back to 0x0, unmount EFI and Restart.

_There is another suggestion to install the combo update, which can be found at https://support.apple.com/kb/DL1970?locale=en_US but I haven't tried that._

## Have fun with your hackintosh.

# Part 2. Install fresh Catalina

With the instruction from https://mighil.com/thinkpad-x230-hackintosh/, I first tried the latest 10.15.4 but Bluetooth is not detectable. I then tried the older 10.15.2 which matches version by the reference link.

Hereby I summarize the installation of both versions. In either case, Activity monitor Network Data received/sec normally appears zero.

## 2.1 Install fresh Catalina 10.15.4 (Bluetooth not working)

### 1. Create installation media

Reference: https://9to5mac.com/2019/06/27/how-to-create-a-bootable-macos-catalina-10-15-usb-install-drive-video/

- download Catalina from App Store

Requires at least 16 GB. I use a 64 GB USB.

Now erase, create Journal extend… and so on.

- Terminal: sudo /Applications/Install\ macOS\ Catalina.app/Contents/Resources/createinstallmedia --volume /Volumes/Hackintosh

After this, a new volume is created and mounted: Install macOS Catalina

### 2. Install Clover to USB

Refer to https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/clover-setup

- Use the latest version: r5108.
- Change Install Location: Install macOS Catalina
- Customize: check all Rec. drivers, HID, File system: ApfdDriverLoader, VBoxHfs, Memory fix drivers: AptioMemoryFix, Additional: EmuVariableUefi.

Then Install.

### 3. EFI configuration:
- (optional) install Clover configurator, mount EFI partition (it’s normally mounted).
- download from https://github.com/mighildotcom/X230-Hackintosh: Download directly from main GitHub site
- unzip, there is a folder with 3 subfolders and a README.md, go to 10.15/EFI. There are 2 folders BOOT and CLOVER
- open EFI partition on HDD, there is another EFI, go there. There are 2 folders BOOT and CLOVER
- go to BOOT, copy BOOTX64.efi from unzipped folder to EFI/EFI/BOOT, select Replace
- go up, go to CLOVER, there are 8 folders and 3 files. Delete CLOVER on the usb EFI, then copy all these to EFI/EFI/CLOVER/
- no need to edit config.plist file
- unmount EFI, eject Install macOS Catalina

### 4. Installation: Follow installation of High Sierra.
- boot from USB
- select Boot macOS Install from Install macOS Catalina
- erase internal disk then install
- First restart, boot directly from hard drive
- Second restart, boot from hard drive again, after a while it shows 16 minutes
- Third restart, there are 2 more options, need to manually select Boot macOS from Catalina

- Follow installation instruction. At Choose your look, select Dark (previous find instability with White)

### 5. Install Clover to have EFI partition

If found not compatible because version r5105 instead of the latest one, just continue)
- Mount and copy EFI from USB to hard drive EFI
- then remove APPLE folder
- Restart

### 6. Wifi Dongle

Old driver doesn’t work. Follow https://github.com/chris1111/Wireless-USB-Adapter:
- mount EFI, open config.plist
- Rt Variables: change CsrActiveConfig from 0x3E7 to 0x67 (Disable). If want to Enable in the future, change to 0x00. Just delete and type 67, it will fill in the remaining.
- Save and Reboot
- Run Wireless USB adapter-V11 (will need permission) then Restart

### 7. Sound setup: like High Sierra

Mic doesn't seem to work but headphone (with mic) works fine.

### 8. Language
System Preferences/Language & Region, add on the left, add Vietnamese. To Shortcut, Keyboard.

### 9. External monitor

System crashed when plugged in the first time, BIOS information got restored to default. After re-configuring BIOS, and restart, external screen works.

### 10. Bluetooth: not working

I've tried several ways but couldn't make it work. Therefore switch back to 10.15.2 as shown below.

## 2.2 Install fresh Catalina 10.15.2

This consists of 2 steps:

- Install original release 10.15 (7 GB)
- Upgrade to 10.15.2 (2 GB)

Useful links for downloads:
- https://congngheviet.com/tao-bo-cai-macos-10-15-va-cac-phien-ban-khac/
- https://maclife.vn/bo-cai-macos-catalina-10-15.html
- https://support.apple.com/downloads

### 1. Install 10.15
Create installation media:
- Open 10.15.dmg, load to Applications.
- Make installation: 

Install clover 5108:
- Clover for UEFI
- ESP
- Rec. drivers
- HID
- FSD: Apfs
- Mem: Aptio.

Replace whole EFI 15.10 (BOOT and EFI)

Install: similar to 10.15.4
- install from usb once, installation will restart twice but select to boot from HDD.
- install Catalina to HDD
- install Clover 5108
- clover configurator, mount EFI
- delete everything on EFI (hdd), copy EFI folder from usb to EFI (hdd)
- change VoodooHDA configuration
- edit CSR to disable
- restart
- install wifi driver
All works flawlessly, except Activity monitor Network Data received/sec normally zero.

### 2. Upgrade 10.15.2
- backup Time machine (optional)
- prepare 10.15.2 and 10.15 ready on usb (optional)
- run macOSUpdCombo10.15.2.dmg (it extracts to .pkg)
- when restart, at Clover boot option, select Boot macOS Install Prebooter from Preboot (it is selected by default, just to clarify)
- at the second restart, select again Preboot (it’s selected by default), it’ll take more than 45 minutes
- at the third restart, the option Boot macOS Install Prebooter disappears, now select Boot macOS from Catalina. This restart takes about 15 minutes to complete.
