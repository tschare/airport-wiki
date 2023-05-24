You can enable SSH on AirPort devices by setting the `dbug` property to `0x3000`.

### With [node-acp](node-acp):

node-acp is preferred as it supports full session encryption and using a hostname or IPv6 address.

```sh
acp --host {hostname} --password {password} setprop dbug 0x3000
```

`{hostname}` should be the hostname or IP address of the AirPort device.  
`{password}` should be the admin password of the AirPort device.

Example:

```sh
acp --host airport-extreme.local --password password setprop dbug 0x3000
acp --host airport-extreme.local --password password reboot
```

### With [AirPyrt](AirPyrt):

```sh
python -m acp -t {ipv4-address} -p {password} --setprop dbug 0x3000
```

`{ipv4-address}` should be the IPv4 address of the AirPort device.  
`{password}` should be the admin password of the AirPort device.

Remember you must restart the device to change any settings.

Example:

```sh
python -m acp -t 10.0.1.1 -p password --setprop dbug 0x3000
python -m acp -t 10.0.1.1 -p password --reboot
```
