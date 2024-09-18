DietPi is a minimal and optimised Debian OS for single-board computers, with features such as software installation, configuration, backup, automation and more.
## Installation
- Download the Raspberry Pi 5 image from [the official website](https://dietpi.com/#download)
- Extract the image from the archive using [7-Zip](https://7-zip.org/) or similar software
- Flash the image onto the microSD card using [Rufus](https://rufus.ie/en/) or similar software
- Insert the microSD card in the card slot of the Pi
- Make sure the Pi is connected to the TV and router before plugging the power supply in
- This is not a headless setup, so you'll also need a keyboard and mouse
- DietPi will ask you to select your keyboard model. If you're not sure, just select the recommended option
- Select your keyboard layout
- DietPi will ask you some questions about your keyboard layout. Again, if you're not sure, just accept the defaults
- The installer will ask you to change your global software password for DietPi-Software Installs. Select "OK" and press <kbd>Enter</kbd> to change it. Make sure to enter a **STRONG** password and write it on a piece of paper; we will transfer this password to KeePassXC later
- After that, the installer will ask you to change the login password for "root" and "dietpi" users. Again, make sure to enter a **STRONG** password and write it on a piece of paper
- We need the serial/UART console to be enabled for our little OLED display to work. Select "No"
- Make sure to change the SSH server from "Dropbear" to "OpenSSH"
- Finally, scroll down to the "Install" option and press <kbd>Enter</kbd>
- Make sure your settings are correct, select "OK" and press <kbd>Enter</kbd>
- Choose "0: Opt OUT and purge uploaded data"

## Desktop environment installation
- Run `sudo dietpi-software`
- Select "Browse Software"
- Select "LXDE: ultra lightweight desktop" using the <kbd>Spacebar</kbd> and press <kbd>Enter</kbd> to confirm
- Scroll down to the "Install" option, press <kbd>Enter</kbd> and then "OK"
- Press <kbd>Enter</kbd> to select Firefox as the default web browser
- The installer will ask you if you want to configure the DietPi-AutoStart option. We need it to automatically start the Desktop environment on boot. Select "OK" and press <kbd>Enter</kbd>
- Under "Desktops" choose "Automatic login"
- Select the "dietpi" user and press <kbd>Enter</kbd>
- Exit from DietPi-AutoStart
- Now run `sudo reboot` and it should automatically start the desktop environment
- You can also start your desktop environment manually by running `startx`

## Enable audio
- Run `sudo dietpi-software`
- Select "DietPi-Config" and then "Audio Options"
- Select "Sound card" and press <kbd>Enter</kbd>, then <kbd>Enter</kbd> again to install it
- After the installer finishes, go back until it asks you to reboot the system and choose "OK"
- You should now have audio enabled

## Additional notes
If your TV cuts edges of the picture, it might be an aspect ratio issue. Just open your TV's picture settings and change your aspect ratio until there are no more clipped edges.