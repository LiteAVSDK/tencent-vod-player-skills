# iOS - API Documentation

## 1. TXVodPlayer (Core Player Class)

The core playback class for VOD scenarios, responsible for video playback control, configuration management, and event listening.

> **Important**: Since version 10.7, you must set the Licence via `TXLiveBase#setLicence` before playback can succeed.

### 1.1 Basic Playback APIs

| Method/Property | Parameters | Description |
|----------------|-----------|-------------|
| `-startVodPlay:` | url: NSString | Start playback via URL |
| `-startVodPlayWithParams:` | params: TXPlayerAuthParams | Play via fileId or URL |
| `-startPlayDrm:` | builder: TXPlayerDrmBuilder | Play DRM-encrypted video (Premium Edition only) |
| `-stopPlay` | None | Stop playback |
| `isPlaying` | Property BOOL | Get whether playback is in progress |
| `-pause` | None | Pause playback |
| `-resume` | None | Resume playback |
| `-seek:` | time: float (seconds) | Seek to specified time position |
| `-seek:accurateSeek:` | time + BOOL | Accurate/imprecise seek |
| `-seekToPdtTime:` | pdtTimeMs: long | Seek to PDT time position (HLS, Premium Edition 11.6+) |
| `currentPlaybackTime` | Property float | Current playback progress (seconds) |
| `duration` | Property float | Total video duration (seconds) |
| `playableDuration` | Property float | Buffered playable duration (seconds) |
| `width` / `height` | Property int | Video resolution |
| `-setupVideoWidget:insertIndex:` | UIView + int | Set render View |
| `-removeVideoWidget` | None | Remove render View |

### 1.2 Player Configuration APIs

| Method/Property | Parameters | Description |
|----------------|-----------|-------------|
| `config` | TXVodPlayConfig | Set player configuration object |
| `isAutoPlay` | BOOL (default YES) | Whether to auto-play |
| `-setStartTime:` | float (seconds) | Start time position |
| `token` | NSString | HLS encryption Token |
| `loop` | BOOL | Loop playback |
| `enableHWAcceleration` | BOOL (default enabled) | Hardware acceleration |
| `-setExtentOptionInfo:` | NSDictionary | Extended parameters |
| `-setAutoMaxBitrate:` | int | Adaptive maximum bitrate |

### 1.3 Video APIs

| Method | Parameters | Description |
|--------|-----------|-------------|
| `-snapshot:` | Callback block | Capture current frame (async), returns UIImage |
| `-setMirror:` | BOOL | Mirror mode |
| `-setRate:` | float (0.5~3.0) | Playback speed |
| `bitrateIndex` | Property int | Current bitrate index |
| `supportedBitrates` | Property NSArray | Supported bitrate list (TXBitrateItem array) |
| `-setBitrateIndex:` | int (-1=adaptive) | Switch resolution |
| `-setRenderMode:` | TX_Enum_Type_RenderMode | Video fill mode (fill/fit) |
| `-setRenderRotation:` | TX_Enum_Type_HomeOrientation | Video rotation angle |

### 1.4 Audio & Subtitle APIs

| Method | Parameters | Description |
|--------|-----------|-------------|
| `-setMute:` | BOOL | Mute |
| `-setAudioPlayoutVolume:` | int (0-100) | Volume |
| `-setAudioNormalization:` | float (LUFS) | Audio normalization |
| `-addSubtitleSource:name:mimeType:` | URL + name + MIME | Add external subtitle (SRT/VTT) |
| `-getSubtitleTrackInfo` | None | Subtitle track list, returns NSArray\<TXTrackInfo\> |
| `-getAudioTrackInfo` | None | Audio track list, returns NSArray\<TXTrackInfo\> |
| `-selectTrack:` | int index | Select track |
| `-deselectTrack:` | int index | Deselect track |
| `-setSubtitleStyle:` | TXPlayerSubtitleRenderModel | Subtitle style |

### 1.5 Picture-in-Picture APIs

| Method | Description |
|--------|-------------|
| `isSupportPictureInPicture` | Check PiP support |
| `isSupportSeamlessPictureInPicture` | Check seamless PiP (Premium Edition) |
| `-setAutoPictureInPictureEnabled:` | Auto PiP when entering background |
| `-enterPictureInPicture` | Enter PiP |
| `-exitPictureInPicture` | Exit PiP |

### 1.6 Event Callback APIs

| Property | Protocol | Description |
|----------|----------|-------------|
| `vodDelegate` | TXVodPlayListener | Playback event callback |
| `videoProcessDelegate` | TXVideoCustomProcessDelegate | Video rendering callback |

### 1.7 TRTC Co-streaming APIs

| Method | Description |
|--------|-------------|
| `-attachTRTC:` | Attach TRTC service |
| `-detachTRTC` | Detach TRTC |
| `-publishVideo` / `-unpublishVideo` | Push/stop video stream |
| `-publishAudio` / `-unpublishAudio` | Push/stop audio stream |

---

## 2. TXVodPlayListener (Playback Event Callback)

### 2.1 onPlayEvent:event:withParam:

Playback event notification (start, first frame, loading, progress, end, etc.).

```objective-c
-(void) onPlayEvent:(TXVodPlayer *)player event:(int)EvtID withParam:(NSDictionary*)param
```

| Parameter | Type | Description |
|-----------|------|-------------|
| player | TXVodPlayer | Current player object |
| EvtID | int | Player event ID (refer to TXVodSDKEventDef) |
| param | NSDictionary | Event parameter dictionary |

### 2.2 onNetStatus:withParam:

Network status callback, triggered once per second.

```objective-c
-(void) onNetStatus:(TXVodPlayer *)player withParam:(NSDictionary*)param
```

| Parameter | Type | Description |
|-----------|------|-------------|
| player | TXVodPlayer | Current player object |
| param | NSDictionary | Network status parameter dictionary |

### 2.3 Picture-in-Picture Callbacks

| Method | Description |
|--------|-------------|
| `-onPlayer:pictureInPictureStateDidChange:withParam:` | PiP state change |
| `-onPlayer:pictureInPictureErrorDidOccur:withParam:` | PiP error |
| `-onPlayer:airPlayStateDidChange:withParam:` | AirPlay state change (system player only) |
| `-onPlayer:airPlayErrorDidOccur:withParam:` | AirPlay error (system player only) |

### 2.4 Subtitle Data Callback

```objective-c
-(void) onPlayer:(TXVodPlayer *)player subtitleData:(NSDictionary *)data
```

---

## 3. TXVodPlayConfig (Player Configuration)

### Enum Definitions

**TX_Enum_PlayerType (Player Type)**:
| Value | Constant | Description |
|-------|----------|-------------|
| 0 | PLAYER_AVPLAYER | iOS system player |
| 1 | PLAYER_THUMB_PLAYER | Proprietary player (default, supports software decoding) |

**TX_Enum_MP4EncryptionLevel (MP4 Encryption Level)**:
| Value | Constant | Description |
|-------|----------|-------------|
| 0 | MP4_ENCRYPTION_LEVEL_NONE | No encryption |
| 1 | MP4_ENCRYPTION_LEVEL_L1 | L1 online encryption |
| 2 | MP4_ENCRYPTION_LEVEL_L2 | L2 local encryption |

**TX_Enum_MediaType (Media Type)**:
| Value | Description |
|-------|-------------|
| MEDIA_TYPE_AUTO | Auto (default) |
| MEDIA_TYPE_HLS_VOD | HLS VOD |
| MEDIA_TYPE_HLS_LIVE | HLS Live |
| MEDIA_TYPE_FILE_VOD | File VOD such as MP4 |
| MEDIA_TYPE_DASH_VOD | DASH VOD |

### Core Configuration Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `connectRetryCount` | int | 3 | Reconnection retry count |
| `connectRetryInterval` | int | 3 (range 3-30) | Reconnection interval (seconds) |
| `timeout` | int | 10 | Connection timeout (seconds) |
| `headers` | NSDictionary | nil | Custom HTTP headers |
| `playerType` | enum | PLAYER_THUMB_PLAYER | Player type |
| `enableAccurateSeek` | BOOL | YES | Accurate seek |
| `autoRotate` | BOOL | YES | MP4 auto-rotation |
| `smoothSwitchBitrate` | BOOL | NO | Smooth bitrate switching |
| `progressInterval` | int (ms) | 500 | Progress callback interval |
| `maxBufferSize` | float (MB) | - | Maximum playback buffer |
| `maxPreloadSize` | float (MB) | - | Preload buffer |
| `firstStartPlayBufferTime` | int (ms) | 100 | First startup buffer duration |
| `nextStartPlayBufferTime` | int (ms) | 250 | Secondary buffer duration |
| `preferredResolution` | long | - | Preferred HLS startup resolution (width×height) |
| `mediaType` | enum | AUTO | Media type |
| `encryptedMp4Level` | enum | NONE | MP4 encryption level (Premium Edition 12.2+) |
| `overlayKey` | NSString | nil | HLS encryption Key |
| `overlayIv` | NSString | nil | HLS encryption IV |
| `enableRenderProcess` | BOOL | NO | Render post-processing (TSR/VR) |
| `keepLastFrameWhenStop` | BOOL | NO | Keep last frame after stop |
| `preferAudioTrack` | NSString | nil | Preferred audio track on startup (Premium Edition 12.3+) |

---

## 4. TXPlayerGlobalSetting (Global Settings)

Global configuration affecting all player instances.

| Method | Parameters | Description |
|--------|-----------|-------------|
| `+setCacheFolderPath:` | NSString, nil=disabled | Cache directory |
| `+cacheFolderPath` | Returns NSString | Get cache directory |
| `+setMaxCacheSize:` | NSInteger (MB) | Maximum cache size |
| `+maxCacheSize` | Returns NSInteger | Get cache size |
| `+setLicenseFlexibleValid:` | BOOL | License flexible validation (first 2 launches pass by default) |
| `+setPlayCGIHosts:` | NSArray\<NSString\*\> | PlayCGI fallback domain list |
| `+getOptions:` | NSNumber featureId | Query feature capability support |

---

## 5. TXVodDownloadManager (Offline Download)

Singleton pattern for managing video offline downloads. Supports MP4/HLS, DRM and private encryption.

### 5.1 Error Codes (TXDownloadError)

| Error Code | Constant | Description |
|------------|----------|-------------|
| 0 | TXDownloadSuccess | Download successful |
| -5001 | TXDownloadAuthFaild | fileId authentication failed |
| -5003 | TXDownloadNoFile | File with this resolution not found |
| -5004 | TXDownloadFormatError | Format not supported |
| -5005 | TXDownloadDisconnet | Network disconnected |
| -5006 | TXDownloadHlsKeyError | HLS decryption key failed |
| -5007 | TXDownloadPathError | Download directory access failed |
| -5008 | TXDownload403Forbidden | Signature expired or invalid request |

### 5.2 Core APIs

```objective-c
// Get singleton
TXVodDownloadManager *manager = [TXVodDownloadManager shareInstance];

// Set delegate (must be set before downloading)
manager.delegate = self; // Conform to TXVodDownloadDelegate protocol

// Set download path (lower priority than TXPlayerGlobalSetting.setCacheFolderPath)
[manager setDownloadPath:path];

// Download by URL
[manager startDownloadUrl:url resolution:preferredResolution userName:@"default"];
// preferredResolution: width*height (e.g. 921600=1280*720), pass -1 for single resolution

// Download by FileId
[manager startDownload:dataSource]; // TXVodDownloadDataSource

// DRM download
[manager startDownloadDrm:drmBuilder resolution:preferredResolution userName:@"default"];

// Stop download
[manager stopDownload:mediaInfo];

// Delete download (cannot delete while downloading)
[manager deleteDownloadMediaInfo:mediaInfo];

// Get download list (time-consuming operation, do NOT call on main thread!)
NSArray *list = [manager getDownloadMediaInfoList];
```

### 5.3 Configuration Items

| Property | Description |
|----------|-------------|
| `headers` | Download HTTP request headers |
| `supportPrivateEncryptMode` | Private encryption mode (proprietary player YES, system player NO) |

---

## 6. TXVodDownloadDelegate (Download Callback Protocol)

### 6.1 Callback Method List

| Method | Description |
|--------|-------------|
| `-onDownloadStart:` | Download started |
| `-onDownloadProgress:` | Download progress update |
| `-onDownloadStop:` | Download stopped (triggered when stopDownload is called) |
| `-onDownloadFinish:` | Download completed |
| `-onDownloadError:errorCode:errorMsg:` | Download error |

### 6.2 Method Signatures

```objective-c
-(void)onDownloadStart:(TXVodDownloadMediaInfo *)mediaInfo;
-(void)onDownloadProgress:(TXVodDownloadMediaInfo *)mediaInfo;
-(void)onDownloadStop:(TXVodDownloadMediaInfo *)mediaInfo;
-(void)onDownloadFinish:(TXVodDownloadMediaInfo *)mediaInfo;
-(void)onDownloadError:(TXVodDownloadMediaInfo *)mediaInfo
             errorCode:(TXDownloadError)code
               errorMsg:(NSString *)msg;
```

### 6.3 onDownloadError Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| mediaInfo | TXVodDownloadMediaInfo | Video download info object |
| code | TXDownloadError | Download error code (refer to 5.1) |
| msg | NSString | Error description |

---

## 7. TXVodDownloadDataSource (Download Data Source)

VOD download resource object for configuring download parameters.

### 7.1 Quality Enum (TXVodQuality)

| Enum Value | Number | Description |
|------------|--------|-------------|
| TXVodQualityOD | 0 | Original quality |
| TXVodQualityFLU | 1 | Smooth |
| TXVodQualitySD | 2 | Standard |
| TXVodQualityHD | 3 | HD |
| TXVodQualityFHD | 4 | Full HD |
| TXVodQuality2K | 5 | 2K |
| TXVodQuality4K | 6 | 4K |
| TXVodQuality240P | 240 | Smooth 240P |
| TXVodQuality360P | 360 | Smooth 360P |
| TXVodQuality480P | 480 | Standard 480P |
| TXVodQuality540P | 540 | Standard 540P |
| TXVodQuality720P | 720 | HD 720P |
| TXVodQuality1080p | 1080 | Full HD 1080P |

### 7.2 Properties

| Property | Type | Description |
|----------|------|-------------|
| `auth` | TXPlayerAuthParams * | VOD fileID authentication info |
| `quality` | TXVodQuality | Download quality (default HD) |
| `token` | NSString * | DRM Token (when set, URL auto-appends voddrm.token) |
| `templateName` | NSString * | Resolution template (takes priority over quality when both set) |
| `fileId` | NSString * | Video file ID |
| `pSign` | NSString * | Signature info |
| `appId` | int | Application ID |
| `userName` | NSString * | Account name (default "default") |
| `overlayKey` | NSString * | HLS EXT-X-KEY encryption Key |
| `overlayIv` | NSString * | HLS EXT-X-KEY encryption IV |

---

## 8. TXVodDownloadMediaInfo (Download Media Info)

### 8.1 Download State Enum (TXVodDownloadMediaInfoState)

| Name | Value | Description |
|------|-------|-------------|
| TXVodDownloadMediaInfoStateInit | 0 | Download initial state |
| TXVodDownloadMediaInfoStateStart | 1 | Download started |
| TXVodDownloadMediaInfoStateStop | 2 | Download stopped |
| TXVodDownloadMediaInfoStateError | 3 | Download error |
| TXVodDownloadMediaInfoStateFinish | 4 | Download completed |

### 8.2 Properties

| Property | Type | Description |
|----------|------|-------------|
| `dataSource` | TXVodDownloadDataSource | Download source info (for fileId downloads) |
| `url` | NSString * | Actual download URL |
| `userName` | NSString * | Account name (default "default") |
| `duration` | int (milliseconds) | Total video duration |
| `playableDuration` | int (milliseconds) | Downloaded playable duration |
| `size` | long (Bytes) | Total file size (fileId only, original file size) |
| `downloadSize` | long (Bytes) | Downloaded size (fileId only) |
| `segments` | int | Total video segments |
| `downloadSegments` | int | Downloaded segments |
| `progress` | float | Download progress (0.0~1.0) |
| `playPath` | NSString * | Local playback path (can be passed to TXVodPlayer) |
| `speed` | int (KBytes/sec) | Download speed (10.9+) |
| `downloadState` | TXVodDownloadMediaInfoState | Download state |
| `preferredResolution` | long | Preferred resolution |
| `isResourceBroken` | BOOL | Whether resource is corrupted (11.0+) |

### 8.3 Methods

| Method | Return Value | Description |
|--------|--------------|-------------|
| `-isDownloadFinished` | BOOL | Check if download is completed |

---

## 9. TXVodPreloadManager (Video Preloading)

Preload video without creating a player instance to speed up playback startup.

> **Prerequisite**: Must first set `TXPlayerGlobalSetting setCacheFolderPath:` and `setMaxCacheSize:`

### 9.1 Core APIs

```objective-c
// Get singleton
TXVodPreloadManager *preloadManager = [TXVodPreloadManager sharedManager];

// Preload by URL
int taskId = [preloadManager startPreload:url
                              preloadSize:preloadSizeMB
                      preferredResolution:resolution
                                 delegate:self];
// preloadSizeMB: preload size (MB)
// preferredResolution: width*height, pass -1 if not specified
// Returns taskId, -1 means invalid

// Preload by Model (recommended, fileId method does NOT support main thread calls!)
int taskId = [preloadManager startPreloadWithModel:params
                                       preloadSize:preloadSizeMB
                               preferredResolution:resolution
                                          delegate:self];
// params: TXPlayerAuthParams (can set headers, preferAudioTrack)

// Stop preloading
[preloadManager stopPreload:taskId];
```

---

## 10. TXVodPreloadManagerDelegate (Preload Callback Protocol)

### 10.1 URL Preload Callbacks

| Method | Description |
|--------|-------------|
| `-onComplete:url:` | Preload completed |
| `-onError:url:error:` | Preload error |

### 10.2 FileId Preload Callbacks

| Method | Description |
|--------|-------------|
| `-onStart:fileId:url:param:` | Preload started (after URL resolution) |
| `-onComplete:url:` | Preload completed |
| `-onError:url:error:` | Preload error |

### 10.3 Method Signatures

```objective-c
// Preload started
-(void)onStart:(int)taskID fileId:(NSString *)fileId url:(NSString *)url param:(NSDictionary *)param;
// taskID: preload task ID
// fileId: video fileId (nil for URL method)
// url: resolved video URL (can be used for subsequent playback)
// param: additional parameters

// Preload completed
-(void)onComplete:(int)taskID url:(NSString *)url;

// Preload error
-(void)onError:(int)taskID url:(NSString *)url error:(NSError *)error;
```

---

## 11. TXPlayerAuthParams (FileId/URL Playback Parameters)

Used to configure playback media parameters, supporting both FileId and URL methods.

### 11.1 Properties

| Property | Type | Description |
|----------|------|-------------|
| `appId` | int | Application ID (required when url is not set) |
| `fileId` | NSString * | Video file ID (required when url is not set) |
| `timeout` | NSString * | Encrypted link timeout timestamp (optional) |
| `exper` | int | Trial duration (seconds, optional) |
| `us` | NSString * | Unique request identifier (optional) |
| `sign` | NSString * | Hotlink protection signature (optional) |
| `https` | BOOL | Whether to use HTTPS (default NO) |
| `url` | NSString * | Video file URL |
| `mediaType` | TX_Enum_MediaType | Media type (default AUTO) |
| `encryptedMp4Level` | TX_Enum_MP4EncryptionLevel | MP4 encryption level (Premium Edition 12.2+) |
| `headers` | NSDictionary * | Custom HTTP headers |
| `preferAudioTrack` | NSString * | Preferred audio track on startup (Premium Edition 12.3+) |

### 11.2 Hotlink Protection Signature Calculation

```
// Standard hotlink protection
sign = md5(KEY + appId + fileId + t + us)

// Preview hotlink protection (with trial)
sign = md5(KEY + appId + fileId + t + exper + us)
```

---

## 12. TXVodDef (Type Definitions)

### 12.1 TXVodSubtitleData (Subtitle Text Data)

| Parameter | Type | Description |
|-----------|------|-------------|
| subtitleData | NSString | Subtitle content (empty=no subtitle) |
| trackIndex | int64_t | Current subtitle track's trackIndex |
| durationMs | int64_t | Subtitle duration (milliseconds, **temporarily empty, do not use**) |
| startPositionMs | int64_t | Subtitle start time (milliseconds, **temporarily empty, do not use**) |

---

## 13. TXTrackInfo (Track Information)

### 13.1 Track Type Enum (TX_VOD_MEDIA_TRACK_TYPE)

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | TX_VOD_MEDIA_TRACK_TYPE_UNKNOW | Unknown type |
| 1 | TX_VOD_MEDIA_TRACK_TYPE_VIDEO | Video track |
| 2 | TX_VOD_MEDIA_TRACK_TYPE_AUDIO | Audio track |
| 3 | TX_VOD_MEDIA_TRACK_TYPE_SUBTITLE | Subtitle track |

### 13.2 Properties

| Property | Type | Description |
|----------|------|-------------|
| `trackType` | TX_VOD_MEDIA_TRACK_TYPE | Track type |
| `trackIndex` | int | Track index |
| `name` | NSString * | Track name |
| `isSelected` | bool | Whether selected |
| `isExclusive` | bool | Whether exclusive (YES=only one track of same type at a time) |
| `isInternal` | bool | Whether it is an internal original track |

### 13.3 Methods

| Method | Return Value | Description |
|--------|--------------|-------------|
| `-getTrackIndex` | int | Get track index |
| `-getTrackType` | TX_VOD_MEDIA_TRACK_TYPE | Get track type |
| `-getTrackName` | NSString * | Get track name |
| `-isEqual:` | bool | Compare two track objects |

---

## 14. TXPlayerSubtitleRenderModel (Subtitle Render Model)

Used to customize subtitle display styles.

### 14.1 Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `fontColor` | uint32_t | 0xFFFFFFFF (white) | Font color (ARGB) |
| `fontSize` | float | - | Font size (requires canvasWidth/Height) |
| `fontScale` | float | 1.0 | Font scale ratio (for VTT subtitles) |
| `familyName` | NSString * | "Helvetica" | Font family name |
| `canvasWidth` | int | Video width | Render canvas width (must match video aspect ratio) |
| `canvasHeight` | int | Video height | Render canvas height |
| `isBondFontStyle` | BOOL | NO | Bold font style |
| `outlineWidth` | float | Default | Outline width |
| `outlineColor` | uint32_t | 0xFF000000 (black) | Outline color (ARGB) |
| `lineSpace` | float | Default | Line spacing (requires canvasWidth/Height) |
| `startMargin` | float | - | Start margin (ratio 0~1, overrides subtitle file parameters) |
| `endMargin` | float | - | End margin (ratio 0~1) |
| `verticalMargin` | float | - | Vertical margin (ratio 0~1) |

---

## 15. TXBitrateItem (Bitrate Information)

Video bitrate information class describing HLS multi-bitrate video stream parameters.

| Property | Type | Description |
|----------|------|-------------|
| `index` | NSInteger | Bitrate index (corresponds to m3u8 sequence number) |
| `width` | NSInteger | Video stream width |
| `height` | NSInteger | Video stream height |
| `bitrate` | NSInteger | Video stream bitrate |
| `bandwidth` | int64_t | Video stream bandwidth |

---

## 16. TXPlayerDrmBuilder (DRM Builder)

Used to build DRM playback information, used with `-[TXVodPlayer startPlayDrm:]`.

### 16.1 Initializer

```objective-c
-(instancetype)initWithDeviceCertificateUrl:(NSString *)certificateUrl
                                 licenseUrl:(NSString *)licenseUrl
                                   videoUrl:(NSString *)videoUrl;
```

| Parameter | Type | Description |
|-----------|------|-------------|
| certificateUrl | NSString * | Certificate provider URL |
| licenseUrl | NSString * | License URL |
| videoUrl | NSString * | Video playback URL |

### 16.2 Properties

| Property | Type | Description |
|----------|------|-------------|
| `deviceCertificateUrl` | NSString * | Certificate provider URL |
| `keyLicenseUrl` | NSString * | Decryption Key URL |
| `playUrl` | NSString * | Playback media URL |

---

## 17. TXImageSprite (Image Sprite)

VOD image sprite parsing utility class.

### 17.1 APIs

| Method | Description |
|--------|-------------|
| `-setVTTUrl:imageUrls:` | Set image sprite URLs (starts background thread for download and parsing) |
| `-getThumbnail:` | Get thumbnail at specified time (seconds), returns UIImage, nil on failure |

### 17.2 Method Signatures

```objective-c
-(void)setVTTUrl:(NSURL *)vttUrl imageUrls:(NSArray<NSURL *> *)images;
-(UIImage *)getThumbnail:(GLfloat)time;
```

---

## 18. TXVodSDKEventDef (Events & Constants)

### 18.1 Video Resolution (TX_Enum_Type_VideoResolution)

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | VIDEO_RESOLUTION_TYPE_360_640 | Recommended bitrate 800kbps |
| 1 | VIDEO_RESOLUTION_TYPE_540_960 | Recommended bitrate 1200kbps |
| 2 | VIDEO_RESOLUTION_TYPE_720_1280 | Recommended bitrate 1800kbps |
| 30 | VIDEO_RESOLUTION_TYPE_1080_1920 | Recommended bitrate 3000kbps |

### 18.2 Video Quality Tier (TX_Enum_Type_VideoQuality)

| Value | Constant | Description |
|-------|----------|-------------|
| 1 | VIDEO_QUALITY_STANDARD_DEFINITION | Standard: 360 x 640 |
| 2 | VIDEO_QUALITY_HIGH_DEFINITION | HD: 540 x 960 |
| 3 | VIDEO_QUALITY_SUPER_DEFINITION | Super HD: 720 x 1280 |
| 4 | VIDEO_QUALITY_LINKMIC_MAIN_PUBLISHER | Co-host main publisher |
| 5 | VIDEO_QUALITY_LINKMIC_SUB_PUBLISHER | Co-host sub publisher |
| 7 | VIDEO_QUALITY_ULTRA_DEFINITION | Ultra HD: 1080 x 1920 |

### 18.3 Screen Rotation Direction (TX_Enum_Type_HomeOrientation)

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | HOME_ORIENTATION_RIGHT | Home button on right, landscape |
| 1 | HOME_ORIENTATION_DOWN | Home button at bottom, portrait |
| 2 | HOME_ORIENTATION_LEFT | Home button on left, landscape |
| 3 | HOME_ORIENTATION_UP | Home button at top, portrait |

### 18.4 Video Fill Mode (TX_Enum_Type_RenderMode)

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | RENDER_MODE_FILL_SCREEN | Fill screen (may crop) |
| 1 | RENDER_MODE_FILL_EDGE | Fit (may have black bars) |

### 18.5 Playback Events

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
| 2019 | VOD_PLAY_EVT_VOD_PLAY_SEEK_COMPLETE | Seek completed (10.3+) |
| 2020 | VOD_PLAY_EVT_SELECT_TRACK_COMPLETE | Track switch completed |
| 2026 | VOD_PLAY_EVT_RCV_FIRST_AUDIO_FRAME | First audio frame played |
| 2030 | VOD_PLAY_EVT_VIDEO_SEI | Video SEI information |
| 2031 | VOD_PLAY_EVT_HEVC_DOWNGRADE_PLAYBACK | HEVC downgrade playback |
| 6001 | VOD_PLAY_EVT_LOOP_ONCE_COMPLETE | One loop cycle completed |

### 18.6 Warning Events

| Event Code | Constant | Description |
|------------|----------|-------------|
| 2103 | PLAY_WARNING_RECONNECT | Network disconnected, auto-reconnection started |
| 2106 | PLAY_WARNING_HW_ACCELERATION_FAIL | Hardware decoding failed, switched to software decoding |

### 18.7 Error Events

| Error Code | Constant | Description |
|------------|----------|-------------|
| -5 | VOD_PLAY_ERR_LICENCE_CHECK_FAIL | Invalid License |
| -2301 | VOD_PLAY_ERR_NET_DISCONNECT | Network disconnected, multiple reconnections failed |
| -2303 | VOD_PLAY_ERR_FILE_NOT_FOUND | File not found |
| -2304 | PLAY_ERR_HEVC_DECODE_FAIL | HEVC decoding failed |
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

### 18.8 PiP State (TX_VOD_PLAYER_PIP_STATE)

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | TX_VOD_PLAYER_PIP_STATE_UNDEFINED | Invalid state |
| 1 | TX_VOD_PLAYER_PIP_STATE_WILL_START | About to start |
| 2 | TX_VOD_PLAYER_PIP_STATE_DID_START | Started |
| 3 | TX_VOD_PLAYER_PIP_STATE_WILL_STOP | About to stop |
| 4 | TX_VOD_PLAYER_PIP_STATE_RESTORE_UI | Restore UI |

### 18.9 PiP Error Type (TX_VOD_PLAYER_PIP_ERROR_TYPE)

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | TX_VOD_PLAYER_PIP_ERROR_TYPE_NONE | No error |
| 1 | TX_VOD_PLAYER_PIP_ERROR_TYPE_DEVICE_NOT_SUPPORT | Device/system not supported |
| 2 | TX_VOD_PLAYER_PIP_ERROR_TYPE_PLAYER_NOT_SUPPORT | Player not supported |
| 3 | TX_VOD_PLAYER_PIP_ERROR_TYPE_VIDEO_NOT_SUPPORT | Video not supported |
| 4 | TX_VOD_PLAYER_PIP_ERROR_TYPE_PIP_IS_NOT_POSSIBLE | PiP controller unavailable |
| 5 | TX_VOD_PLAYER_PIP_ERROR_TYPE_ERROR_FROM_SYSTEM | PiP controller system error |
| 10 | TX_VOD_PLAYER_PIP_ERROR_TYPE_PLAYER_NOT_EXIST | Player object not found |
| 11 | TX_VOD_PLAYER_PIP_ERROR_TYPE_PIP_IS_RUNNING | PiP already running |
| 12 | TX_VOD_PLAYER_PIP_ERROR_TYPE_PIP_NOT_RUNNING | PiP not running |
| 13 | TX_VOD_PLAYER_PIP_ERROR_TYPE_PIP_START_TIMEOUT | PiP start timeout |
| 20 | TX_VOD_PLAYER_PIP_ERROR_TYPE_SEAMLESS_PIP_ERROR | Seamless PiP start failed |
| 21 | TX_VOD_PLAYER_PIP_ERROR_TYPE_SEAMLESS_PIP_NOT_SUPPORT | Seamless PiP not supported |
| 22 | TX_VOD_PLAYER_PIP_ERROR_TYPE_SEAMLESS_PIP_IS_RUNNING | Seamless PiP already running |

### 18.10 AirPlay State & Error (System Player Only)

**AirPlay State (TX_VOD_PLAYER_AIRPLAY_STATE)**:
| Value | Constant | Description |
|-------|----------|-------------|
| 0 | TX_VOD_PLAYER_AIRPLAY_STATE_NOT_RUNNING | Not running |
| 1 | TX_VOD_PLAYER_AIRPLAY_STATE_DID_RUNNING | Running |

**AirPlay Error (TX_VOD_PLAYER_AIRPLAY_ERROR_TYPE)**:
| Value | Constant | Description |
|-------|----------|-------------|
| 0 | TX_VOD_PLAYER_AIRPLAY_ERROR_TYPE_NONE | No error |
| 1 | TX_VOD_PLAYER_AIRPLAY_ERROR_TYPE_PLAYER_NOT_SUPPORT | Player not supported |
| 2 | TX_VOD_PLAYER_AIRPLAY_ERROR_TYPE_VIDEO_NOT_SUPPORT | Video not supported |
| 10 | TX_VOD_PLAYER_AIRPLAY_ERROR_TYPE_PLAYER_INVALID | Player unavailable |
| 11 | TX_VOD_PLAYER_AIRPLAY_ERROR_TYPE_PLAYER_STATE | Player state error |

### 18.11 External Subtitle Type (TX_VOD_PLAYER_SUBTITLE_MIME_TYPE)

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | TX_VOD_PLAYER_MIMETYPE_TEXT_SRT | SRT format |
| 1 | TX_VOD_PLAYER_MIMETYPE_TEXT_VTT | VTT format |

### 18.12 Media Types

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | MEDIA_TYPE_AUTO | Auto-detect |
| 1 | MEDIA_TYPE_HLS_VOD | HLS VOD |
| 2 | MEDIA_TYPE_HLS_LIVE | HLS Live |
| 3 | MEDIA_TYPE_FILE_VOD | File VOD such as MP4 |
| 4 | MEDIA_TYPE_DASH_VOD | DASH VOD |

### 18.13 MP4 Encryption Levels

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | MP4_ENCRYPTION_LEVEL_NONE | No encryption |
| 1 | MP4_ENCRYPTION_LEVEL_L1 | L1 online encryption |
| 2 | MP4_ENCRYPTION_LEVEL_L2 | L2 local encryption |

### 18.14 Module Types (TSR/VR)

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | PLAYER_OPTION_PARAM_MODULE_TYPE_NONE | Disable TSR and VR |
| 1 | PLAYER_OPTION_PARAM_MODULE_TYPE_SR | Super-Resolution type |
| 11 | PLAYER_OPTION_PARAM_MODULE_TYPE_VR_PANORAMA | VR panorama (monocular) |
| 12 | PLAYER_OPTION_PARAM_MODULE_TYPE_VR_BINOCULAR | VR panorama (binocular) |

### 18.15 Player Types

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | PLAYER_SYSTEM_MEDIA_PLAYER | System player |
| 1 | PLAYER_THUMB_PLAYER | Proprietary player (supports software decoding) |
