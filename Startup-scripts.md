AirPort firmware is rebuilt at boot, so any changes made to the system will be cleared when rebooting. The configuration is stored in another partition, mounted at `/mnt/Flash`. Anything in there stays there across reboots. On (at least) the AirPort Extreme (802.11ac, version 7.7.8) there are two symbolic links in the `/etc` directory:

```
airport-extreme# ls -la /etc
...
lrwxr-xr-x   1 root  wheel     19 Aug 30  2016 rc.local -> /mnt/Flash/rc.local
lrwxr-xr-x   1 root  wheel     28 Aug 30  2016 rc.shutdown.local -> /mnt/Flash/rc.shutdown.local
...
```

`/etc/rc.local` is run at boot, and is a symbolic link to `/mnt/Flash/rc.local`.  (`/etc/rc.shutdown.local` is run by the `shutdown` command - at system shutdown.) This means we can add a script there and it will run when the device boots - even with the rest of the system being wiped.

## Setup startup scripts

**Please read this!** Before doing this, explore your AirPort device at the terminal first. If you don't know exactly what you're doing, don't do this! The reason I'm saying that is because I don't know if there is any way to get stuff back off `/mnt/Flash` without accessing the terminal - and you can quite easily just stop `sshd` or something. *The reset button may work, but I haven't tried it.* (Also if a script shuts your AirPort device down, you may not even have a chance to press the reset button anyway.)

~~Also, this will create a potential security risk. It means your AirPort base station will automatically run whatever is on your USB drive. **There is currently no validation of the source of any script.** *Remember Autoplay?*~~

As of https://github.com/samuelthomas2774/airport/commit/490c08bba443fd0a5458055777fdf639dcc93483 there is security against anyone connecting a USB drive and having scripts run on your AirPort device at startup.

You must have a USB drive containing [this repository][samuelthomas2774/airport] connected to your AirPort device for this.

1. Connect to the AirPort device using [SSH](SSH)
2. Check if this will work  
    Type `ls -la /etc` into the terminal.  
    These lines must appear in the output: (The order, dates, etc ... doesn't matter, but the paths must match exactly. Or at least be somewhere else on `/mnt/Flash` (then change the path in step 4).)
    ```
    lrwxr-xr-x   1 root  wheel     19 Aug 30  2016 rc.local -> /mnt/Flash/rc.local
    lrwxr-xr-x   1 root  wheel     28 Aug 30  2016 rc.shutdown.local -> /mnt/Flash/rc.shutdown.local
    ```
    Try updating your device if these symbolic links don't exist. This was found on an AirPort Extreme 802.11ac running version 7.7.8.
3. Mount the USB drive  
    The disk (on my AirPort Extreme) seems to be at `/dev/dk0`, and is only mounted when at least one person is connected to it over SMB/AFP. When it is mounted it is mounted at `/Volumes/dk0`. If it has a different name or mount point you'll have to change the file you're about to copy over.  
    To find the name / mount point of the disk, connect to it over SMB and type `df` into the terminal.
    ```sh
    mount /dev/dk0 /Volumes/dk0 # ... or connect over SMB
    ```
4. Copy [AirPort-run.sh][] as rc.local, [AirPort-rc.local.sh][] and [AirPort-run-on-ifup.sh][] to `/mnt/Flash`  
    This assumes that you've cloned the whole repository to `/airport` on the USB drive.
    ```
    cp /Volumes/dk0/airport/startup-scripts/AirPort-rc.local.sh /mnt/Flash/rc.local
    cp /Volumes/dk0/airport/startup-scripts/AirPort-rc.local.sh /mnt/Flash/rc.local
    cp /Volumes/dk0/airport/startup-scripts/AirPort-rc.local.sh /mnt/Flash/rc.local
    ```
5. To set up security against someone connecting a USB drive to your AirPort base station, run [setup-security.sh][]:
    ```
    /Volumes/dk0/airport/startup-scripts/setup-security.sh
    ```

[samuelthomas2774/airport]: https://github.com/samuelthomas2774/airport
[AirPort-rc.local.sh]: https://github.com/samuelthomas2774/airport/blob/master/startup-scripts/AirPort-rc.local.sh
[AirPort-run.sh]: https://github.com/samuelthomas2774/airport/blob/master/startup-scripts/AirPort-run.sh
[AirPort-run-on-ifup.sh]: https://github.com/samuelthomas2774/airport/blob/master/startup-scripts/AirPort-run-on-ifup.sh
[setup-security.sh]: https://github.com/samuelthomas2774/airport/blob/master/startup-scripts/setup-security.sh

Now when your AirPort base station starts up it will mount `/dev/dk0` and try to run `/AirPort-startup.sh` at it's root. `/dev/dk0` will be unmounted after as otherwise it seems to break disk sharing, so copy any files to somewhere on the root partition first.

The file you copied to `/mnt/Flash/rc.local` won't run without a USB drive (as all it does is try to run something from it), so provides a fallback if you break anything. Just remove the USB drive and restart your AirPort device and it will startup as normal.

If you want to be able to edit startup scripts remotely, just set `/AirPort-startup.sh` on your USB drive to run another script at `/Shared`, or preferably, your own directory in `/Users` (`/Users/{username}`). For example, with the username `S.Elliott`:

```sh
#!/bin/sh
./Users/S.Elliott/AirPort-startup.sh

```

As that script is so short, you could easily add it over SSH, even with no text editor:

```sh
echo "#!/bin/sh" > /Volumes/dk0/AirPort-startup.sh
echo "./Users/S.Elliott/AirPort-startup.sh" >> /Volumes/dk0/AirPort-startup.sh
```