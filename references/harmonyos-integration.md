# HarmonyOS Platform - Integration Guide & VOD Playback

## 1. Development Environment Requirements

- **Development Tool**: DevEco Studio 5.0.3.900 or above
- **System Version**: HarmonyOS SDK API Version 17 or above
- **Developer Account**: An authorized Huawei developer account

---

## 2. SDK Integration Method

Currently, only local integration via HAR package is supported.

### 2.1 Download the SDK

- **SDK Download**: [LiteAVSDK_Professional_OHOS_lastest.zip](https://mediacloud-76607.gzc.vod.tencent-cloud.com/LiteAVSDK/OHOS/LiteAVSDK_Professional_OHOS_lastest.zip)
- **Demo Download**: [flutter_ohos_vod_video_latest.zip](https://mediacloud-76607.gzc.vod.tencent-cloud.com/LiteAVSDK/OHOS/flutter/download/flutter_ohos_vod_video_latest.zip)

### 2.2 Integrate the HAR Package

**Step 1**: Place the `.har` file in the `entry/libs/` directory

```
your_project/
├── entry/
│   ├── libs/
│   │   └── LiteAVSDK_Professional_OHOS_x.x.x.x.har
│   └── ...
└── ...
```

**Step 2**: Add the dependency in `oh-package.json5`

```json
{
  "dependencies": {
    "liteavsdk": "file:libs/LiteAVSDK_Professional_OHOS_x.x.x.x.har"
  }
}
```

**Step 3**: Click **Sync** to synchronize the project

### 2.3 Configure Permissions

Declare permissions in `entry/src/main/module.json5`:

```json
{
  "module": {
    "requestPermissions": [
      {
        "name": "ohos.permission.INTERNET",
        "reason": "$string:permission_internet_reason",
        "usedScene": {
          "abilities": ["EntryAbility"],
          "when": "always"
        }
      },
      {
        "name": "ohos.permission.GET_NETWORK_INFO",
        "reason": "$string:permission_network_reason",
        "usedScene": {
          "abilities": ["EntryAbility"],
          "when": "always"
        }
      }
    ]
  }
}
```

---

## 3. License Authorization Configuration

The License **must** be integrated before using the player features; otherwise, playback may encounter issues (black screen).

### 3.1 Obtain a License

1. Log in to the [RT-Cube Console](https://console.cloud.tencent.com/vcube)
2. Apply for a Player License
3. Obtain the License URL and License Key

### 3.2 Configure the License

It is recommended to configure the License at application startup:

```typescript
import { getV2TXLivePremierShareInstance, V2TXLivePremierObserver } from 'liteavsdk';

@Entry
@Component
struct Index {
  aboutToAppear() {
    // Set License loading listener
    let observer: V2TXLivePremierObserver = {
      onLicenceLoaded: (result: number, reason: string) => {
        if (result === 0) {
          console.log('License loaded successfully');
        } else {
          console.error('License loading failed:', reason);
        }
      }
    };
    
    getV2TXLivePremierShareInstance(getContext()).addObserver(observer);
    
    // Set the License
    getV2TXLivePremierShareInstance(getContext()).setLicence(
      "https://license.vod2.myqcloud.com/license/v2/xxx/v_cube.license",
      "xxxxxxxxxxxxxxxxxxxxxxxx"
    );
  }
}
```

---

## 4. Quick Start

### 4.1 Create a Player

```typescript
import { TXVodPlayer } from 'liteavsdk';

let player: TXVodPlayer = new TXVodPlayer(getContext());
```

### 4.2 Set Up the Rendering View

Use the HarmonyOS `XComponent` to host the video display:

```typescript
@Entry
@Component
struct PlayerPage {
  private player?: TXVodPlayer;
  private surfaceId: string = '';
  
  build() {
    Column() {
      XComponent({
        id: 'video_player',
        type: XComponentType.SURFACE,
        controller: new XComponentController()
      })
        .onLoad((xContext) => {
          this.surfaceId = xContext.getXComponentSurfaceId();
          // Set the render target
          this.player?.setVideoRenderTarget(this.surfaceId);
        })
        .width('100%')
        .height(300)
    }
  }
}
```

### 4.3 Set Up Event Listeners

```typescript
import { TXVodPlayer, ITXVodPlayListener, TXVodConstants } from 'liteavsdk';

let listener: ITXVodPlayListener = {
  onPlayEvent: (player: TXVodPlayer, event: number, eventData?: Map<string, any>) => {
    switch (event) {
      case TXVodConstants.VOD_PLAY_EVT_PLAY_BEGIN:
        console.log('Playback started');
        break;
      case TXVodConstants.VOD_PLAY_EVT_PLAY_PROGRESS:
        let progress = eventData?.get('EVT_PLAY_PROGRESS');
        let duration = eventData?.get('EVT_PLAY_DURATION');
        console.log(`Progress: ${progress}/${duration}`);
        break;
      case TXVodConstants.VOD_PLAY_EVT_PLAY_END:
        console.log('Playback ended');
        break;
      case TXVodConstants.VOD_PLAY_EVT_VOD_PLAY_PREPARED:
        console.log('Player is ready');
        break;
    }
  },
  
  onNetStatus: (player: TXVodPlayer, statusData: Map<string, any>) => {
    let width = statusData.get(TXVodConstants.NET_STATUS_VIDEO_WIDTH);
    let height = statusData.get(TXVodConstants.NET_STATUS_VIDEO_HEIGHT);
    console.log(`Video resolution: ${width}x${height}`);
  }
};

player.setVodPlayCallback(listener);
```

### 4.4 Start Playback

#### Option 1: Play via URL

```typescript
import { TXPlayInfoParams } from 'liteavsdk';

let params = TXPlayInfoParams.createWithUrl('https://example.com/video.mp4');
player.startVodPlay(params);
```

#### Option 2: Play via FileId (Tencent Cloud VOD)

```typescript
import { TXPlayInfoParams } from 'liteavsdk';

let params = TXPlayInfoParams.createWithFileId(
  1252463788,                     // appId
  '4564972819220421305',          // fileId
  'eyJhbGciOiJIUzI1NiIsInR5cCI...' // pSign (playback signature)
);
player.startVodPlay(params);
```

### 4.5 Stop Playback

```typescript
// Stop playback and clear the last frame
player.stopPlay(true);
```

> **Important**: Always call `stopPlay` before playing again; otherwise, memory leaks or screen flashing may occur.

---

## 5. VOD Playback Scenarios

### 5.1 Playback Control

```typescript
// Pause playback
player.pause();

// Resume playback
player.resume();

// Seek to position (seconds)
player.seek(30);

// Accurate seek
player.seek(30, true);

// Stop playback
player.stopPlay(true);
```

### 5.2 Playback Speed

```typescript
// Set 1.5x speed
player.setRate(1.5);

// Supported range: 0.5 ~ 3.0
```

### 5.3 Loop Playback

```typescript
// Enable loop playback
player.setLoop(true);

// Get loop status
let isLoop = player.isLoop();
```

### 5.4 Mute and Volume

```typescript
// Mute
player.setMute(true);

// Set volume (0-100)
player.setAudioPlayoutVolume(80);
```

### 5.5 Resolution Switching

```typescript
// Get supported bitrate list (HLS)
let bitrates = player.getSupportedBitrates();

// Get current bitrate index
let currentIndex = player.getBitrateIndex();

// Switch resolution
player.setBitrateIndex(2);

// Adaptive bitrate
player.setBitrateIndex(-1);
```

### 5.6 Player Configuration

```typescript
import { TXVodPlayConfig, TXVodConstants } from 'liteavsdk';

let config = new TXVodPlayConfig();
config.connectRetryCount = 5;           // Reconnection attempts
config.timeout = 15;                    // Timeout duration
config.enableAccurateSeek = true;       // Accurate seek
config.smoothSwitchBitrate = true;      // Smooth bitrate switching
config.progressInterval = 200;          // Progress callback interval (ms)
config.preferredResolution = 1280 * 720; // Preferred resolution

player.setConfig(config);
```

### 5.7 Progress and Network Speed Monitoring

```typescript
let listener: ITXVodPlayListener = {
  onPlayEvent: (player, event, eventData) => {
    if (event === TXVodConstants.VOD_PLAY_EVT_PLAY_PROGRESS) {
      let progress = eventData?.get('EVT_PLAY_PROGRESS');      // Current progress
      let playable = eventData?.get('EVT_PLAYABLE_DURATION');  // Playable duration
      let duration = eventData?.get('EVT_PLAY_DURATION');      // Total duration
    }
  },
  
  onNetStatus: (player, statusData) => {
    let speed = statusData.get(TXVodConstants.NET_STATUS_NET_SPEED);  // Network speed
    let fps = statusData.get(TXVodConstants.NET_STATUS_VIDEO_FPS);    // Frame rate
  }
};
```

---

## 6. Advanced Features

### 6.1 Video Pre-play (Instant Playback)

Pre-load the next video while playing the current one:

```typescript
// Pre-play player
let prePlayer = new TXVodPlayer(getContext());

// Disable auto-play
prePlayer.setAutoPlay(false);

// Start pre-loading
let params = TXPlayInfoParams.createWithUrl('https://example.com/next-video.mp4');
prePlayer.startVodPlay(params);

// Listen for the ready event
prePlayer.setVodPlayCallback({
  onPlayEvent: (player, event, eventData) => {
    if (event === TXVodConstants.VOD_PLAY_EVT_VOD_PLAY_PREPARED) {
      // Ready; playback can start at any time
      console.log('Pre-play player is ready');
    }
  }
});

// Call resume when ready to play
// prePlayer.resume();
```

### 6.2 Video Preloading

Download partial content in advance without a player instance:

```typescript
import { TXVodPreloadManager, TXVodGlobalSetting, ITXVodPreloadCallback } from 'liteavsdk';

// 1. Set cache path (must match the player configuration)
TXVodGlobalSetting.setCacheFolderPath('/data/storage/el2/base/cache/txcache');
TXVodGlobalSetting.setMaxCacheSize(500);

// 2. Get the preload manager
let preloadManager = TXVodPreloadManager.getInstance(getContext());

// 3. Start preloading
let taskId = preloadManager.startPreloadUrl(
  'https://example.com/video.mp4',
  5,              // Preload 5MB
  -1,             // No preferred resolution
  new Map(),      // Request headers
  {
    onComplete: (taskId, url) => {
      console.log('Preload completed');
    },
    onError: (taskId, url, code, msg) => {
      console.error('Preload failed:', code, msg);
    }
  } as ITXVodPreloadCallback
);

// Stop preloading
// preloadManager.stopPreload(taskId);
```

> **Recommendation**: It is recommended to keep the concurrency of pre-play and preloading to no more than 3 to save bandwidth and thread resources.

### 6.3 Offline Download

```typescript
import { TXVodDownloadManager, TXVodGlobalSetting, ITXVodDownloadCallback, TXVodDownloadDataSource } from 'liteavsdk';

// 1. Set cache path
TXVodGlobalSetting.setCacheFolderPath('/data/storage/el2/base/cache/txdownload');

// 2. Get the download manager
let downloadManager = TXVodDownloadManager.getInstance(getContext());

// 3. Set callbacks
downloadManager.setDownloadCallback({
  onDownloadStart: (mediaInfo) => {
    console.log('Download started');
  },
  onDownloadProgress: (mediaInfo) => {
    let progress = mediaInfo.getProgress() * 100;
    let speed = mediaInfo.getSpeed();
    console.log(`Progress: ${progress.toFixed(1)}%, Speed: ${speed}KB/s`);
  },
  onDownloadStop: (mediaInfo) => {
    console.log('Download stopped');
  },
  onDownloadFinish: (mediaInfo) => {
    let localPath = mediaInfo.getPlayPath();
    console.log('Download complete, local path:', localPath);
    
    // Play the local video
    let params = TXPlayInfoParams.createWithUrl(localPath);
    player.startVodPlay(params);
  },
  onDownloadError: (mediaInfo, error, reason) => {
    console.error('Download error:', error, reason);
  }
} as ITXVodDownloadCallback);

// 4. Download via URL
downloadManager.startDownloadUrl('https://example.com/video.mp4');

// Or download via FileId
let dataSource = new TXVodDownloadDataSource(
  1252463788,
  '4564972819220421305',
  TXVodDownloadDataSource.QUALITY_720P,
  'psignxxxxxxx'
);
downloadManager.startDownloadDataSource(dataSource);
```

### 6.4 Encrypted Playback

```typescript
// Play encrypted video via FileId; pSign must be configured
let params = TXPlayInfoParams.createWithFileId(
  1252463788,
  '4564972819220421305',
  'eyJhbGciOiJIUzI1NiIsInR5cCI...'  // pSign is required
);
player.startVodPlay(params);
```

### 6.5 External Subtitles and Multi-Audio Tracks (Requires Premium License)

```typescript
// Add external subtitles
player.addSubtitleSource(
  'https://example.com/subtitle.vtt',
  'Chinese Subtitles',
  'text/vtt'
);

// Get subtitle track list
let subtitleTracks = player.getSubtitleTrackInfo();

// Get audio track list
let audioTracks = player.getAudioTrackInfo();

// Select a track
player.selectTrack(trackIndex);

// Deselect a track
player.deselectTrack(trackIndex);
```

### 6.6 Snapshot

```typescript
player.snapshotAsync((pixelMap) => {
  if (pixelMap) {
    // Process the snapshot for rendering or saving
    console.log('Snapshot captured successfully');
  }
});
```

---

## 7. Player Event Listeners

### 7.1 Key Event IDs

| Event ID | Constant Name | Description |
|----------|--------------|-------------|
| 2004 | `VOD_PLAY_EVT_PLAY_BEGIN` | Playback started |
| 2006 | `VOD_PLAY_EVT_PLAY_END` | Playback ended |
| 2005 | `VOD_PLAY_EVT_PLAY_PROGRESS` | Progress update |
| 2013 | `VOD_PLAY_EVT_VOD_PLAY_PREPARED` | Player is ready |
| 2007 | `VOD_PLAY_EVT_PLAY_LOADING` | Buffering |
| 2014 | `VOD_PLAY_EVT_VOD_LOADING_END` | Buffering ended |
| 2009 | `VOD_PLAY_EVT_CHANGE_RESOLUTION` | Resolution changed |

### 7.2 Complete Event Listener Example

```typescript
let listener: ITXVodPlayListener = {
  onPlayEvent: (player, event, eventData) => {
    switch (event) {
      case TXVodConstants.VOD_PLAY_EVT_PLAY_BEGIN:
        console.log('Playback started');
        break;
        
      case TXVodConstants.VOD_PLAY_EVT_PLAY_PROGRESS:
        let progress = eventData?.get('EVT_PLAY_PROGRESS');
        let playable = eventData?.get('EVT_PLAYABLE_DURATION');
        let duration = eventData?.get('EVT_PLAY_DURATION');
        console.log(`Progress: ${progress}s / ${duration}s, Playable: ${playable}s`);
        break;
        
      case TXVodConstants.VOD_PLAY_EVT_PLAY_END:
        console.log('Playback ended');
        break;
        
      case TXVodConstants.VOD_PLAY_EVT_PLAY_LOADING:
        console.log('Buffering...');
        break;
        
      case TXVodConstants.VOD_PLAY_EVT_VOD_LOADING_END:
        console.log('Buffering ended');
        break;
        
      case TXVodConstants.VOD_PLAY_EVT_VOD_PLAY_PREPARED:
        // Suitable timing to call resume() when autoPlay=false
        console.log('Player is ready; you can call resume()');
        break;
        
      case TXVodConstants.ERR_PLAY_PLAY_FAILED:
        console.error('Playback failed');
        break;
    }
  },
  
  onNetStatus: (player, statusData) => {
    let width = statusData.get(TXVodConstants.NET_STATUS_VIDEO_WIDTH);
    let height = statusData.get(TXVodConstants.NET_STATUS_VIDEO_HEIGHT);
    let fps = statusData.get(TXVodConstants.NET_STATUS_VIDEO_FPS);
    let speed = statusData.get(TXVodConstants.NET_STATUS_NET_SPEED);
    let cache = statusData.get(TXVodConstants.NET_STATUS_VIDEO_CACHE);
    
    console.log(`Resolution: ${width}x${height}, FPS: ${fps}, Speed: ${speed}KB/s`);
  },
  
  onSnapshotComplete: (player, pixelMap) => {
    console.log('Snapshot completed');
  },
  
  onSubtitleData: (player, subtitleData) => {
    console.log('Subtitle:', subtitleData.subtitleText);
  }
};

player.setVodPlayCallback(listener);
```

---

## 8. Important Notes

1. **License Configuration**: A valid License must be configured before playback; otherwise, the screen will be black
2. **Resource Cleanup**: Always call `stopPlay` before playing again to avoid memory leaks
3. **Thread Management**: Download-related methods starting with `sync` are time-consuming operations and should not be called on the main thread
4. **Cache Configuration**: Preloading and download features require setting the cache path first, and it must match the player configuration
5. **Concurrency Control**: It is recommended to limit the concurrency of pre-play and preloading to no more than 3
6. **Advanced Features**: External subtitles and multi-audio track features require a Premium License

---

## 9. Complete Example Code

```typescript
import { TXVodPlayer, TXVodPlayConfig, TXPlayInfoParams, TXVodConstants,
         ITXVodPlayListener, TXVodGlobalSetting } from 'liteavsdk';
import { getV2TXLivePremierShareInstance } from 'liteavsdk';

@Entry
@Component
struct PlayerDemo {
  private player?: TXVodPlayer;
  private surfaceId: string = '';
  @State isPlaying: boolean = false;
  @State progress: number = 0;
  @State duration: number = 0;
  
  aboutToAppear() {
    // 1. Configure the License
    getV2TXLivePremierShareInstance(getContext()).setLicence(
      "https://license.vod2.myqcloud.com/license/v2/xxx/v_cube.license",
      "xxxxxxxxxxxxxxxxxxxxxxxx"
    );
    
    // 2. Set global cache
    TXVodGlobalSetting.setCacheFolderPath('/data/storage/el2/base/cache/txcache');
    TXVodGlobalSetting.setMaxCacheSize(500);
    
    // 3. Create the player
    this.player = new TXVodPlayer(getContext());
    
    // 4. Configure the player
    let config = new TXVodPlayConfig();
    config.enableAccurateSeek = true;
    config.progressInterval = 200;
    this.player.setConfig(config);
    
    // 5. Set up event listeners
    this.player.setVodPlayCallback(this.createListener());
  }
  
  createListener(): ITXVodPlayListener {
    return {
      onPlayEvent: (player, event, eventData) => {
        switch (event) {
          case TXVodConstants.VOD_PLAY_EVT_PLAY_BEGIN:
            this.isPlaying = true;
            break;
          case TXVodConstants.VOD_PLAY_EVT_PLAY_PROGRESS:
            this.progress = eventData?.get('EVT_PLAY_PROGRESS') || 0;
            this.duration = eventData?.get('EVT_PLAY_DURATION') || 0;
            break;
          case TXVodConstants.VOD_PLAY_EVT_PLAY_END:
            this.isPlaying = false;
            break;
        }
      },
      onNetStatus: (player, statusData) => {
        // Handle network status
      }
    };
  }
  
  build() {
    Column() {
      // Video rendering area
      XComponent({
        id: 'video_player',
        type: XComponentType.SURFACE,
        controller: new XComponentController()
      })
        .onLoad((xContext) => {
          this.surfaceId = xContext.getXComponentSurfaceId();
          this.player?.setVideoRenderTarget(this.surfaceId);
        })
        .width('100%')
        .height(300)
      
      // Progress bar
      Progress({ value: this.progress, total: this.duration })
        .width('90%')
        .margin({ top: 10 })
      
      // Control buttons
      Row() {
        Button(this.isPlaying ? 'Pause' : 'Play')
          .onClick(() => {
            if (this.isPlaying) {
              this.player?.pause();
              this.isPlaying = false;
            } else {
              if (this.progress === 0) {
                // First playback
                let params = TXPlayInfoParams.createWithUrl('https://example.com/video.mp4');
                this.player?.startVodPlay(params);
              } else {
                this.player?.resume();
              }
              this.isPlaying = true;
            }
          })
        
        Button('Stop')
          .onClick(() => {
            this.player?.stopPlay(true);
            this.isPlaying = false;
            this.progress = 0;
          })
          .margin({ left: 20 })
      }
      .margin({ top: 20 })
    }
    .width('100%')
    .height('100%')
  }
  
  aboutToDisappear() {
    // Release resources
    this.player?.stopPlay(true);
  }
}
```
