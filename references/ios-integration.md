# iOS - Integration Guide & VOD Playback

## 1. Development Environment Requirements

- **Xcode**: 9.0 or later
- **Device System**: iPhone or iPad with iOS 9.0 or later
- **Signing**: Valid developer signing configured

---

## 2. SDK Integration Methods

### Method 1: CocoaPods (Recommended)

**1. Install CocoaPods**:
```bash
sudo gem install cocoapods
```

**2. Create Podfile**:
```bash
pod init
```

**3. Edit Podfile**:

Standard Edition:
```ruby
platform :ios, '9.0'
source 'https://github.com/CocoaPods/Specs.git'
target 'App' do
  pod 'TXLiteAVSDK_Player'
end
```

Premium Edition:
```ruby
platform :ios, '9.0'
source 'https://github.com/CocoaPods/Specs.git'
target 'App' do
  pod 'TXLiteAVSDK_Player_Premium'
end
```

Specify version:
```ruby
pod 'TXLiteAVSDK_Player', '~> 10.3.11513'
```

**4. Install SDK**:
```bash
pod install
```

> After installation, open the project using the `.xcworkspace` file.

### Method 2: Manual Integration

1. Download the SDK package (`TXLiteAVSDK_Player_iOS_latest.zip`)
2. Add `TXLiteAVSDK_Player.xcframework` to the project
3. Set Other Linker Flags to `-ObjC`
4. Add system libraries:
   - MetalKit
   - ReplayKit
   - AVFoundation
   - SystemConfiguration
   - CoreTelephony
   - VideoToolbox
   - CoreGraphics
   - Accelerate
   - MobileCoreServices
   - libz
   - libresolv
   - libiconv
   - libc++
   - libsqlite3
5. Add `TXFFmpeg.xcframework` and `TXSoundTouch.xcframework` and set them to Embed & Sign
6. Starting from version 11.7.15343, add `TXLiteAVSDK_Player.bundle` to the project to comply with Apple's privacy manifest

---

## 3. SDK Import

**Method 1: Module Import (Recommended)**
```objective-c
@import TXLiteAVSDK_Player;
// Premium Edition: @import TXLiteAVSDK_Player_Premium;
```

**Method 2: Header File Import**
```objective-c
#import "TXLiteAVSDK_Player/TXLiteAVSDK.h"
// Premium Edition: #import "TXLiteAVSDK_Player_Premium/TXLiteAVSDK.h"
```

**Swift Projects**: Import via bridging header file (`Bridging-Header.h`) or `module.modulemap`.

---

## 4. License Configuration (Required)

Not configuring the License will cause video playback to fail.

1. Go to [License Application](https://console.tencentcloud.com/vcube) to obtain `licenseURL` and `key`
2. Initialize configuration before calling SDK APIs (recommended in AppDelegate's `didFinishLaunchingWithOptions`):
```objective-c
[TXLiveBase setLicenceURL:@"LicenseUrl" key:@"LicenseKey"];
```

---

## 5. VOD Playback - Quick Start

### 1. Create Player

```objective-c
TXVodPlayer *_txVodPlayer = [[TXVodPlayer alloc] init];
```

### 2. Configure Render View

```objective-c
[_txVodPlayer setupVideoWidget:_myView insertIndex:0];
```

### 3. Start Playback

**Method 1: Play via URL**
```objective-c
// Online video
NSString *url = @"http://1252463788.vod2.example.com/xxxxx/v.f20.mp4";
[_txVodPlayer startVodPlay:url];

// Local sandbox video
NSString *documentPath = [NSSearchPathForDirectoriesInDomains(
    NSDocumentDirectory, NSUserDomainMask, YES) firstObject];
NSString *videoPath = [NSString stringWithFormat:@"%@/video1.m3u8", documentPath];
[_txVodPlayer startVodPlay:videoPath];
```

**Method 2: Play via FileId (Recommended)**
```objective-c
TXPlayerAuthParams *p = [TXPlayerAuthParams new];
p.appId = 1252463788;
p.fileId = @"4564972819220421305";
p.sign = @"psignxxxxx";
[_txVodPlayer startVodPlayWithParams:p];
```

**Method 3: Play via TXPlayerAuthParams (URL)**
```objective-c
TXPlayerAuthParams *p = [TXPlayerAuthParams new];
p.url = @"http://your_video_url.mp4";
p.mediaType = MEDIA_TYPE_FILE_VOD;  // Optional: set media type
p.headers = customHeaders;          // Optional: set custom HTTP headers
[_txVodPlayer startVodPlayWithParams:p];
```

### 4. Stop Playback & Cleanup

**Must be called when exiting the view to prevent memory leaks:**
```objective-c
[_txVodPlayer stopPlay];
[_txVodPlayer removeVideoWidget];
```

---

## 6. Common Features

### 1. Playback Control

```objective-c
[_txVodPlayer startVodPlay:url];     // Start playback
[_txVodPlayer pause];                // Pause
[_txVodPlayer resume];               // Resume
[_txVodPlayer stopPlay];             // Stop

// Seek
[_txVodPlayer seek:600.0];                       // Regular seek (seconds)
[_txVodPlayer seek:600.0 accurateSeek:YES];       // Accurate seek
[_txVodPlayer seek:600.0 accurateSeek:NO];        // Imprecise seek

// Loop playback
[_txVodPlayer setLoop:YES];

// Start time position (set before calling startVodPlay)
[_txVodPlayer setStartTime:30.0];

// Auto-play control
_txVodPlayer.isAutoPlay = NO;  // Disable auto-play (for preloading/instant playback)
```

### 2. Video & Volume Settings

```objective-c
// Render mode
[_txVodPlayer setRenderMode:RENDER_MODE_FILL_SCREEN];  // Fill screen (may crop)
[_txVodPlayer setRenderMode:RENDER_MODE_FILL_EDGE];    // Fit (may have black bars)

// Video rotation
[_txVodPlayer setRenderRotation:HOME_ORIENTATION_RIGHT]; // Home button on right, landscape
[_txVodPlayer setRenderRotation:HOME_ORIENTATION_DOWN];  // Home button at bottom, portrait
[_txVodPlayer setRenderRotation:HOME_ORIENTATION_LEFT];  // Home button on left, landscape
[_txVodPlayer setRenderRotation:HOME_ORIENTATION_UP];    // Home button at top, portrait

// Playback speed (0.5x ~ 3.0x)
[_txVodPlayer setRate:1.2];

// Mirror
[_txVodPlayer setMirror:YES];

// Mute
[_txVodPlayer setMute:YES];

// Volume (0-100)
[_txVodPlayer setAudioPlayoutVolume:80];

// Audio normalization
[_txVodPlayer setAudioNormalization:-16.0];

// Loop playback
[_txVodPlayer setLoop:YES];
```

### 3. Resolution Switching (HLS Multi-Bitrate)

```objective-c
// Get supported bitrate list
NSArray *bitrates = _txVodPlayer.supportedBitrates;
for (TXBitrateItem *item in bitrates) {
    NSLog(@"index:%ld width:%ld height:%ld bitrate:%ld",
          (long)item.index, (long)item.width, (long)item.height, (long)item.bitrate);
}

// Switch resolution
_txVodPlayer.bitrateIndex = bitrates[0].index;

// Adaptive bitrate (-1)
_txVodPlayer.bitrateIndex = -1;

// Limit adaptive maximum bitrate
[_txVodPlayer setAutoMaxBitrate:maxBitrate];
```

### 4. Playback Configuration

```objective-c
TXVodPlayConfig *_config = [[TXVodPlayConfig alloc] init];

_config.connectRetryCount = 3;               // Reconnection retry count (default 3)
_config.connectRetryInterval = 3;            // Reconnection interval (seconds, range 3-30, default 3)
_config.timeout = 10;                        // Connection timeout (seconds, default 10)
_config.enableAccurateSeek = YES;            // Accurate seek (default YES)
_config.autoRotate = YES;                    // MP4 auto-rotation (default YES)
_config.smoothSwitchBitrate = NO;            // Smooth bitrate switching (default NO)
_config.progressInterval = 200;              // Progress callback interval (ms, default 500)
[_config setMaxBufferSize:10];               // Maximum buffer (MB)
[_config setMaxPreloadSize:2];               // Preload buffer (MB)
[_config setSmoothSwitchBitrate:YES];        // Smooth bitrate switching
[_config setPreferredResolution:720*1280];   // Preferred startup resolution

_config.playerType = PLAYER_THUMB_PLAYER;    // Player type (default: proprietary)
_config.mediaType = MEDIA_TYPE_AUTO;         // Media type (default: AUTO)
_config.headers = customHeaders;             // Custom HTTP headers
_config.firstStartPlayBufferTime = 100;      // First startup buffer (ms, default 100)
_config.nextStartPlayBufferTime = 250;       // Secondary buffer duration (ms, default 250)
_config.encryptedMp4Level = MP4_ENCRYPTION_LEVEL_NONE;  // MP4 encryption (Premium Edition 12.2+)
_config.overlayKey = nil;                    // Encryption Key (HLS)
_config.overlayIv = nil;                     // Encryption IV
_config.enableRenderProcess = NO;            // Render post-processing (TSR/VR)
_config.keepLastFrameWhenStop = NO;          // Keep last frame after stop
_config.preferAudioTrack = nil;              // Preferred audio track on startup (Premium Edition 12.3+)

[_txVodPlayer setConfig:_config];
```

### 5. Global Cache Settings (Set Once)

```objective-c
NSArray *paths = NSSearchPathForDirectoriesInDomains(
    NSDocumentDirectory, NSUserDomainMask, YES);
NSString *documentsDirectory = [paths objectAtIndex:0];
NSString *preloadDataPath = [documentsDirectory
    stringByAppendingPathComponent:@"/preload"];

[TXPlayerGlobalSetting setCacheFolderPath:preloadDataPath];
[TXPlayerGlobalSetting setMaxCacheSize:200];  // MB
```

### 6. Screen Keep-Awake Control

```objective-c
// Keep screen on during playback/resume
[[UIApplication sharedApplication] setIdleTimerDisabled:YES];

// Restore auto-lock on stop/pause
[[UIApplication sharedApplication] setIdleTimerDisabled:NO];
```

### 7. Event Listening

Implement the `TXVodPlayListener` protocol:

```objective-c
_txVodPlayer.vodDelegate = self;

-(void) onPlayEvent:(TXVodPlayer *)player event:(int)EvtID withParam:(NSDictionary*)param {
    if (EvtID == PLAY_EVT_VOD_PLAY_PREPARED) {
        // Player ready
    } else if (EvtID == PLAY_EVT_RCV_FIRST_I_FRAME) {
        // First video frame rendered
    } else if (EvtID == PLAY_EVT_PLAY_BEGIN) {
        // Playback started
    } else if (EvtID == PLAY_EVT_PLAY_PROGRESS) {
        float playable = [param[EVT_PLAYABLE_DURATION] floatValue];
        float progress = [param[EVT_PLAY_PROGRESS] floatValue];
        float duration = [param[EVT_PLAY_DURATION] floatValue];
    } else if (EvtID == PLAY_EVT_PLAY_END) {
        // Playback ended
    } else if (EvtID == PLAY_EVT_PLAY_LOADING) {
        // Buffering
    } else if (EvtID == PLAY_EVT_VOD_LOADING_END) {
        // Buffering ended
    } else if (EvtID == PLAY_EVT_CHANGE_RESOLUTION) {
        // Resolution changed
    } else if (EvtID == PLAY_EVT_VOD_PLAY_SEEK_COMPLETE) {
        // Seek completed (10.3+)
    } else if (EvtID == PLAY_EVT_GET_PLAYINFO_SUCC) {
        // VOD file info retrieved successfully
        NSString *coverUrl = param[EVT_PLAY_COVER_URL];
        NSString *playUrl = param[EVT_PLAY_URL];
    } else if (EvtID == PLAY_EVT_LOOP_ONCE_COMPLETE) {
        // One loop cycle completed
    } else if (EvtID == PLAY_EVT_VOD_PLAY_FIRST_VIDEO_PACKET) {
        // First video packet received (12.0+)
    } else if (EvtID == PLAY_EVT_SELECT_TRACK_COMPLETE) {
        // Track switch completed
    } else if (EvtID == PLAY_EVT_VIDEO_SEI) {
        // Video SEI information
    } else if (EvtID == PLAY_EVT_HIT_CACHE) {
        // Cache hit on playback start
    }

    // Warning events
    if (EvtID == PLAY_WARNING_RECONNECT) {
        // Network disconnected, auto-reconnecting
    } else if (EvtID == PLAY_WARNING_HW_ACCELERATION_FAIL) {
        // Hardware decoding failed, automatically switched to software decoding
    }

    // Error events (negative values)
    if (EvtID < 0) {
        // Playback failed, needs handling
        // -5: Invalid License
        // -2301: Network disconnected
        // -2303: File not found
        // -2304: HEVC decoding failed
        // -2305: HLS decryption key retrieval failed
        // -2306: Failed to get VOD file info
        // -6004: System player playback error
        // -6005: Demuxer timeout
        // -6006/-6007: Video/audio decoding error
        // -6008: Subtitle decoding error
        // -6009: Video rendering error
        // -6010: Video post-processing error
        // -6011: Video download error
        // -6101: DRM playback failed
    }
}

-(void) onNetStatus:(TXVodPlayer *)player withParam:(NSDictionary*)param {
    int speed = [[param objectForKey:@"NET_SPEED"] intValue];
    int width = [[param objectForKey:@"VIDEO_WIDTH"] intValue];
    int height = [[param objectForKey:@"VIDEO_HEIGHT"] intValue];
}
```

### 8. Picture-in-Picture Callback

```objective-c
// PiP state change
-(void) onPlayer:(TXVodPlayer *)player pictureInPictureStateDidChange:(TX_VOD_PLAYER_PIP_STATE)state withParam:(NSDictionary *)param {
    // TX_VOD_PLAYER_PIP_STATE_WILL_START  = 1: About to start
    // TX_VOD_PLAYER_PIP_STATE_DID_START   = 2: Started
    // TX_VOD_PLAYER_PIP_STATE_WILL_STOP   = 3: About to stop
    // TX_VOD_PLAYER_PIP_STATE_RESTORE_UI  = 4: Restore UI
}

// PiP error
-(void) onPlayer:(TXVodPlayer *)player pictureInPictureErrorDidOccur:(TX_VOD_PLAYER_PIP_ERROR_TYPE)error withParam:(NSDictionary *)param {
    // Error types see TXVodSDKEventDef PiP error codes
}

// AirPlay state change (system player only)
-(void) onPlayer:(TXVodPlayer *)player airPlayStateDidChange:(TX_VOD_PLAYER_AIRPLAY_STATE)state withParam:(NSDictionary *)param;

// AirPlay error (system player only)
-(void) onPlayer:(TXVodPlayer *)player airPlayErrorDidOccur:(TX_VOD_PLAYER_AIRPLAY_ERROR_TYPE)error withParam:(NSDictionary *)param;
```

### 9. Subtitle Data Callback

```objective-c
-(void) onPlayer:(TXVodPlayer *)player subtitleData:(NSDictionary *)data {
    // data contains subtitle text content
}
```

### 10. External Subtitles

```objective-c
// Add external subtitle (SRT/VTT)
[_txVodPlayer addSubtitleSource:subtitleUrl
                           name:@"Subtitle Name"
                       mimeType:@"application/x-subrip"];  // SRT
// VTT format mimeType: @"text/vtt"

// Get subtitle/audio track lists
NSArray *subtitleTracks = [_txVodPlayer getSubtitleTrackInfo];
NSArray *audioTracks = [_txVodPlayer getAudioTrackInfo];

// Select/deselect track
[_txVodPlayer selectTrack:trackIndex];
[_txVodPlayer deselectTrack:trackIndex];

// Set subtitle style
TXPlayerSubtitleRenderModel *model = [[TXPlayerSubtitleRenderModel alloc] init];
model.fontColor = 0xFFFFFFFF;       // White ARGB
model.fontSize = 24.0f;
model.fontScale = 1.0f;             // VTT subtitle scale (default 1.0)
model.familyName = @"Helvetica";    // Font (iOS default "Helvetica")
model.isBondFontStyle = NO;         // Bold style
model.outlineWidth = 1.0f;          // Outline width
model.outlineColor = 0xFF000000;    // Outline color (black)
model.lineSpace = 5.0f;             // Line spacing
model.startMargin = 0.05f;          // Start margin (ratio 0~1)
model.endMargin = 0.05f;            // End margin
model.verticalMargin = 0.05f;       // Vertical margin
model.canvasWidth = 1280;           // Render canvas width (must match video aspect ratio)
model.canvasHeight = 720;           // Render canvas height
[_txVodPlayer setSubtitleStyle:model];
```

### 11. Snapshot

```objective-c
[_txVodPlayer snapshot:^(UIImage *image) {
    // Async callback, handle image on main thread
}];
```

### 12. Pre-playback (Instant Playback)

```objective-c
// Set isAutoPlay to NO for preloading
_txVodPlayer.isAutoPlay = NO;
[_txVodPlayer startVodPlay:url];

// Call resume when ready to play
[_txVodPlayer resume];
```

### 13. DRM Encrypted Playback (Premium Edition Only)

```objective-c
TXPlayerDrmBuilder *builder = [[TXPlayerDrmBuilder alloc]
    initWithDeviceCertificateUrl:certificateUrl
                     licenseUrl:licenseUrl
                       videoUrl:videoUrl];
[_txVodPlayer startPlayDrm:builder];
```

### 14. Picture-in-Picture (iOS 14+)

```objective-c
// Check PiP support
BOOL supported = [_txVodPlayer isSupportPictureInPicture];

// Check seamless PiP (Premium Edition)
BOOL seamless = [_txVodPlayer isSupportSeamlessPictureInPicture];

// Auto PiP when entering background
[_txVodPlayer setAutoPictureInPictureEnabled:YES];

// Enter/exit PiP
[_txVodPlayer enterPictureInPicture];
[_txVodPlayer exitPictureInPicture];
```

Enable "Audio, AirPlay, and Picture in Picture" background mode in Xcode.

### 15. PDT Time Seek (Premium Edition 11.6+ Only)

```objective-c
// Seek to specified PDT time position (HLS format only)
[_txVodPlayer seekToPdtTime:pdtTimeMs];  // milliseconds
```

### 16. TRTC Co-streaming

```objective-c
// Attach TRTC service
[_txVodPlayer attachTRTC:trtcCloud];

// Push video/audio stream
[_txVodPlayer publishVideo];
[_txVodPlayer publishAudio];

// Stop pushing
[_txVodPlayer unpublishVideo];
[_txVodPlayer unpublishAudio];

// Detach TRTC
[_txVodPlayer detachTRTC];
```

### 17. Get Playback Information

```objective-c
float currentTime = _txVodPlayer.currentPlaybackTime;    // Current playback progress (seconds)
float duration = _txVodPlayer.duration;                  // Total video duration (seconds)
float playable = _txVodPlayer.playableDuration;          // Buffered playable duration (seconds)
int width = _txVodPlayer.width;                          // Video width
int height = _txVodPlayer.height;                        // Video height
BOOL isPlaying = _txVodPlayer.isPlaying;                 // Whether playing
```

---

## 7. Player Component (SuperPlayerView)

An open-source player component integrating quality monitoring, video encryption, TSR (Super-Resolution), resolution switching, small window playback, and more. It provides complete functionality with an upper-level UI.

### 1. Integrate Player Component

**Method 1: CocoaPods Integration (Recommended)**

Add to Podfile:
```ruby
# Standard Edition
pod 'SuperPlayer'

# Player-only Edition
pod 'SuperPlayer/Player'

# Professional Edition
pod 'SuperPlayer/Professional'
```

Run `pod install` or `pod update`.

**Method 2: Manual Download**
1. Download SDK + Demo development package
2. Import `TXLiteAVSDK_Player.xcframework` to the project
3. Copy the `SuperPlayerKit` directory to the project
4. Add third-party libraries: AFNetworking, SDWebImage, Masonry
5. Add system libraries: MetalKit, ReplayKit, SystemConfiguration, CoreTelephony, VideoToolbox, etc.

**GitHub Repository**: https://github.com/LiteAVSDK/Player_iOS

### 2. Create Player

```objective-c
#import <SuperPlayer/SuperPlayer.h>

_playerView = [[SuperPlayerView alloc] init];
_playerView.delegate = self;
_playerView.fatherView = self.holderView;
```

### 3. Play Video

```objective-c
SuperPlayerModel *model = [[SuperPlayerModel alloc] init];

// Method A: URL playback
model.videoURL = @"http://your_video_url.mp4";
[_playerView playWithModelNeedLicence:model];

// Method B: FileId playback (recommended)
model.appId = 1400329071;
model.videoId = [[SuperPlayerVideoId alloc] init];
model.videoId.fileId = @"5285890799710173650";
model.videoId.pSign = @"your_psign";
[_playerView playWithModelNeedLicence:model];
```

### 4. Playlist Loop Playback

```objective-c
NSMutableArray *list = [NSMutableArray array];
// ... Add multiple SuperPlayerModel items
[_playerView playWithModelListNeedLicence:list isLoopPlayList:YES startIndex:0];
```

### 5. Fullscreen & Floating Window

```objective-c
// Floating window playback
[SuperPlayerWindowShared setSuperPlayer:self.playerView];
[SuperPlayerWindowShared show];
```

Event callbacks:
```objective-c
// Fullscreen state change
-(void)superPlayerFullScreenChanged:(SuperPlayerView *)player {
    // Handle fullscreen enter/exit
}

// Back action
-(void)superPlayerBackAction:(SuperPlayerView *)player {
    // Handle back
}
```

### 6. Video Cover

```objective-c
SuperPlayerModel *model = [[SuperPlayerModel alloc] init];
model.customCoverImageUrl = @"https://example.com/cover.jpg";
```

### 7. Picture-in-Picture (iOS 14+)

Enable "Audio, AirPlay, and Picture in Picture" background mode in Xcode. Premium Edition supports seamless PiP and encrypted video.

### 8. Video Preview (Trial)

```objective-c
TXVipWatchModel *vipModel = [[TXVipWatchModel alloc] init];
vipModel.canWatchTime = 60;          // Trial duration (seconds)
vipModel.tipTtitle = @"Subscribe to VIP";
```

### 9. Dynamic Watermark

```objective-c
DynamicWaterModel *waterModel = [[DynamicWaterModel alloc] init];
waterModel.dynamicWatermarkText = @"Anti-piracy watermark";
waterModel.textFont = [UIFont systemFontOfSize:28];
waterModel.textColor = [UIColor colorWithWhite:1.0 alpha:0.5];
```

### 10. Video Download

Use `VideoCacheView` to select download resolution, manage download list via `TXVodDownloadManager`. For offline playback, use the `playPath` from `TXVodDownloadMediaInfo`.

### 11. Image Sprite & Key Frame Info

- **Image Sprite**: Displays video thumbnail preview when dragging the progress bar
- **Key Frame Info**: Adds text descriptions at key positions on the progress bar, click to seek

Data is retrieved in the `VOD_PLAY_EVT_GET_PLAYINFO_SUCC` event callback.

### 12. External Subtitles (Premium Edition 11.3+)

Supports SRT and VTT formats. Add subtitle info in `SuperPlayerModel#subtitlesArray`.

### 13. Ghost Watermark

Watermarks are configured in the player signature, briefly appearing at random positions. Retrieved via `EVT_KEY_WATER_MARK_TEXT` in the `PLAY_EVT_GET_PLAYINFO_SUCC` event.

### 14. DRM Encrypted Video Playback

Supports FairPlay (iOS) encryption. Requires component version 12.8+ and Premium Edition License.

### 15. Exit & Release

```objective-c
[_playerView resetPlayer];  // Clean up internal state and release memory
```

---

## 8. Video Offline Download

### 1. Initialize Download Manager

```objective-c
TXVodDownloadManager *manager = [TXVodDownloadManager shareInstance];

// Must set delegate
manager.delegate = self; // Conform to TXVodDownloadDelegate protocol

// Set download path (lower priority than TXPlayerGlobalSetting)
[manager setDownloadPath:path];
```

### 2. Download Delegate Callbacks (TXVodDownloadDelegate)

```objective-c
// Download started
-(void)onDownloadStart:(TXVodDownloadMediaInfo *)mediaInfo {
}

// Download progress update
-(void)onDownloadProgress:(TXVodDownloadMediaInfo *)mediaInfo {
    float progress = mediaInfo.progress;
    int speed = mediaInfo.speed;  // KBytes/sec (10.9+)
}

// Download stopped
-(void)onDownloadStop:(TXVodDownloadMediaInfo *)mediaInfo {
}

// Download completed
-(void)onDownloadFinish:(TXVodDownloadMediaInfo *)mediaInfo {
    NSString *playPath = mediaInfo.playPath;  // Get local playback path
}

// Download error
-(void)onDownloadError:(TXVodDownloadMediaInfo *)mediaInfo
             errorCode:(TXDownloadError)code
               errorMsg:(NSString *)msg {
    // code: error code (see error code table)
    // msg: error description
}
```

### 3. Download Operations

```objective-c
// Download by URL
[manager startDownloadUrl:url resolution:preferredResolution userName:@"default"];
// preferredResolution: width*height (e.g. 921600=1280*720), pass -1 for single resolution

// Download by FileId
TXVodDownloadDataSource *dataSource = [[TXVodDownloadDataSource alloc] init];
dataSource.auth = authParams;          // TXPlayerAuthParams
dataSource.quality = TXVodQuality720P; // Quality
dataSource.token = nil;                // Optional: DRM Token
dataSource.templateName = nil;         // Optional: resolution template
[manager startDownload:dataSource];

// DRM download
[manager startDownloadDrm:drmBuilder resolution:preferredResolution userName:@"default"];

// Stop download
[manager stopDownload:mediaInfo];

// Delete download (cannot delete while downloading)
[manager deleteDownloadMediaInfo:mediaInfo];

// Get download list (time-consuming operation, do NOT call on main thread!)
NSArray *list = [manager getDownloadMediaInfoList];
```

### 4. Download Configuration

| Property | Description |
|----------|-------------|
| `headers` | Download HTTP request headers |
| `supportPrivateEncryptMode` | Private encryption mode (proprietary player YES, system player NO) |

### 5. Download Error Codes (TXDownloadError)

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

---

## 9. Video Preloading

Preload video without creating a player instance to speed up playback startup.

> **Prerequisite**: Must first set `TXPlayerGlobalSetting setCacheFolderPath:` and `setMaxCacheSize:`

### 1. Preload Operations

```objective-c
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

### 2. Preload Callback Protocols

**TXVodPreloadManagerDelegate (URL Preload)**:

```objective-c
// Preload completed
-(void)onComplete:(int)taskID url:(NSString *)url;

// Preload error
-(void)onError:(int)taskID url:(NSString *)url error:(NSError *)error;
```

**TXVodPreloadManagerDelegate (FileId Preload)**:

```objective-c
// Preload started (after URL resolution, before preload begins)
-(void)onStart:(int)taskID fileId:(NSString *)fileId url:(NSString *)url param:(NSDictionary *)param;
// url: Resolved video URL, can be used for subsequent playback

// Preload completed
-(void)onComplete:(int)taskID url:(NSString *)url;

// Preload error
-(void)onError:(int)taskID url:(NSString *)url error:(NSError *)error;
```

---

## 10. Image Sprite (TXImageSprite)

Image sprite utility class for parsing WebVTT files and image sets to get video thumbnails by time position.

```objective-c
// Create instance
TXImageSprite *imageSprite = [[TXImageSprite alloc] init];

// Set image sprite URLs (starts a background thread for download and parsing)
[imageSprite setVTTUrl:vttUrl imageUrls:imagesUrlArray];

// Get thumbnail at specified time position
UIImage *thumbnail = [imageSprite getThumbnail:timeInSeconds];  // Returns nil on failure
```
