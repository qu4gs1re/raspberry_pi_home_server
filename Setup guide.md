This guide will walk you through the entire setup process, from the hardware connections to the software configuration.

## What are we doing, again?
First of all, we're building a Raspberry Pi-powered home setup.

This is the plan:
- [DietPi](https://dietpi.com/), a minimal Debian-based distro for SBCs, as the operating system
- [Pi-hole](https://pi-hole.net/) to block ads and trackers network-wide
- [Unbound](https://nlnetlabs.nl/projects/unbound/about/) to resolve hostnames by querying the root name servers directly, improving our internet privacy
- [WireGuard](https://www.wireguard.com/) so we can connect to our home network when we're away
- [Samba](https://www.samba.org/) for network file sharing
- [Kodi](https://kodi.tv/) to watch movies, TV series, etc.

Additionally, we will also need:
- [A script that automatically updates our Pi-hole blacklists/whitelists](https://github.com/jacklul/pihole-updatelists)
- [A dynamic DNS](https://www.duckdns.org), so we can easily connect to our Pi without knowing our public IP address

While this may seem a lot for just a Pi, it's important to note we won't be using all of the services at the same time, all of them work perfectly fine on older/less powerful models and we can leverage DietPi's scripts to optimize each service for speed.

With that out of the way, let's focus on security for a bit, shall we? :)

We're not trying to secure the Pentagon here, but I tried to include some hardening anyway:
- All router ports should be closed, except for the one used by WireGuard
- WireGuard should be configured to use a non-standard port, like `51888`
- Prefer OpenSSH to Dropbear as it packs more features and is more widespread
- OpenSSH should be configured to [use a non-standard port](https://security.stackexchange.com/questions/189726/does-it-improve-security-to-use-obscure-port-numbers), like `2222`
- [SSH should use key-based authentication](https://security.stackexchange.com/questions/3887/is-using-a-public-key-for-logging-in-to-ssh-any-better-than-saving-a-password) with [root login disabled](https://askubuntu.com/questions/16178/why-is-it-bad-to-log-in-as-root)
- [Prefer Ed25519 over RSA](https://security.stackexchange.com/questions/90077/ssh-key-ed25519-vs-rsa) for keypair generation
- A firewall software such as [UFW](https://launchpad.net/ufw) should be installed
- Firewall rules should be as specific as possible
- IPv6 should be disabled since we're not going to use it
- Passwords should be stored safely in an offline password manager such as [KeePassXC](https://keepassxc.org/)

## Components
- [Raspberry Pi 5 (8 GB) with heat sinks](https://www.amazon.it/dp/B0CRQY2Z86)
- [GeeekPi Mini Tower Kit with power supply](https://www.amazon.it/dp/B0CW9DHY8S)
- [SanDisk 64 GB microSD](https://www.amazon.it/dp/B0B7NXBM6P)
- [micro HDMI to HDMI cable](https://www.amazon.it/dp/B07KSDB25X)
- [WD 2 TB Elements](https://www.amazon.it/dp/B06W55K9N6)
- [Telefunken 32" TV](https://www.amazon.it/dp/B0CSYVCJPY)
- [Eaton UPS 3S 550 DIN](https://www.amazon.it/dp/B082TGLD2Q)
- [Keyboard and mouse (if you don't already have them)](https://www.amazon.it/dp/B08DG6VFFX)

I went with the Raspberry Pi 5 (8 GB model) so I wouldn't have to worry about resources. 

Since the Pi will run 24/7 and Kodi is not very lightweight, I was a little worried about thermals, so I bought the GeeekPi Mini Tower Kit, which includes a huge heatsink and a sleek case for the Pi. This not only allows me to have better heat dissipation compared to regular fans but also makes dusting the Pi a little easier.

We don't need a huge microSD card for this setup, so I went with a relatively small one.

The Raspberry Pi only supports micro HDMI since model 4, so we have to buy a cable separately.

I don't want fancy RAID setups for this build, a single 2 TB HDD will suffice.

I bought a TV to use as a monitor for my Raspberry Pi for two reasons:
- 32" (or bigger) monitors with integrated speakers are usually difficult to come by, expensive and often curved, which I absolutely despise
- I needed a TV

Finally, what happens to our beloved Pi when the power goes out? What if there's a brownout? I don't want to find out (no pun intended).
A UPS (together with [NUT](https://networkupstools.org/)) will allow our Pi to gracefully shutdown after an outage and restart when the power comes back.
Remember: a UPS needs to always be connected to the power grid to preserve batteries. Plug it in and leave it to charge for 24 hours before using it. You can turn the battery backup on by holding the power button for one second. Always connect/disconnect the UPS to/from the power grid *after* turning the battery backup off.

As of June 2024, the total cost of the above components (excluding keyboard and mouse) is 451.23 €.

Refer to the [[Setup diagram.canvas|Setup diagram]] for the connections.

## Let's go!
1. [[Install DietPi]]
2. [[Add terminal shortcut (optional)]]
3. [[Install OLED display drivers (optional)]]
4. [[Set up DHCP reservation]]
5. [[Install KeePassXC]]
6. [[Add new users with sudo privileges]]
7. [[Install Samba]]
8. [[Install UFW]]
9. [[Configure SSH]]
10. [[Install Pi-hole and Unbound]]
11. [[Install WireGuard]]
12. [[Install NUT (UPS configuration)]]
13. [[Install Kodi]]

## Next steps
- Hosting a Minecraft server using [Crafty Controller](https://craftycontrol.com/)