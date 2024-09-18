KeePassXC is a free and open source software that stores and manages your passwords in an encrypted file. Since we're going to have lots of passwords and since we're going to need those passwords across multiple devices, a tool like this will certainly make life easier (and more secure).

## DietPi (Linux)
- First, let's install KeePassXC by running `sudo apt install -y keepassxc`
- Start KeePassXC by running `keepassxc`
- Now we need to create a new database file. This file will store all of our passwords and can only be unlocked with a master password. Make sure the master password is **STRONG** and write it on a piece of paper
- Click "Create new database"
- Change the database name to something meaningful, like "dietpi-passwords"; you can leave the description blank
- Click "Continue" twice
- Enter the master password, click "Done" and save the file as "dietpi-passwords" wherever you want
- You can now add all your passwords by just clicking the plus icon and entering the required information

## Connect from other devices
**After** [[Install Samba|installing Samba]], copy your database file to your shared folder so you can access it from all your devices.

### Windows
- [Download](https://keepassxc.org/download/#windows) and install KeePassXC
- Click "Open Database"
- Enter the following address in the address bar:
```
\\192.168.1.150\SAMBA_DIETPI
```
- Select your database file and click "Open". If you don't see your database file, make sure the "All files (\*)" filter is selected
- Now just enter the password and click "Unlock"

### Android (KeePassDX)
- KeePassXC doesn't have an Android port, so we're going to download [KeePassDX](https://www.keepassdx.com/), which is compatible with KeePassXC
- Tap "Open existing vault", open the hamburger menu (top-left corner of the screen) and tap "CIFS Documents Provider"
- You should see a folder named "192.168.1.150" or something like that. Open it, then tap "SAMBA_DIETPI" and select your database file
- Now just enter the password and tap "Unlock"
- You can also configure the database to unlock with your fingerprint instead of entering the master password every time