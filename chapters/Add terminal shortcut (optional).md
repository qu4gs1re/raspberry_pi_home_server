This step is optional but convenient and doesn't take much.

- Run `nano ~/.config/openbox/lxde-rc.xml`
- Add the following lines at the end of the `<keyboard>` section:
```xml
<!-- Launch LXTerminal -->
<keybind key="C-A-t">
  <action name="Execute">
    <command>lxterminal</command>
  </action>
</keybind>
```
- Save and close the file
- Run `sudo reboot` to apply changes