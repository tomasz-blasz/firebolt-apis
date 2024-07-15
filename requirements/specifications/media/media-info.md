# Media Info Requirements

Document Status: Candidate Specification

See [Firebolt Requirements Governance](../../governance.md) for more info.

| Contributor     | Organization   |
| --------------- | -------------- |
| Anthony Borzota | Comcast        |
| Jeremy LaCivita | Comcast        |
| Stuart Harris   | Sky            |
| Joe Martin      | Comcast        |
| Farhan Mood     | Liberty Global |

## 1. Overview

App developers need to know which audio and video formats can be successfully played on a Firebolt device and its attached peripherals.

An audio format includes values such as the audio container, codec, codec level, and bit rate of the audio stream.

A video format may include values such as the video container, codec, HDR profile, and resolution of the video stream.

Further, apps may also need to know about the media format currently playing in the media pipeline.

An app may need to check IP video playback that it has initiated to see if Dolby Audio is in fact active (as opposed to just present in the encoded data) in order to display metadata or badges to the user. Apps may also be playing [user-cultivated media](./media-access.md), and therefore have no metadata about the format of the files.

A first-party app may need to check all [Media Pipelines](./media-pipeline.md) for media format characteristics to display a global badge.

Additionally, apps may need to know what is supported by the device, *before* initiating any media playback.

To solve this, Firebolt APIs will be created to detect the formats and codecs currently being decoded by the [Media Pipeline](./media-pipeline.md).  Firebolt APIs will also be created to query whether the device and its peripherals support playing content of various formats.

> TODO: Fix the above links

### 1.1. User Stories

#### 1.1.1. As an OTT App developer

I want to know what is supported by the device and it's active AV chain:

- I want to know if a video or audio format (Dolby Vision, Dolby Atmos, HEVC, etc.) will work if playback is attempted.
- I want to know if a format profile or level (HEVC Main 10, VP9 Profile 2, etc.) is supported.
- I want to know how many audio output channels (e.g. 7.1, 5.1) are available.
- I want to know what resolutions and frame rates are supported (e.g. 1080p60).

I want to know what kind of content I'm currently playing:

- I want to know which video or audio format the media I'm currently playing is sending to the decoder.
- I want to know which video or audio format profile or level the media I'm currently playing is being sent to the decoder.
- I want to know how many audio output channels (e.g. 7.1, 5.1) are currently being sent to the decoder.
- I want to know what resolution and frame rate is currently being sent to the decoder.

#### 1.1.2. As a first-party App developer

I want to show an audio/videophile overlay with detailed information:

- I want to know all video or audio formats that are currently being sent to a decoder.
- I want to know all video or audio format profiles/levels currently being sent to a decoder.
- I want to know all audio output channel profiles (e.g. 7.1, 5.1) currently being sent to a decoder.
- I want to know all resolution and framerates currently being sent to a decoder.

## 2. Table of Contents

- [1. Overview](#1-overview)
  - [1.1. User Stories](#11-user-stories)
    - [1.1.1. As an OTT App developer](#111-as-an-ott-app-developer)
    - [1.1.2. As a first-party App developer](#112-as-a-first-party-app-developer)
- [2. Table of Contents](#2-table-of-contents)
- [3. Constants, Types, and Schemas](#3-constants-types-and-schemas)
  - [3.1. Media Container Types](#31-media-container-types)
  - [3.2. Media Formats](#32-media-formats)
  - [3.3. Dimensions](#33-dimensions)
  - [3.4. Resolution User-Friendly Name](#34-resolution-user-friendly-name)
  - [3.5. Video Modes](#35-video-modes)
  - [3.6. HDR Profiles](#36-hdr-profiles)
  - [3.7. HDCP Versions](#37-hdcp-versions)
  - [3.8. Colorimetry](#38-colorimetry)
  - [3.9. Color Depth](#39-color-depth)
  - [3.9. Color Space \& Chroma Subsampling](#39-color-space--chroma-subsampling)
  - [3.10. Audio Output Mode](#310-audio-output-mode)
  - [3.11. Transfer Function](#311-transfer-function)
  - [3.12. Quantization Range](#312-quantization-range)
- [4. AV System Support](#4-av-system-support)
  - [4.1. Video Format Supported](#41-video-format-supported)
  - [4.2. Audio Format Supported](#42-audio-format-supported)
- [5. Display Support](#5-display-support)
  - [5.1. HDR Profiles](#51-hdr-profiles)
  - [5.2. Color Depth](#52-color-depth)
  - [5.3. Display Size](#53-display-size)
  - [5.4. Native Resolution](#54-native-resolution)
  - [5.5. Native Resolution Name](#55-native-resolution-name)
  - [5.6. Refresh Rate](#56-refresh-rate)
  - [5.7. Colorimetry](#57-colorimetry)
  - [5.8. Manufacturer](#58-manufacturer)
  - [5.9. Model](#59-model)
  - [5.10. Source Physical Address](#510-source-physical-address)
  - [5.11. Electro Optical Transfer Function (EOTF)](#511-electro-optical-transfer-function-eotf)
- [6. Device Support](#6-device-support)
  - [6.1. Audio Mode](#61-audio-mode)
  - [6.2. Video Mode](#62-video-mode)
  - [6.3. Pre-Video Mode Change Event](#63-pre-video-mode-change-event)
  - [6.4. Supported Audio Modes](#64-supported-audio-modes)
  - [6.5. Supported Video Modes](#65-supported-video-modes)
  - [6.6. Video Resolution](#66-video-resolution)
  - [6.7. HDR Profile](#67-hdr-profile)
  - [6.8. Supported HDR Profiles](#68-supported-hdr-profiles)
  - [6.9. Maximum Supported HDCP Version](#69-maximum-supported-hdcp-version)
  - [6.11. Color Depth](#611-color-depth)
  - [6.11. Maximum Supported Color Depth](#611-maximum-supported-color-depth)
  - [6.12. Color Space \& Chroma Subsampling](#612-color-space--chroma-subsampling)
  - [6.13. Quantization Range](#613-quantization-range)
- [7. Media Info](#7-media-info)
  - [7.1. MediaInfo for Current App](#71-mediainfo-for-current-app)
    - [7.1.1. Video Format](#711-video-format)
    - [7.1.2. Audio Format](#712-audio-format)
  - [7.2. Global MediaInfo](#72-global-mediainfo)
    - [7.2.1. Active Video Formats](#721-active-video-formats)
    - [7.2.2. Active Audio Formats](#722-active-audio-formats)

## 3. Constants, Types, and Schemas

### 3.1. Media Container Types

The Firebolt `Media` module **MUST** have an `AudioContainer` enumeration of the following media container content types:

| Name         | Description                                                                                                                    |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------ |
| `audio/mp4`  | [MP4 Audio](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Containers#mpeg-4_mp4)                                  |
| `audio/mpeg` | [Moving Picture Experts Group (MPEG1/MPEG2)](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Containers#mpegmpeg-2) |
| `audio/ogg`  | [OGG](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Containers#ogg)                                               |
| `audio/wave` | [Waveform Audio File Format](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Containers#wave_wav)                   |
| `audio/webm` | [Web Media](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Containers#webm)                                        |

The Firebolt `Media` module **MUST** have a `VideoContainer` enumeration of the following media container content types:

| Name         | Description                                                                                                                    |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------ |
| `video/mp2t` | [MPEG transport stream](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types)              |
| `video/mp4`  | [MP4 Audio](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/)                                                       |
| `video/mpeg` | [Moving Picture Experts Group (MPEG1/MPEG2)](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Containers#mpegmpeg-2) |
| `video/webm` | [Web Media](https://developer.mozilla.org/en-US/docs/Web/Media/Formats/Containers#webm)                                        |

### 3.2. Media Formats

The Firebolt `Media` module **MUST** have an `AudioFormat` enumeration:

| Name     | Description                                    |
| -------- | ---------------------------------------------- |
| `aac`    | Advanced Audio Coding                          |
| `ac3`    | Dolby Digital / Dolby Audio Codec 3            |
| `ac4`    | Dolby Audio Codec 4                            |
| `dts-x`  | Digital Theater Systems X                      |
| `eac3`   | Dolby Digital Plus / Dolby Enhanced AC-3 / DD+ |
| `mpeg3`  | MPEG-1 Part 3 & MPEG-2 Part 3                  |
| `opus`   | IETF Opus                                      |
| `pcm`    | Pulse Code Modulation/WAVE                     |
| `truehd` | Dolby TrueHD / MLP                             |
| `vorbis` | Xiph.org Vorbis                                |

The Firebolt `Media` module **MUST** have a `VideoFormat` enumeration:

| Name    | Description                          |
| ------- | ------------------------------------ |
| `av1`   | AOMedia Video 1                      |
| `avc`   | Advanced Video Coding (H.264)        |
| `hevc`  | High Efficiency Video Coding (H.265) |
| `mpeg1` | MPEG-1 Part 2 Visual                 |
| `mpeg2` | MPEG-2 Part 2 Visual                 |
| `vp8`   | Video Processor 8                    |
| `vp9`   | Video Processor 9                    |
| `vp10`  | Video Processor 10                   |

### 3.3. Dimensions

The Firebolt `Types` module must have a `Dimensions` object of the following schema:

| Field    | Type     |
| -------- | -------- |
| `height` | `number` |
| `width`  | `number` |

### 3.4. Resolution User-Friendly Name

The Firebolt `Media` module **MUST** have a `ResolutionName` enumeration:

| Class     | Applicable Resolutions |
| --------- | ---------------------- |
| `sd`      | `576p` and lower       |
| `hd`      | `720p`                 |
| `fhd`     | `1080p`                |
| `uhd`     | `2160` and higher      |
| `unknown` | unknown or no display  |

### 3.5. Video Modes

For the purposes of the Firebolt API, a `VideoMode` shall be defined as the shorthand vertical resolution and frame rate of video content.

The Firebolt `Media` module **MUST** have a `VideoMode` enumeration:

- `480i`
- `480p`
- `576i25`
- `576p50`
- `576p60`
- `720p50`
- `720p60`
- `1080i50`
- `1080i60`
- `1080p24`
- `1080p25`
- `1080p30`
- `1080p50`
- `1080p60`
- `2160p24`
- `2160p25`
- `2160p30`
- `2160p50`
- `2160p60`
- `4320p60`
- `unknown`

Any methods relating to the video mode (such as a device's video output mode) **MUST** return `Media.VideoMode` values.

### 3.6. HDR Profiles

The Firebolt `Media` module **MUST** have an `HDRProfile` enumeration:

- `dolbyVision`
- `hdr10`
- `hdr10plus`
- `hlg`
- `sdr`
- `technicolor`
- `unknown`

### 3.7. HDCP Versions

The Firebolt `Media` module **MUST** have an `HDCPVersion` enumeration:

- `1.4`
- `2.2`
- `unknown`

### 3.8. Colorimetry

The Firebolt `Display` module **MUST** have a `Colorimetry` enumeration:

- `BT2020cYCC`
- `BT2020RGB`
- `BT2020YCC`
- `DCI-P3`
- `ICtCp`
- `opRGB`
- `opYCC601`
- `sYCC601`
- `xvYCC601`
- `xvYCC709`
- `unknown`

### 3.9. Color Depth

The Firebolt `Media` module **MUST** have a `ColorDepth` enumeration:

- `8`
- `10`
- `12`
- `auto`
- `unknown`

### 3.9. Color Space & Chroma Subsampling

The Firebolt `Media` module **MUST** have a `ColorSpace` enumeration:

| Name       | Chroma |
| ---------- | ------ |
| `RGB444`   | 4:4:4  |
| `YCbCr420` | 4:2:0  |
| `YCbCr422` | 4:2:2  |
| `YCbCr444` | 4:4:4  |
| `other`    |        |
| `unknown`  |        |

### 3.10. Audio Output Mode

The Firebolt `Media` module **MUST** have an `AudioMode` enumeration:

- `auto`
- `mono`
- `passthrough`
- `stereo`
- `surround`
- `unknown`

### 3.11. Transfer Function

The Firebolt `Display` module **MUST** have a `TransferFunction` enumeration:

| Name           | Description                              |
| -------------- | ---------------------------------------- |
| `BT1886`       | ITU-R BT.1886                            |
| `BT2100`       | ITU-R BT.2100                            |
| `SMPTE_ST2084` | SMPTE ST.2084; Perceptual quantizer (PQ) |
| `other`        | other                                    |
| `unknown`      | unknown                                  |

### 3.12. Quantization Range

The Firebolt `Device` module **MUST** have a `QuantizationRange` enumeration:

- `full`
- `limited`
- `unknown`

## 4. AV System Support

Apps need to know what types of media support the device and any connected peripherals are together capable of.

To facilitate this, the `AVSystem` module will provide convenience methods that encapsulate the capabilities of the device as well as any of its connected peripherals, including but not limited to, displays, sound bars, and receivers.

These values **MUST NOT** change without a settings change, peripheral change, or firmware update.

### 4.1. Video Format Supported

The `AVSystem` module **MUST** have a `videoFormatSupported` method that returns boolean that provides whether or not the specified format is supported by the current device and its AV chain.

This method **MUST** have a required `format` parameter with the type `Media.VideoFormat`.

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
AVSystem.videoFormatSupported(Media.VideoFormat.HEVC, {
  profile: "main10",
  hdr: Media.HDRProfile.HDR10_PLUS
})
//> true

AVSystem.videoFormatSupported(Media.VideoFormat.VP9, {
  profile: "p2",
  hdr: Media.HDRProfile.HDR10_PLUS,
  resolution: { "width": 3840, "height": 2160 }
})
//> true
```

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:avsystem:info` capability.

### 4.2. Audio Format Supported

The `AVSystem` module **MUST** have an `audioFormatSupported` method that returns a boolean that provides whether or not the specified format is supported by the current device configuration and its AV chain.

This method **MUST** have a required `format` parameter with the type `Media.AudioFormat`.

This method **MUST** have an optional `options` parameter which **MUST** be an object with zero or more of the following properties:

| Property       | Type              | Description                                                                |
| -------------- | ----------------- | -------------------------------------------------------------------------- |
| `atmos`        | `boolean`         | Whether or not Dolby Atmos support for the given format is being requested |
| `codecLevel`   | `string`          | The codec level                                                            |
| `codecProfile` | `string`          | The codec profile:<br>**aac**: `mp2lc`, `mp4he`                            |
| `container`    | `Media.Container` | The container format type                                                  |
| `sampleRate`   | `number`          | The sample rate being requested, in kHz                                    |

If the `options` parameter is provided, then this method **MUST NOT** return `true` unless the format specified is supported with **all** of the properties specified by `options` *all at the same time*.

As multiple audio output modes may be set at the same time, the response **MUST** be based on the best possible audio configuration supported by the device and its AV chain.  For instance, if the device is configured for stereo output (which cannot support Atmos) but the AV chain supports Surround 5.1 (which can support Atmos), the latter would be considered the best possible audio configuration and thus used as the basis for the decision.

```javascript
AVSystem.audioFormatSupported(Media.AudioFormat.AC4, {
  atmos: true
})
//> true

AVSystem.audioFormatSupported(Media.AudioFormat.TRUEHD, {
  atmos: true
  mode: 'mono'
})
//> false (mono output not supported with dolby atmos)
```

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:avsystem:info` capability.

## 5. Display Support

Apps need to know various aspects of the current (or built-in) display of a device.

These will be surfaced in the `Display` module.

### 5.1. HDR Profiles

The `Display` module **MUST** have an `hdrProfiles` method that returns an array of `Media.HDRProfile` values denoting the display's supported HDR profiles.

If no display is present, an empty array **MUST** be returned.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.hdrProfiles()
//> ["dolbyVision", "hdr10", "hdr10plus", "hlg"]
```

### 5.2. Color Depth

The `Display` module **MUST** have a `colorDepth` method that returns a `Media.ColorDepth` value denoting the display's supported color depth.

If no display is present, a value of `unknown` **MUST** be returned.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.colorDepth()
//> "10"
```

### 5.3. Display Size

The `Display` module **MUST** have a `size` method that returns a `Types.Dimensions` object denoting the physical width and height of the display, in centimeters.

If no display is present, the height and width values **MUST** both be zero.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.size()
//> { "width": 157, "height": 91 }
```

### 5.4. Native Resolution

The `Display` module **MUST** have a `resolution` method that returns a `Types.Dimensions` object denoting the `width` and `height` of the display's native resolution as numbers, in pixels.

If no display is present, the height and width values **MUST** both be zero.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.resolution()
//> { "width": 1920, "height": 1080 }
```

### 5.5. Native Resolution Name

The `Display` module **MUST** have a `resolutionName` method that returns a `Media.ResolutionName` value denoting the user-friendly name of the display's native resolution.

If no display is present, a value of `unknown` **MUST** be returned.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.resolutionName()
//> "uhd"
```

### 5.6. Refresh Rate

The `Display` module **MUST** have a `refreshRate` method that returns an number value denoting the optimal refresh rate of the display (in Hz).

If no display is present, a value of zero **MUST** be returned.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.refreshRate()
//> 120
```

### 5.7. Colorimetry

The `Display` module **MUST** have a `colorimetry` method that returns an array of `Media.Colorimetry` values denoting the display's supported colorimetry values.

If no display is present, an empty array **MUST** be returned.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.colorimetry()
//> ["BT2020RGB", "BT2020YCC", "xvYCC601", "xvYCC709"]
```

### 5.8. Manufacturer

The `Display` module **MUST** have a `manufacturer` method that returns a string denoting the display's manufacturer.

If no display is present, an empty string **MUST** be returned.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.manufacturer()
//> "Samsung Electric Company"
```

### 5.9. Model

The `Display` module **MUST** have a `model` method that returns a string denoting the display's model number/product name.

If no display is present, an empty string **MUST** be returned.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.model()
//> "Q80A"
```

### 5.10. Source Physical Address

The `Display` module **MUST** have a `sourcePhysicalAddress` method that returns a string denoting the display's source physical address.

If no display is present, an empty string **MUST** be returned.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.sourcePhysicalAddress()
//> "3.0.0.0"
```

### 5.11. Electro Optical Transfer Function (EOTF)

The `Display` module **MUST** have an `eotf` method that returns a `Display.TransferFunction` value denoting the display's EOTF.

If no display is present, a value of `unknown` **MUST** be returned.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.eotf()
//> "SMPTE_ST2084"
```

## 6. Device Support

Apps need to know various aspects of the device, including its media playing capabilities.

These will be surfaced in the `Device` module.

### 6.1. Audio Mode

The `Device` module **MUST** include an `audioMode` method that returns a `Media.AudioMode` value denoting the device's current audio output mode.

This method **MUST** have a corresponding event to notify listeners after a change to this property has been made and that change has taken effect.

Access to these methods **MUST** require the `use` role of the `xrn:firebolt:capability:device:info` capability.

```javascript
Device.audioMode()
//> "stereo"
```

### 6.2. Video Mode

The `Device` module **MUST** have a `videoMode` method that returns a `Media.VideoMode` value denoting the device's current video mode.

If no display is present, a value of `unknown` is returned.

This method **MUST** have a corresponding event to notify listeners after a change to this property has been made and that change has taken effect.

Access to these methods **MUST** require the `use` role of the `xrn:firebolt:capability:device:info` capability.

```javascript
Device.videoMode()
//> "1080p60"
```

### 6.3. Pre-Video Mode Change Event

The `Device` module **MUST** have a `onPreVideoModeChanged` event that that returns a `Media.VideoMode` value denoting the device's current video mode.

This method **MUST** notify listeners after a change to this property has been made but before the change has taken effect.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:device:info` capability.

```javascript
Device.onPreVideoModeChanged((value) => {
  console.log(value)
  //> "1080p60"
})
```

### 6.4. Supported Audio Modes

The `Device` module **MUST** have an `audioModes` method that returns an array of `Media.AudioMode` values denoting the audio modes supported by the device.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:device:info` capability.

```javascript
Device.audioModes()
//> ["passthrough", "stereo", "surround"]
```

### 6.5. Supported Video Modes

The `Device` module **MUST** have a `videoModes` method that returns an array of `Media.VideoMode` values denoting the video modes supported by the device.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:device:info` capability.

```javascript
Device.videoModes()
//> ["720p50", "720p60", "1080p50", "1080p60"]
```

### 6.6. Video Resolution

The `Device` module **MUST** have a `videoResolution` method that returns a `Types.Dimensions` object denoting the current video output resolution, in pixels.

If no display is present, the height and width values **MUST** both be zero.

This method **MUST** have a corresponding event to notify listeners after a change to the device's `videoMode` property has been made and that change has taken effect.

Access to these methods **MUST** require the `use` role of the `xrn:firebolt:capability:device:info` capability.

```javascript
Device.videoResolution()
//> { "width": 1920, "height": 1080 }
```

### 6.7. HDR Profile

The `Device` module **MUST** have an `hdrProfile` method that returns a `Media.HDRProfile` value denoting the HDR profile currently set on the device for video output.

This method **MUST** have a corresponding event to notify listeners after this property has changed and that change has taken effect.

Access to these methods **MUST** require the `use` role of the `xrn:firebolt:capability:device:info` capability.

```javascript
Device.hdrProfile()
//> "hdr10plus"
```

### 6.8. Supported HDR Profiles

The `Device` module **MUST** have an `hdrProfiles` method that returns an array of `Media.HDRProfile` values denoting the HDR profiles supported by the device.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:device:info` capability.

```javascript
Device.hdrProfiles()
//> ["dolbyVision", "hdr10", "hdr10plus", "hlg"]
```

### 6.9. Maximum Supported HDCP Version

The `Device` module **MUST** have a `maxHdcpVersion` method that returns a `Media.HDCPVersion` value denoting the maximum HDCP version supported by the device for media protection.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:device:info` capability.

```javascript
Device.maxHdcpVersion()
//> "2.2"
```

### 6.11. Color Depth

The `Device` module **MUST** have a `colorDepth` method that returns a `Media.ColorDepth` value denoting the color depth currently set on the device for video output.

This method **MUST** have a corresponding event to notify listeners after this property has changed and that change has taken effect.

Access to these methods **MUST** be governed by the `xrn:firebolt:capability:device:info` capability.

```javascript
Device.colorDepth()
//> "10"
```

### 6.11. Maximum Supported Color Depth

The `Device` module **MUST** have a `maxColorDepth` method that returns a `Media.ColorDepth` value denoting the maximum color depth supported by the device for video output.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:device:info` capability.

```javascript
Device.maxColorDepth()
//> "10"
```

### 6.12. Color Space & Chroma Subsampling

The `Device` module **MUST** have a `colorSpace` method that returns a `Media.ColorSpace` value denoting the color space and chroma subsampling value currently set on the device for video output.

This method **MUST** have a corresponding event to notify listeners after this property has changed and that change has taken effect.

Access to these methods **MUST** be governed by the `xrn:firebolt:capability:device:info` capability.

```javascript
Device.colorSpace()
//> "YCbCr422"
```

### 6.13. Quantization Range

The `Device` module **MUST** have a `quantizationRange` method that returns a `Device.QuantizationRange` value denoting the quantization range currently set on the device for video output.

This method **MUST** have a corresponding event to notify listeners after this property has changed and that change has taken effect.

Access to these methods **MUST** be governed by the `xrn:firebolt:capability:device:info` capability.

```javascript
Device.quantizationRange()
//> "limited"
```

## 7. Media Info

The Firebolt `MediaInfo` module consists of APIs to get information about any media actively being decoded by the Media Pipeline or an active HDMI input.

### 7.1. MediaInfo for Current App

Apps need a way to query the media info for media currently being played by the app. All of the following methods take a single `pipeline` parameter, which identifies the Media Pipeline in the current app's scope that is being queried, e.g.:

```javascript
MediaInfo.videoFormat(1) // return the video format in the current app's media pipeline 1
MediaInfo.videoFormat(2) // return the video format in the current app's media pipeline 2
```

**TODO**: where do we map video tags to ids? need a spec for this? same spec, new spec?
JL: i linked to the Media Pipeline spec and merged it into this branch... we should review.

The `pipeline` parameter is required for the JSON-RPC request, however, the Firebolt SDK **SHOULD** provide a default value of `1` if not provided by the calling app.

For example, the following would query the video format for the app's pipeline `1` in JavaScript (which supports default values for parameters).

```javascript
MediaInfo.videoFormat()
```

#### 7.1.1. Video Format

The `MediaInfo` module **MUST** have a `videoFormat` method that returns an `object` with the video codec (e.g. `avc`, `vp9`, etc.) and resolution of the media currently in the media pipeline (either playing or paused).

The `videoFormat` result **MUST** have an `pipelineId` integer property that denotes the media pipeline/session ID.

The `videoFormat` result **MUST** have a `format` property with one of the values from the `Media.VideoFormat` enum.

The `videoFormat` result **MUST** have an `hdr` array property with zero or more `Media.HDRProfile` values.

If a value is included in the `hdr` array then the media currently in the media pipeline **MUST** include the denoted HDR metadata in the decoded video.

The `videoFormat` result **MUST** have a `resolution` property with a `Types.Dimensions` value.

The `videoFormat` result **MAY** have a `codecProfile` string property that denotes the profile of the codec.

The `videoFormat` result **MAY** have a `codecLevel` string property that denotes the level of the codec.

The `videoFormat` method **MUST** be a Firebolt `property:readonly` API, and
have a corresponding `onVideoFormatChanged` notification.

Access to the `videoFormat` method requires access to the `use` role of the
`xrn:firebolt:capability:media-info:video-format` capability.

```javascript
MediaInfo.videoFormat(1)
/*
{
  "pipelineId": 1,
  "codecLevel": "4.2",
  "codecProfile": "main",
  "container": "video/mp4",
  "format": "hevc",
  "frameRate": 30,
  "hdr": [
    "hdr10"
  ],
  "resolution": {
    "width": 1920,
    "height": 1080
  }
}
*/
```

#### 7.1.2. Audio Format

The `MediaInfo` module **MUST** have a `audioFormat` method that returns an `object` with the below values describing media currently in the media pipeline (either playing or paused).

The `audioFormat` result **MUST** have an `pipelineId` integer property that denotes the media pipeline/session ID.

The `audioFormat` result **MUST** have a `biteRate` number property that denotes the audio bit rate.

The `audioFormat` result **MUST** have a `channels` string property that denotes the audio output channels.

The `audioFormat` result **MUST** have a `format` property with one of the values from the `Media.AudioFormat` enum.

The `audioFormat` result **MUST** have a `sampleRate` number property that denotes the audio sample rate.

The `audioFormat` result **MAY** have a `codecLevel` string property that denotes the level of the codec.

The `audioFormat` result **MAY** have a `codecProfile` string property that denotes the profile of the codec.

The `audioFormat` method **MUST** have a corresponding `onAudioFormatChanged` notification.

Access to the `audioFormat` method requires access to the `use` role of the `xrn:firebolt:capability:media-info:audio-format` capability.

```javascript
MediaInfo.audioFormat(1)
/*
{
  "pipelineId": 1,
  "bitRate": 128,
  "channels": 2,
  "format": "aac",
  "container": "audio/mp4",
  "sampleRate": 48
}
*/
```

### 7.2. Global MediaInfo

First party apps need a way to query which media formats are currently being output to the [media pipeline](./media-pipeline.md), without caring about which pipeline.

#### 7.2.1. Active Video Formats

The `MediaInfo` module **MUST** have a `activeVideoFormats` method that returns an array of `objects` for all media currently in the media pipeline (either playing or paused).

Each item in the `activeVideoFormats` result array **MUST** conform to the same requirements as the individual results from the [`videoFormat` method](#711-video-format).

Additionally, the `MediaInfo` module **MUST** have an `onActiveVideoFormatsChanged` event that fires whenever any pipeline starts, stops, or changes its current video format.

Example:

```javascript
MediaInfo.activeVideoFormats().find(f => f.format === Media.VideoFormat.HEVC)
/*
[
  {
    "pipelineId": 1,
    "codecLevel": "4.2",
    "codecProfile": "main",
    "container": "video/mp4",
    "format": "hevc",
    "frameRate": 30,
    "hdr": [
        "hdr10"
    ],
    "resolution": {
      "width": 1920,
      "height": 1080
    }
  }
]
*/
```

Access to the `activeVideoFormats` method is gated by `manage` access to the `xrn:firebolt:capability:media-info:video-format` capability.

#### 7.2.2. Active Audio Formats

The `MediaInfo` module **MUST** have a `activeAudioFormats` method that returns an array of `objects` for all media currently in the media pipeline (either playing or paused).

Each item in the `activeAudioFormats` result array **MUST** conform to the same requirements as the individual results from the [`audioFormat` method](#712-audio-format).

Additionally, the `MediaInfo` module **MUST** have an `onActiveAudioFormatsChanged` event that fires whenever any pipeline starts, stops, or changes its current audio format.

Example:

```javascript
MediaInfo.activeAudioFormats().find(f => f.format === Media.AudioFormat.AAC)
/*
[
  {
    "pipelineId": 1,
    "bitRate": 128,
    "channels": 2,
    "container": "audio/mp4",
    "format": "aac",
    "sampleRate": 48
  }
]
*/
```

Access to the `activeAudioFormats` method is gated by `manage` access to the `xrn:firebolt:capability:media-info:audio-format` capability.
