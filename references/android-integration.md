# Android - Integration Guide & VOD Playback

## 1. Development Environment Requirements

- **Android Studio**: 2.0 or later
- **Android System Version**: 4.1 (SDK API 16) or later

---

## 2. SDK Integration Methods

### Method 1: Automatic Loading (AAR) - Recommended

The SDK is published to the MavenCentral repository.

**1. Configure Repository** (root `build.gradle`):
```groovy
repositories {
    mavenCentral()
}
```

**2. Add Dependency** (`app/build.gradle`):

Standard Edition:
```groovy
implementation 'com.tencent.liteav:LiteAVSDK_Player:latest.release'
```

Premium Edition:
```groovy
implementation 'com.tencent.liteav:LiteAVSDK_Player_Premium:latest.release'
```

**3. Specify CPU Architectures** (`app/build.gradle` → `defaultConfig`):
```groovy
defaultConfig {
    ndk {
        abiFilters "armeabi", "armeabi-v7a", "arm64-v8a"
    }
}
```

**4. Sync Project** (Sync Now)

### Method 2: Manual Download (AAR)

1. Download the SDK:
   - Standard Edition: https://liteav.sdk.qcloud.com/download/latest/TXLiteAVSDK_Player_Android_latest.zip
   - Premium Edition: https://liteav.sdk.qcloud.com/download/latest/TXLiteAVSDK_Player_Premium_Android_latest.zip
2. Copy the `.aar` file to the `app/libs` directory
3. Add `flatDir` in `build.gradle` to specify the local repository path:
```groovy
repositories {
    flatDir {
        dirs 'libs'
    }
}
```
4. Add the dependency:
```groovy
implementation(name: 'LiteAVSDK_Player_x.x.x.xxxxx', ext: 'aar')
```

### Method 3: JAR + SO Libraries

Copy the `jar` file and `armeabi`, `armeabi-v7a`, `arm64-v8a` folders to the `app/libs` directory, and add references in `build.gradle`:
```groovy
dependencies {
    implementation fileTree(dir: "libs", include: ["*.jar"])
}

android {
    sourceSets {
        main {
            jniLibs.srcDirs = ['libs']
        }
    }
}
```

---

## 3. Configure App Packaging Options (Optional)

Starting from version 11.7, the SDK has removed `libijkhlscache-master.so` by default. It is recommended to add:
```groovy
packagingOptions {
    exclude "lib/armeabi/libijkhlscache-master.so"
    exclude "lib/armeabi-v7a/libijkhlscache-master.so"
    exclude "lib/arm64-v8a/libijkhlscache-master.so"
}
```

---

## 4. Configure App Permissions

Add the following to `AndroidManifest.xml`:
```xml
<!-- Network permissions -->
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>

<!-- Storage permissions -->
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>

<!-- Floating window permission (used by player component) -->
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
```

### Network Security Configuration (targetSdkVersion >= 28)

You must allow cleartext HTTP traffic to the local proxy (127.0.0.1); otherwise, video playback will fail.

1. Create `network_security_config.xml` in `res/xml/`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="true">
        <domain includeSubdomains="true">127.0.0.1</domain>
    </domain-config>
</network-security-config>
```

2. Reference it in the `<application>` tag of `AndroidManifest.xml`:
```xml
<application
    android:networkSecurityConfig="@xml/network_security_config"
    ... >
</application>
```

---

## 5. Obfuscation Rules

Add to `proguard-rules.pro`:
```
-keep class com.tencent.** {*;}
```

---

## 6. License Configuration (Required)

Failure to configure a License will cause video playback to fail (black screen).

1. Go to the [License Application](https://console.cloud.tencent.com/vcube) page to obtain `licenseURL` and `key`
2. Initialize before calling any playback API (recommended in Application's onCreate):
```java
TXLiveBase.getInstance().setLicence(context, "LicenseUrl", "LicenseKey");
```

---

## 7. VOD Playback - Quick Start

### 1. Add Layout View

```xml
<com.tencent.rtmp.ui.TXCloudVideoView
    android:id="@+id/video_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_centerInParent="true"
    android:visibility="gone"/>
```

### 2. Create Player and Bindto View

```java
import com.tencent.rtmp.ui.TXCloudVideoView;
import com.tencent.rtmp.TXVodPlayer;
import com.tencent.rtmp.TXLiveConstants;

// Bindto the layout View
TXCloudVideoView mPlayerView = findViewById(R.id.video_view);

// Create Player object
TXVodPlayer mVodPlayer = new TXVodPlayer(getActivity());

// bindPlayer with View
mVodPlayer.setPlayerView(mPlayerView);
```

### 3. Start Playback

**Method A: Play via URL**
```java
String url = "http://1252463788.vod2.example.com/xxxxx/v.f20.mp4";
mVodPlayer.startVodPlay(url);
```

**Method B: Play via FileId (Recommended)**
```java
TXPlayInfoParams playInfoParam = new TXPlayInfoParams(
    1252463788,                    // AppId
    "4564972819220421305",         // FileId
    "psignxxxxxxx"                 // psign (player signature)
);
mVodPlayer.startVodPlay(playInfoParam);
```

**Method C: Play via TXPlayInfoParams (URL)**
```java
TXPlayInfoParams playInfoParam = new TXPlayInfoParams("http://your_video_url.mp4");
playInfoParam.setMediaType(TXVodConstants.MEDIA_TYPE_FILE_VOD);  // Optional: set media type
playInfoParam.setHeaders(customHeaders);  // Optional: set custom HTTP headers
mVodPlayer.startVodPlay(playInfoParam);
```

### 4. Stop Playback and Release Resources

**Must be called when the Activity/Fragment is destroyed to prevent memory leaks:**
```java
@Override
public void onDestroy() {
    super.onDestroy();
    if (mVodPlayer != null) {
        mVodPlayer.stopPlay(true); // true: clear the last frame
        mVodPlayer = null;
    }
    if (mPlayerView != null) {
        mPlayerView.onDestroy();
        mPlayerView = null;
    }
}
```

---

## 8. Common Features

### 1. Playback Controls

```java
mVodPlayer.startVodPlay(url);    // Start playback
mVodPlayer.pause();              // Pause
mVodPlayer.resume();             // Resume

// Seek
mVodPlayer.seek(600.0f);              // Normal seek (seconds)
mVodPlayer.seek(600.0f, true);        // Accurate seek (align to keyframe)

// Loop playback
mVodPlayer.setLoop(true);

// Set start time (call before startVodPlay)
mVodPlayer.setStartTime(30.0f);  // Start from the 30th second

// Auto-play control
mVodPlayer.setAutoPlay(false);   // Do not auto-play (for preloading / instant playback)
```

### 2. Video Display Settings

```java
// Render mode
mVodPlayer.setRenderMode(TXLiveConstants.RENDER_MODE_FULL_FILL_SCREEN);  // Fill screen (may crop)
mVodPlayer.setRenderMode(TXLiveConstants.RENDER_MODE_ADJUST_RESOLUTION); // Fit (may have black bars)

// Rotation
mVodPlayer.setRenderRotation(TXLiveConstants.RENDER_ROTATION_PORTRAIT);   // Portrait
mVodPlayer.setRenderRotation(TXLiveConstants.RENDER_ROTATION_LANDSCAPE);  // Landscape (90° right)

// Mirror
mVodPlayer.setMirror(true);
```

### 3. Playback Rate and Resolution

```java
// Playback rate (0.5x ~ 3.0x)
mVodPlayer.setRate(1.2f);

// Resolution switching (HLS multi-bitrate)
ArrayList<TXBitrateItem> bitrates = mVodPlayer.getSupportedBitrates();
if (bitrates != null && !bitrates.isEmpty()) {
    // Select the first bitrate
    mVodPlayer.setBitrateIndex(bitrates.get(0).index);
}
// -1 = adaptive bitrate
mVodPlayer.setBitrateIndex(-1);

// Limit the maximum bitrate for adaptive streaming
mVodPlayer.setAutoMaxBitrate(maxBitrate);
```

### 4. Playback Configuration

```java
TXVodPlayConfig config = new TXVodPlayConfig();
config.setConnectRetryCount(3);          // Reconnection retry count (default 3)
config.setTimeout(10);                   // Connection timeout in seconds (default 10)
config.setEnableAccurateSeek(true);      // Accurate seek (default true)
config.setAutoRotate(true);              // Auto-rotate for MP4 (default true)
config.setSmoothSwitchBitrate(false);    // Smooth bitrate switching (default false)
config.setProgressInterval(200);         // Progress callback interval in ms (default 500)
config.setMaxBufferSize(10);             // Max buffer size in MB
config.setMaxPreloadSize(2);             // Preload buffer size in MB
config.setPreferredResolution(720*1280); // Preferred HLS startup resolution (width × height)
config.setPlayerType(1);                 // Player type (1=built-in player, 0=system player)
config.setMediaType(TXVodConstants.MEDIA_TYPE_AUTO); // Media type
config.setHeaders(customHeaders);        // Custom HTTP headers
config.setEncryptedMp4Level(0);          // MP4 encryption level (Premium 12.2+)
config.setPreferredAudioTrack("name");   // Preferred audio track at startup (Premium 12.3+)
config.setEnableRenderProcess(false);    // Render post-processing (TSR/VR)
mVodPlayer.setConfig(config);
```

**Deprecated API Reminders**:
- `setCacheFolderPath` → Use `TXPlayerGlobalSetting.setCacheFolderPath`
- `setMaxCacheItems` → Use `TXPlayerGlobalSetting.setMaxCacheSize`
- `setFirstStartPlayBufferTime` → Use `setMaxBufferSize` or `setMaxPreloadSize`

### 5. Hardware Decoding

```java
// Switching between hardware/software decoding requires stopping and restarting playback
mVodPlayer.stopPlay(true);
mVodPlayer.enableHardwareDecode(true);  // true=hardware, false=software (hardware enabled by default)
mVodPlayer.startVodPlay(url);
```

### 6. Event Listeners

```java
mVodPlayer.setVodListener(new ITXVodPlayListener() {
    @Override
    public void onPlayEvent(TXVodPlayer player, int event, Bundle param) {
        switch (event) {
            case TXLiveConstants.PLAY_EVT_VOD_PLAY_PREPARED:
                // Player prepared, video info available
                break;
            case TXLiveConstants.PLAY_EVT_RCV_FIRST_I_FRAME:
                // First video frame rendered
                break;
            case TXLiveConstants.PLAY_EVT_PLAY_BEGIN:
                // Playback started
                break;
            case TXLiveConstants.PLAY_EVT_PLAY_PROGRESS:
                // Playback progress
                int progress = param.getInt(TXLiveConstants.EVT_PLAY_PROGRESS_MS);
                int duration = param.getInt(TXLiveConstants.EVT_PLAY_DURATION_MS);
                break;
            case TXLiveConstants.PLAY_EVT_PLAY_END:
                // Playback ended
                break;
            case TXLiveConstants.PLAY_EVT_PLAY_LOADING:
                // Buffering
                break;
            case TXLiveConstants.PLAY_EVT_VOD_LOADING_END:
                // Buffering ended
                break;
            case TXLiveConstants.PLAY_EVT_CHANGE_RESOLUTION:
                // Resolution changed
                break;
            case TXLiveConstants.PLAY_EVT_VOD_PLAY_SEEK_COMPLETE:
                // Seek completed (10.3+)
                break;
            case TXLiveConstants.PLAY_EVT_GET_PLAYINFO_SUCC:
                // VOD file info retrieved successfully
                String coverUrl = param.getString(TXLiveConstants.EVT_PLAY_COVER_URL);
                String playUrl = param.getString(TXLiveConstants.EVT_PLAY_URL);
                break;
            case TXLiveConstants.PLAY_EVT_LOOP_ONCE_COMPLETE:
                // One loop cycle completed
                break;
            case TXLiveConstants.PLAY_EVT_VOD_PLAY_FIRST_VIDEO_PACKET:
                // First video packet received (12.0+)
                break;
            case TXLiveConstants.PLAY_EVT_SELECT_TRACK_COMPLETE:
                // Track switch completed
                break;
            case TXLiveConstants.PLAY_EVT_VIDEO_SEI:
                // Video SEI information event
                break;
            case TXLiveConstants.PLAY_EVT_HIT_CACHE:
                // Cache hit on startup
                break;
        }

        // Warning events
        if (event == TXLiveConstants.PLAY_WARNING_RECONNECT) {
            // Network disconnected, auto-reconnecting
        } else if (event == TXLiveConstants.PLAY_WARNING_HW_ACCELERATION_FAIL) {
            // Hardware decoding failed, auto-fallback to software decoding
        }

        // Error events (negative values)
        if (event < 0) {
            // Playback failed, handle accordingly
            // -5: Invalid License
            // -2301: Network disconnected
            // -2303: File not found
            // -2304: HEVC decoding failed
            // -2305: HLS decryption key retrieval failed
            // -2306: VOD file info retrieval failed
            // -6101: DRM playback failed
        }
    }

    @Override
    public void onNetStatus(TXVodPlayer player, Bundle bundle) {
        int speed = bundle.getInt(TXLiveConstants.NET_STATUS_NET_SPEED);       // Network speed
        int width = bundle.getInt(TXLiveConstants.NET_STATUS_VIDEO_WIDTH);     // Video width
        int height = bundle.getInt(TXLiveConstants.NET_STATUS_VIDEO_HEIGHT);   // Video height
        int fps = bundle.getInt(TXLiveConstants.NET_STATUS_VIDEO_FPS);         // Video frame rate
        int bitrate = bundle.getInt(TXLiveConstants.NET_STATUS_VIDEO_BITRATE); // Video bitrate
        float cpu = bundle.getFloat(TXLiveConstants.NET_STATUS_CPU_USAGE);     // CPU usage
    }
});
```

### 7. Global Cache Settings

```java
// Set in Application (only needs to be set once globally)
TXPlayerGlobalSetting.setCacheFolderPath(cachePath);  // Cache directory (null = disabled)
TXPlayerGlobalSetting.setMaxCacheSize(200);            // Max cache size in MB
```

### 8. Audio Controls

```java
// Mute
mVodPlayer.setMute(true);

// Volume (0-100)
mVodPlayer.setAudioPlayoutVolume(80);

// Audio normalization (LUFS value)
mVodPlayer.setAudioNormalization(-16.0f);
```

### 9. External Subtitles

```java
// Add external subtitles (SRT/VTT)
mVodPlayer.addSubtitleSource(subtitleUrl, "Subtitle Name", "application/x-subrip");  // SRT
mVodPlayer.addSubtitleSource(subtitleUrl, "Subtitle Name", "text/vtt");              // VTT

// Get subtitle/audio track list
List<TXTrackInfo> subtitleTracks = mVodPlayer.getSubtitleTrackInfo();
List<TXTrackInfo> audioTracks = mVodPlayer.getAudioTrackInfo();

// Select/deselect track
mVodPlayer.selectTrack(trackIndex);
mVodPlayer.deselectTrack(trackIndex);

// Set subtitle style
TXSubtitleRenderModel model = new TXSubtitleRenderModel();
model.fontColor = 0xFFFFFFFF;       // White (ARGB)
model.fontSize = 24.0f;
model.fontScale = 1.0f;             // VTT subtitle scale (default 1.0)
model.familyName = "Roboto";        // Font (Android default: "Roboto")
model.isBondFontStyle = false;      // Bold style
model.outlineWidth = 1.0f;          // Outline width
model.outlineColor = 0xFF000000;    // Outline color (black)
model.lineSpace = 5.0f;             // Line spacing
model.startMargin = 0.05f;          // Start margin (ratio 0~1)
model.endMargin = 0.05f;            // End margin
model.verticalMargin = 0.05f;       // Vertical margin
model.canvasWidth = 1280;           // Render canvas width (must match video aspect ratio)
model.canvasHeight = 720;           // Render canvas height
mVodPlayer.setSubtitleStyle(model);

// Subtitle data callback
mVodPlayer.setVodSubtitleDataListener(new ITXVodSubtitleDataListener() {
    @Override
    public void onSubtitleData(TXVodDef.TXVodSubtitleData subtitleData) {
        // subtitleData.subtitleData: subtitle content (empty = no subtitle)
        // subtitleData.trackIndex: current subtitle track index
        // subtitleData.durationMs: subtitle duration (currently empty, do not use)
        // subtitleData.startPositionMs: subtitle start time (currently empty, do not use)
    }
});
```

### 10. Snapshot

```java
mVodPlayer.snapshot(new ITXSnapshotListener() {
    @Override
    public void onSnapshot(Bitmap bitmap) {
        // Async callback, handle bitmap on the main thread
    }
});
```

### 11. DRM Encrypted Playback (Premium Edition Only)

```java
TXPlayerDrmBuilder builder = new TXPlayerDrmBuilder(licenseUrl, playUrl);
builder.setDeviceCertificateUrl(provisionUrl);  // Certificate provider URL (optional for Widevine)
builder.setKeyLicenseUrl(keyLicenseUrl);        // Decryption key URL
builder.setPlayUrl(playUrl);                    // Playback media URL
mVodPlayer.startPlayDrm(builder);
```

### 12. PDT Time Seek (Premium Edition 11.6+ Only)

```java
// Seek to a specific PDT time point (HLS only)
mVodPlayer.seekToPdtTime(pdtTimeMs);  // Milliseconds
```

### 13. Pre-Playback (Instant Start)

```java
// Set auto-play to false for preloading
mVodPlayer.setAutoPlay(false);
mVodPlayer.startVodPlay(url);

// Call resume when ready to play
mVodPlayer.resume();
```

### 14. TRTC Video Streaming

```java
// Bindto TRTC service
mVodPlayer.attachTRTC(trtcCloud);

// Publish video/audio streams
mVodPlayer.publishVideo();
mVodPlayer.publishAudio();

// Stop publishing
mVodPlayer.unpublishVideo();
mVodPlayer.unpublishAudio();

// Unbind TRTC
mVodPlayer.detachTRTC();
```

### 15. Get Playback Information

```java
float currentTime = mVodPlayer.getCurrentPlaybackTime();    // Current playback position (seconds)
float duration = mVodPlayer.getDuration();                  // Total video duration (seconds)
float bufferDuration = mVodPlayer.getBufferDuration();      // Current buffer duration (seconds)
float playableDuration = mVodPlayer.getPlayableDuration();  // Playable duration (seconds)
int width = mVodPlayer.getWidth();                          // Video resolution width
int height = mVodPlayer.getHeight();                        // Video resolution height
boolean isPlaying = mVodPlayer.isPlaying();                 // Whether currently playing
boolean isLoop = mVodPlayer.isLoop();                       // Whether loop playback is enabled
```

---

## 9. Player Component (SuperPlayerView)

The Player Component is an open-source component that integrates quality monitoring, video encryption, TSR, resolution switching, floating window playback, and more. It provides a complete feature set with a built-in UI.

### 1. Integrate the Player Component

**Method 1: Gradle Integration (Recommended)**

Copy the `Demo/superplayerkit` module into your project and configure in `build.gradle`:
```groovy
implementation 'com.tencent.liteav:LiteAVSDK_Player:latest.release'
implementation project(':superplayerkit')
```

**GitHub Repository**: https://github.com/LiteAVSDK/Player_Android

### 2. Create Player

```xml
<com.tencent.liteav.demo.superplayer.SuperPlayerView
    android:id="@+id/superVodPlayerView"
    android:layout_width="match_parent"
    android:layout_height="200dp" />
```

### 3. Play Video

```java
SuperPlayerModel model = new SuperPlayerModel();
model.appId = 1400329073;

// Method A: URL playback
model.url = "http://your_video_url.mp4";
mSuperPlayerView.playWithModelNeedLicence(model);

// Method B: FileId playback (Recommended)
model.videoId = new SuperPlayerVideoId();
model.videoId.fileId = "5285890799710173650";
model.videoId.pSign = "your_psign";
mSuperPlayerView.playWithModelNeedLicence(model);
```

### 4. Playlist Loop

```java
// Build video list
List<SuperPlayerModel> list = new ArrayList<>();
// ... add multiple SuperPlayerModel items
mSuperPlayerView.playWithModelListNeedLicence(list, true, 0);
// Parameters: list, loop, start index
```

### 5. Fullscreen and Floating Window

```java
// Switch to fullscreen mode
mControllerCallback.onSwitchPlayMode(SuperPlayerDef.PlayerMode.FULLSCREEN);

// Switch to floating window mode
mSuperPlayerView.switchPlayMode(SuperPlayerDef.PlayerMode.FLOAT);
```

### 6. Video Cover

```java
SuperPlayerModel model = new SuperPlayerModel();
model.coverPictureUrl = "https://example.com/cover.jpg";
```

### 7. Picture-in-Picture (Android 8.0+)

Configure in `AndroidManifest.xml`:
```xml
<activity
    android:supportsPictureInPicture="true"
    ... />
```

Use `PictureInPictureHelper` to enter/exit PiP mode.

### 8. Video Preview (Trial)

```java
VipWatchModel vipWatchModel = new VipWatchModel();
vipWatchModel.canWatchTime = 60;  // Trial duration (seconds)
vipWatchModel.tipTtitle = "Subscribe to VIP";
```

### 9. Dynamic Watermark

```java
DynamicWaterConfig waterConfig = new DynamicWaterConfig();
waterConfig.dynamicWatermarkText = "Anti-piracy watermark";
waterConfig.textFont = 28;
waterConfig.textColor = Color.parseColor("#80FFFFFF");
```

### 10. Video Download

Use `DownloadMenuListView` to select download quality, and manage the download list via `VideoDownloadListView`.

### 11. Image Sprite and Keyframe Thumbnails

- **Image Sprite**: Displays video thumbnails when dragging the progress bar
- **Keyframe Thumbnails**: Adds text descriptions at key positions on the progress bar; tap to seek

Data is retrieved from the `VOD_PLAY_EVT_GET_PLAYINFO_SUCC` event callback.

### 12. External Subtitles (Premium 11.3+)

Supports SRT and VTT formats. Add subtitles via `SubtitleSourceModel` and configure styles using `TXSubtitleRenderModel`.

### 13. DRM Encrypted Video Playback

Supports WideVine encryption. The render view type must be set to `PlayerRenderType.SURFACE_VIEW`.

### 14. Exit and Release

```java
mSuperPlayerView.resetPlayer();  // Clear internal state and release memory
```

---

## 10. Offline Download

### 1. Initialize Download Manager

```java
TXVodDownloadManager manager = TXVodDownloadManager.getInstance();

// Must set listener
manager.setListener(new ITXVodDownloadListener() {
    @Override
    public void onDownloadStart(TXVodDownloadMediaInfo mediaInfo) {
        // Download started
    }

    @Override
    public void onDownloadProgress(TXVodDownloadMediaInfo mediaInfo) {
        // Download progress update
        float progress = mediaInfo.getProgress();
        int speed = mediaInfo.getSpeed();  // KByte/s (10.9+)
    }

    @Override
    public void onDownloadStop(TXVodDownloadMediaInfo mediaInfo) {
        // Download stopped
    }

    @Override
    public void onDownloadFinish(TXVodDownloadMediaInfo mediaInfo) {
        // Download finished
        String playPath = mediaInfo.getPlayPath();  // Get local playback path
    }

    @Override
    public void onDownloadError(TXVodDownloadMediaInfo mediaInfo, int error, String reason) {
        // Download error
        // error: error code (see error code table below)
        // reason: error description
    }

    @Override
    public int hlsKeyVerify(TXVodDownloadMediaInfo mediaInfo, String url, byte[] receive) {
        // HLS decryption key verification (deprecated, use empty implementation)
        return 0;
    }
});
```

### 2. Download Operations

```java
// Download via URL
TXVodDownloadMediaInfo info = manager.startDownloadUrl(url, preferredResolution, userName);
// preferredResolution: width*height (e.g., 921600=1280*720), pass -1 for single resolution
// userName: user identifier, default "default"

// Download via FileId
TXVodDownloadDataSource dataSource = new TXVodDownloadDataSource(
    appId,      // VOD application appId
    fileId,     // VOD video fileId
    quality,    // Quality ID (e.g., TXVodDownloadDataSource.QUALITY_720P)
    pSign,      // Player signature
    userName    // Account name
);
dataSource.setToken(token);  // Optional: HLS encryption token
manager.startDownload(dataSource);

// DRM download
manager.startDownloadDrm(drmBuilder, preferredResolution, userName);

// Stop download
manager.stopDownload(downloadMediaInfo);

// Delete download
manager.deleteDownloadMediaInfo(downloadMediaInfo);

// Get download list (time-consuming, do NOT call on the main thread!)
List<TXVodDownloadMediaInfo> list = manager.getDownloadMediaInfoList();
```

### 3. Download Quality Constants (TXVodDownloadDataSource)

| Constant | Value | Description |
|----------|-------|-------------|
| `QUALITY_OD` | 0 | Original quality |
| `QUALITY_240P` | 240 | Smooth 240P |
| `QUALITY_360P` | 360 | Smooth 360P |
| `QUALITY_480P` | 480 | SD 480P |
| `QUALITY_540P` | 540 | SD 540P |
| `QUALITY_720P` | 720 | HD 720P |
| `QUALITY_1080P` | 1080 | FHD 1080P |

### 4. Download Media Info (TXVodDownloadMediaInfo)

| Method | Return Type | Description |
|--------|-------------|-------------|
| `getDataSource()` | TXVodDownloadDataSource | Download source info (for FileId downloads) |
| `getDuration()` | int (ms) | Total video duration |
| `getPlayableDuration()` | int (ms) | Downloaded playable duration |
| `getSize()` | long (Byte) | Total file size (FileId only) |
| `getDownloadSize()` | long (Byte) | Downloaded size (FileId only) |
| `getProgress()` | float | Download progress |
| `getDownloadState()` | int | Download state (0=init, 1=started, 2=stopped, 3=error, 4=finished) |
| `isDownloadFinished()` | boolean | Whether download is complete |
| `getPlayPath()` | String | Local playback path (can be passed to TXVodPlayer) |
| `getUrl()` | String | Actual download URL |
| `getSpeed()` | int (KByte/s) | Download speed (10.9+) |
| `getTaskId()` | int | Task ID |
| `isResourceBroken()` | boolean | Whether the resource is corrupted (11.0+) |
| `getUserName()` | String | Download account name |
| `getPreferredResolution()` | long | Preferred download resolution |

### 5. Download Error Codes

| Value | Constant | Description |
|-------|----------|-------------|
| 0 | DOWNLOAD_SUCCESS | Download successful |
| -5001 | DOWNLOAD_AUTH_FAILED | FileId authentication failed |
| -5003 | DOWNLOAD_NO_FILE | File not found |
| -5004 | DOWNLOAD_FORMAT_ERROR | Format not supported |
| -5005 | DOWNLOAD_DISCONNECT | Network error |
| -5006 | DOWNLOAD_HLS_KEY_ERROR | HLS decryption key failed |
| -5007 | DOWNLOAD_PATH_ERROR | Download directory access failed |
| -5008 | DOWNLOAD_403FORBIDDEN | Signature expired or invalid request |

---

## 11. Video Preloading

Pre-download video content without creating a player instance to speed up playback startup.

> **Prerequisite**: You must first set `TXPlayerGlobalSetting.setCacheFolderPath` and `setMaxCacheSize`

### 1. Preload Operations

```java
TXVodPreloadManager preloadManager = TXVodPreloadManager.getInstance(context);

// Preload via URL
int taskId = preloadManager.startPreload(url, preloadSizeMB, preferredResolution, listener);
// preloadSizeMB: preload size in MB
// preferredResolution: width*height, pass -1 if not specified
// Returns taskId, -1 indicates invalid

// Preload via FileId (Recommended, must NOT be called on the main thread!)
int taskId = preloadManager.startPreload(playInfoParams, preloadSizeMB, preferredResolution, listener);

// Stop preload
preloadManager.stopPreload(taskId);
```

### 2. URL Preload Callback (ITXVodPreloadListener)

```java
new ITXVodPreloadListener() {
    @Override
    public void onComplete(int taskID, String url) {
        // Preload completed
    }

    @Override
    public void onError(int taskID, String url, int code, String msg) {
        // Preload failed
    }
}
```

### 3. FileId Preload Callback (ITXVodFilePreloadListener)

```java
new ITXVodFilePreloadListener() {
    @Override
    public void onStart(int taskID, String fileId, String url, Bundle bundle) {
        // Preload started (after URL resolution, before download begins)
        // url: resolved video URL, can be used for subsequent playback
    }

    @Override
    public void onComplete(int taskID, String url) {
        // Preload completed
    }

    @Override
    public void onError(int taskID, String url, int code, String msg) {
        // Preload failed
    }
}
```

---

## 12. Image Sprite (TXImageSprite)

Image Sprite utility class for parsing WebVTT files and image sets to retrieve video thumbnails at specific time points.

```java
// Create instance
TXImageSprite imageSprite = new TXImageSprite(context);

// Set image sprite URL (starts a background thread to download and parse)
imageSprite.setVTTUrlAndImageUrls(vttUrl, imagesUrlList);

// Get thumbnail at a specific time point
Bitmap thumbnail = imageSprite.getThumbnail(timeInSeconds);  // Returns null on failure

// Release resources (must be called to prevent memory leaks!)
imageSprite.release();
```
