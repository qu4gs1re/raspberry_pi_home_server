This step is optional but if you don't do it your cute little display won't work :(

- Open the terminal and run `sudo dietpi-config`
- Choose "4: Advanced Options"
- Scroll down to "I2C state" and press <kbd>Enter</kbd> to enable it
- After that, keep choosing "Exit" until you're asked to reboot the system and then choose "OK"
- Open the terminal again and run `sudo apt update` and then `sudo apt upgrade -y` for good measure
- Now run `sudo i2cdetect -y 1`
- If you see "3c" in the chart, it means the display has been recognized by the Pi. If you don't see anything, too bad!
- Run `sudo apt install -y python3 python3-pip python3-pil libjpeg-dev zlib1g-dev libfreetype6-dev libopenjp2-7 python3-dev build-essential` to install dependencies (`python3` and `libopenjp2-7` should be already installed)
- Run `sudo usermod -a -G gpio,i2c dietpi` to add the `dietpi` user to the `gpio` and `i2c` groups
- Install git by running `sudo apt install -y git`
- Download the necessary files from the GeeekPi repository and install:
```
git clone https://github.com/geeekpi/luma.examples.git
cd luma.examples/
sudo -H pip3 install -e . --break-system-packages
sudo -H pip3 install psutil --break-system-packages
sudo -H pip3 install netifaces --break-system-packages
```
- Now we need to edit the display script a little. Run `nano sys_info.py`
- Import the `netifaces` library by adding the following line next to the other imports:
```python
import netifaces as ni
```
- Delete the `cpu_usage()` function and replace it with the following code:
```python
def uptime_temp():
    uptime = datetime.now() - datetime.fromtimestamp(psutil.boot_time())
    temp = psutil.sensors_temperatures()['cpu_thermal'][0].current
    return "Up: %s\nTemp: %s °C" \
        % (str(uptime).split('.')[0], round(temp))
```
- Delete the `network()` function and replace it with the following code:
```python
def network(iface):
    ip_info = ni.ifaddresses(iface)[ni.AF_INET][0]
    ip_address = ip_info['addr']
    netmask = ip_info['netmask']

    netmask_parts = netmask.split('.')
    cidr = sum(bin(int(part)).count('1') for part in netmask_parts)

    return "\n%s: %s/%s" % (iface, ip_address, cidr)
```
- Inside the `mem_usage()` function, change `"Mem: %s %.0f%%"` to `"\nMem: %s %.0f%%"`
- Inside the `disk_usage()` function, change `"SD:  %s %.0f%%"` to `"\nSD: %s %.0f%%"`
- Inside the `stats()` function, change `cpu_usage()` to `uptime_temp()` and `wlan0` to `eth0`
- Save and close the file
- Now we need to add this file as a service. Run `sudo nano /etc/systemd/system/minitower_oled.service`
- Add the following lines to the file:
```markdown
[Unit]
Description=Minitower OLED service

[Service]
Type=forking
User=dietpi
ExecStart=/bin/bash -c 'sudo python3 /home/dietpi/luma.examples/examples/sys_info.py &'
Restart=always
RestartSec=30

[Install]
WantedBy=multi-user.target
```
- Save and close the file
- Let's test it:
```
sudo systemctl daemon-reload
sudo systemctl enable minitower_oled.service
sudo systemctl start minitower_oled.service
```
- The OLED display should turn on
- Let's run `sudo reboot` and see if it automatically starts
- If it does and you see something like the image below, give yourself a pat on the back :)
  ![OLED display](img/oled_display.jpeg)