# Hackintosh High Sierra on Thinkpad X230

I used to be a loyal Windows user until I got my first Macbook. The moment I got an extra SSD, I planned to install macOS on my Thinkpad X230. Tutorials are scattered around, some links no longer exist, some repositories uses different version of macOS from their description. So I decided to make this as a detailed reference for me. If you find it useful, I'm glad to help.

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
- F12, select USB HDD: Kingston
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
- done
