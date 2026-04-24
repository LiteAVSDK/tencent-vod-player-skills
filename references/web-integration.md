# Web Platform - Integration Guide & VOD Playback

## 1. Development Environment Requirements

- **Browser**: Modern browsers that support HTML5
- **Protocol Support**: HTTP/HTTPS
- **Encoding Format**: Only H.264 encoded video is supported

### Browser Compatibility

| Browser | MP4 | HLS | FLV | WebRTC |
|---------|-----|-----|-----|--------|
| Chrome (Desktop) | ✓ | ✓ | ✓ | ✓ |
| Firefox (Desktop) | ✓ | ✓ | ✓ | ✓ |
| Safari (Desktop) | ✓ | ✓ | ✗ | ✓ |
| Edge | ✓ | ✓ | ✓ | ✓ |
| Chrome (Mobile) | ✓ | ✓ | Partial | ✓ |
| Safari (iOS) | ✓ | ✓ | ✗ | ✓ |

---

## 2. SDK Integration Methods

### Option 1: CDN Import (Recommended)

Import directly in your HTML page:

```html
<!-- Stylesheet -->
<link href="https://tcsdk.com/player/tcplayer/release/v5.3.4/tcplayer.min.css" rel="stylesheet"/>

<!-- JavaScript -->
<script src="https://tcsdk.com/player/tcplayer/release/v5.3.4/tcplayer.v5.3.4.min.js"></script>
```

> **Note**: To play HLS in Chrome/Firefox, you may need to additionally include `hls.js`.

### Option 2: NPM Installation

```bash
npm install tcplayer.js
```

```javascript
import TCPlayer from 'tcplayer.js';
import 'tcplayer.js/dist/tcplayer.min.css';
```

### Option 3: Self-Hosting

1. Download the SDK resource package
2. Deploy the resources on your own server
3. Maintain the original folder directory structure
4. Update the reference paths

> **Recommendation**: For production environments, it is recommended to self-host the player resources for improved stability.

---

## 3. License Authorization Configuration

### 3.1 Obtain a License

1. Log in to the [RT-Cube Console](https://console.cloud.tencent.com/vcube)
2. Apply for a Player License
3. Obtain the License URL

### 3.2 License Types

| Type | Features | Price | Authorization Unit |
|------|----------|-------|-------------------|
| Standard Edition | Basic playback features | Free | Exact domains (up to 10) |
| Premium Edition | Standard + VR + Security check | Paid | Wildcard domains |

### 3.3 Local Development

Local development (`localhost` or `127.0.0.1`) does not require License verification.

---

## 4. Quick Start

### Step 1: Add the Player Container

```html
<video id="player-container-id" 
       width="414" 
       height="270" 
       preload="auto" 
       playsinline 
       webkit-playsinline>
</video>
```

**Important Attribute Descriptions:**

| Attribute | Purpose |
|-----------|---------|
| `preload="auto"` | Preload the video after page load for faster playback |
| `playsinline` | Inline playback on mobile to prevent fullscreen hijacking |
| `webkit-playsinline` | Inline playback support for iOS Safari |

### Step 2: Initialize the Player

#### Play via URL

```javascript
var player = TCPlayer('player-container-id', {
  sources: [{
    src: 'https://example.com/video.mp4',
    type: 'video/mp4'
  }],
  licenseUrl: 'https://license.vod2.myqcloud.com/license/v2/xxx/v_cube.license'
});
```

#### Play via FileID (Tencent Cloud VOD)

```javascript
var player = TCPlayer('player-container-id', {
  fileID: '3701925921299637010',    // Video fileID
  appID: '1500005696',              // VOD account appID
  psign: 'eyJhbGci...',             // Player signature
  licenseUrl: 'https://license.vod2.myqcloud.com/license/v2/xxx/v_cube.license'
});
```

### Step 3: Playback Control

```javascript
// Play
player.play();

// Pause
player.pause();

// Seek to position
player.currentTime(30);  // Seek to the 30th second

// Set playback speed
player.playbackRate(1.5);

// Set volume
player.volume(0.8);

// Mute
player.muted(true);

// Fullscreen
player.requestFullscreen();
```

### Step 4: Event Listening

```javascript
// Playback started
player.on('play', function() {
  console.log('Playback started');
});

// Playback progress update
player.on('timeupdate', function() {
  console.log('Current progress:', player.currentTime());
});

// Playback ended
player.on('ended', function() {
  console.log('Playback ended');
});

// Playback error
player.on('error', function(e) {
  console.error('Playback error:', e);
});
```

### Step 5: Destroy the Player

```javascript
// Destroy the player before leaving the page
player.dispose();
```

---

## 5. VOD Playback Scenarios

### 5.1 Basic Playback

```javascript
var player = TCPlayer('player-container-id', {
  sources: [{ src: 'video.mp4' }],
  licenseUrl: 'license-url',
  autoplay: false,
  controls: true,
  poster: 'poster.jpg'
});
```

### 5.2 Multi-Resolution Switching

```javascript
var player = TCPlayer('player-container-id', {
  fileID: 'xxxxx',
  appID: '1500005696',
  psign: 'xxxxx',
  licenseUrl: 'license-url'
});
// Tencent Cloud VOD automatically provides multi-resolution switching
```

### 5.3 Playback Speed

```javascript
var player = TCPlayer('player-container-id', {
  sources: [{ src: 'video.mp4' }],
  licenseUrl: 'license-url',
  playbackRates: [0.5, 0.75, 1, 1.25, 1.5, 2]  // Custom speed options
});
```

### 5.4 Resume Playback

```javascript
var player = TCPlayer('player-container-id', {
  sources: [{ src: 'video.mp4' }],
  licenseUrl: 'license-url',
  plugins: {
    ContinuePlay: {
      auto: true  // Automatically resume from last position
    }
  }
});
```

### 5.5 Thumbnail Preview

```javascript
var player = TCPlayer('player-container-id', {
  sources: [{ src: 'video.mp4' }],
  licenseUrl: 'license-url',
  plugins: {
    VttThumbnail: {
      vttUrl: 'path/to/thumbnails.vtt'
    }
  }
});
```

### 5.6 Dynamic Watermark

```javascript
var player = TCPlayer('player-container-id', {
  sources: [{ src: 'video.mp4' }],
  licenseUrl: 'license-url',
  plugins: {
    DynamicWatermark: {
      type: 'text',
      content: 'Tencent Cloud',
      speed: 0.2,
      fontSize: 16,
      color: 'rgba(255, 255, 255, 0.5)'
    }
  }
});
```

### 5.7 Playlist

```javascript
var player = TCPlayer('player-container-id', {
  licenseUrl: 'license-url',
  plugins: {
    PlayList: {
      list: [
        { name: 'Video 1', src: 'video1.mp4' },
        { name: 'Video 2', src: 'video2.mp4' },
        { name: 'Video 3', src: 'video3.mp4' }
      ]
    }
  }
});
```

---

## 6. Live Streaming Scenarios

### 6.1 HLS Live

```javascript
var player = TCPlayer('player-container-id', {
  sources: [{
    src: 'https://example.com/live/stream.m3u8',
    type: 'application/x-mpegURL'
  }],
  licenseUrl: 'license-url',
  live: true
});
```

### 6.2 FLV Live

```javascript
var player = TCPlayer('player-container-id', {
  sources: [{
    src: 'https://example.com/live/stream.flv',
    type: 'video/x-flv'
  }],
  licenseUrl: 'license-url',
  live: true
});
```

### 6.3 WebRTC Live (Low-Latency Live)

```javascript
var player = TCPlayer('player-container-id', {
  sources: [{
    src: 'webrtc://example.com/live/stream'
  }],
  licenseUrl: 'license-url',
  webrtcConfig: {
    retryCount: 3,
    connectTimeout: 5000
  }
});

// Listen to WebRTC events
player.on('webrtcevent', function(e) {
  console.log('WebRTC event:', e.data.code);
});
```

---

## 7. Advanced Features

### 7.1 HLS Encrypted Playback

```javascript
var player = TCPlayer('player-container-id', {
  fileID: 'xxxxx',
  appID: '1500005696',
  psign: 'xxxxx',  // Player signature must be configured
  licenseUrl: 'license-url'
});
```

### 7.2 Preview (Trial Watch)

```javascript
// Configure trial duration via psign (server-side configuration)
var player = TCPlayer('player-container-id', {
  fileID: 'xxxxx',
  appID: '1500005696',
  psign: 'psign-with-trial-duration',
  licenseUrl: 'license-url'
});
```

### 7.3 VR Playback (Requires Premium License)

```javascript
var player = TCPlayer('player-container-id', {
  sources: [{ src: 'vr-video.mp4' }],
  licenseUrl: 'license-url',
  plugins: {
    VR: {
      enable: true
    }
  }
});
```

### 7.4 Custom Control Bar

```javascript
var player = TCPlayer('player-container-id', {
  sources: [{ src: 'video.mp4' }],
  licenseUrl: 'license-url',
  controlBar: {
    playToggle: true,
    progressControl: true,
    currentTimeDisplay: true,
    timeDivider: true,
    durationDisplay: true,
    fullscreenToggle: true,
    QualitySwitcherMenuButton: true,
    playbackRateMenuButton: true
  }
});
```

---

## 8. FAQ

### 8.1 Video Cannot Play

**Possible Causes:**
1. License not configured or expired
2. Video format not supported (only H.264 is supported)
3. Cross-origin issues (CORS)
4. Network issues

**Solutions:**
```javascript
player.on('error', function(e) {
  switch(e.data.code) {
    case -1:
      console.log('Check playback source configuration');
      break;
    case 50:
    case 51:
      console.log('Check License configuration');
      break;
    case 4:
      console.log('Check video format and CORS');
      break;
  }
});
```

### 8.2 Autoplay Failure

Modern browsers restrict autoplay. Solutions:

```javascript
var player = TCPlayer('player-container-id', {
  sources: [{ src: 'video.mp4' }],
  licenseUrl: 'license-url',
  autoplay: true,
  muted: true  // Muted autoplay usually succeeds
});

// Listen for autoplay being blocked
player.on('blocked', function() {
  console.log('Autoplay was blocked; user interaction is required');
});
```

### 8.3 Mobile Fullscreen Issues

Add inline playback attributes:

```html
<video id="player" playsinline webkit-playsinline x5-playsinline></video>
```

### 8.4 HLS Playback Issues

Ensure hls.js is included:

```html
<script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
<script src="tcplayer.min.js"></script>
```

---

## 9. Complete Example

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>TCPlayer Example</title>
  <link href="https://tcsdk.com/player/tcplayer/release/v5.3.4/tcplayer.min.css" rel="stylesheet"/>
  <style>
    .player-container {
      width: 100%;
      max-width: 800px;
      margin: 0 auto;
    }
  </style>
</head>
<body>
  <div class="player-container">
    <video id="player-container-id" 
           width="100%" 
           preload="auto" 
           playsinline 
           webkit-playsinline>
    </video>
  </div>

  <script src="https://tcsdk.com/player/tcplayer/release/v5.3.4/tcplayer.v5.3.4.min.js"></script>
  <script>
    // Initialize the player
    var player = TCPlayer('player-container-id', {
      fileID: '3701925921299637010',
      appID: '1500005696',
      psign: 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...',
      licenseUrl: 'https://license.vod2.myqcloud.com/license/v2/xxx/v_cube.license',
      autoplay: false,
      controls: true,
      playbackRates: [0.5, 1, 1.5, 2],
      plugins: {
        ContinuePlay: { auto: true }
      }
    });

    // Event listeners
    player.on('loadedmetadata', function() {
      console.log('Video duration:', player.duration());
    });

    player.on('play', function() {
      console.log('Playback started');
    });

    player.on('pause', function() {
      console.log('Playback paused');
    });

    player.on('timeupdate', function() {
      // Can be used to update a custom progress bar
    });

    player.on('ended', function() {
      console.log('Playback ended');
    });

    player.on('error', function(e) {
      console.error('Playback error:', e.data);
    });

    // Destroy the player when the page unloads
    window.addEventListener('beforeunload', function() {
      player.dispose();
    });
  </script>
</body>
</html>
```
