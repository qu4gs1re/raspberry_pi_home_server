For this setup, we need our Pi to always have the same IP address. This can be done through DHCP reservation or by assigning a static IP address. I'm going to configure DHCP reservation through my router's admin page since it's the easiest thing to do.

- Look for DHCP reservation in your router's settings
- Make sure the DHCP pool does not include your desired IP. If it does, change it. I changed my address pool to `192.168.1.2 - 192.168.1.100`
- Now you can reserve a DHCP address for you Pi. I chose `192.168.1.150`
- Save and exit
- Run `sudo reboot` to get the new IP