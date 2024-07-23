# Media Capabilities Requirements

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

An audio format includes values such as the audio codec, container, codec level, and bit rate of the audio stream.

A video format may include values such as the video container, format/codec, HDR profile, and resolution of the video stream.

Further, apps may also need to know about the media format currently playing in the media pipeline.

An app may need to check IP video playback that it has initiated to see if Dolby Audio is in fact active (as opposed to just present in the encoded data) in order to display metadata or badges to the user. Apps may also be playing [user-cultivated media](./media-access.md), and therefore have no metadata about the format of the files.

A first-party app may need to check all [Media Pipelines](./media-pipeline.md) for media format characteristics to display a global badge.

Additionally, apps may need to know what is supported by the device, *before* initiating any media playback.

To solve this, Firebolt APIs will be created to detect the formats and codecs currently being decoded by the [Media Pipeline](./media-pipeline.md).  Firebolt APIs will also be created to query whether the device and its peripherals support playing content of various formats.

> TODO: Fix the above links

### 1.1. User Stories

#### 1.1.1. As an OTT App developer

I want to know what is supported by the device and it's active AV chain:

- I want to know if content of a particular video or audio format (Dolby Vision, Dolby Atmos, HEVC, etc.) is supported
- I want to know if content decoding with a particular codec profile or level (HEVC Main 10, VP9 Profile 2, etc.) is supported.
- I want to know whether content of a particular resolution and frame rate is supported

I want to know what kind of content I'm currently playing:

- I want to know which video or audio format the media I'm currently playing is sending to the decoder.
- I want to know which video or audio format profile or level the media I'm currently playing is being sent to the decoder.
- I want to know what resolution and frame rate is currently being sent to the decoder.

#### 1.1.2. As a first-party App developer

I want to show an audio/videophile overlay with detailed information:

- I want to know all video or audio formats that are currently being sent to a decoder.
- I want to know all video or audio format profiles/levels currently being sent to a decoder.
- I want to know all resolution and framerates currently being sent to a decoder.

## 2. Table of Contents

- [1. Overview](#1-overview)
  - [1.1. User Stories](#11-user-stories)
    - [1.1.1. As an OTT App developer](#111-as-an-ott-app-developer)
    - [1.1.2. As a first-party App developer](#112-as-a-first-party-app-developer)
- [2. Table of Contents](#2-table-of-contents)
- [3. Constants, Types, and Schemas](#3-constants-types-and-schemas)
  - [3.1. Media Codecs](#31-media-codecs)
  - [3.2. Dimensions](#32-dimensions)
  - [3.3. Resolution User-Friendly Name](#33-resolution-user-friendly-name)
  - [3.4. Video Modes](#34-video-modes)
  - [3.5. HDR Profiles](#35-hdr-profiles)
  - [3.6. Colorimetry](#36-colorimetry)
  - [3.7. Color Depth](#37-color-depth)
  - [3.8. Color Space \& Chroma Subsampling](#38-color-space--chroma-subsampling)
  - [3.9. Audio Output Mode](#39-audio-output-mode)
  - [3.10. Quantization Range](#310-quantization-range)
- [4. Media Capabilities](#4-media-capabilities)
  - [4.1. Supported Audio Modes](#41-supported-audio-modes)
  - [4.2. Supported Video Modes](#42-supported-video-modes)
  - [4.3. Supported Audio Codecs](#43-supported-audio-codecs)
  - [4.4. Supported Video Codecs](#44-supported-video-codecs)
  - [4.5. Color Depth](#45-color-depth)
  - [4.6. HDR Profiles](#46-hdr-profiles)
  - [4.7. Atmos Supported](#47-atmos-supported)
- [5. Display Support](#5-display-support)
  - [5.1. HDR Profiles](#51-hdr-profiles)
  - [5.2. Color Depth](#52-color-depth)
  - [5.3. Display Size](#53-display-size)
  - [5.4. Native Resolution](#54-native-resolution)
  - [5.5. Native Resolution Name](#55-native-resolution-name)
  - [5.6. Native Refresh Rate](#56-native-refresh-rate)
  - [5.7. Colorimetry](#57-colorimetry)
  - [5.10. Supported Video Modes](#510-supported-video-modes)
- [6. Audio Output](#6-audio-output)
  - [6.1. Mode](#61-mode)
- [7. Video Output](#7-video-output)
  - [7.1. Mode](#71-mode)
  - [7.2. Resolution](#72-resolution)
  - [7.3. HDR Profile](#73-hdr-profile)
  - [7.4. Color Depth](#74-color-depth)
  - [7.5. Colorimetry](#75-colorimetry)
  - [7.6. Color Space \& Chroma Subsampling](#76-color-space--chroma-subsampling)
  - [7.7. Quantization Range](#77-quantization-range)
  - [7.8. Current Output Settings](#78-current-output-settings)

## 3. Constants, Types, and Schemas

### 3.1. Media Codecs

The Firebolt `Media` module **MUST** have an `AudioCodec` enumeration:

| Name     | Description                                    |
| -------- | ---------------------------------------------- |
| `aac`    | Advanced Audio Coding                          |
| `ac3`    | Dolby Digital / Dolby Audio Codec 3            |
| `ac4`    | Dolby Audio Codec 4                            |
| `eac3`   | Dolby Digital Plus / Dolby Enhanced AC-3 / DD+ |
| `mpeg3`  | MPEG-1 Part 3 & MPEG-2 Part 3                  |
| `opus`   | IETF Opus                                      |
| `pcm`    | Pulse Code Modulation/WAVE                     |
| `truehd` | Dolby TrueHD / MLP                             |
| `vorbis` | Xiph.org Vorbis                                |

The Firebolt `Media` module **MUST** have a `VideoCodec` enumeration:

| Name    | Description                                    |
| ------- | ---------------------------------------------- |
| `av1`   | AOMedia Video 1                                |
| `avc`   | Advanced Video Coding / MPEG-4 Part 10 (H.264) |
| `hevc`  | High Efficiency Video Coding (H.265)           |
| `mpeg1` | MPEG-1 Part 2 Visual                           |
| `mpeg2` | MPEG-2 Part 2 Visual                           |
| `vp8`   | Video Processor 8                              |
| `vp9`   | Video Processor 9                              |

### 3.2. Dimensions

The Firebolt `Types` module must have a `Dimensions` object of the following schema:

| Field    | Type     |
| -------- | -------- |
| `height` | `number` |
| `width`  | `number` |

### 3.3. Resolution User-Friendly Name

The Firebolt `Media` module **MUST** have a `ResolutionName` enumeration:

| Class     | Applicable Resolutions |
| --------- | ---------------------- |
| `sd`      | `576p` and lower       |
| `hd`      | `720p`                 |
| `fhd`     | `1080p`                |
| `uhd`     | `2160` and higher      |
| `unknown` | unknown or no display  |

### 3.4. Video Modes

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

### 3.5. HDR Profiles

The Firebolt `Media` module **MUST** have an `HDRProfile` enumeration:

- `dolbyVision`
- `hdr10`
- `hdr10plus`
- `hlg`
- `sdr`
- `technicolor`
- `unknown`

### 3.6. Colorimetry

The Firebolt `Media` module **MUST** have a `Colorimetry` enumeration:

- `BT2020cYCC`
- `BT2020RGB`
- `BT2020YCC`
- `DCI-P3`
- `opRGB`
- `opYCC601`
- `sYCC601`
- `xvYCC601`
- `xvYCC709`
- `unknown`

> TODO: Do we need to add `ICtCp`?  https://professional.dolby.com/siteassets/pdfs/ictcp_dolbywhitepaper_v071.pdf

### 3.7. Color Depth

The Firebolt `Media` module **MUST** have a `ColorDepth` enumeration:

- `8`
- `10`
- `12`
- `auto`
- `unknown`

### 3.8. Color Space & Chroma Subsampling

The Firebolt `Media` module **MUST** have a `ColorSpace` enumeration:

| Name       | Chroma |
| ---------- | ------ |
| `RGB444`   | 4:4:4  |
| `YCbCr420` | 4:2:0  |
| `YCbCr422` | 4:2:2  |
| `YCbCr444` | 4:4:4  |
| `other`    |        |
| `unknown`  |        |

### 3.9. Audio Output Mode

The Firebolt `Media` module **MUST** have an `AudioMode` enumeration:

- `auto`
- `mono`
- `passthrough`
- `stereo`
- `surround`
- `unknown`

### 3.10. Quantization Range

The Firebolt `VideoOutput` module **MUST** have a `QuantizationRange` enumeration:

- `full`
- `limited`
- `unknown`

## 4. Media Capabilities

Apps need to know what type of media the device and its connected peripherals are together capable of playing.

To facilitate this, the `MediaCapabilities` module will provide convenience methods that encapsulate the media-playing capabilities of the device as well as any of its connected peripherals, including (but not limited to) displays, sound bars, and receivers.

These values **MUST NOT** change without a settings change, peripheral change, or firmware update.

### 4.1. Supported Audio Modes

The `MediaCapabilities` module **MUST** have an `audioModes` method that returns an array of `Media.AudioMode` values describing the audio modes commonly supported across all relevant peripherals in the user's AV chain.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:media-capabilities:info` capability.

```javascript
MediaCapabilities.audioModes()
//> ["passthrough", "stereo", "surround"]
```

### 4.2. Supported Video Modes

The `MediaCapabilities` module **MUST** have a `videoModes` method that returns an array of `Media.VideoMode` values describing the video modes commonly supported across all relevant peripherals in the user's AV chain.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:media-capabilities:info` capability.

```javascript
MediaCapabilities.videoModes()
//> ["720p50", "720p60", "1080p50", "1080p60"]
```

### 4.3. Supported Audio Codecs

The `MediaCapabilities` module **MUST** have an `audioCodecs` method that returns an array of `Media.AudioCodec` values describing the audio codecs commonly supported across all relevant peripherals in the user's AV chain.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:media-capabilities:info` capability.

```javascript
MediaCapabilities.audioCodecs()
//> [
//>   "aac",
//>   "ac3",
//>   "ac4",
//>   "eac3",
//>   "mpeg3",
//>   "pcm"
//> ]
```

### 4.4. Supported Video Codecs

The `MediaCapabilities` module **MUST** have an `videoCodecs` method that returns an array of `Media.VideoCodec` values describing the video codecs commonly supported across all relevant peripherals in the user's AV chain.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:media-capabilities:info` capability.

```javascript
MediaCapabilities.videoCodecs()
//> [
//>   "av1",
//>   "avc",
//>   "hevc",
//>   "mpeg1",
//>   "mpeg2",
//>   "vp8",
//>   "vp9",
//>   "vp10"
//> ]
```

### 4.5. Color Depth

The `MediaCapabilities` module **MUST** have a `colorDepth` method that returns a numeric value describing the maximum color depth commonly supported across all relevant peripherals in the user's AV chain.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:media-capabilities:info` capability.

```javascript
MediaCapabilities.colorDepth()
//> 10
```

### 4.6. HDR Profiles

The `MediaCapabilities` module **MUST** have an `hdrProfiles` method that returns an array of `Media.HDRProfile` values describing the HDR capabilities commonly supported across all relevant peripherals in the user's AV chain.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:media-capabilities:info` capability.

```javascript
MediaCapabilities.hdrProfiles()
//> ["dolbyVision", "hdr10", "hdr10plus", "hlg"]
```

### 4.7. Atmos Supported

The `MediaCapabilities` module **MUST** have an `atmosSupported` method that returns a boolean describing whether or not Dolby Atmos is commonly  supported across all relevant peripherals in the user's AV chain for immersive audio output.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:media-capabilities:info` capability.

```javascript
MediaCapabilities.atmosSupported()
//> true
```

## 5. Display Support

Apps need to know various aspects of the current (or built-in) video output properties of a device and its connected display.

These will be surfaced in the `Display` module.

### 5.1. HDR Profiles

The `Display` module **MUST** have an `hdrProfiles` method that returns an array of `Media.HDRProfile` values describing the display's supported HDR profiles.

If no display is present, an empty array **MUST** be returned.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.hdrProfiles()
//> ["dolbyVision", "hdr10", "hdr10plus", "hlg"]
```

### 5.2. Color Depth

The `Display` module **MUST** have a `colorDepth` method that returns a numeric value describing the display's supported color depth.

If no display is present, a value of `0` **MUST** be returned.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.colorDepth()
//> 10
```

### 5.3. Display Size

The `Display` module **MUST** have a `size` method that returns a `Types.Dimensions` object describing the physical width and height of the display, in centimeters.

If no display is present, the height and width values **MUST** both be zero.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.size()
//> { "width": 157, "height": 91 }
```

### 5.4. Native Resolution

The `Display` module **MUST** have an `nativeResolution` method that returns a `Types.Dimensions` object describing the `width` and `height` of the display's native resolution as numbers, in pixels.

If no display is present, the height and width values **MUST** both be zero.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.nativeResolution()
//> { "width": 1920, "height": 1080 }
```

### 5.5. Native Resolution Name

The `Display` module **MUST** have a `nativeResolutionName` method that returns a `Media.ResolutionName` value describing the user-friendly name of the display's native resolution.

If no display is present, a value of `unknown` **MUST** be returned.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.nativeResolutionName()
//> "uhd"
```

### 5.6. Native Refresh Rate

The `Display` module **MUST** have a `nativeRefreshRate` method that returns an number value describing the native refresh rate of the display (in Hz).

If no display is present, a value of zero **MUST** be returned.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.nativeRefreshRate()
//> 120
```

### 5.7. Colorimetry

The `Display` module **MUST** have a `colorimetry` method that returns an array of `Media.Colorimetry` values describing the display's supported colorimetry values.

If no display is present, an empty array **MUST** be returned.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.colorimetry()
//> ["BT2020RGB", "BT2020YCC", "xvYCC601", "xvYCC709"]
```

### 5.10. Supported Video Modes

The `Display` module **MUST** have a `videoModes` method that returns an array of `Media.VideoMode` values describing the video modes supported by the display.

If no display is present, an empty array **MUST** be returned.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:display:info` capability.

```javascript
Display.videoModes()
//> ["720p50", "720p60", "1080p50", "1080p60"]
```

## 6. Audio Output

Apps need to know various aspects of the audio system, such as which audio output mode is currently set.

These APIs will be surfaced in the `AudioOutput` module.

### 6.1. Mode

The `AudioOutput` module **MUST** include a `mode` method that returns a `Media.AudioMode` value describing the device's current audio output mode.

This method **MUST** have a corresponding `onModeChanged` event to notify listeners after a change to this property has been made and that change has taken effect.

Access to these methods **MUST** require the `use` role of the `xrn:firebolt:capability:audio-output:mode` capability.

```javascript
AudioOutput.mode()
//> "stereo"
```

## 7. Video Output

Apps need to know various aspects of the video output system, such as the current video mode/resolution.

These APIs will be surfaced in the `VideoOutput` module.

### 7.1. Mode

The `VideoOutput` module **MUST** have a `mode` property that returns a `Media.VideoMode` value describing the currently set video output mode.

This method **MUST** have a corresponding `onModeWillChange` event to notify listeners when a change to this property has been made but before that change has taken effect.

This method **MUST** have a corresponding `onModeChanged` event to notify listeners after a change to this property has been made and that change has taken effect.

Access to these methods **MUST** require the `use` role of the `xrn:firebolt:capability:video-output:mode` capability.

```javascript
VideoOutput.mode()
//> "1080p60"
```

### 7.2. Resolution

The `VideoOutput` module **MUST** have a `resolution` method that returns a `Types.Dimensions` object describing the current video output resolution, in pixels.

This method **MUST** have a corresponding `onResolutionChanged` event to notify listeners after a change to the device's `videoMode` property has been made and that change has taken effect.

Access to these methods **MUST** require the `use` role of the `xrn:firebolt:capability:video-output:mode` capability.

```javascript
VideoOutput.resolution()
//> { "width": 1920, "height": 1080 }
```

### 7.3. HDR Profile

The `VideoOutput` module **MUST** have an `hdrProfile` method that returns a `Media.HDRProfile` value describing the HDR profile currently set for video output.

This method **MUST** have a corresponding `onHdrProfileChanged` event to notify listeners after this property has changed and that change has taken effect.

Access to these methods **MUST** require the `use` role of the `xrn:firebolt:capability:video-output:hdrprofile` capability.

```javascript
VideoOutput.hdrProfile()
//> "hdr10plus"
```

### 7.4. Color Depth

The `VideoOutput` module **MUST** have a `colorDepth` method that returns a `Media.ColorDepth` value describing the current color depth setting for video output.

This method **MUST** have a corresponding `onColorDepthChanged` event to notify listeners after this property has changed and that change has taken effect.

Access to these methods **MUST** be governed by the `xrn:firebolt:capability:video-output:colordepth` capability.

```javascript
VideoOutput.colorDepth()
//> "10"
```

### 7.5. Colorimetry

The `VideoOutput` module **MUST** have a `colorimetry` method that returns a `Media.Colorimetry` value describing the current colorimetry setting for video output.

Access to this method **MUST** require the `use` role of the `xrn:firebolt:capability:video-output:colorimetry` capability.

```javascript
VideoOutput.colorimetry()
//> "BT2020YCC"
```

### 7.6. Color Space & Chroma Subsampling

The `VideoOutput` module **MUST** have a `colorSpace` method that returns a `Media.ColorSpace` value describing the color space and chroma subsampling value currently set for video output.

This method **MUST** have a corresponding `onColorSpaceChanged` event to notify listeners after this property has changed and that change has taken effect.

Access to these methods **MUST** be governed by the `xrn:firebolt:capability:video-output:colorspace` capability.

```javascript
VideoOutput.colorSpace()
//> "YCbCr422"
```

### 7.7. Quantization Range

The `VideoOutput` module **MUST** have a `quantizationRange` method that returns a `VideoOutput.QuantizationRange` value describing the quantization range currently set for video output.

Access to this method **MUST** be governed by the `xrn:firebolt:capability:video-output:quantization` capability.

```javascript
VideoOutput.quantizationRange()
//> "limited"
```

### 7.8. Current Output Settings

The `VideoOutput` module **MUST** have a `currentSettings` method that returns an object describing various properties currently used for video output.

This method **MUST** return the following properties:

| Property      | Type                |
| ------------- | ------------------- |
| `colorDepth`  | `Media.ColorDepth`  |
| `colorimetry` | `Media.Colorimetry` |
| `colorSpace`  | `Media.ColorSpace`  |
| `hdrProfile`  | `Media.HDRProfile`  |
| `mode`        | `Media.VideoMode`   |
| `resolution`  | `Types.Dimensions`  |

Access to this method **MUST** be governed by the `xrn:firebolt:capability:video-output:info` capability.

This method **MUST** have a corresponding `onCurrentSettingsChanged` event to notify listeners after any of the specified output properties have changed and that those changes have taken effect.

```javascript
Video.currentSettings()
//> {
//>   "colorDepth": "10",
//>   "colorimetry": "BT2020YCC",
//>   "colorSpace": "YCbCr422",
//>   "hdrProfile": "hdr10plus",
//>   "mode": "1080p60",
//>   "resolution": { "width": 1920, "height": 1080 }
//> }
```
