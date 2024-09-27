# Raspberry Pi 5 MX Linux Setup Guide

## 1. Prepare the SD Card

1. Download and install Raspberry Pi Imager on your computer:
   https://www.raspberrypi.com/software/

2. Insert the MicroSD card into your computer.

3. Open Raspberry Pi Imager and select the latest Raspberry Pi OS (64-bit).

4. Choose your MicroSD card as the destination.

5. Click "Write" to flash the OS to the SD card.

## 2. Boot from SD Card and Prepare NVMe SSD

1. Make a copy of downloaded mx23.4.img file in root partition and place it inside of home folder.

2. Insert the SD card into your Raspberry Pi 5 and power it on.

3. Open a terminal and run the following commands:

   ```bash
   # View connected storage devices
   lsblk

   # Create a new partition table on the NVMe SSD
   sudo fdisk /dev/nvme0n1

   # Create a new ext4 filesystem on the NVMe SSD partition
   sudo mkfs.ext4 /dev/nvme0n1p1

   # Copy MX Linux image to the NVMe SSD
   time sudo dd if=./mx23.4_rpi.img of=/dev/nvme0n1 bs=4M status=progress
   ```

## 3. Configure MX Linux on NVMe SSD

1. Mount the boot partition:
   ```bash
   sudo mount /dev/nvme0n1p1 /mnt
   ```

2. Edit cmdline.txt to ensure proper boot parameters:
   ```bash
   sudo nano /mnt/cmdline.txt
   ```
   Add this line according to your needs (important for network to work, connected via ethernet cable for stable IP):
   ```
   ip=162.166.xx.xx::162.166.x.x:255.255.255.0:rpi01:eth0:off
   ```
   Note: First field is IP address, then DNS address. Rest you could keep the same.

3. Create an empty ssh file to enable SSH on boot:
   ```bash
   sudo touch /mnt/ssh
   ```

4. Unmount the boot partition:
   ```bash
   sudo umount /mnt
   ```

5. Mount the root partition:
   ```bash
   sudo mount /dev/nvme0n1p2 /mnt
   ```

6. Set the timezone (change London to your preferred timezone):
   ```bash
   sudo nano /mnt/etc/timezone
   sudo ln -sf /usr/share/zoneinfo/Europe/London /mnt/etc/localtime
   ```

7. Configure DNS servers:
   ```bash
   sudo nano /mnt/etc/resolv.conf
   ```
   Add the following lines:
   ```
   nameserver 8.8.8.8
   nameserver 8.8.4.4
   ```

8. Enable SSH service on boot:
   ```bash
   sudo nano /mnt/etc/rc.local
   ```
   Add the following lines before 'exit 0':
   ```bash
   #!/bin/bash
   # Start SSH service
   systemctl start ssh
   ```

9. Make rc.local executable:
   ```bash
   sudo chmod +x /mnt/etc/rc.local
   ```

10. Unmount the root partition:
    ```bash
    sudo umount /mnt
    ```

11. Remove the SD card and reboot the Raspberry Pi. It should now boot from the NVMe SSD with faster performance.
