You can use [acp](acp-command), [node-acp](node-acp) or [AirPyrt](AirPyrt) to query / update ACP properties.

Some of the properties that I've said don't exist on the latest firmware may actually still exist but require a specific configuration to successfully return anything.

#### Types

These are the types supported in node-acp. As node-acp is based on AirPyrt most of them are supported in AirPyrt as well.

node-acp type | Description
--------|--------------
`mac`   | MAC address
`boo`   | Unsigned 8 bit integer where 0 means false and 1 means true
`ui8`   | Unsigned 8 bit integer
`u16`   | Unsigned 16 bit integer
~~`dec`~~| ~~Unsigned 8/16/32 bit integer - always writes as 32 bit~~
`u32`   | Unsigned 32 bit integer
`u64`   | Unsigned 64 bit integer (uses the JavaScript bigint type in node-acp)
`hex`   | Hexadecimal
`bin`   | Binary (this is normally used by node-acp when the format of a property is unknown or node-acp shouldn't decode it itself)
`cfb`   | CFL Binary Property List
`log`   | Null-separated log data
`str`   | String
`ip4`   | IPv4 address
`ip6`   | IPv6 address
`bpl`   | Binary Property List

TypeScript type definitions for (CFL) Binary Property Lists are included in node-acp. These all seem to match the format of the properties in AirPort Utility backups.

#### Backup Types

This is the type used in the backup XML property list created by AirPort Utility.

General
---

Name   | Description                | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`raMA` | Wi-Fi 5GHz MAC address     | `mac` | String [MAC address]|
`laMA` | Ethernet MAC address       | `mac` | String [MAC address]|
`waMA` | Ethernet MAC address       | `mac` | String [MAC address]|
`syNm` | Base Station Name          | `str` | String        |
`syPW` | Base Station Password      | `str` | String        | The password is stored in plain text. No encryption / hashing.
`syCt` | Contact                    | -  \* | String        | Used in older versions of AirPort Utility.
`syLo` | Location                   | -  \* | String        | Used in older versions of AirPort Utility.
`syVs` | Version                    | `str` | String \*.\*.\*|
`SUEn` | Check for firmware updates | `boo` | Boolean       | `true` to automatically check for updates.
`SUFq` | Update frequency           | `u32` | Number        | `3600` - Hourly,<br /> `86400` - Daily,<br /> `604800` - Weekly,<br /> `2678400` - Monthly
`sySt` | Status                     | `cfl` | Dictionary    | An object containing an array with the key `problems`, containing objects containing a string with the key `code`, containing a 4-character code identifying the problem. See [AirPort status codes](AirPort-status-codes).
`sySN` | Serial Number              | `str` | String        |
`isAC` | Power                      | `boo` | Number        | `0` - Ethernet (802.3af),<br /> `1` - External AC Adapter
`leAc` | Status Light               | `u32` | Number        | `1` - Always On (default),<br /> `2` - Flash On Activity
`bsWC` | Allow configuration over WAN| - \* | Boolean       |
`bsSS` | Base Station Status        | -  \* |               |

\* Doesn't exist on latest firmware.

Product information
---

Name   | Description                | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`syDs` | Description                | `str` | String        | On my AirPort Extreme (802.11ac, version 7.7.8) this is set to `Apple Base Station V7.7.8`.
`syRe` | Country                    | `u32` | Number 0-54   |
`syAP` | Product Name               | `u32` | Hexadecimal   | `0` - AirPort,<br /> `1` - AirPort,<br /> `3` - AirPort Extreme with 802.11g,<br /> `102` - AirPort Express,<br /> `104` - AirPort Extreme with 802.11n (Fast Ethernet),<br /> `105` - AirPort Extreme with 802.11n (Gigabit Ethernet),<br /> `115` - AirPort Express 2nd generation,<br /> `120` - AirPort Extreme 6th generation (with 802.11ac)
`bsSP` | Product Name               | -  \* | Number        | `0` - AirPort Base Station,<br /> `3` - AirPort Extreme,<br /> `102` - AirPort Express

\* Doesn't exist on latest firmware.

Time
---

Name   | Description                | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`time` | Base Station Time          | `u32` | Hexadecimal   |
`timz` | Time Zone                  | `cfl` | Dictionary    | An object containing three elements - `zoneFile`, data, `zoneName`, a string containing the name of the timezone (e.g. Europe/London) and `zoneVersion`, a string containing the time the timezone information was last updated in the format `YYYYr` (e.g. `2017b`).
`ntSV` | Network Time Server        | -  \* | String        |
`bsNP` | Network Time Server        | -  \* | String        | `1` - time.apple.com,<br /> `2` - time.euro.apple.com,<br /> `3` - time.asia.apple.com
`bsTZ` | Time Zone                  | -  \* |               |

\* Doesn't exist on latest firmware.

Bonjour
---

Name   | Description                | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`wbEn` | Use wide-area Bonjour      | `boo` | Boolean       | `true` to enable wide-area Bonjour (shown as Dynamic Global Hostname in AirPort Utility.
`wbHN` | Wide-area Bonjour Hostname | `str` | String        |
`wbHU` | Wide-area Bonjour TSIG Key | `str` | String        | Name of the TSIG key for updating the wide-area Bonjour hostname.
`wbHP` | Wide-area Bonjour TSIG Pass| `str` | String        | `hmac-md5` TSIG key data for updating the wide-area Bonjour hostname.
`wbAC` | Wide-area Bonjour DNS-SD   | `boo` | Boolean       |

File Sharing
---

Name   | Description                | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`usrd` | File Sharing Users         | `cfl` | Dictionary    |
`MaSt` | Disks                      | `cfl` | Dictionary    |
`bsFS` | Enable file sharing        | -  \* | Boolean       |
`bsRF` | Share disks over WAN       | -  \* | Boolean       |
`bsWF` | Advertise disks using wide-area Bonjour|- \*|Boolean|
`bsGA` | Guest Access               | -  \* | Number        | `0` - Not allowed,<br /> `1` - Read only,<br /> `2` - Read and write
`bsFM` | Secure Shared Disks        | -  \* | Number        | `0` - With accounts,<br /> `1` - With a disk password,<br /> `2` - With base station password

\* Doesn't exist on latest firmware.

Printer Sharing
---

Name   | Description                | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`prnI` | USB Printer Name           | `str` | String        |
`prni` | USB Printers               | `cfb` | -             |
`prnR` | USB Printers               | `bin` |               | Unknown format
`bsRP` | Share printers over WAN    | -  \* | Boolean       |
`bsWP` | Advertise printers using wide-area Bonjour|- \*|Boolean|

\* Doesn't exist on latest firmware.

AirPlay
---

Name   | Description                | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`auNN` | AirPlay Speaker Name       | ?     | String        |
`auNP` | AirPlay Password           | `str` | String        |
`aWan` | Allow AirPlay over Ethernet| ?     | Boolean       |
`auRR` | Enable AirPlay             | `u16` | Boolean       | `0` - Disabled,<br /> `1` - Enabled
`auHK` | AirPlay 2 HomeKit pairing data|`bpl`| Boolean      |
`auHE` | Paired with HomeKit        | `boo` | ?             | This seems to control whether AirPort Utility shows "This AirPort Express is set up as a HomeKit accessory. ..." but doesn't affect `airtunesd`.

When setting up HomeKit pairing `auHE` will be set to `1` and the `HomeKit Accessories` and `HomeKit Enabled` properties of `auHK` will be set. `HomeKit Access Level` is only set when changed from the default. `auNP` is still used to set the AirPlay password when a HomeKit pairing isn't required.

When removing from a home `auHE` will be set to `0` and all entries will removed from `HomeKit Accessories` and `HomeKit Enabled` will be set to `false` in `auHK`.

Logging
---

Name   | Description                | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`slCl` | Syslog Destination         | -  \* | String        |
`slvl` | Level                      | `u32` | Hexadecimal   | `0` - Emergency,<br /> `1` - Alert,<br /> `2` - Critical,<br /> `3` - Error,<br /> `4` - Warning,<br /> `5` - Notice (default),<br /> `6` - Informational,<br /> `7` - Debug

\* Doesn't exist on latest firmware.

Simple Network Management Protocol
---

Name   | Description                | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`snCS` | SNMP Community String      | -  \* |               |
`bsSN` | Enable SNMP                | -  \* |               |
`bsSW` | Allow SNMP over WAN        | -  \* |               |

\* Doesn't exist on latest firmware.

Wi-Fi
---

Name   | Description                | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`WiFi` | Wi-Fi configuration        | `cfl` | Dictionary    | [Most Wi-Fi related settings are in this property.](#wi-fi-configuration)
`acTa` | Access Control             | -  \* |               |
`tACL` | Timed Access Control       | `cfl` | Dictionary    | Object containing an array with the key `entries`, containing objects with the keys `description`, string, `expiryTime`, number, `macAddress`, string [MAC address] and `wirelessAccessTimes`, array containing strings.
`wdLs` | WDS Nodes                  | -  \* |               |
`bsP1` | RADIUS server #1 Port      | -  \* | Number        | `1` - 1645,<br /> `2` - 1812
`bsP2` | RADIUS server #2 Port      | -  \* | Number        | `1` - 1645,<br /> `2` - 1812
`bsrT` | RADIUS Type                | -  \* | Number        | `0` - Default,<br /> `1` - Alternate
`bsSM` | Wireless Security          | -  \* | Number        | `1` - None,<br /> `2` - WEP 40 bit,<br /> `3` - WEP 128 bit,<br /> `4` - WPA Personal,<br /> `5` - WPA/WPA2 Personal,<br /> `6` - WPA Personal,<br /> `7` - WPA2 Personal,<br /> `8` - WEP (Transitional Security Network),<br /> `9` - WPA Enterprise,<br /> `10` - WPA/WPA2 Enterprise,<br /> `11` - WPA Enterprise,<br /> `12` - WPA2 Enterprise
`bsRC` | Channel                    | -  \* | Number        | ...,<br /> `1000` - Automatic
`bsAC` | Allow wireless clients     | -  \* | Boolean       |
`bsSK` | Wireless Password          | -  \* | String        |
`bsWM` | WDS Mode                   | -  \* | Number        | `0` - WDS not enabled,<br /> `1` - WDS main,<br /> `2` - WDS relay,<br /> `3` - WDS remote
`bsGK` | WPA Group Key Timeout      | -  \* |               |
`bsNM` | Wireless Mode              | -  \* | Number        | `0` - Create a wireless network,<br /> `1` - Join a wireless network,<br /> `3` - Off,<br /> `10` - Participate in a WDS network,<br /> `20` - Extend a wireless network
`bsAT` | MAC Address Access Control | -  \* | Number        | `0` - Not Enabled,<br /> `1` - Local,<br /> `2` - RADIUS,<br /> `3` - Timed Access
`bsDA` | Allow wireless clients     | -  \* | Boolean       |

\* Doesn't exist on latest firmware.

Wi-Fi configuration
---

These are not ACP properties - they are properties of the `WiFi` property. They probably were ACP properties before 5 GHz capable AirPort base stations though.

Name   | Description                | Type      | Backup Type   | Notes
-------|----------------------------|-----------|---------------|--------
`raNm` | Wi-Fi Network Name         | `string`  | String        | Or SSID
`raCl` | Closed network             | `boolean` | Boolean       | `true` if the SSID should not be broadcast.
`raKT` | WPA Group Key Timeout      | `number`  | Number        |
`raRO` | Use interference robustness| `boolean` | Boolean       | `true` to enable interference robustness.
`raWC` | Use wide channels          | `boolean` | Boolean       |
`raMu` | Multicast Rate             | `number`  | Number        | `1` - 1 Mbps,<br /> `2` - 2 Mbps,<br /> `85` - 5.5 Mbps,<br /> `6` - 6 Mbps,<br /> `9` - 9 Mbps,<br /> `17` - 11 Mbps,<br /> `18` - 12 Mbps,<br /> `24` - 18 Mbps,<br /> `36` - 24 Mbps
`raMd` | Radio Mode                 | `number`  | Number        | `1` - 802.11b only,<br /> `2` - 802.11b/g compatible,<br /> `3` - 802.11g only,<br /> `4` - 802.11a only,<br /> `5` - 802.11n (802.11a compatible),<br /> `6` - 802.11n (802.11b/g compatible),<br /> `7` - 802.11n only (2.4 GHz),<br /> `8` - 802.11n only (5 GHz)
`raPo` | Transmit Power             | `number`  | Number        | `10` - 10%,<br /> `25` - 25%,<br /> `50` - 50%,<br /> `100` - 100%
`raSt` | Wireless Network Mode      | `number`  | Number        | `0` - Create a wireless network,<br /> `1` - Join an existing wireless network,<br /> `3` - Wireless disabled
`dWDS` | Allow this network to be extended|`boolean`|           |
`raI1` | RADIUS server #1 IP address| `string`  | String [IPv4] |
`raSe` | RADIUS server #1 secret    | `string`  | String        |
`raI2` | RADIUS server #2 IP address| `string`  | String [IPv4] |
`raS2` | RADIUS server #2 Secret    | `string`  | String        |

Ethernet
---

`Code` | Name                       | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`waSD` | Ethernet WAN Port          | `ui8` | Number        | `0` - Automatic (default),<br /> `2` - 10 Mbps/Half Duplex,<br /> `3` - 10 Mbps/Full Duplex,<br /> `5` - 100 Mbps/Half Duplex,<br /> `6` - 100 Mbps/Full Duplex,<br /> `8` - 1000 Mbps/Half Duplex,<br /> `9` - 000 Mbps/Full Duplex,<br /> `15` - Ethernet Disabled

Wide Area Network
---

Name   | Description                | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`waCV` | Configure IPv4             | ?     | Number        | `768` - Using DHCP,<br /> `1024` - Manually
`waD1` | IPv4 DNS Server #1         | `ip4` | String [IPv4] |
`waD2` | IPv4 DNS Server #2         | `ip4` | String [IPv4] |
`waD3` | IPv4 DNS Server #3         | `ip4` | String [IPv4] | Set to `0.0.0.0` by default - a third DNS server is not sent by the DHCP server when set to something else.
`waC1` | Automatic IPv4 DNS Server #1|`ip4` | String [IPv4] | DNS server advertised by DHCP / PPPoE.
`waC2` | Automatic IPv4 DNS Server #2|`ip4` | String [IPv4] | DNS server advertised by DHCP / PPPoE.
`waC3` | Automatic IPv4 DNS Server #3|`ip4` | String [IPv4] |
`waIP` | WAN IPv4 Address           | `ip4` | String [IPv4] |
`waSM` | WAN Subnet Mask            | `ip4` | String [IPv4] |
`waRA` | WAN IPv4 Router Address    | `ip4` | String [IPv4] |
`waDC` | DHCP Client ID             | `str` | String        |
`waDN` | Search Domain              | `str` | String        |
`bsIC` | Connect Using              | -  \* | Number        | `1` - Ethernet,<br /> `2` - PPPoE,<br /> `3` - Modem,<br /> `4` - AirPort (WDS),<br /> `5` - Wireless Network
`bsUA` | Use AOL                    | -  \* | Boolean       |
`bsDI` | PPP Dial-in                | -  \* |               |
`bsTP` | Dialing                    | -  \* | Number        | `1` - Tone,<br /> `2` - Pulse
`bsCC` | Country Code               | -  \* |               |
`bsPC` | Connection                 | -  \* | Number        | `0` - Always On,<br /> `1` - Automatic,<br /> `2` - Manual
`bsRM` | Connection Sharing         | -  \* | Number        | `0` - Share a public IP address,<br /> `1` - Distribute a range of IP addresses,<br /> `2` - Using NAT Only,<br /> `3` - Off (Bridge Mode)

\* Doesn't exist on latest firmware.

Local Network
---

Name   | Description                | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`raDS` | Enable DHCP server         | `boo` | Boolean       | `true` to enable DHCP server.
`raNA` | Enable NAT                 | `boo` | Boolean       | `true` to enable Network Address Translation.
`laIP` | LAN IPv4 Address           | `ip4` | String [IPv4] |
`laSM` | LAN Subnet Mask            | `ip4` | String [IPv4] |
`dhBg` | DHCP Beginning Address     | `ip4` | String [IPv4] |
`dhEn` | DHCP Ending Address        | `ip4` | String [IPv4] |
`dhLe` | DHCP Lease                 | `u32` | Number        |
`dhMg` | DHCP Message               | -  \* | String        |
`dh95` | LDAP Server                | -  \* | String [IPv4] |
`DRes` | DHCP Reservations          | `cfl` | Array         |
`naFl` | Enable NAT-PMP             | `u32` | Boolean       | `0` - Disabled,<br /> `1` - Enabled<br /> (requires Mac OS X 10.4 or later)
`naAF` | Enable AOL Parental Controls| - \* | Boolean      |
`nDMZ` | Default Host               | `ip4` | String [IPv4] |
`fire` | IPv4 Port Mappings         | `cfl` | Dictionary    |
`pmTa` | IPv4 Port Mappings         | -  \* |               |
`SMBw` | Workgroup                  | -  \* | String        |
`SMBs` | WINS Server                | -  \* | String [IP]   |

\* Doesn't exist on latest firmware.

IPv6
---

Name   | Description                | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`6cfg` | IPv6 Mode                  | `u32` | Number        | `0` - Link-local only,<br /> `1` - Node,<br /> `3` - Tunnel
`6aut` | Configure IPv6             | `boo` | Boolean       | `true` if IPv6 is configured automatically.
`6Wad` | WAN IPv6 address           | `ip6` | String [IPv6] |
`6Wfx` | WAN Prefix Length          | ?     | Number        |
`6Wgw` | WAN IPv6 Router Address    | `ip6` | String [IPv6] | 
`6Wte` | Tunnel Server IPv4 Address | `ip4` | String [IPv4] |
`6Lfw` | LAN forwarding             | `boo` | Boolean       | `true` to allow devices on the LAN to use IPv6 addresses on a prefix delegated to the AirPort device.
`6Lad` | LAN IPv6 Address           | `ip6` | String [IPv6] |
`6Lfx` | LAN Prefix Length          | `u32` | Hexadecimal   |
`6sfw` | Enable IPv6 Firewall       | `boo` | Boolean       | `true` to enable the IPv6 firewall. "Block incoming IPv6 connections" in AirPort Utility.
`6trd` | Allow Teredo tunnels       | `boo` | Boolean       | `true` to allow Teredo tunnels. Has no effect when the IPv6 firewall is disabled.
`6sec` | Allow IPSec authentication | ?     | Boolean       | `true` to allow incoming IPSec authentication though the IPv6 firewall.
`6fwl` | IPv6 Firewall              | ?     | Dictionary    | Object containing IPv6 firewall rules.

PPPoE
---

Name   | Description                | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`peSN` | Service Name               | ?     | String        |
`peUN` | Username                   | `str` | String        |
`pePW` | Password                   | `str` | String        |
`peSC` | Always stay connected      | -  \* | Boolean       |
`peAC` | Automatically connect      | -  \* | Boolean       |
`peID` | Disconnect if Idle         | -  \* | Number        | `30` - 30 seconds,<br /> `60` - 1 minute,<br /> `120` - 2 minutes,<br /> `300` - 5 minutes,<br /> `600` - 10 minutes,<br /> `900` - 5 minutes,<br /> `1200` - 20 minutes,<br /> `1800` - 30 minutes,<br /> `0` - Never

\* Doesn't exist on latest firmware.

Modem
---

`Code` | Name                       | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`moPN` | Phone Number               | -  \* |               |
`moAP` | Secondary Phone Number     | -  \* |               |
`moUN` | Account Name               | -  \* |               |
`moPW` | Password                   | -  \* |               |
`moID` | Disconnect if Idle         | -  \* |               | `30` - 30 seconds,<br /> `60` - 1 minute,<br /> `120` - 2 minutes,<br /> `300` - 5 minutes,<br /> `600` - 10 minutes,<br /> `900` - 15 minutes,<br /> `1200` - 20 minutes,<br /> `1800` - 30 minutes,<br /> `0` - Never
`moDT` | Ignore Dial Tone           | -  \* |               |
`moAD` | Automatically Dial         | -  \* | Boolean       |
`moMP` | Protocol                   | -  \* |               | `1` - v.90,<br /> `2` - v.34

\* Doesn't exist on latest firmware.

...
---

Name   | Description                | Type  | Backup Type   | Notes
-------|----------------------------|-------|---------------|--------
`pdUN` | Account Name               | -  \* |               |
`pdPW` | Password                   | -  \* |               |
`pdAR` | Answer on ring             | -  \* |               |
`pdID` | Idle Disconnect After      | -  \* |               | `30` - 30 seconds,<br /> `60` - 1 minute,<br /> `120` - 2 minutes,<br /> `300` - 5 minutes,<br /> `600` - 10 minutes,<br /> `900` - 15 minutes,<br /> `1200` - 20 minutes,<br /> `1800` - 30 minutes,<br /> `0` - Never
`pdMC` | Maximum Connect Time       | -  \* |               | `900` - 15 minutes,<br /> `1800` - 30 minutes,<br /> `3600` - 1 hour,<br /> `7200` - 2 hours,<br /> `14400` - 4 hours,<br /> `28800` - 8 hours,<br /> `0` - Never Disconnect

\* Doesn't exist on latest firmware.
