AirPyrt is a tool written in Python for controlling AirPort devices, including querying / updating [ACP properties](ACP-Properties).

GitHub: https://github.com/x56/airpyrt-tools

### Installation

To install AirPyrt, run:

```sh
git clone https://github.com/x56/airpyrt-tools.git
cd airpyrt-tools
python setup.py install --user

```

### Using

You need to use the IPv4 address to connect.

#### Get property

```sh
python -m acp -t {ipv4-address} -p {password} --getprop {property}

```

`{ipv4-address}` should be the IPv4 address of the AirPort device.  
`{password}` should be the admin password of the AirPort device.  
`{property}` should be the 4 character code to specify which property you want to get. See [ACP Properties](ACP-Properties) for more information.

Example:

```sh
python -m acp -t 10.0.1.1 -p password --getprop syNm
AirPort Extreme

```

#### Set property

```sh
python -m acp -t {ipv4-address} -p {password} --setprop {property} {value}

```

`{ipv4-address}` should be the IPv4 address of the AirPort device.  
`{password}` should be the admin password of the AirPort device.  
`{property}` should be the 4 character code to specify which property you want to set. See [ACP Properties](ACP-Properties) for more information.  
`{value}` should be the new value of the property.

Example:

```sh
python -m acp -t 10.0.1.1 -p password --setprop syNm AirPort\ Extreme

```

#### Restart

```sh
python -m acp -t {ipv4-address} -p {password} --reboot

```

`{ipv4-address}` should be the IPv4 address of the AirPort device.  
`{password}` should be the admin password of the AirPort device.

Example:

```sh
python -m acp -t 10.0.1.1 -p password --reboot

```