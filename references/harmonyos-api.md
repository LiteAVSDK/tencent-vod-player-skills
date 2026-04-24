# HarmonyOS Platform - API Documentation

## 1. TXVodPlayer (Core Player Class)

The core playback class for VOD scenarios, responsible for video playback control, configuration management, and event listening.

> **Important**: Before use, you must set the Licence via `getV2TXLivePremierShareInstance(context).setLicence(url, key)` for playback to work; otherwise, the screen will be black.

### 1.1 Basic Playback APIs

| Method | Parameters | Return Value | Description |
|--------|-----------|--------------|-------------|
| `startVodPlay(params)` | params: TXPlayInfoParams | number | Start playback via fileId or URL |
| `stopPlay(clearLastImg)` | clearLastImg: boolean | void | Stop playback; optionally clear the last frame |
| `isPlaying()` | None | boolean | Returns whether playback is in progress |
| `pause()` | None | void | Pause playback, retaining the last frame |
| `resume()` | None | void | Resume playback |
| `seek(time)` | time: number (seconds) | void | Seek to a specified time point (non-accurate seek) |
| `seek(time, accurate)` | time + accurate: boolean | void | Accurate/non-accurate seek |

### 1.2 Time and Progress APIs

| Method | Return Value | Description |
|--------|--------------|-------------|
| `getCurrentPlaybackTime()` | number (seconds) | Get current playback progress |
| `getDuration()` | number (seconds) | Get total video duration |
| `getBufferDuration()` | number (seconds) | Get buffer duration |
| `getPlayableDuration()` | number (seconds) | Get playable duration |
| `getWidth()` | number | Get video width |
| `getHeight()` | number | Get video height |

### 1.3 Player Configuration APIs

| Method | Parameters | Description |
|--------|-----------|-------------|
| `setAutoPlay(autoPlay)` | boolean (default true) | Set whether to auto-play |
| `setStartTime(time)` | number (seconds) | Set start time (call before playback starts) |
| `setLoop(loop)` | boolean | Set loop playback |
| `isLoop()` | None | Get loop playback status |
| `setConfig(config)` | TXVodPlayConfig | Set player configuration |
| `setVideoRenderTarget(target)` | Render target | Set video render target (set before playback) |

### 1.4 Video-Related APIs

| Method | Parameters | Description |
|--------|-----------|-------------|
| `snapshotAsync(callback)` | Callback function | Asynchronously capture the current frame (time-consuming operation) |
| `setRate(rate)` | number (0.5~3.0) | Set playback speed |
| `getSupportedBitrates()` | None | Get HLS supported bitrate list; returns TXBitrateItem[] |
| `getBitrateIndex()` | None | Get the current bitrate index |
| `setBitrateIndex(index)` | number (-1=adaptive) | Switch resolution |
| `setAutoMaxBitrate(max)` | number | Set the maximum bitrate limit for adaptive mode |

### 1.5 Audio and Subtitle APIs

| Method | Parameters | Description |
|--------|-----------|-------------|
| `setMute(mute)` | boolean | Set mute |
| `setAudioPlayoutVolume(volume)` | number (0-100) | Set volume |
| `addSubtitleSource(url, name, mimeType)` | VTT/SRT URL | Add external subtitles (requires Premium License) |
| `getSubtitleTrackInfo()` | None | Get subtitle track list; returns TXTrackInfo[] |
| `getAudioTrackInfo()` | None | Get audio track list; returns TXTrackInfo[] |
| `selectTrack(index)` | Track index | Select a track |
| `deselectTrack(index)` | Track index | Deselect a track |

### 1.6 Event Callback APIs

| Method | Parameters | Description |
|--------|-----------|-------------|
| `setVodPlayCallback(callback)` | ITXVodPlayCallback | Set the playback event callback interface |

---

## 2. ITXVodPlayListener (Playback Event Callback)

The callback listener interface for playback events and network status.

### 2.1 Interface Methods

| Method | Description |
|--------|-------------|
| `onPlayEvent(player, event, eventData)` | Playback event notification (start, first frame, loading, progress, end, etc.) |
| `onNetStatus(player, statusData)` | Network status event callback (bitrate, frame rate, speed, etc.) |
| `onSnapshotComplete?(player, pixelMap)` | Snapshot completion callback (optional) |
| `onSubtitleData?(player, subtitleData)` | Subtitle data callback (optional) |

### 2.2 onPlayEvent Callback

```typescript
onPlayEvent(player: TXVodPlayer, event: number, eventData?: Map<string, ParamType>): void
```

**Parameter Description:**
- `player`: The current player object
- `event`: Player event ID (corresponds to TXVodConstants)
- `eventData`: Event parameters (Key-Value)

### 2.3 onNetStatus Callback

```typescript
onNetStatus(player: TXVodPlayer, statusData: Map<string, ParamType>): void
```

**Parameter Description:**
- `player`: The current player object
- `statusData`: Network status parameters (bitrate, resolution, download speed, etc.)

---

## 3. TXVodGlobalSetting (Global Settings)

Global configuration class for the VOD player.

### 3.1 API List

| Method | Parameters | Description |
|--------|-----------|-------------|
| `setCacheFolderPath(path)` | string | Set the cache directory for the playback engine |
| `setMaxCacheSize(sizeMB)` | number (MB) | Set the maximum cache size |
| `setPlayCGIHosts(hosts)` | string[] | Set backup domain names for Tencent Cloud PlayCGI |

### 3.2 Usage Example

```typescript
import { TXVodGlobalSetting } from 'liteavsdk';

// Set cache path
TXVodGlobalSetting.setCacheFolderPath('/data/storage/el2/base/cache/txcache');

// Set maximum cache to 500MB
TXVodGlobalSetting.setMaxCacheSize(500);
```

---

## 4. TXVodPlayConfig (Player Configuration Class)

### 4.1 Configuration Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `connectRetryCount` | number | 3 | Connection retry count |
| `connectRetryInterval` | number | 3 | Retry interval (seconds) |
| `timeout` | number | 10 | Connection timeout (seconds) |
| `headers` | Map<string, string> | null | Custom HTTP request headers |
| `enableAccurateSeek` | boolean | true | Enable accurate seek |
| `smoothSwitchBitrate` | boolean | true | Smooth bitrate switching (HLS) |
| `progressInterval` | number | 500 | Progress callback interval (milliseconds) |
| `maxBufferSize` | number | 0 | Maximum buffer size (MB; 0 = default) |
| `maxPreloadSize` | number | 0 | Maximum preload buffer size (MB) |
| `preferredResolution` | number | -1 | Preferred resolution (width × height) |
| `mediaType` | number | AUTO | Media type |

### 4.2 Media Type Constants

| Constant | Value | Description |
|----------|-------|-------------|
| `MEDIA_TYPE_AUTO` | 0 | Auto-detect |
| `MEDIA_TYPE_HLS_VOD` | 1 | HLS VOD |
| `MEDIA_TYPE_HLS_LIVE` | 2 | HLS Live |
| `MEDIA_TYPE_FILE_VOD` | 3 | File VOD (MP4, etc.) |
| `MEDIA_TYPE_DASH_VOD` | 4 | DASH VOD |

### 4.3 Usage Example

```typescript
import { TXVodPlayConfig, TXVodConstants } from 'liteavsdk';

let config = new TXVodPlayConfig();
config.connectRetryCount = 5;
config.timeout = 15;
config.enableAccurateSeek = true;
config.smoothSwitchBitrate = true;
config.progressInterval = 200;
config.preferredResolution = 1280 * 720;  // 720P
config.mediaType = TXVodConstants.MEDIA_TYPE_HLS_VOD;

player.setConfig(config);
```

---

## 5. TXVodDownloadManager (Download Manager)

Video download interface class using the singleton pattern. Supports MP4 and HLS format downloads.

### 5.1 Get Instance

```typescript
import { TXVodDownloadManager } from 'liteavsdk';

let manager = TXVodDownloadManager.getInstance(context);
```

### 5.2 Error Code Constants

| Constant Name | Value | Description |
|--------------|-------|-------------|
| `DOWNLOAD_FORMAT_ERROR` | -5004 | Unsupported download format |
| `DOWNLOAD_DISCONNECT` | -5005 | Network error |
| `DOWNLOAD_PATH_ERROR` | -5007 | Download directory access failed |
| `DOWNLOAD_403FORBIDDEN` | -5008 | Signature expired or illegal request |

### 5.3 API List

| Method | Parameters | Description |
|--------|-----------|-------------|
| `getInstance(context)` | Context | Get the singleton instance |
| `setHeaders(headers)` | Map<string, string> | Set download HTTP request headers |
| `setDownloadCallback(callback)` | ITXVodDownloadCallback | Set the download callback (must be set before downloading) |
| `startDownloadUrl(url, preferredResolution?)` | string, number? | Start download via URL |
| `startDownloadDataSource(dataSource)` | TXVodDownloadDataSource | Start download via fileId |
| `stopDownload(dinfo)` | TXVodDownloadMediaInfo | Stop a download task |
| `deleteDownloadMediaInfo(info)` | TXVodDownloadMediaInfo | Delete download info and files |
| `syncGetDownloadMediaInfoList()` | None | Synchronously get all download list (time-consuming operation) |
| `syncGetDownloadMediaInfoByFileId(appId, fileId, qualityId)` | ... | Get download info by fileId |
| `syncGetDownloadMediaInfoByUrl(url, preferredResolution?)` | ... | Get download info by URL |

### 5.4 Usage Example

```typescript
import { TXVodDownloadManager, TXVodGlobalSetting, ITXVodDownloadCallback } from 'liteavsdk';

// 1. Set cache path (required)
TXVodGlobalSetting.setCacheFolderPath('/data/storage/el2/base/cache/txdownload');

// 2. Get the download manager
let manager = TXVodDownloadManager.getInstance(getContext());

// 3. Set download callbacks
manager.setDownloadCallback({
  onDownloadStart: (mediaInfo) => {
    console.log('Download started');
  },
  onDownloadProgress: (mediaInfo) => {
    console.log('Download progress:', mediaInfo.getProgress());
  },
  onDownloadStop: (mediaInfo) => {
    console.log('Download stopped');
  },
  onDownloadFinish: (mediaInfo) => {
    console.log('Download complete, path:', mediaInfo.getPlayPath());
  },
  onDownloadError: (mediaInfo, error, reason) => {
    console.error('Download error:', error, reason);
  }
} as ITXVodDownloadCallback);

// 4. Start download
manager.startDownloadUrl('https://example.com/video.mp4');
```

---

## 6. ITXVodDownloadCallback (Download Callback Interface)

### 6.1 Callback Methods

| Method | Parameters | Description |
|--------|-----------|-------------|
| `onDownloadStart(mediaInfo)` | TXVodDownloadMediaInfo | Download started |
| `onDownloadProgress(mediaInfo)` | TXVodDownloadMediaInfo | Download progress updated |
| `onDownloadStop(mediaInfo)` | TXVodDownloadMediaInfo | Download stopped |
| `onDownloadFinish(mediaInfo)` | TXVodDownloadMediaInfo | Download completed |
| `onDownloadError(mediaInfo, error, reason)` | ..., number, string | Download error |

---

## 7. TXVodDownloadDataSource (Download Data Source)

### 7.1 Quality Constants

| Constant Name | Value | Description |
|--------------|-------|-------------|
| `QUALITY_UNKOWN` | -1 | Unknown quality |
| `QUALITY_240P` | 240 | Smooth 240P |
| `QUALITY_360P` | 360 | Smooth 360P |
| `QUALITY_480P` | 480 | Standard 480P |
| `QUALITY_540P` | 540 | Standard 540P |
| `QUALITY_720P` | 720 | HD 720P |
| `QUALITY_1080P` | 1080 | Full HD 1080P |
| `QUALITY_2K` | 2000 | 2K |
| `QUALITY_4K` | 4000 | 4K |

### 7.2 Constructor

```typescript
constructor(appId: number, fileId: string, quality: number, pSign?: string)
```

**Parameter Description:**
- `appId`: Tencent Cloud VOD application ID
- `fileId`: Tencent Cloud VOD video ID
- `quality`: Video quality ID
- `pSign`: Playback signature (required for encrypted videos)

### 7.3 Usage Example

```typescript
import { TXVodDownloadDataSource } from 'liteavsdk';

let dataSource = new TXVodDownloadDataSource(
  1252463788,                          // appId
  '4564972819220421305',               // fileId
  TXVodDownloadDataSource.QUALITY_720P, // quality
  'psignxxxxxxx'                        // pSign
);

manager.startDownloadDataSource(dataSource);
```

---

## 8. TXVodDownloadMediaInfo (Download Media Info)

### 8.1 State Constants

| Constant Name | Value | Description |
|--------------|-------|-------------|
| `STATE_INIT` | 0 | Download initial state |
| `STATE_START` | 1 | Download started |
| `STATE_STOP` | 2 | Download stopped |
| `STATE_ERROR` | 3 | Download error |
| `STATE_FINISH` | 4 | Download completed |

### 8.2 API Methods

| Method | Return Value | Description |
|--------|--------------|-------------|
| `getDataSource()` | TXVodDownloadDataSource | Get download source info (fileId download) |
| `getDuration()` | number (milliseconds) | Get total video duration |
| `getPlayableDuration()` | number (milliseconds) | Get downloaded playable duration |
| `getSize()` | number | Get total file size (fileId download only) |
| `getDownloadSize()` | number | Get downloaded size (fileId download only) |
| `getProgress()` | number | Get download progress |
| `getPlayPath()` | string | Get local playback path |
| `getDownloadState()` | number | Get download state |
| `isDownloadFinished()` | boolean | Whether the download is complete |
| `getSpeed()` | number | Get download speed |
| `getTaskId()` | number | Get task ID |
| `getUrl()` | string | Get download URL |

---

## 9. TXVodPreloadManager (Preload Manager)

Allows preloading partial video content without creating a player instance.

### 9.1 API List

| Method | Parameters | Return Value | Description |
|--------|-----------|--------------|-------------|
| `getInstance(context)` | Context | TXVodPreloadManager | Get the singleton instance |
| `startPreloadUrl(url, preloadSizeMB, preferredResolution, headers, callback)` | ... | number (task ID) | Start preloading |
| `stopPreload(taskId)` | number | void | Stop preloading |

### 9.2 Usage Example

```typescript
import { TXVodPreloadManager, TXVodGlobalSetting, ITXVodPreloadCallback } from 'liteavsdk';

// Cache configuration must be set first
TXVodGlobalSetting.setCacheFolderPath('/data/storage/el2/base/cache/txcache');
TXVodGlobalSetting.setMaxCacheSize(500);

let preloadManager = TXVodPreloadManager.getInstance(getContext());

let taskId = preloadManager.startPreloadUrl(
  'https://example.com/video.mp4',
  5,              // Preload 5MB
  -1,             // No preferred resolution
  new Map(),      // HTTP request headers
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

---

## 10. TXVodConstants (Constant Definitions)

### 10.1 Player Event Constants

| Constant | Value | Description |
|----------|-------|-------------|
| `VOD_PLAY_EVT_CONNECT_SUCC` | 2001 | Successfully connected to server |
| `VOD_PLAY_EVT_RTMP_STREAM_BEGIN` | 2002 | RTMP stream started |
| `VOD_PLAY_EVT_RCV_FIRST_I_FRAME` | 2003 | First video frame received |
| `VOD_PLAY_EVT_PLAY_BEGIN` | 2004 | Video playback started |
| `VOD_PLAY_EVT_PLAY_PROGRESS` | 2005 | Playback progress update |
| `VOD_PLAY_EVT_PLAY_END` | 2006 | Playback ended |
| `VOD_PLAY_EVT_PLAY_LOADING` | 2007 | Entered buffering state |
| `VOD_PLAY_EVT_START_VIDEO_DECODER` | 2008 | Decoder started |
| `VOD_PLAY_EVT_CHANGE_RESOLUTION` | 2009 | Resolution changed |
| `VOD_PLAY_EVT_GET_PLAYINFO_SUCC` | 2010 | File info retrieved successfully |
| `VOD_PLAY_EVT_CHANGE_ROTATION` | 2011 | Rotation angle changed |
| `VOD_PLAY_EVT_VOD_PLAY_PREPARED` | 2013 | Player is ready |
| `VOD_PLAY_EVT_VOD_LOADING_END` | 2014 | Buffering ended |
| `VOD_PLAY_EVT_SEEK_COMPLETE` | 2019 | Seek completed |

### 10.2 Network Status Constants

| Constant | Description |
|----------|-------------|
| `NET_STATUS_CPU_USAGE` | CPU usage |
| `NET_STATUS_VIDEO_FPS` | Video frame rate |
| `NET_STATUS_VIDEO_BITRATE` | Video bitrate |
| `NET_STATUS_AUDIO_BITRATE` | Audio bitrate |
| `NET_STATUS_NET_SPEED` | Network receive speed |
| `NET_STATUS_VIDEO_WIDTH` | Video width |
| `NET_STATUS_VIDEO_HEIGHT` | Video height |
| `NET_STATUS_VIDEO_CACHE` | Video buffer size |
| `NET_STATUS_AUDIO_CACHE` | Audio buffer size |
| `NET_STATUS_SERVER_IP` | Server IP |

### 10.3 Render Mode Constants

| Constant | Value | Description |
|----------|-------|-------------|
| `RENDER_MODE_FILL_SCREEN` | 0 | Fill the screen (may crop) |
| `RENDER_MODE_ADJUST_RESOLUTION` | 1 | Maintain aspect ratio (may have black bars) |

### 10.4 Rotation Constants

| Constant | Value | Description |
|----------|-------|-------------|
| `RENDER_ROTATION_0` | 0 | No rotation |
| `RENDER_ROTATION_90` | 90 | Rotate 90° clockwise |
| `RENDER_ROTATION_180` | 180 | Rotate 180° clockwise |
| `RENDER_ROTATION_270` | 270 | Rotate 270° clockwise |

### 10.5 Player Error Codes

| Constant | Value | Description |
|----------|-------|-------------|
| `ERR_PLAY_PLAY_FAILED` | -2301 | Playback failed |
| `ERR_PLAY_GET_PLAYINFO_FAIL` | -2302 | Failed to get playback info |
| `ERR_PLAY_PLAYURL_EMPTY` | -2303 | Playback URL is empty |
| `ERR_PLAY_NO_SUPPORT_FORMAT` | -2304 | Unsupported playback format |

---

## 11. TXVodDef (Type Definitions)

### 11.1 TXPlayInfoParams (Playback Parameters)

```typescript
interface TXPlayInfoParams {
  appId: number;           // Tencent Cloud VOD appId (required)
  fileId?: string;         // Tencent Cloud VOD fileId
  pSign?: string;          // Encryption signature (required for encrypted videos)
  url?: string;            // Playback URL
  mediaType?: number;      // Media type
}

// Static methods
TXPlayInfoParams.createWithFileId(appId, fileId, pSign);  // FileId playback
TXPlayInfoParams.createWithUrl(url);                       // URL playback
```

### 11.2 TXBitrateItem (Bitrate Info)

```typescript
interface TXBitrateItem {
  index: number;    // Bitrate index
  width: number;    // Video width
  height: number;   // Video height
  bitrate: number;  // Bitrate value
}
```

### 11.3 TXTrackInfo (Track Info)

```typescript
interface TXTrackInfo {
  trackType: number;     // Track type
  trackIndex: number;    // Track index
  name: string;          // Track name
  isSelected: boolean;   // Whether selected
  isExclusive: boolean;  // Whether exclusive
  isInternal: boolean;   // Whether internal track
}

// Track type constants
const TX_VOD_MEDIA_TRACK_TYPE_VIDEO = 0;    // Video track
const TX_VOD_MEDIA_TRACK_TYPE_AUDIO = 1;    // Audio track
const TX_VOD_MEDIA_TRACK_TYPE_SUBTITLE = 3; // Subtitle track
```

### 11.4 TXVodSubtitleData (Subtitle Data)

```typescript
interface TXVodSubtitleData {
  subtitleText: string;      // Subtitle content
  trackIndex: number;        // Subtitle track index
  startPositionMs: number;   // Start time (milliseconds)
}
```
