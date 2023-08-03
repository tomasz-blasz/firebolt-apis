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