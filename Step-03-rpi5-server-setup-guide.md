# Raspberry Pi 5 Multi-Purpose Server Setup Guide

This guide will walk you through the process of setting up your Raspberry Pi 5 as a multi-purpose server, including Docker installation, Portainer setup, and various container configurations.

## Prerequisites

- Raspberry Pi 5 with MX Linux installed and booting from SSD (refer to the previous setup guide)
- External drive (1TB or 2TB) for network storage

## Step 1: Install Docker

```bash
curl -sSL https://get.docker.com | sh
sudo usermod -aG docker tspl
sudo apt install docker-compose
sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl status docker
```

## Step 2: Install Portainer

Portainer provides an easy-to-use interface for managing Docker containers.

```bash
sudo docker pull portainer/portainer-ce:latest
sudo docker run -d -p 9000:9000 --name=portainer --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data portainer/portainer-ce:latest
```

Access Portainer by navigating to `localhost:9000` in your web browser.

## Step 3: Set Up Containers

### Container 1: Pi-hole (Ad Blocking and Network Security)

1. Add this repository to Portainer's settings:
   `https://raw.githubusercontent.com/pi-hosted/pi-hosted/master/template/portainer-v2-arm64.json`

2. Deploy the pi-hole-unbound container from the Templates section.

3. Generate a Pi-hole password:
   ```bash
   pihole -a -p
   ```

4. Access Pi-hole at `localhost:1010` and log in with the generated password.

5. Add additional blocklists from sources like:
   - https://avoidthehack.com/best-pihole-blocklists
   - https://github.com/blocklistproject/Lists
   - https://sefinek.net/blocklist-generator
   - https://github.com/mmotti/pihole-regex/tree/master
   - https://v.firebog.net/hosts/lists.php

6. Activate blocklists by running: `tools > update gravity`

Tip: Use the DroidHole app from Google Play Store to monitor Pi-hole on your Android phone.

### Cloud Server 2: Network Storage (Samba)

1. Identify the external drive:
   ```bash
   lsblk
   ```

2. Create and mount the storage folder:
   ```bash
   sudo mkdir -p /mnt/cloudshare/
   sudo mount /dev/sdb1 /mnt/cloudshare/
   systemctl daemon-reload
   ```

3. Set permissions:
   ```bash
   sudo mkdir -p /mnt/cloudshare/mycloud
   sudo chmod -R 0770 /mnt/cloudshare/mycloud
   sudo useradd -G sambashare tspl
   sudo smbpasswd -a tspl
   sudo chown -R tspl:sambashare /mnt/cloudshare/mycloud
   ```

4. Configure Samba:
   ```bash
   sudo nano /etc/samba/smb.conf
   ```
   Add the following at the end of the file:
   ```
   [mycloud]
   path=/mnt/cloudshare/mycloud
   browseable = yes
   writeable = yes
   create mask = 0777
   directory mask = 0777
   public=no
   valid users = @sambashare
   ```

5. Restart Samba:
   ```bash
   sudo systemctl restart smbd
   ```

6. Set up automatic mounting:
   a. Find the UUID of the drive:
      ```bash
      lsblk -f
      ```
   b. Edit the fstab file:
      ```bash
      sudo nano /etc/fstab
      ```
   c. Add one of the following lines (depending on drive format):
      ```
      UUID=xxxxxxxxxxxxxxx  /mnt/cloudshare  ntfs-3g  defaults,noatime,nofail 0 0
      ```
      or
      ```
      UUID=xxxxxxxxxxxxx  /mnt/cloudshare  ext4  defaults,noatime,nofail 0 0
      ```
7. Now on the file manager of Linux, type:

   ```bash
   smb://ip-addr-of-pi5/mycloud/
   ```
   Similarly this cloud storage could be added as you normally do from windows using file-manager

### Container 3: DuckDNS (Dynamic DNS)

1. Install DuckDNS from Portainer.
2. Create a domain on the DuckDNS website and note your token.

### Container 4: WireGuard (VPN for Remote Access)

Install WireGuard using the Stack option in Portainer. Adjust configurations for your DuckDNS domain.

## Conclusion

You have now set up your Raspberry Pi 5 as a multi-purpose server with ad blocking, network storage, and remote access capabilities. Continue to explore and add more features as needed.

Remember to regularly update your system and back up important data.

