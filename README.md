# k4a
***Use your Android device to run Klipper, Moonraker, Fluidd/Mainsail and KlipperScreen***

***
**The main work was already done by others. I only made some improvements.**
- Original klipper, moonraker, xterm and telegram init scripts by [@d4rk50ul1](https://github.com/d4rk50ul1/klipper-on-android)
- Original ttyACM0 initialization script by [@CODERUS](https://gist.github.com/CODERUS/a5ec4a456f5b58186cbebb66a8542a2e)
- Orginal setup script by [@gaifeng8864](https://github.com/gaifeng8864/klipper-on-android) 
***
My current setup: Ender3 + MKS Robin nano v3  with Klipper Firmware + Oneplus One running Klipper+Moonraker+Fluidd+Klipperscreen

## Requirements
- A rooted Android device with the following installed:
  - Linux Deploy app: [Google Play](https://play.google.com/store/apps/details?id=ru.meefik.linuxdeploy) or [Github](https://github.com/meefik/linuxdeploy/releases/tag/2.6.0)
  - XServer-XSDL app: [Google Play](https://play.google.com/store/apps/details?id=x.org.server) or [Github](https://sourceforge.net/projects/libsdl-android/files/apk/XServer-XSDL/)
  - optional kerneladiutor: ( to enable all CPU cores) [F-Droid link](https://f-droid.org/en/packages/com.nhellfire.kerneladiutor/)

- OTG+Charge cable up and running for android device ( please check [this(https://www.youtube.com/watch?v=8afFKyIbky0) video for referenc)
- 3D printer already flashed with Klipper firmware

## Setup Instructions
### 0. Install kerneladiutor ###

Kerneladiutor is a simple and easy-to-use Android kernel management software, used to adjust the frequency and performance of CPU and GPU. All CPU cores can be turned on to make full use of the performance of the phone.

Install, open it and give root permissions.
Open the menu, go to CPU Hotplug, disable MPDecision and enable execution on boot.

### 1. Install XServer-XSDL ###

After the installation is complete, you need to click the "Change Device Settings" button at the top of the screen on the first startup interface to enter the setting interface, and then click "Mouse emulation" --- "Mouse emulation Mode" --- "Desktop, no emulation" , then scroll down to the bottom and click OK.
Again scroll down and click OK.
On the next black screen click once to select the display resolution. I use native and X2.5.
Check the `Display number` is set to `0` and click Okay.

If you miss the interface for the first startup, just close XServer-XSDL running in the background and start XServer-XSDL again.

### 2. Install Linux-deploy ###

Afer start click on the menu on the upper left corner and open Settings.
Set the following options:
 - Lock screen (no)
 - Lock WiFi (yes)
 - Wake lock (yes)
 - Autostart (yes)
 - Autostart delay (5)
 - Track network changes (yes)
 - Track power changes (yes)
 - other options remain default  

Return to the main screen.

### 3. Install debian container ###
Now open the settings on the lower right corner and create a container using the following settings:
  - **Bootstrap**:
    - **Distro**: `Debian`
    - **Installation type**: `Directory`   
    - **Installation path**: `/data/local/debian11`  
    *Note: You can choose a different location but if it's within `${EXTERNALDATA}` then SSH may fail to start.*  
    - **User name**: `print3D`  
    *Note: You can choose something else.*
    - **User password**: `set your own`
  - **INIT**:
    - **Enable**: `yes`
    - **Init system**: `sysv`
  - **SSH**:
    - **Enable**: `yes`
  - **GUI**:
    - **Enable**: `yes`
    - **Graphics subsystem**: `X11`
    - **Desktop environment**: `XTerm`
    
Return to the main screen and click the 3dots menu in the upper right corner and select "Install".
When "<<< deploy" appears at the bottom of the interface, the installation is complete.
Now click "STOP" and then click "START" to load the Debian container.

### 4. Install Klipper environment ###

- SSH into the container and execute the following command:
  ```bash
  sudo usermod -a -G aid_inet,aid_net_raw root
  ```
  This will fix the network permission for the root/sudo user.

- Install git, wget and Kiauh.
  ```bash
  cd ~
  sudo apt -q update
  sudo apt install -y git wget
  git clone https://github.com/th33xitus/kiauh.git
  ```
- Install Klipper, Moonraker, Fluidd (and/or Mainsail) and KlipperScreen using Kiauh:
  ```bash
  ./kiauh/kiauh.sh
  ```
- Install the fixes for chroot container:
  ```bash
  wget https://raw.githubusercontent.com/stream2me/k4a/main/chroot-fix.sh | bash -
  ```
  After the script is finished the phone will restart. KlipperScreen should now appear in XServer XSDL and Mainsail and/or Fluidd should be accesible using your Android device's IP address in a browser.
  
## Troubleshooting ##  

- The 3D printer is correctly flashed and connected to the phone, but the script still prompt " **Please connect your phone to the printer** ". Then run the following command:

      ls -al /dev/tty*

  If there is no device starting with `ttyUSB` or `ttyACM` the script will not work yet for you.

- Error when installing Telegram-Bot  
  run:
  ```bash
  rm -rf $HOME/moonraker-telegram-bot
  pip install ujson
  ```
  Now try again.
