In `/usr/bin` on AirPort devices, there is a utility called `acp` that lets you query and update [ACP properties](ACP-Properties). As this is done over SSH on the device itself, it is more secure than [AirPyrt](AirPyrt), which uses an older version of the ACP protocol (which doesn't encrypt anything) to communicate with AirPort devices.

According to the usage information output by running `acp` without any arguments, it can do more than just querying / updating as well, but that functionality seems to have been left out of the version distributed to production devices.

### Usage

#### Get property

```sh
acp -q {property}
```

{property} should be the 4 character code to specify which property you want to get. See [ACP Properties](ACP-Properties) for more information.

Example:

```sh
acp -q syNm
AirPort Extreme

```

#### Set property

```sh
acp -q {property}={value}

```

`{property}` should be the 4 character code to specify which property you want to set. See [ACP Properties](ACP-Properties) for more information.  
`{value}` should be the new value of the property.

Example:

```sh
acp -q syNm=AirPort\ Extreme

```

#### List properties

```sh
acp acpprop

```

In the usage information `acpprop` should be able to get codes by their "name" (as well as the code and listing all codes), however this does not seems to work.

See [ACP Properties](ACP-Properties) for more information.

### Other

Running the `error` command shows that some features of the `acp` utility are left out on production devices:

```
airport-extreme# acp error 
### error code names not available in release builds

```