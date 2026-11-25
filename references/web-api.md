# Web Platform - TCPlayer API Documentation

## 1. Overview

TCPlayer is a web player SDK provided by Tencent Cloud for both VOD and live streaming. It comes with a built-in UI and can be quickly integrated.

### Supported Protocols and Formats

| Type | Formats |
|------|---------|
| Audio | MP3 |
| VOD | MP4, HLS (M3U8), FLV (Full support on PC, partial on mobile) |
| Live | HLS, FLV, WebRTC (Low-latency live) |

> **Note**: Only H.264 encoded video is supported.

### Core Features

- Playback speed control, mirroring, progress bar markers, HLS adaptive bitrate
- Referer hotlink protection, preview (trial watch), HLS encrypted playback
- Custom UI, danmaku (bullet comments), watermark, ghost watermark, video playlist
- VR playback (requires Premium License)

---

## 2. License Authorization

Starting from version 5.0.0, using TCPlayer **requires** a License authorization.

| License Type | Features | Authorization Unit | Validity |
|-------------|----------|-------------------|----------|
| **Standard Edition (Free)** | All features available before version 5.0.0 | Exact domains (up to 10) | 1 year (renewable for free) |
| **Premium Edition (Paid)** | Standard + VR playback + Security check | Wildcard domains | - |

> **Tip**: Local development (`localhost` or `127.0.0.1`) does not require License verification.

---

## 3. Initialization Parameters

The player is initialized via `TCPlayer('container-id', options)`.

### 3.1 Basic Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `licenseUrl` | String | None | **Required** Player License URL |
| `appID` | String | None | Required for FileID playback; Tencent Cloud account appID |
| `fileID` | String | None | Required for FileID playback; Tencent Cloud VOD video ID |
| `psign` | String | None | Required for FileID playback; Player signature |
| `sources` | Array | None | Used for URL playback; format: `[{ src: 'url', type: 'video/mp4' }]` |
| `width` | String/Number | None | Player width (can also be controlled via CSS) |
| `height` | String/Number | None | Player height (can also be controlled via CSS) |

### 3.2 Playback Control Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `autoplay` | Boolean | false | Whether to autoplay |
| `controls` | Boolean | true | Whether to show the control bar |
| `poster` | String | None | Full URL of the cover image |
| `muted` | Boolean | false | Whether to play muted |
| `loop` | Boolean | false | Whether to loop playback |
| `preload` | String | "auto" | Preload strategy: "auto" / "meta" / "none" |
| `playbackRates` | Array | [0.5, 1, 1.25, 1.5, 2] | Playback speed options |
| `swf` | String | None | URL of the Flash player SWF file |
| `posterImage` | Boolean | true | Whether to show the cover image |
| `bigPlayButton` | Boolean | true | Whether to show the centered play button |
| `language` | String | "zh-CN" | UI language: "zh-CN" / "en" |
| `languages` | Object | None | Custom multilingual dictionary |
| `reportable` | Boolean | true | Whether to enable data reporting |
| `fakeFullscreen` | Boolean | false | CSS-based pseudo-fullscreen |

### 3.3 Advanced Configuration Parameters

#### Configuration Objects

| Parameter | Type | Description |
|-----------|------|-------------|
| `hlsConfig` | Object | hls.js startup configuration |
| `xp2pConfig` | Object | X-P2P media acceleration configuration |

#### controlBar (Control Bar Configuration)

Configure the visibility of each button in the control bar:
- `playToggle`: Play/Pause button
- `progressControl`: Progress bar
- `fullscreenToggle`: Fullscreen button
- `QualitySwitcherMenuButton`: Resolution switcher
- `volumePanel`: Volume control
- `currentTimeDisplay`: Current time
- `durationDisplay`: Total duration
- `timeDivider`: Time separator
- `playbackRateMenuButton`: Playback rate selector
- `fullscreenRotate`: Screen rotation button
- `pictureInPictureToggle`: Picture-in-Picture button

#### plugins (Plugin Configuration)

```javascript
plugins: {
  // Resume playback
  ContinuePlay: { auto: true },
  
  // Thumbnail preview
  VttThumbnail: { vttUrl: 'path/to/vtt' },
  
  // Dynamic watermark (supports text and image)
  DynamicWatermark: {
    type: 'text',          // 'text' or 'image'
    content: 'Watermark content',
    speed: 0.2
  },
  
  // Playlist
  PlayList: { data: [...], title: 'text', loop: false },

  // Progress bar markers
  ProgressMarker: true,

  // Context menu
  ContextMenu: { mirror: true, statistic: true },
  
  // VR playback (requires Premium License)
  VR: { enable: true },
}
```

#### webrtcConfig (WebRTC Configuration)

```javascript
webrtcConfig: {
  connectRetryCount: 3,     // Reconnection attempts
  connectRetryDelay: 1,     // Reconnection delay (seconds)
  receiveVideo: true,       // Whether to receive video
  receiveAudio: true,       // Whether to receive audio
  showLog: false,           // Whether to print logs to the console
  receiveSEI: false,        // Whether to receive SEI messages
  fallback: true,           // Whether fallback is allowed
  fallbackUrl: ''           // Fallback stream address
}
```

---

## 4. Object Methods

The `player` object returned from initialization supports the following methods:

### 4.1 Playback Control

| Method | Parameters | Return Value | Description |
|--------|-----------|--------------|-------------|
| `play()` | None | void | Play/Resume playback |
| `pause()` | None | void | Pause playback |
| `dispose()` | None | void | Destroy the player |
| `ready(function)` | Function | void | Callback after the player has finished initializing |
| `unload()` | None | void | Stop playback and disconnect the stream (5.0.0+) |

### 4.2 Source Switching

| Method | Parameters | Description |
|--------|-----------|-------------|
| `src(url)` | String | Switch video via URL |
| `loadVideoByID(options)` | Object | Switch video via FileID |

```javascript
// Switch video via FileID
player.loadVideoByID({
  fileID: 'xxxxx',
  appID: '1500005696',
  psign: 'xxxxx'
});
```

### 4.3 Time and Progress

| Method | Parameters | Return Value | Description |
|--------|-----------|--------------|-------------|
| `currentTime(time)` | Number (optional) | Number | Get/set current playback time (seconds) |
| `duration()` | None | Number | Get total video duration (seconds) |
| `currentPDT(timestamp)` | Number (optional) | Number | Get/set the current PDT time |
| `buffered()` | None | TimeRanges | Return the buffered time ranges |
| `bufferedPercent()` | None | Number [0,1] | Return the buffered length as a percentage of the duration |

### 4.4 Volume Control

| Method | Parameters | Return Value | Description |
|--------|-----------|--------------|-------------|
| `volume(vol)` | Number [0,1] (optional) | Number | Get/set volume |
| `muted(flag)` | Boolean (optional) | Boolean | Get/set mute status |

### 4.5 Playback Settings

| Method | Parameters | Return Value | Description |
|--------|-----------|--------------|-------------|
| `playbackRate(rate)` | Number (optional) | Number | Get/set playback speed |
| `poster(src)` | String (optional) | String | Get/set the cover image |
| `width(value)` | Number (optional) | Number | Get/set player width |
| `height(value)` | Number (optional) | Number | Get/set player height |
| `videoWidth()` | None | Number | Get the video resolution width |
| `videoHeight()` | None | Number | Get the video resolution height |

### 4.6 Fullscreen Control

| Method | Description |
|--------|-------------|
| `requestFullscreen()` | Enter fullscreen mode |
| `exitFullscreen()` | Exit fullscreen mode |
| `isFullscreen()` | Return whether the player is in fullscreen mode |

### 4.7 Event Listening

| Method | Parameters | Description |
|--------|-----------|-------------|
| `on(type, listener)` | Event type, callback function | Listen for an event |
| `off(type, listener)` | Event type, callback function | Remove an event listener |
| `one(type, listener)` | Event type, callback function | Listen for an event; the handler runs at most once |

---

## 5. Events

Use `player.on(type, callback)` to listen for events.

### 5.1 Basic Events

| Event Name | Description |
|------------|-------------|
| `play` | Playback started (triggered when `play()` is called or autoplay takes effect) |
| `playing` | Playback resumed after buffering pause; frames begin rendering |
| `pause` | Triggered when paused |
| `ended` | Triggered when playback ends |
| `error` | Triggered when a playback error occurs |
| `timeupdate` | Triggered when the playback position changes |
| `waiting` | Playback stopped, waiting for next frame data |
| `loadedmetadata` | Metadata loading complete |
| `fullscreenchange` | Triggered when fullscreen state changes |
| `blocked` | Triggered when autoplay is blocked by the browser |
| `loadstart` | Data loading started |
| `durationchange` | Video duration data changed |
| `loadeddata` | Current frame data loaded, but not enough data for the next frame |
| `progress` | Media data received |
| `canplay` | The player can start playing |
| `canplaythrough` | The player expects to play through without buffering |
| `seeking` | Seeking started |
| `seeked` | Seeking finished |
| `ratechange` | Playback rate changed |
| `volumechange` | Volume or muted changed |
| `resolutionswitching` | Resolution switch in progress |
| `resolutionswitched` | Resolution switch completed |
| `webrtcsei` | SEI data received (when `receiveSEI` is enabled) |

### 5.2 WebRTC-Specific Events

#### webrtcevent

Events during WebRTC playback:

| Event Code | Description |
|-----------|-------------|
| 1001 | Started pulling stream |
| 1002 | Connected to server |
| 1003 | Video playback started |
| 1004 | Stopped pulling stream; playback ended |
| 1005 | Connection failed, automatic reconnection initiated |
| 1006 | Retrieved stream data is empty |
| 1007 | Started requesting the signaling server (callback: `localSdp`) |
| 1008 | Signaling server request succeeded (callback: `remoteSdp`) |
| 1009 | Stream pull stalling (waiting) |
| 1010 | Stream pull stalling recovered |

#### webrtcstats

Statistics data during WebRTC playback.

#### webrtcsei

SEI data received during WebRTC playback.

#### webrtcfallback

Fallback triggered during WebRTC playback.

---

## 6. Error Codes

Error codes returned when the `error` event is triggered:

### 6.1 Basic Errors

| Error Code | Description | Solution |
|-----------|-------------|----------|
| -1 | No available video URL detected | Check playback source configuration |
| -2 | Video data retrieval timed out | Check network connection |
| 1 | Video loading was interrupted | Check network requests |
| 2 | Loading failed due to network issues | Check network connection |
| 3 | Video decoding error | Video data is abnormal; try re-encoding |
| 4 | Format not supported or data cannot be loaded | CDN resource does not exist or the format is not supported in this environment |
| 5 | Video decryption error | Incorrect key or API error |

### 6.2 VOD Data API Errors

| Error Code | Description |
|-----------|-------------|
| 10 | VOD media data API timed out |
| 11 | VOD media data API returned no data |
| 12 | VOD media data API returned abnormal data |
| 10008 | Media data service could not find the corresponding data (verify appID and fileID) |
| 403 | Hotlink protection parameter authentication failed |
| 1001 | Video file does not exist; check `fileID` |
| 1002 | Preview duration is invalid; it must be within the video duration |
| 1003 | Player config `pcfg` is not unique |
| 1004 | License has expired; check the expiration time setting |
| 1005 | No playable adaptive bitstream found |
| 1006 | Invalid request format |
| 1007 | AppID does not exist |
| 1008 | Missing hotlink protection information |
| 1009 | `psign` verification failed |
| 1010 | Other error |
| 1011 | Failed to obtain the DRM license URL |
| 1012 | Failed to obtain the DRM certificate URL |
| 1013 | Player signature missing the `contentInfo` field |

### 6.3 HLS.js Related Errors

| Error Code | Description |
|-----------|-------------|
| 14 | HTML5 + hls.js network error |
| 15 | HTML5 + hls.js multimedia error |
| 16 | HTML5 + hls.js multiplexing error |
| 13 | No playable video data detected; transcode the video |
| 17 | HTML5 + hls.js other error (`event.source`) |
| 18 | Current browser does not support DRM |
| 19 | Response data verification failed |
| 20 | Website needs device motion permission (iOS 13+) |
| 21 | Website needs device motion permission (iOS 12.2 ~ 13) |
| 30 | `hls.js` failed to load |
| 31 | WebRTC playback library (`TXLivePlayer`) failed to load |
| 32 | `flv.js` failed to load |
| 33 | `dash.js` failed to load |

### 6.4 License Errors

| Error Code | Description |
|-----------|-------------|
| 50 | License verification failed |
| 51 | License expired |
| 52 | License domain verification failed |
| 53 | License time verification failed |
| 54 | Wrong License type |
| 55 | Missing License URL |
| 56 | Failed to fetch License data |

### 6.5 Low-Latency Live Errors

| Error Code | Description |
|-----------|-------------|
| -2002 | Low-latency live stream pull API backend error (stream does not exist or authentication failed) |
| -2001 | Current browser does not support this stream; choose another playback method |
| -2003 | Playback failed; refresh the page and replay |
| -2004 | Connection failed and the reconnection count exceeded the configured limit |
| -2006 | Low-latency live multi-resolution smooth switching API request failed |

### 6.6 Security Plugin Errors

| Error Code | Description |
|-----------|-------------|
| 60 | Player element check failed (player DOM element tampered with or removed) |
| 61 | API check failed (player API tampered with) |
| 62 | Environment check failed (abnormal runtime) |
| 63 | Environment check failed (abnormal runtime) |

---

## 7. Usage Examples

### 7.1 Play via URL

```html
<!DOCTYPE html>
<html>
<head>
  <link href="https://tcsdk.com/player/tcplayer/release/v5.3.4/tcplayer.min.css" rel="stylesheet"/>
  <script src="https://tcsdk.com/player/tcplayer/release/v5.3.4/tcplayer.v5.3.4.min.js"></script>
</head>
<body>
  <video id="player-container-id" width="414" height="270" preload="auto" playsinline webkit-playsinline></video>
  
  <script>
    var player = TCPlayer('player-container-id', {
      sources: [{
        src: 'https://example.com/video.mp4',
        type: 'video/mp4'
      }],
      licenseUrl: 'https://license.vod2.myqcloud.com/license/v2/xxx/v_cube.license',
      autoplay: false,
      poster: 'https://example.com/poster.jpg'
    });
    
    // Event listeners
    player.on('play', function() {
      console.log('Playback started');
    });
    
    player.on('error', function(e) {
      console.error('Playback error:', e);
    });
  </script>
</body>
</html>
```

### 7.2 Play via FileID (Tencent Cloud VOD)

```javascript
var player = TCPlayer('player-container-id', {
  fileID: '3701925921299637010',
  appID: '1500005696',
  psign: 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...',
  licenseUrl: 'https://license.vod2.myqcloud.com/license/v2/xxx/v_cube.license'
});
```

### 7.3 NPM Import

```bash
npm install tcplayer.js
```

```javascript
import TCPlayer from 'tcplayer.js';
import 'tcplayer.js/dist/tcplayer.min.css';

const player = TCPlayer('player-container-id', {
  sources: [{ src: 'video.mp4' }],
  licenseUrl: 'license-url'
});
```

### 7.4 Switch Video

```javascript
// Switch via URL
player.src('https://example.com/another-video.mp4');

// Switch via FileID
player.loadVideoByID({
  fileID: 'new-file-id',
  appID: '1500005696',
  psign: 'new-psign'
});
```

### 7.5 Playback Speed

```javascript
// Set 2x playback speed
player.playbackRate(2);

// Get current playback speed
var rate = player.playbackRate();
```

### 7.6 Progress Control

```javascript
// Seek to a specific time (seconds)
player.currentTime(120);

// Get current playback time
var currentTime = player.currentTime();

// Get total video duration
var duration = player.duration();
```

---

## 8. Important Notes

1. **License Configuration**: A valid `licenseUrl` must be configured for production environments
2. **Cross-Origin Issues**: Video resources must have the correct CORS headers configured
3. **Mobile Adaptation**: Add `playsinline webkit-playsinline` attributes to support inline playback
4. **Browser Compatibility**: Some parameters (such as `controls`, `playbackRates`) may not work when browser playback is hijacked
5. **Method Call Timing**: Some methods can only be called correctly after the `loadedmetadata` event is triggered
6. **Resource Deployment**: For production environments, it is recommended to self-host the player resources rather than directly referencing the CDN
