node-acp is an ACP client and server based on [AirPyrt](AirPyrt) written in JavaScript for Node.js. It also supports full session encryption.

### Installation

To install node-acp, run:

```
npm install --global node-acp
```

### Usage

#### Get property

```sh
acp getprop {property} --host {hostname} --password {password}
```

`{hostname}` should be the hostname or IP address of the AirPort device.  
`{password}` should be the admin password of the AirPort device.  
`{property}` should be the 4 character code to specify which property you want to get. See [ACP Properties](ACP-Properties) for more information.

Example:

```sh
acp getprop syNm --host airport-extreme.local --password password
AirPort Extreme
```

#### Set property

```sh
acp setprop {property} {value} --host {hostname} --password {password}
```

`{hostname}` should be the hostname or IP address of the AirPort device.  
`{password}` should be the admin password of the AirPort device.  
`{property}` should be the 4 character code to specify which property you want to set. See [ACP Properties](ACP-Properties) for more information.  
`{value}` should be the new value of the property.

Example:

```sh
acp setprop syNm AirPort\ Extreme --host airport-extreme.local --password password
```

#### Restart

```sh
acp --host {hostname} --password {password} reboot
```

`{hostname}` should be the hostname or IP address of the AirPort device.  
`{password}` should be the admin password of the AirPort device.  

Example:

```sh
acp --host airport-extreme.local --password password reboot
```