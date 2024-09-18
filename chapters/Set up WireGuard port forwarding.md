Since WireGuard allows you to connect to your home network from the Internet, you need to tell your router that incoming connections on the port used by WireGuard should be allowed and forwarded to your WireGuard server (i.e. your Pi), otherwise they'll be automatically rejected and you won't be able to connect.

Every router is different, so you'll have to do this on your own.

- Look for a section called "Port forwarding" or "Virtual servers"
- Create a new forwarding rule
- Enter a meaningful name for your forwarding rule, like "dietpi-wireguard"
- Your internal IP should be set to your Pi's IP (mine is `192.168.1.150`)
- Your internal and external ports should be set to the same value (mine is `51888`)
- Your protocol should be set to `UDP`
- Make sure the forwarding rule is enabled before saving