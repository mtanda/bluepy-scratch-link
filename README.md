# bluepy-scratch-link

Bluepy-scratch-link is [Scratch-link](https://github.com/LLK/scratch-link)
implemented on bluepy as a small python script. As of October 2019, Scratch-link
is a software module which connects [Scratch](https://scratch.mit.edu/) and
Bluetooth devices such as [micro:bit](https://microbit.org/). However, it works
only on Windows and MacOS, and cannot connect Scratch and micro:bit on Linux.

Bluepy-scratch-link allows Linux PCs to connect Scratch and micro:bit. It uses
Linux Bluetooth protocol stack [Bluez](http://www.bluez.org/) and its python
interfaces [pybluez](https://github.com/pybluez/pybluez) to handle Bluetooth,
and [bluepy](https://github.com/IanHarvey/bluepy) to handle Bluetooth Low
Energy, or BLE, connections with micro:bit. It is confirmed that
bluepy-scratch-link connects Scratch 3.0 and a micro:bit, and a Lego Mindstorms
EV3.

This is a minimal implementation to support micro:bit and Lego Mindstorms EV3.
It may work with other devices but these are untested. Some Scratch-link
features are not implemented.

Bluepy-scratch-link requires python version 3.6 and later to use websockets.
If your system has python older than version 3.6, install newer version. If your
Linux system has explicit command names python3 and pip3 for python version 3,
use them in the instructions below.

Confirmed Environments
----------------------
The instructions below was confirmed with following devices and distros.
Trial with other distros and feed-backs will be appreciated.

The bluepy-scratch-link was confirmed with following devices:
* micro:bit by @kawasaki
* Lego Mindstorm EV3: by @chrisglencross

The bluepy-scratch-link was confirmed with following Linux distros:
* Arch Linux by @kawasaki
* elementary OS 5.0 Juno by @kawasaki
* Raspbian by @chirsglencross

The bluepy-scratch-link was confirmed with following browsers:
* FireFox by @kawasaki
* Chromium by @kawasaki

Installation
------------
1. Prepare Bluetooth/BLE controller.

   Confirm that your Linux PC has a Bluetooth controller with BLE support.
   Bluetooth 4.0 controller supports BLE. If your PC does not have it, need
   to plug USB Bluetooth 4.0 adapter.

   Note: BLED112 USB dongle with Bluegiga BGAPI is not supported.

2. Install required packages.

    ```sh
    Ubuntu
    $ sudo apt install bluez libbluetooth-dev libnss3-tools libcap2-bin
    Arch
    $ sudo pacman -S bluez bluez-utils nss libcap
    ```

3. Install python modules.

    ```sh
    $ sudo pip install bluepy pybluez websockets
    Or if your system has python3 command,
    $ sudo pip3 install bluepy pybluez websockets
    ```

4. Get bluepy-scratch-link.

   Example below installs bluepy-scratch-link under your home directory.
    ```sh
    $ cd ~
    $ git clone https://github.com/kawasaki/bluepy-scratch-link.git
    ```

5. Set bluepy-helper capability
    ```
    $ sudo ./bluepy_helper_cap.py
    Set capacbility 'cap_net_raw,cap_net_admin' to /usr/lib/python3.8/site-packages/bluepy-1.3.0-py3.8.egg/bluepy/bluepy-helper
    ```

6. If using a micro:bit, install Scratch-link hex on your device.

    * Download and unzip the [micro:bit Scratch Hex file](https://downloads.scratch.mit.edu/microbit/scratch-microbit-1.1.0.hex.zip).
    * Flash the micro:bit over USB with the Scratch .Hex File, you will see the
      five character name of the micro:bit scroll across the screen such as
      'zo9ev'.

Usage
-----
1. For Lego Mindstorms EV3, pair your Linux PC to the EV3 brick.

   First, turn on the EV3 and ensure Bluetooth is enabled.

   Then, pair using your Linux desktop's the Bluetooth settings.

   If using Gnome:
      * Settings -> Bluetooth
      * Click on the EV3 device name
      * Accept the connection on EV3 brick
      * Enter a matching PIN on EV3 brick and Linux PC. '1234' is the value Scratch suggests.
      * Confirm EV3 status is "Disconnected" in Bluetooth settings

   With a Raspberry Pi default Raspbian desktop, click the Bluetooth logo in the top right of the screen and
   Add Device. Then follow the Gnome instructions. You will be warned that the Raspberry Pi
   does not know how to talk to this device; that is not a problem.

   Alternatively you can perform pairing from the command-line:
   ```shell script
   $ bluetoothctl

   [bluetooth]# power on
   Changing power on succeeded

   [bluetooth]# pairable on
   Changing pairable on succeeded

   [bluetooth]# agent KeyboardOnly
   Agent registered

   [bluetooth]# devices
   ...
   Device 00:16:53:53:D3:19 EV3
   ...

   [bluetooth]# pair 00:16:53:53:D3:19
   Attempting to pair with 00:16:53:53:D3:19

   # Confirm pairing on the EV3 display, set PIN to 1234

   Request PIN code
   [agent] Enter PIN code: 1234
   [CHG] Device 00:16:53:53:D3:19 Connected: yes
   [CHG] Device 00:16:53:53:D3:19 Paired: yes
   Pairing successful

   [bluetooth]# quit
   ```

2. Start scratch-link python script.
    ```sh
    $ cd ~/bluepy-scratch-link
    $ ./scratch_link.py
    Or if your system has python3 command,
    $ python3 ./scratch_link.py
    ```

3. Connect scratch to micro:bit or Lego Mindstorms:
    * Open FireFox or Chrome and access [Scratch 3.0](https://scratch.mit.edu/)
    * Select the "Add Extension" button
    * Select micro:bit or Lego Mindstorms EV3 extension and follow the prompts to connect
    * Build your project with the extension blocks

In Case You Fail to Connect
---------------------------

1. If Scratch says "Make sure you have Scratch Link installed" but you are sure
   that scratch-link python script is running, check that Firefox or Chrome
   allows local server certificate.
    * Open Firefox or Chrome and access [https://device-manager.scratch.mit.edu:20110/](https://device-manager.scratch.mit.edu:20110/). You will see a security risk warning.
    * In **Firefox**: Click "Advanced" and click "Accept Risk and Continue".
    * In **Chrome**: type the special bypass keyword `thisisunsafe`.
    * Immediately, you will see "Failed to open a WebSocket connection". This is expected.

2. If device scan fails, check systemd bluetooth service status.
    ```
    systemctl status bluetooth.service
    ```
    * If the service is not working, refer guide of your distro to set it up.
    * If the service is working, also check that /etc/bluetooth/main.conf sets AutoEnable=true.

3. If scratch_link.py says "failed to connect to BT device: [Errno 13] Permission denied",
   make sure to pair the bluetooth device to your PC before connecting to Scratch.
