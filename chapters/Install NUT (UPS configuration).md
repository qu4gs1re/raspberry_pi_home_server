Network UPS Tools (NUT) is a collection of programs that monitor and administer UPS, PDU and SCD hardware over the network. It supports various UPS models, access control, status logging, web interface and more.

First things first, I had to choose a good and cheap UPS that was supported by NUT. To do this, I used the [hardware compatibility list](https://networkupstools.org/stable-hcl.html) to look for a decently supported UPS that could be administered over USB, as network-enabled UPSs are more expensive.
The [Eaton 3S 550](https://networkupstools.org/ddl/Eaton/3S_550.html) was exactly what I needed: best support level, USB port and around 80 € on Amazon (as of June 2024).

Our goal is to connect our Pi to the UPS using both a USB cable and a power plug. This way, the Pi can communicate with the UPS to detect when the power goes out and it's running on battery. If the power goes out, the Pi will wait a few minutes to see if it comes back on. If it doesn't, the Pi will send a shutdown command to the UPS and then shutdown itself. When power is restored, the Pi will automatically turn on.

What if the power comes back during the shutdown, but before the UPS powers off? The UPS won't reboot and our Pi will stay off. How can we fix that?
Fortunately, [NUT has got our back](https://networkupstools.org/docs/FAQ.html#_i_8217_m_facing_a_power_race). We can simply add the `POWEROFF_WAIT` directive in our [nut.conf](https://networkupstools.org/docs/man/nut.conf.html) file. The `POWEROFF_WAIT` directive will make our Pi wait for a specified amount of time for the UPS to cut power after sending the shutdown signal. If the UPS doesn't cut power, it means the power has returned, and the Pi will just reboot.

## Let's get down to business
- First, install NUT by running `sudo apt install -y nut`
- Run `sudo nut-scanner -U` to list all NUT-compatible USB devices currently plugged in
- You should see something like this:
```
[nutdev1]
        driver = "usbhid-ups"
        port = "auto"
        vendorid = "0463"
        productid = "FFFF"
        product = "Eaton 3S"
        serial = "Blank"
        vendor = "EATON"
        bus = "003"
```
- Take note of the `driver`, `port`, `vendorid` and `productid`
- Run `sudo nano /etc/nut/ups.conf` and create a new section at the end of the file:
```
[eaton3s550]
    driver = "usbhid-ups"
    port = "auto"
    vendorid = "0463"
    productid = "FFFF"
    desc = "Eaton 3S 550"
    # time (in seconds) to wait before the ups cuts power
    #
    # you can also edit the 'ondelay' time, which is the
    # delay before the ups powers the load after power is restored
    offdelay = 90
    
```
- Save and close the file
- Start the UPS driver by running `sudo upsdrvctl start` and make sure it doesn't report any errors. If the driver doesn't start cleanly, make sure you have picked the right one for your hardware. In our case, it's [usbhid-ups](https://networkupstools.org/docs/man/usbhid-ups.html)
- Our Pi is the only computer connected to the UPS, so we'll be running in standalone mode. Run `sudo nano /etc/nut/nut.conf`, replace `MODE=none` with `MODE=standalone` and then add the following line at the end of the file:
```
POWEROFF_WAIT=300s
```
- Save and close the file
- Start the network data server by running `sudo upsd` and make sure it is able to connect to the driver. If it doesn't, check your `ups.conf` file
- Make sure that the UPS is providing good status data by running `upsc eaton3s550@localhost ups.status`. You should just see `OL` in the response, which means your system is running on line power. If it says something else (like `OB` - on battery, or `LB` - low battery), your driver was probably misconfigured: use `sudo upsdrvctl stop` to stop it before reconfiguring it and then start it again after you're done
- You can also look at all of the status data by running `upsc eaton3s550@localhost`. The NUT documentation contains [a comprehensive list of variables](https://networkupstools.org/docs/developer-guide.chunked/apas02.html) with descriptions and example values
- Run `sudo nano /etc/nut/upsd.users` and create a new NUT user called `monuser`. `upsmon` will connect to `upsd` and authenticate with these credentials:
```
[monuser]
    password = <**STRONG** password>
    upsmon primary
```
- Save and close the file
- Reload `upsd` by running `sudo upsd -c reload`
- If that doesn't work, just do the following:
```
sudo upsd -c stop
sudo upsd
```
- Create a new system user called `nutmon`. We're doing this so we're not forced to run `upsmon` as `root`, which is not secure.
    - Run `sudo adduser nutmon`
    - Enter a **STRONG** password
    - You'll be asked to fill in some information about the new user. It's fine to accept the defaults and leave everything blank
    - Add your newly created user to the `nut` group by running `sudo usermod -a -G nut nutmon`
- We're going to [make `upsmon` call `upssched`](https://networkupstools.org/docs/user-manual.chunked/Advanced_usage_scheduling_notes.html#_the_advanced_approach_using_upssched) when our Pi changes state (e.g. from `ONLINE` to `ONBATT`) so we can perform an early shutdown and other cool stuff. Run `sudo nano /etc/nut/upsmon.conf` and add the following at the end of the file:
```
RUN_AS_USER nutmon

NOTIFYCMD /usr/sbin/upssched

NOTIFYMSG ONLINE "UPS %s - on line power"
NOTIFYMSG ONBATT "UPS %s - on battery"
NOTIFYMSG LOWBATT "UPS %s - battery is low"

NOTIFYFLAG ONLINE SYSLOG+WALL+EXEC
NOTIFYFLAG ONBATT SYSLOG+WALL+EXEC
NOTIFYFLAG LOWBATT SYSLOG+WALL+EXEC

MONITOR eaton3s550@localhost 1 monuser <password> primary
```
- Save and close the file
- Run `sudo nano /etc/nut/upssched.conf`
- Change the following lines from this:
```
# PIPEFN /run/nut/upssched/upssched.pipe
# LOCKFN /run/nut/upssched/upssched.lock
```
- to this:
```
PIPEFN /var/lib/nut/upssched.pipe
LOCKFN /var/lib/nut/upssched.lock
```
- Add the following lines at the end of the file:
```
AT ONBATT * START-TIMER onbatt 300
AT ONLINE * CANCEL-TIMER onbatt
AT LOWBATT * START-TIMER lowbatt 30
AT ONLINE * CANCEL-TIMER lowbatt
```
- Save and close the file
- Run `sudo nano /bin/upssched-cmd` and replace the default script with the following one:
```sh
case $1 in
    onbatt)
        logger -t upssched-cmd "The UPS has been on battery for over 5 minutes, forcing shutdown"

        /sbin/upsmon -c fsd
        ;;
    lowbatt)
        logger -t upssched-cmd "UPS battery level is low, forcing shutdown"

        /sbin/upsmon -c fsd
        ;;
    *)
        logger -t upssched-cmd "Unrecognized command: $1"
        ;;
esac
```
- Save and close the file
- Start `upsmon` by running `sudo upsmon`. If it complains about something, check your configuration
- Test the shutdown sequence by running `sudo upsdrvctl -t shutdown`. It will display the sequence without actually calling the drivers
- Now run `sudo upsmon -c fsd` to execute a full shutdown sequence, as presented in [Shutdown sequence](https://networkupstools.org/docs/user-manual.chunked/Configuration_notes.html#Shutdown_design), starting from the third step. If everything works correctly, the Pi will be forcibly powered off and the UPS will be switched off after `ups.delay.shutdown` seconds (we set it to 90). Hold down the UPS's power button for one second to switch it on again
- If you're feeling brave, you can test your setup by just pulling the plug. The Pi should run on battery for 5 minutes, then shut down. After `ups.delay.shutdown` seconds (we set it to 90), the UPS should switch off. If you plug the UPS back in, it should start after `ups.delay.start` seconds (which is 30 for our chosen UPS)

I couldn't get the `ups.delay.start` variable to work, unfortunately. I ran some tests and I think there's an issue with my UPS's firmware because even though the delay is set to 30 seconds by default, it powers the load immediately after power is restored. I also tried setting a custom value but nothing changed. It shouldn't be a big issue but if it is for you, invest on a better UPS :P

## References
- [NUT user manual](https://networkupstools.org/docs/user-manual.chunked/index.html)
- [NUT man pages](https://networkupstools.org/docs/man/index.html#User_man)
- [NUT FAQ](https://networkupstools.org/docs/FAQ.html)