If you followed this guide correctly, you should already have OpenSSH installed and enabled on your system. We just have to do some basic hardening.
- Run `sudo nano /etc/ssh/sshd_config`
- Change `#Port 22` to `Port 2222`
- Change `#PermitRootLogin prohibit-password` to `PermitRootLogin no`
- Change `#PasswordAuthentication yes` to `PasswordAuthentication no`
- Save and close the file
- Apply the changes by running `sudo systemctl restart ssh`

## Windows
- Install [PuTTY](https://putty.org/)
- Open the PuTTYgen tool
- Choose the "EdDSA" (Ed25519) key type and click "Generate"
- Move your mouse over the blank area to generate some randomness for the key
- Enter a **STRONG** passphrase
- Click "Save private key" and save it wherever you want
- Copy the public key to a file on your Samba share
- Close the PuTTYgen tool
- Run the following commands on your Pi:
```
sudo su windows_user
mkdir -p ~/.ssh
cat /media/wd_hdd_2tb/network_storage/windows_pubkey >> ~/.ssh/authorized_keys
rm /media/wd_hdd_2tb/network_storage/windows_pubkey
exit
```
- Open PuTTY
- In the sidebar, browse to Connection > Data
- Enter your username
- Browse to Connection > SSH > Auth > Credentials
- Click "Browse..." next to the "Private key file for authentication" field and select your private key
- Click "Session" at the top of the sidebar
- Enter your Pi's IP (mine is `192.168.1.150`)
- Change the port to `2222`
- Click "Default Settings" and then "Save" to save your configuration
- Click "Open" to connect
- PuTTY will ask you if you trust the server you're connecting to. Click "Yes"
- Enter your passphrase to unlock your private key
- You should now be logged in as `windows_user`

## Android
- Install [JuiceSSH](https://juicessh.com/) and open it
- Tap "Manage Connections" and select the "Identities" tab
- Tap the plus button in the bottom-right corner of the screen
- Enter your username **ONLY**
- Generate your private key and make sure to enter a **STRONG** passphrase
- Tap the tick icon in the top-right corner of the screen to save your identity
- Long-press the identity and tap "Export Public Key"
- Enter your passphrase and tap "OK"
- Save the key to a file on your Samba share
- Run the following commands on your Pi:
```
sudo su android_user
mkdir -p ~/.ssh
cat /media/wd_hdd_2tb/network_storage/android_pubkey >> ~/.ssh/authorized_keys
rm /media/wd_hdd_2tb/network_storage/android_pubkey
exit
```
- From JuiceSSH, go to the "Connections" tab and tap the plus button in the bottom-right corner of the screen
- Enter your Pi's IP (mine is `192.168.1.150`)
- Select the identity you created before
- Change the port to `2222`
- Tap the tick icon in the top-right corner of the screen to save this connection
- Now simply tap the connection you just created to connect
- JuiceSSH will ask you if you trust the server you're connecting to. Tap "ACCEPT"
- Enter your passphrase to unlock your private key
- You should now be logged in as `android_user`