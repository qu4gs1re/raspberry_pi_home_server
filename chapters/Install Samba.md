## HDD setup
- Connect your external HDD to one of the USB 3.0 ports on the Pi (the blue ones)
- Create a directory called `wd_hdd_2tb` under `/media` by running `sudo mkdir /media/wd_hdd_2tb`. This will be our mount point
- Create a directory called `network_storage` under `wd_hdd_2tb` by running `sudo mkdir /media/wd_hdd_2tb/network_storage`. This will be our Samba directory
- Install the `ntfs-3g` driver by running `sudo apt install -y ntfs-3g`. This is required for our HDD to work properly
- Run `sudo fdisk -l` to check if your HDD was recognized correctly by the system
- Take note of its path. Mine was `/dev/sda1`
- Run `sudo nano /etc/fstab`
- At the bottom of the file, add `/dev/sda1 /media/wd_hdd_2tb ntfs-3g defaults 0 0`
- Save and close the file
- Run `mount -a`. If you see no output, everything is good
- Reboot your system by running `sudo reboot`
- Your drive should now be automatically mounted

## Samba setup
- Run `sudo dietpi-software`
- Select "Browse Software"
- Scroll down and select "Samba Server: Feature-rich SMB/CIFS server" using the <kbd>Spacebar</kbd> and press <kbd>Enter</kbd> to confirm
- Select "Install", press <kbd>Enter</kbd> and then choose "OK"
- Create a Samba user account for each user that should be able to access the network share by running `sudo smbpasswd -a <username>`
- You’ll be prompted to enter a password. Make sure to choose a **STRONG** password

Now we have to configure Samba to share files and folders over the network. 

- Run `sudo nano /etc/samba/smb.conf` to open Samba's configuration file
- Replace the `[dietpi]` section at the bottom of the file with the following code:
```
[SAMBA_DIETPI]
path = /media/wd_hdd_2tb/network_storage
valid users = user1, user2, ...
writable = yes
read only = no
guest ok = no
```
- Restart the Samba service for the changes to take effect by running `sudo systemctl restart smbd`

## Connect from Windows
- Open the file explorer and enter the following in the address bar:
```
\\192.168.1.150\SAMBA_DIETPI
```
- Enter username and password, save your credentials and click "OK"
- You should now see your Samba share

## Connect from Android
- Download and install [CIFS Documents Provider](https://github.com/wa2c/cifs-documents-provider)
- Open it, then tap the folder icon on the bottom-right corner of the screen and select "Search"
- Tap your Pi's IP (mine is `192.168.1.150`)
- Enter username and password and tap "Save"
- You should now be able to access the Samba share from the default Files app by just opening the hamburger menu (top-left corner of the screen) and tapping "CIFS Documents Provider"
- If you install [VLC](https://play.google.com/store/apps/details?id=org.videolan.vlc), you can also directly stream your movies and TV shows from your Samba share