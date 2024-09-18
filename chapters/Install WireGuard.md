Unlike the other apps, we'll install WireGuard manually. This is because, as of June 2024, DietPi's scripts don't allow you to set up a split tunnel, which is what I want to do. Basically, a split tunnel will allow us to use our home network only for the services we installed and explicitly allowed, while the rest of the traffic will just be routed normally.

- First, you need to [[Configure a Dynamic DNS name for your public IP|configure a Dynamic DNS name for your public IP]]
- After that, run `sudo apt install -y wireguard`
- Create the private key for WireGuard and change its permissions by running `wg genkey | sudo tee /etc/wireguard/private.key && sudo chmod go= /etc/wireguard/private.key`
- You should receive a single line of base64-encoded output, which is the private key. Take note of the private key, since you’ll need to add it to WireGuard’s configuration file later
- Create the corresponding public key, which is derived from the private key, by running `sudo cat /etc/wireguard/private.key | wg pubkey | sudo tee /etc/wireguard/public.key`
- You should receive a single line of base64-encoded output, which is the public key. Take note of the public key, since you’ll need to distribute it to any peer that connects to the Pi
- Run `sudo nano /etc/wireguard/wg0.conf`
- Add the following lines to the file:
```
[Interface]
PrivateKey = <private_key>
Address = 10.0.0.1/24
ListenPort = 51888
SaveConfig = true
PostUp = ufw route allow in on wg0 out on eth0 from 0.0.0.0/0
PostUp = ufw allow from 10.0.0.0/24 to any port 2222 proto tcp
PostUp = ufw allow from 10.0.0.0/24 to any port 53
PostUp = ufw allow from 10.0.0.0/24 to any port 80 proto tcp
PostUp = ufw allow from 10.0.0.0/24 to any app Samba
PostUp = iptables -t nat -I POSTROUTING -o eth0 -j MASQUERADE
PostDown = ufw route delete allow in on wg0 out on eth0 from 0.0.0.0/0
PostDown = ufw delete allow from 10.0.0.0/24 to any port 2222 proto tcp
PostDown = ufw delete allow from 10.0.0.0/24 to any port 53
PostDown = ufw delete allow from 10.0.0.0/24 to any port 80 proto tcp
PostDown = ufw delete allow from 10.0.0.0/24 to any app Samba
PostDown = iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
```
- Save and close the file
- Change its permissions by running `sudo chmod go= /etc/wireguard/wg0.conf`
- We need our Pi to forward traffic that is not directly addressed to it. We're doing this so we can access other services on our network, like our router's admin page
- Run `sudo nano /etc/sysctl.conf` and uncomment the following line:
```
#net.ipv4.ip_forward=1
```
- Save and close the file
- Run `sudo sysctl -p` to read the file and load the new value for your current terminal session
- Enable the WireGuard service by running `sudo systemctl enable wg-quick@wg0.service`
- Now start the service by running `sudo systemctl start wg-quick@wg0.service`
- If you run `ip a`, you should see the newly created `wg0` interface
- Finally, you have to [[Set up WireGuard port forwarding|forward the 51888/UDP port on your router]]

## Android client
- Install the [WireGuard](https://www.wireguard.com) app and open it
- Click the plus button in the bottom-right corner of the screen
- Click "Create from scratch"
- Enter a meaningful name for your new WireGuard tunnel
- Click the button next to the "Private key" field to generate your key pair
- Take note of your public key, you'll need it later
- Inside "Addresses", enter `10.0.0.2/24` (increment the last number for every client you add)
- Inside "DNS servers", enter your Pi's IP address (mine is `192.168.1.150`)
- Click "Add peer"
- Enter your Pi's public key
- On your Pi, create a pre-shared key [for additional security](https://www.wireguard.com/protocol/#key-exchange-and-data-packets) by running `wg genpsk | sudo tee /etc/wireguard/android_psk.key && sudo chmod go= /etc/wireguard/android_psk.key`
- You should receive a single line of base64-encoded output, which is the pre-shared key. Copy the pre-shared key to the dedicated field on your WireGuard app
- Enter your DNS name followed by your WireGuard port as the "Endpoint" (e.g. `mydomain.duckdns.org:51888`)
- Enter `192.168.1.0/24` inside "AllowedIPs"
- Click the save icon in the top-right corner of the screen
- Back on your Pi, run `sudo nano /etc/wireguard/wg0.conf` and add the following lines at the end of the file:
```
[Peer]
PublicKey = <android_client_public_key>
PresharedKey = <android_client_preshared_key>
AllowedIPs = 10.0.0.2/32
```
- You should now be able to connect!

## Windows client
- On your Pi, create a pre-shared key [for additional security](https://www.wireguard.com/protocol/#key-exchange-and-data-packets) by running `wg genpsk | sudo tee /etc/wireguard/windows_psk.key && sudo chmod go= /etc/wireguard/windows_psk.key`
- You should receive a single line of base64-encoded output, which is the pre-shared key. Take note of it
- On your Windows client, install [WireGuard](https://www.wireguard.com/) and open it
- Click the downward arrow next to "Add Tunnel" in the bottom-left corner of the screen and then "Add empty tunnel..."
- Enter a meaningful name for your new WireGuard tunnel
- Take note of your public key, you'll need it later
- Edit the text area to include the following lines:
```
[Interface]
PrivateKey = <windows_client_private_key>
Address = 10.0.0.3/24
DNS = 192.168.1.150

[Peer]
PublicKey = <pi_public_key>
PresharedKey = <windows_client_preshared_key>
Endpoint = mydomain.duckdns.org:51888
AllowedIPs = 192.168.1.0/24
```
- Click "Save"
- Back on your Pi, run `sudo nano /etc/wireguard/wg0.conf` and add the following lines at the end of the file:
```
[Peer]
PublicKey = <windows_client_public_key>
PresharedKey = <windows_client_preshared_key>
AllowedIPs = 10.0.0.3/32
```
- You should now be able to connect!