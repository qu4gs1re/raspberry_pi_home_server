After installing Pi-hole and Unbound, we need to make it so every device connected to our network is automatically set up to use our Pi as its DNS resolver.

To do this, we need to log into our router's settings and edit its DHCP configuration.

Every router is different, so you'll have to do this on your own. Just remember to set your Pi's IP (mine is `192.168.1.150`) as both the primary **AND** secondary DNS server.

After setting your DNS IP, just reconnect from all your devices to receive the new DHCP settings.