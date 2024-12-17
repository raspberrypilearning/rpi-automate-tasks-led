## Run on boot

You can use `systemd` to run your code when Raspberry Pi starts up.

**Note** `systemd` is only available from Jessie versions of the Raspbian OS and onward.

--- task ---

Open Terminal and type:

```bash
sudo nano /etc/systemd/system/blink_on_start.service
```

--- /task ---

--- task ---

Define a new unit 

Set the `Description` to 'Blink LED on start' and set it to run after the multi-user environment is available. 

```bash
[Unit]
Description=Blink LED on start
After=multi-user.target
```

--- /task ---

--- task ---

Configure the service.

1. Set the `Type` to 'idle' so the 'ExecStart' command runs only when everything else has loaded.

1. Set the `ExecStart` parameter to the command to run.

1. Set the `Environment` parameter to allow the sound player to find the pulse audio server.

```bash
[Unit]
Description=Blink LED on start
After=multi-user.target

[Service]
Type=idle
ExecStart=/home/username/blink.sh
Environment="PULSE_RUNTIME_PATH=/run/user/1000/pulse/"
```

**Note**: Change `username` to your username.

--- /task ---

--- task ---

Add a `WantedBy` directive set to 'multi-user.target'. This creates a directory called multi-user.target.wants in /etc/systemd/system, with a link to your unit.

```bash
[Unit]
Description=Blink LED on start
After=multi-user.target

[Service]
Type=idle
ExecStart=/home/username/blink.sh
Environment="PULSE_RUNTIME_PATH=/run/user/1000/pulse/"

[Install]
WantedBy=multi-user.target
```
--- /task ---

--- task ---

Save and exit nano by pressing `Ctrl + x` and then press `y` and `Enter` when you are prompted to save.

--- /task ---

--- task ---

In Terminal, tell systemd to enable your new service.

```bash
sudo systemctl daemon-reload
sudo systemctl enable blink_on_start.service
```

--- /task ---

--- task ---

You can check the status of your service using:
```bash
systemctl status blink_on_start.service
```

--- /task ---

--- task ---

**Test**: your service runs when you reboot your Raspberry Pi.

```bash
sudo reboot
```

--- /task ---

Restore the default behaviour for the activity LED. 

--- task ---

Typically, this is set to `mmc0` (for SD card activity).

Open Terminal and type:

```bash
echo mmc0 | sudo tee /sys/class/leds/ACT/trigger
```
--- /task ---

### Disable your service.

--- task ---

So your blink script does not run every time your Pi starts up, disable it with:

```bash
sudo systemctl disable blink_on_start.service
```

You will see that disabling your unit removes the link and the dependency relationship that indicates that the service should be started automatically.

--- /task ---
