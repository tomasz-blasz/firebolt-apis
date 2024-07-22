# Media Capabilities Requirements

Document Status: Candidate Specification

### Audio Format Supported

The `MediaCapabilities` module **MUST** have an `audioFormatSupported` method that returns a boolean that provides whether or not the specified format is commonly supported by the device and all relevant peripherals in the user's AV chain.

This method **MUST** have a required `codec` parameter with the type `Media.AudioCodec`.

This method **MUST** have an optional `options` parameter which **MUST** be an object with zero or more of the following properties:

| Property       | Type              | Description                                           |
| -------------- | ----------------- | ----------------------------------------------------- |
| `atmos`        | `boolean`         | Whether or not Dolby Atmos support is being requested |
| `codecLevel`   | `string`          | The codec level                                       |
| `codecProfile` | `string`          | The codec profile:<br>**aac**: `mp2lc`, `mp4he`       |
| `container`    | `Media.Container` | The container format type                             |
| `sampleRate`   | `number`          | The sample rate being requested, in kHz               |

If the `options` parameter is provided, then this method **MUST NOT** return `true` unless the format specified is supported with **all** of the properties specified by `options` *at the same time*.

As multiple audio output modes may be set at the same time, the response **MUST** be based on the best possible audio configuration supported by the device and its AV chain.  For instance, if the device is configured for stereo output (which cannot support Atmos) but the AV chain supports Surround 5.1 (which can support Atmos), the latter would be considered the best possible audio configuration and thus used as the basis for the decision.

```javascript
MediaCapabilities.audioFormatSupported(Media.AudioCodec.AC4, {
  atmos: true
})
//> true

MediaCapabilities.audioFormatSupported(Media.AudioCodec.TRUEHD, {
  atmos: true
  mode: 'mono'
})
//> false (mono output not supported with dolby atmos)
```

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:media-capabilities:info` capability.

### Video Format Supported

The `MediaCapabilities` module **MUST** have a `videoFormatSupported` method that returns boolean that provides whether or not the specified format is commonly supported by the device and all relevant peripherals in the user's AV chain.

This method **MUST** have a required `codec` parameter with the type `Media.VideoCodec`.

This method **MUST** have an optional `info` parameter which **MUST** be an object with zero or more of the following properties:

| Property     | Type                   | Description                                                                                                  |
| ------------ | ---------------------- | ------------------------------------------------------------------------------------------------------------ |
| `container`  | `Media.VideoContainer` | The content container format                                                                                 |
| `hdr`        | `Media.HDRProfile`     | The HDR profile to check                                                                                     |
| `level`      | `string`               | The codec level:<br>**hevc**: `4.1`, `4.2`, `5.0`, `5.1`<br>**vp9**:`3.0`, `3.1`, `4.0`, `4.1`, `5.0`, `5.1` |
| `profile`    | `string`               | The codec profile:<br>**hevc**: `main`, `high`, `main10`<br>**vp9**: `p0`, `p2`                              |
| `resolution` | `Types.Dimensions`     | The dimensions of the media content, in pixels                                                               |

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:device:info` capability.

```javascript
MediaCapabilities.videoFormatSupported(Media.VideoCodec.HEVC, {
  profile: "main10",
  hdr: Media.HDRProfile.HDR10_PLUS
})
//> true

MediaCapabilities.videoFormatSupported(Media.VideoCodec.VP9, {
  profile: "p2",
  hdr: Media.HDRProfile.HDR10_PLUS,
  resolution: { "width": 3840, "height": 2160 }
})
//> true
```

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:media-capabilities:info` capability.
