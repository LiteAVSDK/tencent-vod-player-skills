---
name: tencent-vod-player-skills
description: "This skill provides comprehensive guidance for integrating and using the Tencent Cloud Player SDK (腾讯云播放器 SDK) across Android, iOS, Flutter, Web and HarmonyOS platforms. It should be used when the user needs to implement video playback functionality using TXVodPlayer/TCPlayer, configure player settings, handle playback events, manage video downloads, or set up video preloading. Trigger keywords include: 腾讯云播放器 SDK, 播放器, TXVodPlayer, TCPlayer, 点播播放, 视频播放, 播放器集成, Tencent Cloud Player SDK, Player SDK, Vod Player, 视频下载, 预下载, TXVodPlayConfig, TXVodDownloadManager, TXVodPreloadManager, Flutter播放器, SuperPlayerPlugin, License, 播放器License, Web播放器, HarmonyOS播放器, 鸿蒙播放器, 短视频组件, TUIPlayerShortVideo, 画中画, TUIPIP, VR播放, 全景视频, 终端极速高清, 超分, TUIPlayerKit."
license: Apache-2.0
metadata: 
    author: dokieyang
    version: 1.4
---

# Tencent Cloud Player SDK

## Product Overview

The Tencent Cloud Player SDK provides video playback capabilities for both live streaming and Video on Demand (VOD) scenarios. It supports multiple platforms and protocols, and is an industry-leading audio/video playback solution.

### Product Strengths

- **Full Platform Coverage**: Supports Android, iOS, Web, Flutter, HarmonyOS and other mainstream platforms
- **Comprehensive Protocol Support**: Supports HLS, DASH, MP4, FLV, WebRTC and other mainstream protocols
- **Rich Features**: Provides playback rate control, resolution switching, bullet comments, watermarks, offline download, and more
- **Superior Performance**: Instant first frame, play-while-caching, adaptive bitrate and other optimization techniques
- **Security & Reliability**: Supports hotlink protection, HLS encryption, DRM and other security mechanisms
- **Quick Integration**: Offers both Integration (UI Included) and Integration (No UI) options to meet different needs

### Application Scenarios

- **Online Education**: Course replays, live classes
- **Short Video**: Feed-based short video playback
- **Long Video**: Movies, TV series, shows on demand
- **Live Streaming**: Showroom, gaming, e-commerce live streams
- **Enterprise Training**: Internal training video playback

### Supported Platforms

| Platform | Minimum Version Requirement | Status |
|----------|---------------------------|--------|
| **Android** | API 16+ (Android 4.1+) | Stable |
| **iOS** | iOS 9.0+ | Stable |
| **Web/H5** | HTML5 modern browsers | Stable |
| **Flutter** | Flutter 2.0+ | Stable |
| **HarmonyOS** | API Version 17+ | Stable |

### Supported Protocols

| Scenario | Protocol Formats |
|----------|-----------------|
| **VOD** | HLS (M3U8), DASH, MP4, MP3, FLV |
| **Live** | RTMP, FLV, HLS, WebRTC (LEB) |

### Core Features

- **Multi-source Playback**: URL playback, FileID playback (VOD), local video playback
- **Playback Optimization**: Instant first frame, play-while-caching, preloading, accurate seek, adaptive bitrate
- **Playback Control**: Speed control (0.5x–3x), loop playback, resume playback, background playback, Picture-in-Picture (PiP)
- **Video Security**: Hotlink protection, HLS encryption, DRM (FairPlay/Widevine), dynamic watermark
- **Advanced Features**: VR panoramic video, HDR/SDR, H.265 hardware decoding, multi-audio track, external subtitles

### SDK Editions

| Edition | Features | Use Cases |
|---------|----------|-----------|
| **Standard** | Basic playback capabilities | General playback needs |
| **Premium** | DRM, short video component, HEVC downgrade playback, etc. | Copyright protection, advanced features |

### License Requirements

- **Mobile (Android/iOS/Flutter/HarmonyOS)**: Starting from version 10.7, a License must be configured for playback to work
- **Web**: Starting from version 5.0.0, a License authorization must be configured
- Failure to configure a License will cause playback failure (black screen)

---

## Usage Guide

Refer to the corresponding reference files for detailed integration guides and API documentation based on the target platform:

### By Platform

| Platform | Reference File | Content |
|----------|---------------|---------|
| **Android** | `references/android-integration.md` | SDK integration guide, VOD usage, complete code examples |
| **Android** | `references/android-api.md` | TXVodPlayer API, callback interfaces, config classes, constants, download manager, preloading |
| **iOS** | `references/ios-integration.md` | SDK integration guide, VOD usage, complete code examples |
| **iOS** | `references/ios-api.md` | TXVodPlayer API, callback interfaces, config classes, global settings, download manager, preloading |
| **Flutter** | `references/flutter-integration.md` | SDK integration guide, VOD usage, player component, complete code examples |
| **Flutter** | `references/flutter-api.md` | SuperPlayerPlugin, TXVodPlayerController, TXLivePlayerController, download controller, config classes |
| **Web** | `references/web-integration.md` | TCPlayer integration guide, CDN/NPM import, VOD & live scenarios, complete code examples |
| **Web** | `references/web-api.md` | TCPlayer initialization parameters, object methods, event listeners, error codes |
| **HarmonyOS** | `references/harmonyos-integration.md` | SDK integration guide (HAR package), VOD usage, complete code examples |
| **HarmonyOS** | `references/harmonyos-api.md` | TXVodPlayer API, callback interfaces, config classes, download manager, preloading, constants |

### Common Documents

| Reference File | Content |
|---------------|---------|
| `references/license-guide.md` | License application, binding, renewal, upgrade, configuration methods for each platform |
| `references/faq.md` | License FAQs, mobile playback issues, Flutter FAQs, log extraction |
| `references/advanced-features.md` | Short video component (TUIPlayerShortVideo), Picture-in-Picture (TUIPIP), VR playback, TSR (super-resolution) |

### Typical Workflow

1. **Identify Platform** → Read the corresponding platform's integration file for integration steps
2. **Configure License** → Read `references/license-guide.md` for License application and configuration
3. **Implement Playback** → Read the corresponding platform's API file for specific interfaces
4. **Advanced Features** → Read `references/advanced-features.md` for short video component, PiP, VR, TSR, etc.
5. **Troubleshoot Issues** → Read `references/faq.md` for platform-specific FAQ solutions

### Quick Search Guide

Since the reference files are large, use the following grep patterns to quickly locate the content you need:

#### Search by Feature

| Feature | Search Pattern | Description |
|---------|---------------|-------------|
| Play methods | `startVodPlay\|startPlay` | Find playback start APIs |
| Pause/Resume | `pause\|resume` | Find playback control APIs |
| Progress control | `seek\|getCurrentPlaybackTime\|getDuration` | Find progress-related APIs |
| Playback speed | `setRate\|playbackRate` | Find speed settings |
| Resolution | `Bitrate\|bitrateIndex` | Find bitrate/resolution switching |
| Offline download | `Download` | Find download management |
| Preloading | `Preload` | Find video preloading |
| Player config | `Config` | Find config classes |
| Event callbacks | `Listener\|Callback` | Find event listeners |
| License | `Licence\|License\|setLicence` | Find authorization config |
| Error codes | `Error\|EVT_` | Find error definitions |
| Short video component | `TUIShortVideo\|TUIPlayerKit` | Find short video component |
| Picture-in-Picture | `PictureInPicture\|PIP` | Find PiP feature |
| VR playback | `VR\|PANORAMA` | Find VR playback |
| TSR (super-resolution) | `SuperResolution\|Monet` | Find super-resolution feature |

#### Platform-specific Search Examples

```bash
# Find download-related APIs for Android
grep -i "download" references/android-api.md

# Find config classes for iOS
grep -i "config" references/ios-api.md

# Find License configuration across all platforms
grep -i "setLicence\|License" references/*-integration.md

# Find player controllers for Flutter
grep -i "Controller" references/flutter-api.md

# Find Web player events
grep -i "on\|event" references/web-api.md

# Find callback interfaces for HarmonyOS
grep -i "Listener\|Callback" references/harmonyos-api.md
```

#### Common Combined Searches

```bash
# Find initialization flow across all platforms
grep -i "init\|setup" references/*.md

# Find video rendering related
grep -i "View\|render\|Surface\|XComponent" references/*-api.md

# Find subtitle-related features
grep -i "subtitle\|track" references/*.md

# Find cache-related config
grep -i "cache\|buffer" references/*.md
```

### Quick Key Class Reference

| Class Name | Function | Platform |
|-----------|----------|----------|
| `TXVodPlayer` | Core player class | Android / iOS / HarmonyOS |
| `TCPlayer` | Web player class | Web |
| `TXVodPlayerController` | Flutter VOD controller | Flutter |
| `TXLivePlayerController` | Flutter live controller | Flutter |
| `SuperPlayerPlugin` | Flutter global static interface | Flutter |
| `SuperPlayerController` | Flutter player component controller | Flutter |
| `TXVodPlayConfig` / `FTXVodPlayConfig` | Player configuration | Android & iOS & HarmonyOS / Flutter |
| `TXPlayerGlobalSetting` / `TXVodGlobalSetting` | Global settings (cache path, etc.) | Android & iOS / HarmonyOS |
| `TXVodDownloadManager` / `TXVodDownloadController` | Offline download manager | Android & iOS & HarmonyOS / Flutter |
| `TXVodPreloadManager` | Video preloading | Android / iOS / Flutter / HarmonyOS |
| `ITXVodPlayListener` (Android/HarmonyOS) / `TXVodPlayListener` (iOS) | Playback event callback | Respective platforms |
| `TXVodConstants` (Android/HarmonyOS) / `TXVodSDKEventDef` (iOS) | Constants/event definitions | Respective platforms |
| `TXCloudVideoView` (Android) / `TXPlayerVideo` (Flutter) / `XComponent` (HarmonyOS) | Video render view | Respective platforms |
| `TXPlayInfoParams` (Android & Flutter & HarmonyOS) / `TXPlayerAuthParams` (iOS) | FileID playback parameters | Respective platforms |
| `TUIShortVideoView` / `TUIShortVideoView` | Short video component view | Android / iOS |
| `FTUIPlayerShortController` | Flutter short video controller | Flutter |
| `TUIPlayerVodStrategyModel` | Short video strategy config | Android / iOS |
| `MonetPlugin` / `TXCMonetPluginManager` | TSR (super-resolution) plugin | Android / iOS |

### Platform Feature Comparison

| Feature | Android | iOS | Flutter | Web | HarmonyOS |
|---------|---------|-----|---------|-----|-----------|
| URL Playback | ✓ | ✓ | ✓ | ✓ | ✓ |
| FileID Playback | ✓ | ✓ | ✓ | ✓ | ✓ |
| Playback Speed | ✓ | ✓ | ✓ | ✓ | ✓ |
| Resolution Switching | ✓ | ✓ | ✓ | ✓ | ✓ |
| Offline Download | ✓ | ✓ | ✓ | ✗ | ✓ |
| Video Preloading | ✓ | ✓ | ✓ | ✗ | ✓ |
| Picture-in-Picture | ✓ | ✓ (Premium) | ✓ | ✗ | ✗ |
| VR Playback | ✓ (Premium) | ✓ (Premium) | ✗ | ✓ (Premium) | ✗ |
| DRM | ✓ (Premium) | ✓ (Premium) | ✗ | ✗ | ✗ |
| External Subtitles | ✓ | ✓ | ✓ | ✓ | ✓ (Premium) |
| Multi-audio Track | ✓ | ✓ | ✓ | ✓ | ✓ (Premium) |
| Short Video Component | ✓ (Premium) | ✓ (Premium) | ✓ (Premium) | ✗ | ✗ |
| TSR (Super-resolution) | ✓ (Premium) | ✓ (Premium) | ✗ | ✗ | ✗ |
