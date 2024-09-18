- Run `sudo usermod -a -G cdrom,audio,render,video,plugdev,users,dialout,dip,input dietpi` and then `sudo reboot` to apply changes
- Run `sudo dietpi-software`
- Choose "Browse Software"
- Scroll down and select "Kodi: The media centre for Linux" with the <kbd>Spacebar</kbd>, then press <kbd>Enter</kbd> to confirm
- Select "Install", press <kbd>Enter</kbd> and then choose "OK"
- When the installer asks you to set up the DietPi-AutoStart option, choose "Cancel" (we'll do it later)
- Kodi should start automatically
- Enable the "Version Check" add-on and click "OK"
- Go to Settings (gear icon) > System > Input and change the "Hardware keyboard layouts" to your preferred keyboard layout

## Install TV channels (optional if you bought a TV)
- Run `sudo apt install -y kodi-pvr-iptvsimple`
- Start Kodi
- Enable the "IPTV Simple Client" add-on and then click "Cancel"
- Go to Settings (gear icon) > System > Add-ons and enable "Unknown sources"
- Go back and select File manager > Add source
- Click "\<None\>", enter `https://worldlivetv.github.io/repo` and click "OK"
- Change the name of this repo to something meaningful, like "WorldLiveTV repo", and then click "OK"
- Go back and select Add-ons > Install from zip file
- Click "WorldLiveTV repo" and then "repository.wltv-x.x.x.zip"
- Go back to Kodi's home screen
- Click "Add-ons" in the sidebar
- Click the little box icon in the top-left corner of the screen
- Click "Install from repository" and then "WorldLiveTV Addon Repository"
- Click "Video add-ons" and then "World Live TV"
- Click "Install" in the bottom-right corner of the screen and then "OK" to install the dependencies
- Wait until the installation finishes, then go back to the home screen
- Select "Add-ons" and then click "World Live TV"
- Click "Select List" and choose a list. I'm going to choose "DTT"
- Go back to the home screen
- Click "TV" and you should see all your favorite TV channels!

## Install the Otaku add-on (great if you watch anime)
- Go to Settings (gear icon) > System > Add-ons and enable "Unknown sources"
- Go back and select File manager > Add source
- Click "\<None\>", enter `https://goldenfreddy0703.github.io/repository.hooty` and click "OK"
- Change the name of this repo to something meaningful, like "Hooty's repo", and then click "OK"
- Go back and select Add-ons > Install from zip file
- Click "Hooty's repo" and then "repository.hooty-x.x.zip"
- Go back to Kodi's home screen
- Click "Add-ons" in the sidebar
- Click the little box icon in the top-left corner of the screen
- Click "Install from repository" and then "Hooty's Repository"
- Click "Video add-ons" and then "Otaku"
- Click "Install" in the bottom-right corner of the screen and then "OK" to install the dependencies
- Wait until the installation finishes, then go back to the home screen
- You can find the Otaku add-on by selecting "Add-ons" in the sidebar

## Install the YouTube add-on
### Add-on installation
- Run `sudo apt install -y kodi-inputstream-adaptive`
- Open Kodi and click "Add-ons" in the sidebar
- Click the little box icon in the top-left corner of the screen
- Click "Install from repository" > "All repositories" > "Video add-ons"
- Choose "YouTube" and click "Install"
- Wait until the installation finishes, configure the add-on however you want and then go back to the home screen
- You can now access the YouTube add-on from the "Add-ons" section
### API key setup
- From your PC, open the [Google Cloud Console](https://console.developers.google.com/apis/library) and log in
- Click "Select a project" in the top-left corner of the screen and then "NEW PROJECT"
- Give the project a meaningful name, like "dietpi-kodi", and click "CREATE"
- In the search bar, write "youtube data api v3" and hit <kbd>Enter</kbd>
- Click "YouTube Data API v3" > "dietpi-kodi" > "ENABLE"
- Click "Credentials" in the sidebar > "CREATE CREDENTIALS" > "API key"
- Copy your API key to a text file (make sure to delete it after you're finished)
- Click "OAuth consent screen" in the sidebar
- Check "External" and click "CREATE"
- Give it the same name as before: "dietpi-kodi"
- Enter your email address as the user support email
- Scroll down to the end and enter your email address as the developer contact information
- Click "SAVE AND CONTINUE" until you reach the "Test users" section
- Click "ADD USERS"
- Enter your YouTube account email and click "SAVE". **MAKE SURE TO DOUBLE CHECK YOUR EMAIL BECAUSE YOU WON'T BE ABLE TO REMOVE IT!**
- Click "SAVE AND CONTINUE" and then "BACK TO DASHBOARD"
- Open the "Credentials" section again > "CREATE CREDENTIALS" > "OAuth client ID"
- Choose "TVs and Limited Input devices"
- Give it the same name as before: "dietpi-kodi"
- Copy the Client ID and Client secret to the same text file as the API key and click OK

Great! Let's go back to Kodi.
- Select the "Add-ons" section from the sidebar, left click on the YouTube add-on and choose "Settings"
- Select the "API" section and enter your API key, Client ID and Client secret. If you [[Install Samba|installed Samba]], you can share the text file we created over the network and copy your keys from there
- Click "OK"
- Now open the YouTube add-on and click "Sign In"
- Click "OK" when it asks you to log in twice
- Do what the prompt says to allow the add-on to sign into your YouTube account
- After you're finished, you'll have to do it again
- You should now be logged in!

## Yatse setup
Yatse is a remote for Kodi. It will allow us to comfortably use Kodi with our smartphone.

- Start Kodi
- Go to Settings (gear icon) > Services > Control
- Set a **STRONG** password
- Enable "Allow remote control via HTTP"
- Make sure that both the "Allow remote control from applications on this system" and "Allow remote control from applications on other systems" options are enabled
- Select the "General" tab and make sure the "Announce services to other systems" option under "Zeroconf" is enabled
- Install [Yatse](https://www.yatse.tv/) on your smartphone and open it
- Tap "Add Host..."
- Tap the plus icon in the bottom-right corner of the screen
- Select "KODI"
- If Kodi is detected, tap on it, enter your username (which should be "kodi") and password and tap "Add host"
- If Kodi is NOT detected, just tap "Next" to configure it manually
	- Host IP address: `192.168.1.150`
	- Webserver port `8080`
	- Tap "Add host"
	- Enter your username and password and tap "Add host"
- Click "Yes" on your Pi if Kodi gives you a warning to proceed

You can also play YouTube videos or YT Music songs directly by simply tapping the share button and choosing the "Play on Media Center" option. If the option is not there, tap "More" or "Share with other apps".

## Add movies and TV shows
- After [[Install Samba|installing Samba]], create a folder called "kodi" inside your Samba share by running `sudo mkdir /media/wd_hdd_2tb/network_storage/kodi`
- After that, create the "movies" and "tv_shows" subfolders by running `sudo mkdir /media/wd_hdd_2tb/network_storage/kodi/movies /media/wd_hdd_2tb/network_storage/kodi/tv_shows`
- Transfer everything inside these two folders
- Start Kodi
- Select "Movies" from the sidebar
- Click "Add videos..."
- Click Browse > Root filesystem > media > wd_hdd_2tb > network_storage > kodi > movies
- Enter a meaningful name for this media source (like "Movies") and click "OK"
- Now click "This directory contains", select "Movies", then click "OK" and "Yes"
- Do the same from the "TV shows" tab
- After that, you should see all your movies and TV shows
- If you add more content, you need to refresh your media sources:
	- From the Kodi home screen, select "Videos" (sidebar)
	- Click "Files"
	- Right click on your media source and select "Scan for new content"

## Fix black screen when Kodi is started before TV
Sometimes, if you start Kodi before turning your TV on, video will be disabled. The root cause of this issue is the EDID (Extended Display Identification Data) handshake. The system may not correctly perform the handshake with the TV, resulting in a black screen. To fix this, we need to "hardcode" the EDID info so that the system doesn't need to perform a handshake with the TV.

- Find your EDID file inside `/sys/class/drm/card?-HDMI-A-?/edid`
- Run `cat /sys/class/drm/card?-HDMI-A-?/status` (for each of your cards). If the command returns `connected`, that's your card
- My card directory was `/sys/class/drm/card1-HDMI-A-1`, but yours could be different
- Run `sudo mkdir /lib/firmware/edid`
- Run `sudo cp /sys/class/drm/card1-HDMI-A-1/edid /lib/firmware/edid/edid-HDMI-A-1.bin`
- Run `sudo nano /boot/cmdline.txt` and add the following text to the end of the line:
```
drm.edid_firmware=HDMI-A-1:edid/edid-HDMI-A-1.bin video=HDMI-A-1:D
```
- Save and close the file
- Run `sudo reboot` to apply changes

## Make Kodi start on boot
- Run `sudo dietpi-autostart`
- Select the "Kodi" option under "Media" and hit <kbd>Enter</kbd>
- Make sure to select the "dietpi" user and hit <kbd>Enter</kbd> again
- Exit from DietPi-AutoStart
- Run `sudo reboot` and it should automatically start Kodi