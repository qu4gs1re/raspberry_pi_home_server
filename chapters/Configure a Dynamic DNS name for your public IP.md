Since we want to connect to our home network when we're away, we need to set up a dynamic DNS name. You *could* use your public IP, but chances are your public IP is dynamic, which means it changes from time to time, so having a DNS name tied to your ever-changing IP is surely convenient.

- Go to [DuckDNS](https://www.duckdns.org/)
- Sign in using your preferred method (they rely on third-party authentication)
- Complete the CAPTCHA if you're asked to
- Write your domain name (e.g. `mydomain`) and click "add domain"
- Take note of your update token
- On your Pi, run `sudo dietpi-ddns`
- Make sure "DuckDNS" is selected and hit <kbd>Enter</kbd>
- Enter your domain name (e.g. `mydomain.duckdns.org`) and hit <kbd>Enter</kbd>
- Enter your update token and hit <kbd>Enter</kbd>
- Enter your update token again to confirm and hit <kbd>Enter</kbd>
- Review your settings and change anything if needed
- Make sure the "Apply" option is selected and hit <kbd>Enter</kbd>
- Select "Exit" and hit <kbd>Enter</kbd>