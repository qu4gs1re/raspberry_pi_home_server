Pi-hole is a software that blocks ads and trackers across your entire network. It works by capturing your DNS requests and filtering them based on rules.

We will pair our Pi-hole installation with Unbound, which is a validating, recursive, caching DNS resolver. It is designed to be fast and lean and incorporates modern features based on open standards. 

Basically, our DNS requests will be sent to Pi-hole, which will either block (if they take us to malicious domains) or allow them. If a particular request is allowed, Unbound will be used to resolve it.

- Run `sudo dietpi-software`
- Select "Browse Software"
- Scroll down and select "Pi-hole: block adverts for any device on your network" using the <kbd>Spacebar</kbd>
- Select "Unbound: validating, recursive, caching DNS resolver" using the <kbd>Spacebar</kbd> and press <kbd>Enter</kbd> to confirm
- Pi-hole needs a static IP to work. Since we already reserved the `192.168.1.150` address, we can skip this step by hitting <kbd>Enter</kbd>
- Select "Install", press <kbd>Enter</kbd> and then choose "OK"
- Select your preferred webserver based on your needs. I'm going to accept the default option (Apache) and hit <kbd>Enter</kbd>
- After some time, you'll be greeted by the Pi-hole automated installer. Hit <kbd>Enter</kbd> to skip the first two info screens
- The installer will warn you again about the static IP requirement. Select "Continue" and hit <kbd>Enter</kbd>
- In the "Upstream DNS Provider" selection screen, scroll down to "Custom" and hit <kbd>Enter</kbd>
- Enter `127.0.0.1#5335` and hit <kbd>Enter</kbd>
- Review your settings and hit <kbd>Enter</kbd> to confirm
- Hit <kbd>Enter</kbd> to use the default blocklist. We will add more blocklists later
- Make sure "Show everything" is selected and hit <kbd>Enter</kbd>
- No need to take note of your password as we will change it anyway, just hit <kbd>Enter</kbd>
- Make sure "NO" is selected when the installer asks you to block public access to the Pi-hole admin panel (we're already doing it on our own) and hit <kbd>Enter</kbd>
- Run `sudo pihole -a -p`, enter your new Pi-hole password and make sure it's **STRONG**
- Visit `192.168.1.150/admin` from within your web browser
- Enter your password and click "Log in"
- If it works, congrats!
- The final step is to [[Set Pi's IP as DNS in router DHCP config|set the Pi's IP address as the DNS server in your router's DHCP config]]

## Third-party lists (optional but recommended)

In order to increase Pi-hole's effectiveness, we have to download more blocklists and also whitelists. We won't be doing it manually, there's already a handy script that will download third-party lists and even keep them updated for us, so don't worry!

- Run `wget -O - https://raw.githubusercontent.com/jacklul/pihole-updatelists/master/install.sh | sudo bash`
- Run `sudo nano /etc/pihole-updatelists.conf`
- Make sure to set these variables as follows:
```
ADLISTS_URL="https://v.firebog.net/hosts/lists.php?type=tick"

WHITELIST_URL="https://raw.githubusercontent.com/anudeepND/whitelist/master/domains/whitelist.txt"

BLACKLIST_URL="https://big.oisd.nl"

REGEX_BLACKLIST_URL="https://raw.githubusercontent.com/mmotti/pihole-regex/master/regex.list"
```
- Save and close the file
- Run `sudo reboot` to apply changes
- You should now see about 900k domains in your Pi-hole admin webpage
- You can add more lists, if you want!

## Testing websites
- https://canyoublockit.com/extreme-test/
- https://d3ward.github.io/toolz/adblock.html
- https://fuzzthepiguy.tech/adtest/