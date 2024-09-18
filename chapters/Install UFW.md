The Uncomplicated Firewall (UFW) is a frontend for iptables and is particularly well-suited for host-based firewalls. In this section, we're going to install UFW and define which services are allowed to reach our Pi.

- Run `sudo apt install -y ufw`
- Enable the ufw service by running `sudo ufw enable`
- Let's set up our default policies:
```
sudo ufw default deny incoming
sudo ufw default allow outgoing
```
- Finally, we can add our rules:
```
### SSH ###
sudo ufw allow from 192.168.1.0/24 to any port 2222 proto tcp

### Pi-hole DNS ###
sudo ufw allow from 192.168.1.0/24 to any port 53

### Pi-hole web interface ###
sudo ufw allow from 192.168.1.0/24 to any port 80 proto tcp

### Pi-hole FTL ###
sudo ufw allow from 127.0.0.0/8 to any port 4711 proto tcp

### Unbound ###
sudo ufw allow from 127.0.0.0/8 to any port 5335

### Samba ###
sudo ufw allow from 192.168.1.0/24 to any app Samba

### Kodi (remote control) ###
sudo ufw allow from 192.168.1.0/24 to any port 8080 proto tcp
sudo ufw allow from 192.168.1.0/24 to any port 9777 proto tcp

### NUT ###
sudo ufw allow from 127.0.0.0/8 to any port 3493 proto tcp

### WireGuard ###
sudo ufw allow to 0.0.0.0/0 port 51888 proto udp
```

The above rules combined with the default policies should only allow IPv4 connections. Incoming IPv6 connections will just be blocked. 

## Useful commands
- You can remove every configured rule by running `sudo ufw reset`
- You can temporarily disable UFW by running `sudo ufw disable`
- `sudo ufw app list` allows you to list the currently registered apps so you can allow/deny them instead of individual ports
- `sudo ufw status numbered` returns a numbered list of all configured rules
- `sudo ufw status verbose` returns a list of all configured rules, including logging info, default policies and new profiles
- `sudo ufw delete <number>` allows you to delete a rule by number

## References
- [Pi-hole's required ports documentation](https://docs.pi-hole.net/main/prerequisites/#ports)
- [unbound - Pi-hole documentation](https://docs.pi-hole.net/guides/dns/unbound/)
- [Yatse - Configure Kodi for remote control access](https://yatse.tv/wiki/configure-kodi-remote-control-access)