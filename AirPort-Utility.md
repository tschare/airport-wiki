AirPort Utility is Apple's application for managing AirPort base stations using the ACP protocol.

### Connecting

When AirPort Utility first connects to an AirPort device it requests the device's status and configuration and starts a monitoring session.

> This is for AirPort Utility 6.3.9 (639.13) and a (fake) AirPort Express 2nd generation reporting version 7.8.

1. AirPort Utility starts connection #1 and enables encryption.
2. AirPort Utility sends a get property message for the property `feat`.
3. AirPort Utility sends a get property message for the property `fe01`.
4. AirPort Utility sends a get property message for the property `syAP`.
5. AirPort Utility sends a get property message for the property `syFl`.
6. AirPort Utility sends a get property message for the property `feat`.
7. AirPort Utility sends a get property message for properties `fire`, `srcv`, `syNm`, `syDN`, `syPW`, `syPR`, `syCt`, `syLo`, `syVs`, `syVr`, `syAM`, `sySN`, `sySK`, `syRe`, `syLR`, `syUT`, `minS`, `sySt`, `syIg`, `time`, `timz`, `usrd`, `raLC`, `raLF`, `raMA`, `raDS`, `raNA`, `raWB`, `raPx`, `raTr`, `raSL`, `raSR`, `WiFi`, `peSN`, `peUN`, `pePW`, `peSC`, `peAC`, `peID`, `peAO`, `waCV`, `waIn`, `waD1`, `waD2`, `waD3`, `waC1`, `waC2`, `waC3`, `waIP`, `waSM`, `waRA`, `waDC`, `waDS`, `waMA`, `waDN`, `waCD`, `waNM`, `waSD`, `laIP`, `laSM`, `laMA`, `dhBg`, `dhEn`, `dhSN`, `dhLe`, `dhMg`, `dh95`, `DRes`, `dhSL`, `gnBg`, `gnEn`, `gnSN`, `gnRo`, `gnLe`, `gnMg`, `gn95`, `naFl`, `naBg`, `naEn`, `naSN`, `naRo`, `naAF`, `nDMZ`, `pmPI`, `pmPS`, `pmPR`, `tACL`, `ntSV`, `slCl`, `slvl`, `snAF`, `snLW`, `snLL`, `snRW`, `snWW`, `snRL`, `snWL`, `snCS`, `usbF`, `prnI`, `prnR`, `seFl`, `cver`, `ctim`, `LLnk`, `WLnk`, `PHYS`, `isAC`, `Prof`, `maAl`, `maPr`, `leAc`, `lcVs`, `AUVs`, `lcVr`, `lcmV`, `lcMV`, `wsci`, `auPr`, `auNN`, `auNP`, `aWan`, `auRR`, `auHE`, `6cfg`, `6aut`, `6Wad`, `6Wfx`, `6Wgw`, `6Wte`, `6Lfw`, `6Lad`, `6Lfx`, `6sfw`, `6trd`, `6sec`, `6fwl`, `6NS1`, `6NS2`, `6PDa`, `6PDl`, `6CWa`, `6CWp`, `6CWg`, `6CLa`, `6NSa`, `6NSb`, `6CPa`, `6CPl`, `wbEn`, `wbHN`, `wbHU`, `wbHP`, `wbRD`, `wbRU`, `wbRP`, `wbAC`, `iCld`, `SUEn`, `SUFq`, `SUSv`, `trCo`.
    > TODO: and any of `feat`, `fe01`, `syAP`, `syFl`, `feat`?
8. AirPort Utility closes connection #1.
9. AirPort Utility starts connection #2 and enables encryption.
10. AirPort Utility sends a monitor request with this CFL binary plist:
    ```json
    {
        "filters": {
            "logm": {},
            "ACPRemoteBonjour": {},
            "MaSt": {},
            "waCD": {},
            "waC1": {},
            "waRA": {},
            "daSt": {},
            "tACL": {},
            "dmSt": {},
            "waIP": {},
            "wsci": {},
            "sySt": {},
            "stat": {},
            "raCh": {},
            "waC2": {},
            "DynS": {},
            "prnR": {},
            "waSM": {},
            "iCld": {},
            "deSt": {}
        }
    }
    ```
11. AirPort Utility starts connection #3 and enables encryption. This connection isn't used for anything yet.

At this point, AirPort Utility has two open connections (#2 and #3). I don't know how the monitoring messages work yet but they use connection #2. Connection #3 is used for RPC messages.

When connecting from the main interface (through a Bonjour advertisement) AirPort Utility also sends an RPC message with this CFL binary plist at some point:

```json
{
    "inputs": {},
    "function": "acpd.system.interfaces"
}
```

### Updating configuration

Sends an `acpd.parseDirtyPlist` RPC message with updated properties as inputs over connection #3. AirPort Utility immediately shows the configuration was updated successfully (before or after sending the RPC message?) and does not close the two open connections.

### RPC messages

AirPort base stations expect a CFL binary plist with an `input` object and a `function` string, and respond with a CFL binary plist with an `outputs` object and numeric `status`.

```json
{
    "inputs": {},
    "function": "..."
}
```
```json
{
    "outputs": {},
    "status": 0
}
```
