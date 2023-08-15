## 4. HDMI Input Sources
The `HDMIInput` module **MUST** have a `sources` method that lists all HDMI input sources contected to the device.

The `sources` API **MUST** return an array of `HDMISource objects.

The properties of each `HDMISource` object **MUST** have values that represent the cross product of the physical port and the input source device plugged into it.

An example response:

```json
[
    {
        "port": "HDMI1",
        "osdName": "PlayStation 4",
        "cecVersion": "Version 2.0",
        "logicalAddress": 4,
        "physicalAddress": "A.B.C.D",
        "arc": true
    }
]
```

**TODO**: physical address hex or decimal? if hex caps or lower case strings?

The `HDMISource` object **MUST** have a `port` string property, which is the unique ID of the port the source device is connected through.

The `port` property **MUST** match the pattern:

 ```regexp
 /^HDMI[0-9]+$/
 ```

The `HDMISource` object **MUST** have an `osdName` string property, which is the display name of the HDMI input source device.

The `HDMISource` object **MUST** have a `cecVersion` string proeprty, which is the CEC version protocol advertised by the HDMI input source device.

The `cecVersion` property **MUST** be one of the following values:

- `"unknown"` - unknown version
- `"Version 1.3a"`
- `"Version 1.4"` - Version 1.4, 1.4a or 1.4b.
- `"Version 2.0"`

The `HDMISource` object **MUST** have a `logicalAddress` integer property, which is the logical HDMI address of the input source device within the scope of this output sink device.


The `HDMISource` object **MUST** have a `physicalAddress` string property, which is the physical HDMI address of the port the input source device is connected to.

**TODO**: Is "bus" the best word?

The `physicalAddress` property **MUST** match the pattern:

 ```regexp
 /^[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+$/
 ```

The `HDMISource` object **MUST** have an `arc` boolean property, which is true if this HDMI port and the connected HDMI device both support ARC and/or eARC device connections.

The `HDMISource` object **MUST** have an `autoLowLatencyMode` boolean property, which is true if the connected HDMI device is signalling ALLM.

### 4.1. Get Source by Logical Address
The `HDMIInput` module **MUST** have a `getSourceByLogicalAddress` method that returns info on a single HDMI source device.

The `getSourceByLogicalAddress` API **MUST** return an `HDMISource` object that corresponds to the provided `logicalAddress` parameter.

```javascript
HDMIInput.getSourceByLogicalAddress(1)
```

### 4.2. Get Source by Physical Address
The `HDMIInput` module **MUST** have a `getSourceByPhysicalAddress` method that returns info on a single HDMI source device.

The `getSourceByPhysicalAddress` API **MUST** return an `HDMISource` object that corresponds to the provided `physicalAddress` parameter.

```javascript
HDMIInput.getSourceByPhysicalAddress("A.B.C.D")
```

## 7. Input Source OSD Name Notification
The `HDMIInput` module **MUST** have an `onSourceOSDNameChanged` notification that fires when any HDMI input device changes its OSD Name.

This notification **MUST** have an object payload.

The object payload **MUST** have a `port` string property that denotes which input port has detected a change to the ALLM signaling.

The `port` property **MUST** match the pattern:

 ```regexp
 /^HDMI[0-9]+$/
 ```

The object payload **MUST** have an `osdName` string property that denotes the updated value of the input device OSD Name.

Example payload:

```json
  {
      "port": "HDMI1",
      "osdName": "XBox"
  }
```

## 10. HDMI CEC Events
AS has a very basic model compared to the HDMI 2.1 spec...

i'm thinking we want a specific event for each CEC message, so that we can define separate schemas for the operands of each message.

More to come... but maybe not top priority?

CEC relates to both HDMIInput and HDMIOutput, because CEC is a bus mechanism where all devices share the same command set.

Should we have 3 Firebolt APIs: HDMIInput, HDMIOutput, HDMICEC?

JL> I think splitting Input & Output is a great idea, as it removes a likely source of momentary confusion / headaches for our users
JL> Is there such a thing as a port that is both an input and output port on the same device? If so, is this ARC, or different, or both?
JL> for CEC, don't the devices attached to input ports send most commands, and devices attached to output ports receive them?
JL> I suppose if that is true, we can still have it all in the HDMICEC module, so i'm fine with three modules (and we can always do four if need be)
JL> Proposal: remove CEC so we can upgrade the status of this doc to Candidate Spec and get feedback

TODO: split output & input APIs into separate docs
TODO: do we need to do output requirements now, or can we do them later?

Source LA = logical address, 4-bit unsigned int
Destination LA is omitted, but could be directly addressed to device or broadcast.

```
// CEC control, mapped from Thunder event notifications
* onCECEnabled
* onWakeupFromStandby - needs some thought because of interaction with standby mode

// CEC incoming messages, mapped from Thunder event notifications
* onActiveSource [Source LA], [Physical Address]
* onInactiveSource [Source LA], [Physical Address]
* onImageViewOn [Source LA]
* onTextViewOn [Source LA]
* onReportAudioStatus ?? or through DisplaySettings??
* onFeatureAbort [Source LA], [Dest LA], [Feature Opcode], [Abort Reason]
* onStandby [Source LA]
* ...

// CEC incoming messages, handled internally by Thunder today, but could be exposed up to app if we modify Thunder API
* onLogicalAddress [Source LA], [Physical Address], [Device Type]
* onSetOSDName [Source LA], [ASCII up to 14 chars]
* onCECVersion [Source LA], [CEC Version]
* onDeviceVendorID [Source LA], [Vendor ID]
* onReportPowerStatus [Source LA], [Power Status]
```

## 11. CEC Commands

```
// Commands mapped from Thunder API
* getActiveRoute
* 

// New commands coming as part of HDMI ARC volume control press-and-hold functionality
    * sendUserControlPressed [Dest LA], [UI Command]
    * sendUserControlReleased [Dest LA], [UI Command]
```