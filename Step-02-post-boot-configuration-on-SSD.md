# Raspberry Pi 5 Post-Boot Configuration Guide

## Step 2: Configuration After Booting from SSD

Now that you've successfully booted your Raspberry Pi 5 from the NVMe SSD, you need to decide how you want to use the system—either as a server accessed through SSH or with the full MX Linux GUI.

### Case 1: Headless Server (SSH Only)

If you plan to use your Raspberry Pi purely as a server without connecting a monitor (for example, accessing it via SSH), follow these steps:

1. **SSH into your Raspberry Pi**
   Use the IP address of your Raspberry Pi to connect via SSH. The default user and password are demo:

   ```bash
   ssh demo@<your-ip>
   ```

2. **Fix DNS Settings**
   If your DNS settings reset after boot, you may need to manually configure the resolv.conf file:

   ```bash
   sudo nano /etc/resolv.conf
   ```

   Add the following lines:
   ```
   nameserver 8.8.8.8
   nameserver 8.8.4.4
   ```

3. **Enable SSH on Startup**
   Ensure SSH starts automatically on boot:

   ```bash
   sudo systemctl enable ssh
   ```

4. **Add a New User and Remove Default**
   Create your own user (say, tspl) and remove the default demo user:

   ```bash
   sudo adduser tspl
   sudo usermod -aG sudo tspl
   sudo passwd tspl

   # Now exit and log back in using your new user tspl

   sudo deluser demo
   sudo deluser --remove-home demo
   ```

5. **Enable Remote Desktop (Optional)**
   If you'd like to access the Raspberry Pi via remote desktop, install XRDP:

   ```bash
   sudo apt install xrdp
   sudo systemctl start xrdp
   sudo systemctl enable xrdp
   ```

6. **Pair Bluetooth Devices (Optional)**
   If you're using Bluetooth devices, set up Bluetooth through the terminal:

   ```bash
   sudo systemctl start bluetooth
   bluetoothctl
   ```

   Run the following commands in bluetoothctl:

   ```
   power on
   agent on
   scan on
   pair <MAC address>
   connect <MAC address>
   trust <MAC address>
   ```

7. **Install MPV for Media Playback (Optional)**
   For basic media playback on the Pi:

   ```bash
   sudo apt install mpv
   ```

   You can now check audio playback by running some music through mpv.

   Important: Do not connect an HDMI monitor until you finish these steps to avoid the sound being forwarded to the HDMI device.

8. **Install Pironman Software for RGB and Small Monitor Control**
   Create a directory for software installations:

   ```bash
   mkdir Softwares
   cd Softwares
   ```

   Follow the official guide for setting up Pironman software:
   https://docs.sunfounder.com/projects/pironman5/en/latest/set_up/set_up_rpi_os.html

   Then, install the required software:
   ```bash
   sudo apt-get update
   sudo apt-get install git python3 python3-pip python3-setuptools

   git clone https://github.com/sunfounder/pironman5.git
   cd ~/pironman5
   sudo python3 install.py
   ```

   This will enable control of the RGB fans and the small monitor in the Pironman case.

### Case 2: Full GUI (MX Linux)

If you plan to use your Raspberry Pi 5 with a full desktop GUI environment, connect a monitor via HDMI and follow these steps:

1. **Connect HDMI and Monitor**
   - Connect the HDMI cable and power on the Raspberry Pi.
   - Wait for the screen to load (this can take 3-5 minutes depending on the system).

2. **Follow On-Screen Instructions**
   After the screen loads, follow the on-screen instructions to complete the initial setup.

3. **Fix DNS Settings**
   Once booted, if you experience DNS issues, configure the resolv.conf file:

   ```bash
   sudo nano /etc/resolv.conf
   ```

   Add the following lines:
   ```
   nameserver 8.8.8.8
   nameserver 8.8.4.4
   ```

4. **Enable SSH for Remote Access**
   To enable SSH access for remote management:

   ```bash
   sudo systemctl enable ssh
   ```

5. **Enable Remote Desktop (Optional)**
   Install and enable XRDP for remote desktop access:

   ```bash
   sudo apt install xrdp
   sudo systemctl start xrdp
   sudo systemctl enable xrdp
   ```

6. **Pair Bluetooth Devices**
   If you're using a Bluetooth speaker or other Bluetooth peripherals, pair them through the GUI:

   In the desktop environment, click the speaker icon to change your audio output to either the Bluetooth device or the monitor's built-in speakers.

7. **Install Pironman Software for RGB and Small Monitor Control**
   Create a directory for software installations:

   ```bash
   mkdir Softwares
   cd Softwares
   ```

   Follow the official guide for setting up Pironman software:
   https://docs.sunfounder.com/projects/pironman5/en/latest/set_up/set_up_rpi_os.html

   Then, install the required software:
   ```bash
   sudo apt-get update
   sudo apt-get install git python3 python3-pip python3-setuptools

   git clone https://github.com/sunfounder/pironman5.git
   cd ~/pironman5
   sudo python3 install.py
   ```

   This will enable control of the RGB fans and the small monitor in the Pironman case.
