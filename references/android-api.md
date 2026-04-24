# Android - API Documentation

## 1. TXVodPlayer (Core Player Class)

The core playback class for VOD scenarios, responsible for video playback control, configuration management, and event listening.

> **Important**: Since version 10.7, you must set the Licence via `TXLiveBase#setLicence` before playback can succeed.

### 1.1 Basic Playback APIs

| Method | Parameters | Return Value | Description |
|--------|-----------|--------------|-------------|
| `startVodPlay(String url)` | playUrl: HTTP URL | int (0=success) | Start playback via URL |
| `startVodPlay(TXPlayInfoParams params)` | params: FileId/URL parameters | int | Start playback via Tencent Cloud fileId or URL |
| `startPlayDrm(TXPlayerDrmBuilder builder)` | builder: DRM builder | void | Play DRM-encrypted video (Premium Edition only) |
| `stopPlay(boolean clearLastImg)` | true=clear last frame | void | Stop playback |
| `isPlaying()` | None | boolean | Returns whether playback is in progress |
| `pause()` | None | void | Pause playback |
| `resume()` | None | void | Resume playback |
| `seek(float time)` | time: seconds | void | Seek to specified time position |
| `seek(float time, boolean accurate)` | accurate=true for precise seeking | void | Precise/imprecise seek |
| `seekToPdtTime(long pdtTimeMs)` | pdtTimeMs: milliseconds | void | Seek to PDT time position (HLS only, Premium Edition 11.6+) |
| `setAutoPlay(boolean autoPlay)` | Default: auto-play | void | Set whether to auto-play |
| `setStartTime(float pos)` | pos: seconds | void | Set start time position (call before playback) |
| `setLoop(boolean loop)` | None | void | Set loop playback |
| `isLoop()` | None | boolean | Get loop playback status |

### 1.2 Information Retrieval APIs

| Method | Return Value | Description |
|--------|--------------|-------------|
| `getCurrentPlaybackTime()` | float (seconds) | Current playback progress |
| `getDuration()` | float (seconds) | Total video duration |
| `getBufferDuration()` | float (seconds) | Current buffer duration |
| `getPlayableDuration()` | float (seconds) | Playable duration |
| `getWidth()` | int | Video resolution width |
| `getHeight()` | int | Video resolution height |
| `getSupportedBitrates()` | ArrayList\<TXBitrateItem\> | List of supported bitrates for HLS |
| `getBitrateIndex()` | int | Current bitrate index |

### 1.3 View & Configuration APIs

| Method | Parameters | Description |
|--------|-----------|-------------|
| `setConfig(TXVodPlayConfig config)` | Configuration object | Set player configuration |
| `setPlayerView(TXCloudVideoView view)` | Render View | Set TXCloudVideoView for rendering |
| `setPlayerView(TextureRenderView view)` | TextureView | Set TextureView for rendering |
| `setSurface(Surface surface)` | Surface | Set rendering Surface |
| `setRenderMode(int mode)` | 0=fill screen, 1=fit | Set video fill mode |
| `setRenderRotation(int rotation)` | 0=portrait, 270=landscape | Set video rotation angle |
| `setBitrateIndex(int index)` | -1=adaptive | Switch resolution |
| `setToken(String token)` | token string | Set HLS encryption Token |

### 1.4 Advanced Feature APIs

| Method | Parameters | Description |
|--------|-----------|-------------|
| `enableHardwareDecode(boolean enable)` | Default: enabled | Toggle hardware decoding (requires stop and restart) |
| `snapshot(ITXSnapshotListener listener)` | Callback listener | Capture current frame (async) |
| `setMirror(boolean mirror)` | None | Set mirror mode |
| `setRate(float rate)` | 0.5~3.0, 1.0=normal speed | Set playback speed |
| `setAutoMaxBitrate(int max)` | Maximum bitrate | Set adaptive bitrate upper limit |

### 1.5 Audio & Subtitle APIs

| Method | Parameters | Description |
|--------|-----------|-------------|
| `addSubtitleSource(String url, String name, String mimeType)` | VTT/SRT URL | Add external subtitle |
| `getSubtitleTrackInfo()` | None | Get subtitle track list, returns List\<TXTrackInfo\> |
| `getAudioTrackInfo()` | None | Get audio track list, returns List\<TXTrackInfo\> |
| `selectTrack(int index)` | Track index | Select track |
| `deselectTrack(int index)` | Track index | Deselect track |
| `setSubtitleStyle(TXSubtitleRenderModel model)` | Style model | Set subtitle style |
| `setMute(boolean mute)` | None | Set mute |
| `setAudioPlayoutVolume(int volume)` | 0-100 | Set volume |
| `setAudioNormalization(float value)` | LUFS value | Audio normalization |

### 1.6 Event Callback APIs

| Method | Parameters | Description |
|--------|-----------|-------------|
| `setVodListener(ITXVodPlayListener listener)` | Callback listener | Core event callback |
| `setVodSubtitleDataListener(ITXVodSubtitleDataListener listener)` | Callback listener | Subtitle data callback |

### 1.7 TRTC Co-streaming APIs

| Method | Description |
|--------|-------------|
| `attachTRTC(Object trtcCloud)` | Attach TRTC service |
| `detachTRTC()` | Detach TRTC |
| `publishVideo()` / `unpublishVideo()` | Push/stop video stream |
| `publishAudio()` / `unpublishAudio()` | Push/stop audio stream |

---

## 2. ITXVodPlayListener (Playback Event Callback)

### 2.1 onPlayEvent

Playback event notification (start, first frame, loading, progress, end, etc.).

```java
public void onPlayEvent(final TXVodPlayer player, final int event, final Bundle param)
```

| Parameter | Type | Description |
|-----------|------|-------------|
| player | TXVodPlayer | Current player object |
| event | int | Player event ID (refer to TXVodConstants) |
| param | Bundle | Event parameters (Key-Value) |

### 2.2 onNetStatus

Network status callback, triggered once per second.

```java
public void onNetStatus(final TXVodPlayer player, final Bundle status)
```

| Parameter | Type | Description |
|-----------|------|-------------|
| player | TXVodPlayer | Current player object |
| status | Bundle | Network status parameters (speed, resolution, frame rate, etc.) |

---

## 3. ITXVodSubtitleDataListener (Subtitle Data Callback)

### 3.1 onSubtitleData

Subtitle text data callback.

```java
public void onSubtitleData(TXVodDef.TXVodSubtitleData subtitleData)
```

| Parameter | Type | Description |
|-----------|------|-------------|
| subtitleData | TXVodDef.TXVodSubtitleData | Subtitle text data object |

---

## 4. TXVodPlayConfig (Player Configuration)

Set various player parameters before playback starts.

### Core Configuration Items

| Method | Default | Description |
|--------|---------|-------------|
| `setConnectRetryCount(int count)` | 3 | Reconnection retry count |
| `setConnectRetryInterval(int seconds)` | 3 (range 3-30) | Reconnection interval (seconds) |
| `setTimeout(int seconds)` | 10 | Connection timeout (seconds) |
| `setEnableAccurateSeek(boolean)` | true | Accurate seek |
| `setAutoRotate(boolean)` | true | MP4 auto-rotation |
| `setSmoothSwitchBitrate(boolean)` | false | Smooth bitrate switching |
| `setProgressInterval(int ms)` | 500 | Progress callback interval (milliseconds) |
| `setMaxBufferSize(float mb)` | - | Maximum playback buffer (MB) |
| `setMaxPreloadSize(float mb)` | - | Preload buffer (MB) |
| `setPreferredResolution(long)` | - | Preferred HLS startup resolution (width×height) |
| `setHeaders(Map headers)` | - | Custom HTTP headers |
| `setPlayerType(int type)` | 1 (proprietary player) | Player type (0=system, 1=proprietary) |
| `setMediaType(int type)` | AUTO | Media type |
| `setEncryptedMp4Level(int level)` | 0 (no encryption) | MP4 encryption level (Premium Edition 12.2+) |
| `setPreferredAudioTrack(String name)` | - | Preferred audio track on startup (Premium Edition 12.3+) |
| `setEnableRenderProcess(boolean)` | false | Render post-processing service (TSR/VR) |

### Deprecated APIs
- `setCacheFolderPath` → Use `TXPlayerGlobalSetting.setCacheFolderPath`
- `setMaxCacheItems` → Use `TXPlayerGlobalSetting.setMaxCacheSize`
- `setFirstStartPlayBufferTime` → Use `setMaxBufferSize` or `setMaxPreloadSize`

---

## 5. TXPlayerGlobalSetting (Global Settings)

Global configuration affecting all player instances.

| Method | Parameters | Description |
|--------|-----------|-------------|
| `setCacheFolderPath(String path)` | SD card absolute path, null=disabled | Set cache directory |
| `setMaxCacheSize(int sizeMB)` | MB | Maximum cache size |
| `setDrmProvisionEnv(DrmProvisionEnv env)` | COM/CN | DRM certificate environment (11.2+) |
| `setPlayCGIHosts(List<String> hosts)` | Domain list | PlayCGI fallback domains |

---

## 6. TXVodDownloadManager (Offline Download)

Singleton pattern for managing video offline downloads.

### 6.1 Download Status Codes

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | DOWNLOAD_SUCCESS | Download successful |
| -5001 | DOWNLOAD_AUTH_FAILED | fileId authentication failed |
| -5003 | DOWNLOAD_NO_FILE | File does not exist |
| -5004 | DOWNLOAD_FORMAT_ERROR | Format not supported |
| -5005 | DOWNLOAD_DISCONNECT | Network error |
| -5006 | DOWNLOAD_HLS_KEY_ERROR | HLS decryption key failed |
| -5007 | DOWNLOAD_PATH_ERROR | Download directory access failed |
| -5008 | DOWNLOAD_403FORBIDDEN | Signature expired or invalid request |

### 6.2 Core APIs

```java
// Get singleton
TXVodDownloadManager manager = TXVodDownloadManager.getInstance();

// Set listener (must be set before downloading)
manager.setListener(listener);

// Download by URL
TXVodDownloadMediaInfo info = manager.startDownloadUrl(url, preferredResolution, userName);
// preferredResolution: width*height (e.g. 921600=1280*720), pass -1 for single resolution
// userName: user identifier, default "default"

// Download by FileId
manager.startDownload(dataSource); // TXVodDownloadDataSource

// DRM download
manager.startDownloadDrm(drmBuilder, preferredResolution, userName);

// Stop download
manager.stopDownload(downloadMediaInfo);

// Delete download (recommended)
manager.deleteDownloadMediaInfo(downloadMediaInfo);

// Get download list (time-consuming operation, do NOT call on main thread!)
List<TXVodDownloadMediaInfo> list = manager.getDownloadMediaInfoList();
```

---

## 7. ITXVodDownloadListener (Download Callback Interface)

### 7.1 Callback Method List

| Method | Description |
|--------|-------------|
| `onDownloadStart(TXVodDownloadMediaInfo mediaInfo)` | Download started |
| `onDownloadProgress(TXVodDownloadMediaInfo mediaInfo)` | Download progress update |
| `onDownloadStop(TXVodDownloadMediaInfo mediaInfo)` | Download stopped (triggered when stopDownload is called) |
| `onDownloadFinish(TXVodDownloadMediaInfo mediaInfo)` | Download completed |
| `onDownloadError(TXVodDownloadMediaInfo mediaInfo, int error, String reason)` | Download error |
| `hlsKeyVerify(TXVodDownloadMediaInfo mediaInfo, String url, byte[] receive)` | HLS decryption key verification (**deprecated**, empty implementation is fine) |

### 7.2 onDownloadError Parameters

```java
void onDownloadError(TXVodDownloadMediaInfo mediaInfo, int error, String reason)
```

| Parameter | Type | Description |
|-----------|------|-------------|
| mediaInfo | TXVodDownloadMediaInfo | Video download info object |
| error | int | Download error code (refer to 6.1 Download Status Codes) |
| reason | String | Error description |

### 7.3 hlsKeyVerify Return Values

| Return Value | Description |
|--------------|-------------|
| 0 | Verification passed, continue download |
| Other values | Verification failed, throw download error |

---

## 8. TXVodDownloadDataSource (Download Data Source)

Used to configure FileId download parameters.

### 8.1 Quality Constants

| Constant Name | Value | Description |
|---------------|-------|-------------|
| `QUALITY_OD` | 0 | Original quality |
| `QUALITY_240P` | 240 | Smooth 240P |
| `QUALITY_360P` | 360 | Smooth 360P |
| `QUALITY_480P` | 480 | Standard 480P |
| `QUALITY_540P` | 540 | Standard 540P |
| `QUALITY_720P` | 720 | HD 720P |
| `QUALITY_1080P` | 1080 | Full HD 1080P |

### 8.2 Constructor (Recommended)

```java
public TXVodDownloadDataSource(int appId, String fileId, int quality, String pSign, String userName)
```

| Parameter | Type | Description |
|-----------|------|-------------|
| appId | int | Tencent Cloud VOD application appId |
| fileId | String | Tencent Cloud VOD video fileId |
| qualityId | int | Video quality ID (e.g. QUALITY_720P) |
| pSign | String | Video playback signature |
| userName | String | Account name, default "" |

### 8.3 API Methods

| Method | Description |
|--------|-------------|
| `setToken(String token)` | Set HLS encryption Token |
| `setQuality(int quality)` | Set quality ID |
| `getAppId()` | Get appId |
| `getFileId()` | Get fileId |
| `getPSign()` | Get signature |
| `getQuality()` | Get quality |
| `getUserName()` | Get username (default "default") |
| `getToken()` | Get Token |

### 8.4 Deprecated Constructors

```java
// Deprecated - use the new constructor above instead
public TXVodDownloadDataSource(TXPlayerAuthBuilder authBuilder, int quality)
public TXVodDownloadDataSource(TXPlayerAuthBuilder authBuilder, String templateName)
```

---

## 9. TXVodDownloadMediaInfo (Download Media Info)

### 9.1 Download State Constants

| Constant Name | Value | Description |
|---------------|-------|-------------|
| `STATE_INIT` | 0 | Download initial state |
| `STATE_START` | 1 | Download started |
| `STATE_STOP` | 2 | Download stopped |
| `STATE_ERROR` | 3 | Download error |
| `STATE_FINISH` | 4 | Download completed |

### 9.2 API Methods

| Method | Return Value | Description |
|--------|--------------|-------------|
| `getDataSource()` | TXVodDownloadDataSource | Download source info (for fileId downloads) |
| `getDuration()` | int (milliseconds) | Total video duration |
| `getPlayableDuration()` | int (milliseconds) | Downloaded playable duration |
| `getSize()` | long (Bytes) | Total file size (fileId only, original file size) |
| `getDownloadSize()` | long (Bytes) | Downloaded size (fileId only) |
| `getProgress()` | float | Download progress |
| `getDownloadState()` | int | Download state (refer to constants above) |
| `isDownloadFinished()` | boolean | Whether download is completed |
| `getPlayPath()` | String | Local playback path (can be passed to TXVodPlayer) |
| `getUrl()` | String | Actual download URL |
| `getSpeed()` | int (KBytes/sec) | Download speed (10.9+) |
| `getTaskId()` | int | Task ID |
| `isResourceBroken()` | boolean | Whether resource is corrupted (11.0+) |
| `getUserName()` | String | Download account name |
| `getPreferredResolution()` | long | Preferred download resolution |

---

## 10. TXVodPreloadManager (Video Preloading)

Preload video without creating a player instance to speed up playback startup.

> **Prerequisite**: Must first set `TXPlayerGlobalSetting.setCacheFolderPath` and `setMaxCacheSize`

### 10.1 Core APIs

```java
// Get singleton
TXVodPreloadManager preloadManager = TXVodPreloadManager.getInstance(context);

// Preload by URL
int taskId = preloadManager.startPreload(url, preloadSizeMB, preferredResolution, listener);
// preloadSizeMB: preload size (MB)
// preferredResolution: width*height, pass -1 if not specified
// Returns taskId, -1 means invalid

// Preload by FileId (recommended, do NOT call on main thread!)
int taskId = preloadManager.startPreload(playInfoParams, preloadSizeMB, preferredResolution, listener);

// Stop preloading
preloadManager.stopPreload(taskId);
```

---

## 11. ITXVodPreloadListener (URL Preload Callback)

| Method | Description |
|--------|-------------|
| `onComplete(int taskID, String url)` | Preload completed |
| `onError(int taskID, String url, int code, String msg)` | Preload failed |

### onComplete

```java
void onComplete(int taskID, String url);
```

| Parameter | Type | Description |
|-----------|------|-------------|
| taskID | int | Preload task ID |
| url | String | Preload task URL |

### onError

```java
void onError(int taskID, String url, int code, String msg);
```

| Parameter | Type | Description |
|-----------|------|-------------|
| taskID | int | Preload task ID |
| url | String | Preload task URL |
| code | int | Error code |
| msg | String | Error description |

---

## 12. ITXVodFilePreloadListener (FileId Preload Callback)

| Method | Description |
|--------|-------------|
| `onStart(int taskID, String fileId, String url, Bundle bundle)` | Preload started (after URL resolution, before preload begins) |
| `onComplete(int taskID, String url)` | Preload completed |
| `onError(int taskID, String url, int code, String msg)` | Preload failed |

### onStart

```java
public void onStart(int taskID, String fileId, String url, Bundle bundle)
```

| Parameter | Type | Description |
|-----------|------|-------------|
| taskID | int | Preload task ID |
| fileId | String | Video fileId for preloading |
| url | String | Resolved video URL (can be used for subsequent playback) |
| bundle | Bundle | Additional preload information |

---

## 13. TXPlayInfoParams (FileId/URL Playback Parameters)

Used to configure playback media parameters, supporting both FileId and URL methods.

### 13.1 Constructors

```java
// Create with FileId (recommended)
public TXPlayInfoParams(int appId, String fileId, String pSign)

// Create with URL
public TXPlayInfoParams(String url)
```

### 13.2 FileId Constructor Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| appId | int | Tencent Cloud VOD application appId |
| fileId | String | Tencent Cloud VOD resource fileId |
| pSign | String | Playback signature |

### 13.3 Configuration Methods

| Method | Description |
|--------|-------------|
| `setMediaType(int mediaType)` | Set media type (default AUTO) |
| `setHeaders(Map<String, String> headers)` | Custom HTTP headers |
| `setEncryptedMp4Level(int level)` | MP4 encryption level (Premium Edition 12.2+) |
| `setPreferredAudioTrack(String audioTrackName)` | Preferred audio track on startup (Premium Edition 12.3+) |

---

## 14. TXVodDef (Type Definitions)

### 14.1 TXVodSubtitleData (Subtitle Text Data)

| Parameter | Type | Description |
|-----------|------|-------------|
| subtitleData | String | Subtitle content (empty=no subtitle) |
| trackIndex | long | Current subtitle track's trackIndex |
| durationMs | long | Subtitle duration (milliseconds, **temporarily empty, do not use**) |
| startPositionMs | long | Subtitle start time (milliseconds, **temporarily empty, do not use**) |

---

## 15. TXTrackInfo (Track Information)

### 15.1 Track Type Constants

| Constant | Description |
|----------|-------------|
| `TX_VOD_MEDIA_TRACK_TYPE_VIDEO` | Video track |
| `TX_VOD_MEDIA_TRACK_TYPE_AUDIO` | Audio track |
| `TX_VOD_MEDIA_TRACK_TYPE_SUBTITLE` | Subtitle track |

### 15.2 Fields

| Parameter | Type | Description |
|-----------|------|-------------|
| trackIndex | int | Track index |
| trackType | int | Track type (see constants above) |
| name | String | Track name |
| isSelected | boolean | Whether selected |
| isExclusive | boolean | Whether exclusive (true=only one track of the same type can be selected at a time) |
| isInternal | boolean | Whether it is an internal original track |

### 15.3 Methods

| Method | Return Value | Description |
|--------|--------------|-------------|
| `getTrackIndex()` | int | Get track index |
| `getTrackType()` | int | Get track type |
| `getName()` | String | Get track name |

---

## 16. TXSubtitleRenderModel (Subtitle Render Model)

Used to customize subtitle display styles.

### 16.1 Fields

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| fontColor | int | 0xFFFFFFFF (white) | Font color (ARGB format) |
| fontSize | float | - | Font size (requires canvasWidth/Height to be set) |
| fontScale | float | 1.0 | Font scale ratio (for VTT subtitles) |
| familyName | String | "Roboto" | Font family name |
| canvasWidth | int | Video width | Render canvas width (must match video aspect ratio) |
| canvasHeight | int | Video height | Render canvas height |
| isBondFontStyle | boolean | false | Bold font style |
| outlineWidth | float | Default | Outline width |
| outlineColor | int | 0xFF000000 (black) | Outline color (ARGB) |
| lineSpace | float | Default | Line spacing (requires canvasWidth/Height to be set) |
| startMargin | float | - | Start margin (ratio 0~1, overrides subtitle file parameters) |
| endMargin | float | - | End margin (ratio 0~1) |
| verticalMargin | float | - | Vertical margin (ratio 0~1) |

---

## 17. TXBitrateItem (Bitrate Information)

Describes HLS multi-bitrate video stream parameters.

| Parameter | Type | Description |
|-----------|------|-------------|
| index | int | Bitrate index (corresponds to sequence number in m3u8 file) |
| width | int | Video stream width (resolution) |
| height | int | Video stream height |
| bitrate | int | Video stream bitrate |

---

## 18. TXPlayerDrmBuilder (DRM Builder)

Used to build DRM playback information, used with `TXVodPlayer#startPlayDrm`.

### 18.1 Constructor

```java
public TXPlayerDrmBuilder(String licenseUrl, String playUrl)
```

### 18.2 Methods

| Method | Parameters | Description |
|--------|-----------|-------------|
| `setDeviceCertificateUrl(String provisionUrl)` | Certificate URL | Set certificate provider URL (optional for Widevine) |
| `setKeyLicenseUrl(String keyLicenseUrl)` | Key URL | Set decryption Key URL |
| `setPlayUrl(String playUrl)` | Playback URL | Set playback media URL |

---

## 19. TXImageSprite (Image Sprite)

VOD image sprite parsing utility class for displaying video thumbnails.

### 19.1 APIs

| Method | Description |
|--------|-------------|
| `TXImageSprite(Context context)` | Constructor |
| `setVTTUrlAndImageUrls(String vttUrl, List<String> imagesUrl)` | Set image sprite URLs (starts a background thread for download and parsing) |
| `getThumbnail(float time)` | Get thumbnail at specified time point (seconds), returns Bitmap, returns null on failure |
| `release()` | Release resources (**must be called to prevent memory leaks!**) |

---

## 20. TXVodConstants (Constants)

### 20.1 Render Mode

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | RENDER_MODE_FULL_FILL_SCREEN | Fill screen (may crop) |
| 1 | RENDER_MODE_ADJUST_RESOLUTION | Fit (may have black bars) |

### 20.2 Render Rotation

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | RENDER_ROTATION_PORTRAIT | Portrait |
| 270 | RENDER_ROTATION_LANDSCAPE | Landscape (rotated 90° clockwise) |

### 20.3 Playback Events

| Event Code | Constant | Description |
|------------|----------|-------------|
| 2002 | VOD_PLAY_EVT_HIT_CACHE | Cache hit on playback start |
| 2003 | VOD_PLAY_EVT_RCV_FIRST_I_FRAME | First video frame rendered |
| 2004 | VOD_PLAY_EVT_PLAY_BEGIN | Playback started |
| 2005 | VOD_PLAY_EVT_PLAY_PROGRESS | Playback progress |
| 2006 | VOD_PLAY_EVT_PLAY_END | Playback ended |
| 2007 | VOD_PLAY_EVT_PLAY_LOADING | Loading (buffering) |
| 2008 | VOD_PLAY_EVT_START_VIDEO_DECODER | Decoder started |
| 2009 | VOD_PLAY_EVT_CHANGE_RESOLUTION | Resolution changed |
| 2010 | VOD_PLAY_EVT_GET_PLAYINFO_SUCC | VOD file info retrieved successfully |
| 2011 | VOD_PLAY_EVT_CHANGE_ROTATION | Video rotation info changed |
| 2013 | VOD_PLAY_EVT_VOD_PLAY_PREPARED | Player ready |
| 2014 | VOD_PLAY_EVT_VOD_LOADING_END | Buffering ended |
| 2017 | VOD_PLAY_EVT_VOD_PLAY_FIRST_VIDEO_PACKET | First video packet received (12.0+) |
| 2019 | VOD_PLAY_EVT_SEEK_COMPLETE | Seek completed (10.3+) |
| 2020 | VOD_PLAY_EVT_SELECT_TRACK_COMPLETE | Track switch completed |
| 2026 | VOD_PLAY_EVT_RCV_FIRST_AUDIO_FRAME | First audio frame played |
| 2030 | VOD_PLAY_EVT_VIDEO_SEI | Video SEI information |
| 2031 | VOD_PLAY_EVT_HEVC_DOWNGRADE_PLAYBACK | HEVC downgrade playback |
| 6001 | VOD_PLAY_EVT_LOOP_ONCE_COMPLETE | One loop cycle completed |

### 20.4 Warning Events

| Event Code | Constant | Description |
|------------|----------|-------------|
| 2103 | PLAY_WARNING_RECONNECT | Network disconnected, auto-reconnection started |
| 2106 | PLAY_WARNING_HW_ACCELERATION_FAIL | Hardware decoding failed, automatically switched to software decoding |

### 20.5 Error Events

| Error Code | Constant | Description |
|------------|----------|-------------|
| -5 | VOD_PLAY_ERR_INVALID_LICENCE | Invalid License |
| -2301 | VOD_PLAY_ERR_NET_DISCONNECT | Network disconnected, multiple reconnection attempts failed |
| -2303 | VOD_PLAY_ERR_FILE_NOT_FOUND | File not found |
| -2304 | VOD_PLAY_ERR_HEVC_DECODE_FAIL | HEVC decoding failed |
| -2305 | VOD_PLAY_ERR_HLS_KEY | HLS decryption key retrieval failed |
| -2306 | VOD_PLAY_ERR_GET_PLAYINFO_FAIL | Failed to get VOD file info |
| -6004 | VOD_PLAY_ERR_SYSTEM_PLAY_FAIL | System player playback error |
| -6005 | VOD_PLAY_ERR_DEMUXER_TIMEOUT | Demuxer timeout |
| -6006 | VOD_PLAY_ERR_DECODE_VIDEO_FAIL | Video decoding error |
| -6007 | VOD_PLAY_ERR_DECODE_AUDIO_FAIL | Audio decoding error |
| -6008 | VOD_PLAY_ERR_DECODE_SUBTITLE_FAIL | Subtitle decoding error |
| -6009 | VOD_PLAY_ERR_RENDER_FAIL | Video rendering error |
| -6010 | VOD_PLAY_ERR_PROCESS_VIDEO_FAIL | Video post-processing error |
| -6011 | VOD_PLAY_ERR_DOWNLOAD_FAIL | Video download error |
| -6101 | VOD_PLAY_ERR_DRM | DRM playback failed |

### 20.6 Event Parameter Keys

| Key | Description |
|-----|-------------|
| EVT_PLAY_PROGRESS_MS | Playback progress (milliseconds) |
| EVT_PLAY_DURATION_MS | Total duration (milliseconds) |
| EVT_PLAYABLE_DURATION | Playable duration |
| EVT_PLAY_URL | Video URL |
| EVT_PLAY_COVER_URL | Video cover URL |
| EVT_KEY_WATER_MARK_TEXT | Ghost watermark text |
| NET_STATUS_CPU_USAGE | CPU usage |
| NET_STATUS_VIDEO_WIDTH | Video width |
| NET_STATUS_VIDEO_HEIGHT | Video height |
| NET_STATUS_NET_SPEED | Network speed |
| NET_STATUS_VIDEO_FPS | Video frame rate |
| NET_STATUS_VIDEO_BITRATE | Video bitrate |

### 20.7 Media Types

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | MEDIA_TYPE_AUTO | Auto-detect |
| 1 | MEDIA_TYPE_HLS_VOD | HLS VOD |
| 2 | MEDIA_TYPE_HLS_LIVE | HLS Live |
| 3 | MEDIA_TYPE_FILE_VOD | File VOD such as MP4 (11.2+) |
| 4 | MEDIA_TYPE_DASH_VOD | DASH VOD (11.2+) |

### 20.8 Player Types

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | PLAYER_SYSTEM_MEDIA_PLAYER | System player |
| 1 | PLAYER_THUMB_PLAYER | Proprietary player (default, supports software decoding) |

### 20.9 MP4 Encryption Levels

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | MP4_ENCRYPTION_LEVEL_NONE | No encryption |
| 2 | MP4_ENCRYPTION_LEVEL_L2 | L2 local encryption |
