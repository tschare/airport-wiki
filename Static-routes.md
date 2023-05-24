As we can now [create a script that will run when your AirPort base station starts up](Startup-scripts), we can do something useful -  for example, setting static routes. That is, routing an IP address, or a block of IP addresses, to another destination. You may want to do this, for example, if you have setup a VPN server on your local network.

You can setup routes using the `route` command. As the `man` command isn't available on AirPort firmware, you'll need to [go here instead](http://netbsd.gw.com/cgi-bin/man-cgi?route++NetBSD-current) to find how to use the `route` command.

For example, to route everything in `2001:db8:0:1::/64` to a gateway at `2001:db8:0:1::`, you'd use a command like this:

```sh
route add -inet6 2001:db8:0:1::/64 2001:db8:0:1::
```

Execute that (with your own IP addresses) on your AirPort base station, and everything going to an IP address in `2001:db8:0:1::/64` will go to `2001:db8:0:1::`. But only until you shut down (or restart) your AirPort base station. To make it persist, it needs to be run every time the device starts up.

To do that, instead of running it manually over SSH, you can add it to `/AirPort-startup.sh` on the USB drive connected to your AirPort base station and it will be run every time the device starts up (and the USB drive is connected). [You will have to have setup your device to do this first though.](Startup-scripts)

However, `/etc/rc.local` is called *before* the network is completely up. So we need to wait a second first. Using `ifwatchd` we can have a script run when the network interface becomes active. In [startup-scripts][] there is a file called [AirPort-run-on-ifup.sh][], which will run `ifwatchd` and call a script once and filter IPv4 / IPv6 addresses.

For example, with this repository in `/airport` on your USB drive:

[startup-scripts]: https://github.com/samuelthomas2774/airport/tree/master/startup-scripts
[AirPort-run-on-ifup.sh]: https://github.com/samuelthomas2774/airport/blob/master/startup-scripts/AirPort-run-on-ifup.sh

```sh
cp /Volumes/dk0/AirPort-routes-inet6.sh /AirPort-routes-inet6.sh
/Volumes/dk0/airport/startup-scripts/AirPort-run-on-ifup.sh bridge0 /AirPort-routes-inet6.sh inet6

# Assuming the following exists in /Volumes/dk0/AirPort-routes-inet6.sh:
#!/bin/sh
route add -inet6 2001:db8:0:1::/64 2001:db8:0:1::
```

`bridge0` is the interface we're waiting for, `/AirPort-routes-inet6.sh` is the script to run when the interface is up and `inet6` means we're waiting for specifically an IPv6 address (because we're setting up an IPv6 route). You could replace `inet6` with `inet4` if you're setting up an IPv4 route.

Now we can add that to `/AirPort-startup.sh`:

```sh
echo "cp /Volumes/dk0/AirPort-routes-inet6.sh /AirPort-routes-inet6.sh" >> /Volumes/dk0/AirPort-startup.sh
echo "/Volumes/dk0/airport/startup-scripts/AirPort-run-on-ifup.sh bridge0 /AirPort-routes-inet6.sh inet6" >> /Volumes/dk0/AirPort-startup.sh
```

If there's nothing already in `/AirPort-startup.sh`, that will create it. If it already exists, check if there's a new line at the end first (and that nothing stops the script before). As said in [Startup scripts](Startup-scripts), you may prefer to have that script run another script in a directory you can access over SMB/AFP. In that case, add that line to whichever file `/AirPort-startup.sh` runs.
