# Flutter Player SDK - API Documentation

## SuperPlayerPlugin Class (Global Static Interface)

The global control class for the player, responsible for License configuration, global environment settings, and system-level function calls.

### License and Configuration

| Method | Description |
|--------|-------------|
| `setGlobalLicense(String licenceUrl, String licenceKey)` | Initialize the License (**must be called**; otherwise, playback will fail) |
| `setGlobalMaxCacheSize(int size)` | Set the maximum cache size for the playback engine (MB) |
| `setGlobalCacheFolderPath(String postfixPath)` | Set the cache path (relative path) |
| `setLogLevel(int logLevel)` | Set the log output level (0-6) |
| `setConsoleEnabled(bool enabled)` | Enable or disable native log output |
| `setUserId(String userId)` | Set the user ID for data tracking |
| `setLicenseFlexibleValid(bool enabled)` | Enable License flexible validation |
| `setDrmProvisionEnv(TXDrmProvisionEnv env)` | Set the DRM certificate environment |
| `getLiteAVSDKVersion()` | Get the SDK version number |

### System Interaction

| Method | Description | Platform |
|--------|-------------|----------|
| `setBrightness(double brightness)` | Set the current app brightness (0.0~1.0) | Android/iOS |
| `getBrightness()` | Get the current app brightness | Android/iOS |
| `setSystemVolume(double volume)` | Set the system volume | Android/iOS |
| `getSystemVolume()` | Get the system volume | Android/iOS |
| `requestAudioFocus()` | Request audio focus | **Android only** |
| `abandonAudioFocus()` | Release audio focus | **Android only** |
| `isDeviceSupportPip()` | Check if the device supports Picture-in-Picture | Android/iOS |
| `startVideoOrientationService()` | Start gravity sensor monitoring (for orientation switching) | **Android only** |

---

## TXVodPlayerController Class (VOD Controller)

Used to control Video on Demand (VOD) playback, state management, and advanced features.

### Playback Control

| Method | Description |
|--------|-------------|
| `startVodPlay(String url)` | Play via URL (requires License) |
| `startVodPlayWithParams(TXPlayInfoParams params)` | Play via FileId and other parameters |
| `pause()` | Pause playback |
| `resume()` | Resume playback |
| `stop({bool isNeedClear})` | Stop playback |
| `seek(double progress)` | Seek to a playback position (seconds) |
| `seekToPdtTime(int timeMs)` | Seek to a specific HLS PDT time point |
| `setRate(double rate)` | Set playback speed (0.5~3.0) |
| `setAutoPlay({bool isAutoPlay})` | Set whether to auto-play |

### Playback State and Properties

| Method | Description |
|--------|-------------|
| `isPlaying()` | Whether playback is in progress |
| `setMute(bool mute)` | Set mute |
| `setLoop(bool loop)` | Set loop playback |
| `setAudioPlayoutVolume(int volume)` | Set volume (0~100) |
| `getDuration()` | Get total video duration |
| `getCurrentPlaybackTime()` | Get current playback time |
| `getWidth()` | Get video width |
| `getHeight()` | Get video height |
| `setPlayerView(int viewId)` | Bind the video rendering view |
| `dispose()` | Release controller resources |

### Multi-Bitrate and Resolution

| Method | Description |
|--------|-------------|
| `getSupportedBitrates()` | Get the list of supported bitrates |
| `setBitrateIndex(int index)` | Switch resolution (index=-1 for adaptive) |

### Picture-in-Picture

| Method | Description |
|--------|-------------|
| `enterPictureInPictureMode(...)` | Enter Picture-in-Picture mode |
| `exitPictureInPictureMode()` | Exit Picture-in-Picture mode |

**`enterPictureInPictureMode` Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `backIconForAndroid` | String | Back button icon path (Android only) |
| `playIconForAndroid` | String | Play button icon path (Android only) |
| `pauseIconForAndroid` | String | Pause button icon path (Android only) |
| `forwardIconForAndroid` | String | Forward button icon path (Android only) |

### Subtitles and Audio Tracks (Requires version 11.7+)

| Method | Description |
|--------|-------------|
| `addSubtitleSource(String url, String name, String mimeType)` | Add an external subtitle source |
| `getSubtitleTrackInfo()` | Get the list of subtitle track information |
| `getAudioTrackInfo()` | Get the list of audio track information |
| `selectTrack(int trackIndex)` | Select a specific track (subtitle/audio) |
| `deselectTrack(int trackIndex)` | Deselect a specific track |

### Snapshot and Image Sprite

| Method | Description |
|--------|-------------|
| `snapshot()` | Capture the current playback frame |
| `initImageSprite(String vttUrl, List<String> imageUrls)` | Initialize image sprite |
| `getImageSprite(double time)` | Get the image sprite thumbnail at a specified time |

### TRTC Integration

| Method | Description |
|--------|-------------|
| `enableTRTC(bool isEnable)` | Enable/disable TRTC connection |
| `publishVideo()` | Push the player video stream to a TRTC room |
| `publishAudio()` | Push the player audio stream to a TRTC room |

### Decoding Control

| Method | Description |
|--------|-------------|
| `enableHardwareDecode(bool enable)` | Enable/disable hardware decoding (must call stopPlay first) |

### Configuration

| Method | Description |
|--------|-------------|
| `setConfig(FTXVodPlayConfig config)` | Set the player configuration |

---

## TXVodPlayerController Event Listeners

### onPlayerEventBroadcast

A broadcast stream for playback events. Use `listen` to monitor various player events.

```dart
_controller.onPlayerEventBroadcast.listen((event) {
  int eventCode = event["event"];
  // Handle events...
});
```

**Common Event Codes:**

| Event | Description |
|-------|-------------|
| `TXVodPlayEvent.PLAY_EVT_PLAY_BEGIN` | Playback started |
| `TXVodPlayEvent.PLAY_EVT_PLAY_PROGRESS` | Playback progress update |
| `TXVodPlayEvent.PLAY_EVT_PLAY_END` | Playback ended |
| `TXVodPlayEvent.PLAY_EVT_PLAY_LOADING` | Buffering started |
| `TXVodPlayEvent.PLAY_EVT_VOD_PLAY_PREPARED` | Player is ready |
| `TXVodPlayEvent.PLAY_EVT_VOD_LOADING_END` | Buffering ended |
| `TXVodPlayEvent.PLAY_EVT_CHANGE_RESOLUTION` | Video resolution changed |
| `TXVodPlayEvent.PLAY_EVT_RCV_FIRST_I_FRAME` | First I-frame received |

**Progress Event Parameters:**

| Parameter | Description |
|-----------|-------------|
| `TXVodPlayEvent.EVT_PLAY_PROGRESS` | Current playback progress (seconds) |
| `TXVodPlayEvent.EVT_PLAY_DURATION` | Total video duration (seconds) |
| `TXVodPlayEvent.EVT_PLAYABLE_DURATION_MS` | Playable duration (milliseconds) |
| `TXVodPlayEvent.EVT_PARAM1` | Video width (on resolution change) |
| `TXVodPlayEvent.EVT_PARAM2` | Video height (on resolution change) |

### onPlayerNetStatusBroadcast

A broadcast stream for network status, used to monitor network quality metrics:

```dart
_controller.onPlayerNetStatusBroadcast.listen((event) {
  double speed = (event[TXVodNetEvent.NET_STATUS_NET_SPEED]).toDouble();
});
```

**Common Network Status Parameters:**

| Parameter | Description |
|-----------|-------------|
| `TXVodNetEvent.NET_STATUS_NET_SPEED` | Real-time network speed (KB/s) |
| `TXVodNetEvent.NET_STATUS_VIDEO_BITRATE` | Video bitrate |
| `TXVodNetEvent.NET_STATUS_AUDIO_BITRATE` | Audio bitrate |
| `TXVodNetEvent.NET_STATUS_VIDEO_FPS` | Video frame rate |
| `TXVodNetEvent.NET_STATUS_VIDEO_WIDTH` | Video width |
| `TXVodNetEvent.NET_STATUS_VIDEO_HEIGHT` | Video height |

### onPlayerState

A playback state listener stream, returning `TXPlayerState` enum values:

| State | Description |
|-------|-------------|
| `playing` | Currently playing |
| `paused` | Paused |
| `buffering` | Buffering |
| `stopped` | Stopped |
| `failed` | Playback failed |

---

## TXLivePlayerController Class (Live Player Controller)

Used to control live stream playback. The functionality is similar to VOD but optimized for live streaming scenarios.

### Playback Control

| Method | Description |
|--------|-------------|
| `startLivePlay(String url)` | Start playing a live stream |
| `pause()` | Pause playback |
| `resume()` | Resume playback |
| `stop({bool isNeedClear})` | Stop playback |
| `setLiveMode(TXPlayerLiveMode mode)` | Set live mode (Auto, Speed, Smooth) |
| `switchStream(String url)` | Switch live stream URL |
| `setMute(bool mute)` | Set mute |
| `setVolume(int volume)` | Set volume |
| `setPlayerView(int viewId)` | Bind the video rendering view |
| `dispose()` | Release controller resources |

### Live-Specific Features

| Method | Description |
|--------|-------------|
| `enableReceiveSeiMessage(bool isEnabled, int payloadType)` | Enable receiving SEI messages |
| `snapshot()` | Take a screenshot |
| `enableHardwareDecode(bool enable)` | Enable/disable hardware decoding |

### Local Recording

| Method | Description |
|--------|-------------|
| `startLocalRecording(FTXLiveLocalRecordingParams params)` | Start local recording |
| `stopLocalRecording()` | Stop local recording |

### Live Callbacks (FTXLiveListener)

Configured via the `liveListener` property:

| Callback | Description |
|----------|-------------|
| `recordBeginCallback` | Recording started |
| `recordingCallback` | Recording in progress |
| `recordCompleteCallback` | Recording completed |
| `snapshotCompleteCallback` | Screenshot completed |

### TXPlayerLiveMode Enum

| Value | Description |
|-------|-------------|
| `Automatic` | Auto mode |
| `Speed` | Speed mode (low latency) |
| `Smooth` | Smooth mode |

---

## TXVodDownloadController Class (Download Controller)

Manages video preloading and download tasks.

### Preloading

| Method | Description |
|--------|-------------|
| `startPreLoad(String playUrl, double preloadSizeMB, int preferredResolution, {FTXVodPreLoadListener? listener})` | Start URL preloading |
| `startPreLoadByParams(TXPlayInfoParams params, double preloadSizeMB, int preferredResolution, {FTXVodPreLoadListener? listener})` | Start FileId preloading |
| `stopPreLoad(int taskId)` | Stop a preload task |

### Download

| Method | Description |
|--------|-------------|
| `startDownload(TXVodDownloadMedialnfo mediaInfo)` | Start downloading a video |
| `stopDownload(TXVodDownloadMedialnfo mediaInfo)` | Stop download |
| `getDownloadList()` | Get the list of all download tasks |
| `deleteDownloadMediaInfo(TXVodDownloadMedialnfo mediaInfo)` | Delete a downloaded video |
| `setDownloadObserver(FTXVodDownloadListener listener)` | Set download status and error listener |

### FTXVodDownloadListener Callbacks

```dart
FTXVodDownloadListener(
  onStateChange: (int taskId, int state, TXVodDownloadMedialnfo info) {
    // state: 0-Initial, 1-Downloading, 2-Paused, 3-Completed, 4-Failed
  },
  onDownloadProgress: (int taskId, TXVodDownloadMedialnfo info) {
    // Download progress callback
  },
  onDownloadError: (int taskId, int errorCode, String errorMsg) {
    // Download error callback
  },
)
```

### FTXVodPreLoadListener Callbacks

```dart
FTXVodPreLoadListener(
  onComplete: (int taskId, String url) {
    // Preload completed
  },
  onError: (int taskId, String url, int errorCode, String errorMsg) {
    // Preload error
  },
)
```

---

## TXVodDownloadMedialnfo Class

Download media information class.

| Property | Type | Description |
|----------|------|-------------|
| `dataSource` | `TXVodDownloadDataSource?` | Download data source |
| `playPath` | `String?` | Playback URL |
| `downloadState` | `int?` | Download state (0-Initial, 1-Downloading, 2-Paused, 3-Completed, 4-Failed) |
| `userName` | `String?` | Download account name |
| `duration` | `int?` | Video duration (seconds) |
| `size` | `int?` | Total file size (bytes) |
| `downloadSize` | `int?` | Downloaded size (bytes) |
| `progress` | `double?` | Download progress (0.0~1.0) |
| `speed` | `int?` | Download speed (bytes/second) |

---

## TXVodDownloadDataSource Class

Download data source configuration.

| Property | Type | Description |
|----------|------|-------------|
| `appId` | `int?` | Application AppId |
| `fileId` | `String?` | File FileId |
| `pSign` | `String?` | Playback signature |
| `quality` | `int?` | Download quality (see DownloadQuality constants) |
| `userName` | `String?` | Download account name |
| `token` | `String?` | Download Token |

### DownloadQuality Constants

| Constant | Value | Description |
|----------|-------|-------------|
| `DownloadQuality.QUALITY_OD` | 0 | Original quality |
| `DownloadQuality.QUALITY_FLU` | 1 | Smooth |
| `DownloadQuality.QUALITY_SD` | 2 | Standard definition |
| `DownloadQuality.QUALITY_HD` | 3 | High definition |
| `DownloadQuality.QUALITY_FHD` | 4 | Full HD |
| `DownloadQuality.QUALITY_2K` | 5 | 2K |
| `DownloadQuality.QUALITY_4K` | 6 | 4K |
| `DownloadQuality.QUALITY_720P` | 7 | 720p |
| `DownloadQuality.QUALITY_1080P` | 8 | 1080p |

---

## TXPlayInfoParams Class

FileId playback parameters.

| Property | Type | Description |
|----------|------|-------------|
| `appId` | `int` | Application AppId |
| `fileId` | `String` | File FileId |
| `psign` | `String?` | Playback signature (hotlink protection) |

---

## FTXVodPlayConfig Class (VOD Configuration)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `connectRetryCount` | `int` | 3 | Connection retry count |
| `connectRetryInterval` | `int` | 3 | Connection retry interval (seconds) |
| `timeout` | `int` | 30 | Connection timeout (seconds) |
| `playerType` | `int` | 0 | Player type |
| `headers` | `Map<String, String>?` | null | Custom HTTP request headers |
| `enableAccurateSeek` | `bool` | true | Accurate seek |
| `autoRotate` | `bool` | true | MP4 auto rotation |
| `smoothSwitchBitrate` | `bool` | false | Smooth bitrate switching (reduces stuttering) |
| `cacheMp4ExtName` | `String` | ".mp4" | Cache file extension |
| `progressInterval` | `int` | 500 | Progress callback interval (milliseconds) |
| `maxBufferSize` | `int` | 10 | Maximum buffer size (MB) |
| `maxPreloadSize` | `int` | 1 | Maximum preload size (MB) |
| `firstStartPlayBufferTime` | `int` | 100 | Initial buffer time (milliseconds) |
| `nextStartPlayBufferTime` | `int` | 250 | Subsequent buffer time (milliseconds) |
| `overlayKey` | `String?` | null | HLS security hardening encryption/decryption key |
| `overlayIv` | `String?` | null | HLS security hardening encryption/decryption IV |
| `extInfoMap` | `Map<String, Object>?` | null | Extended information |
| `preferredResolution` | `int` | 0 | Preferred playback resolution (width × height, e.g., `720*1280`) |
| `enableRenderProcess` | `bool` | true | Whether to enable post-processing |
| `mediaType` | `int?` | null | Media type |

---

## FTXLivePlayConfig Class (Live Configuration)

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `minAutoAdjustCacheTime` | `double` | 1.0 | Minimum auto-adjust cache time (seconds) |
| `maxAutoAdjustCacheTime` | `double` | 5.0 | Maximum auto-adjust cache time (seconds) |
| `connectRetryCount` | `int` | 3 | Connection retry count |
| `connectRetryInterval` | `int` | 3 | Connection retry interval (seconds) |
| `headers` | `Map<String, String>?` | null | Custom HTTP request headers |

---

## FTXLiveLocalRecordingParams Class

Live local recording parameters.

| Property | Type | Description |
|----------|------|-------------|
| `filePath` | `String` | Recording file path |
| `maxDurationMs` | `int` | Maximum recording duration (milliseconds); -1 for unlimited |

---

## SuperPlayerController Class (Player Component Controller)

An advanced player component controller that encapsulates UI interaction logic.

### Common Methods

| Method | Description |
|--------|-------------|
| `playWithModelNeedLicence(SuperPlayerModel model)` | Play video using a model |
| `pause()` | Pause |
| `resume()` | Resume |
| `reStart()` | Restart |
| `seek(double progress)` | Seek to position (seconds) |
| `switchStream(SuperPlayerUrl videoQuality)` | Switch resolution |
| `releasePlayer()` | Release player resources (**must be called when leaving the page**) |
| `onBackPress()` | Handle back press event (returns true to exit fullscreen when in fullscreen mode) |
| `setPlayConfig(FTXVodPlayConfig config)` | Set playback configuration |
| `startDownload()` | Download the currently playing video |
| `enterPictureInPictureMode(...)` | Enter Picture-in-Picture mode |

### Event Listeners

Listen to component events via `onSimplePlayerEventBroadcast`:

| Event Name | Description |
|------------|-------------|
| `SuperPlayerViewEvent.onStartFullScreenPlay` | Entered fullscreen |
| `SuperPlayerViewEvent.onStopFullScreenPlay` | Exited fullscreen |
| `SuperPlayerViewEvent.onSuperPlayerDidStart` | Playback started |
| `SuperPlayerViewEvent.onSuperPlayerDidEnd` | Playback ended |
| `SuperPlayerViewEvent.onSuperPlayerError` | Playback error |
| `SuperPlayerViewEvent.onSuperPlayerBackAction` | Back action |

---

## SuperPlayerModel Class

Player component playback model.

| Property | Type | Description |
|----------|------|-------------|
| `appId` | `int` | Application AppId |
| `videoURL` | `String?` | Video URL |
| `videoId` | `SuperPlayerVideoId?` | FileId playback configuration |
| `title` | `String?` | Video title |
| `coverUrl` | `String?` | Cover image URL |
| `isEnableDownload` | `bool` | Whether to enable download capability |
| `multiVideoURLs` | `List<SuperPlayerUrl>?` | Multi-resolution URL list |
| `defaultPlayIndex` | `int` | Default playback resolution index |

## SuperPlayerVideoId Class

| Property | Type | Description |
|----------|------|-------------|
| `fileId` | `String` | File FileId |
| `pSign` | `String?` | Playback signature |

## SuperPlayerUrl Class

| Property | Type | Description |
|----------|------|-------------|
| `title` | `String` | Resolution title (e.g., "Ultra HD") |
| `url` | `String` | Video URL |

---

## TXLogLevel Log Level Constants

| Constant | Description |
|----------|-------------|
| `TXLogLevel.LOG_LEVEL_VERBOSE` | Verbose log |
| `TXLogLevel.LOG_LEVEL_DEBUG` | Debug log |
| `TXLogLevel.LOG_LEVEL_INFO` | Info log |
| `TXLogLevel.LOG_LEVEL_WARN` | Warning log |
| `TXLogLevel.LOG_LEVEL_ERROR` | Error log |
| `TXLogLevel.LOG_LEVEL_FATAL` | Fatal error log |
| `TXLogLevel.LOG_LEVEL_NULL` | Disable all logs |

---

## TXDrmProvisionEnv DRM Environment Enum

| Value | Description |
|-------|-------------|
| `TXDrmProvisionEnv.production` | Production environment |
| `TXDrmProvisionEnv.staging` | Staging environment |

---

## Important Notes

1. **License Must Be Set**: Starting from version 10.7, the License must be set by calling `SuperPlayerPlugin.setGlobalLicense` for both VOD and live playback; otherwise, playback will fail (black screen).
2. **Version Differences**: Some features (such as track selection via `selectTrack` and `addSubtitleSource`) require Premium Edition 11.7 or above.
3. **Platform Limitations**: Some APIs (such as audio focus, PiP icon configuration, and brightness control) may have different behaviors or support levels on Android and iOS.
4. **Lifecycle**: After use, you must call `dispose()` (TXVodPlayerController) or `releasePlayer()` (SuperPlayerController) to release resources and prevent memory leaks.
5. **Initialization Change**: Starting from SDK 12.3.1, calling `initialize()` is no longer required.
6. **Hardware Decoding Switch**: You must call `stopPlay` before switching between software and hardware decoding (`enableHardwareDecode`); otherwise, screen artifacts may occur.
