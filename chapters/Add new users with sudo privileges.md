Since I need to be able to log into my Pi from two different devices, I'm going to add two new sudo-enabled users. You can add as many users as you need or you can use the default `dietpi` user, but it's better for each device to have its own user.

- Run `sudo adduser <username>`
- Enter a **STRONG** password
- You'll be asked to fill in some information about the new user. It's fine to accept the defaults and leave everything blank
- Run `sudo usermod -aG sudo <username>`