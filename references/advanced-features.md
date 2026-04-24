# Advanced Features - Integration Guide

This document describes the advanced feature components of the Tencent Cloud Player SDK, including the Short Video Component, Picture-in-Picture (PiP), VR Playback, and TSR (Super-Resolution).

> **Note**: All features below require a **Player SDK Premium Edition License**; otherwise, playback will fail or display a black screen.

---

## 1. Short Video Component (TUIPlayerShortVideo)

The Short Video Component provides a TikTok-like swipe-to-play experience, featuring instant first-frame rendering and smooth scrolling.

### 1.1 Features

| Feature | Description |
|---------|-------------|
| **Instant First Frame** | Achieves 10–30ms startup time via pre-playback, preloading, and player reuse |
| **Smooth Scrolling** | Optimized list scrolling with low memory and CPU usage |
| **Quick Integration** | Encapsulates complex playback logic, provides default UI, supports FileId/URL playback |
| **Layer Customization** | Supports custom UI layers (likes, comments, progress bar, etc.) |

### 1.2 Environment Requirements

| Platform | Minimum Version |
|----------|-----------------|
| Android | SDK ≥ 19 |
| iOS | iOS ≥ 9.0, Xcode ≥ 14.0 |
| Flutter | SDK ≥ 3.3.0, Android SDK ≥ 21, iOS ≥ 12 |

### 1.3 SDK Download

| Platform | Download Link |
|----------|---------------|
| Android | [TUIPlayerKit_Android_latest.zip](https://mediacloud-76607.gzc.vod.tencent-cloud.com/TUIPlayerKit/download/latest/TUIPlayerKit_Android_latest.zip) |
| iOS | Refer to official documentation for TUIPlayerCore.xcframework and TUIPlayerShortVideo.xcframework |
| Flutter | Online dependency or local dependency on FTUIPlayerKit |

---

## 2. Android Short Video Component

### 2.1 Integration Steps

**Step 1: Add Dependencies**

```groovy
// build.gradle
// Player SDK
api 'com.tencent.liteav:LiteAVSDK_Player:latest.release'

// Short Video Component (place downloaded aar files in libs)
implementation (name:'tuiplayercore-release_x.x.x', ext:'aar')
implementation (name:'tuiplayershortvideo-release_x.x.x', ext:'aar')

// Dependencies
implementation 'androidx.appcompat:appcompat:1.0.0'
implementation 'androidx.viewpager2:viewpager2:1.1.0-beta02'
```

**Step 2: Configure Permissions and Obfuscation**

```xml
<!-- AndroidManifest.xml -->
<uses-permission android:name="android.permission.INTERNET"/>
```

```proguard
# proguard-rules.pro
-keep class com.tencent.** { *; }
```

**Step 3: Initialize License**

```java
TUIPlayerConfig config = new TUIPlayerConfig.Builder()
    .enableLog(true)
    .licenseKey("Your license key")
    .licenseUrl("Your license url")
    .build();
TUIPlayerCore.init(context, config);
```

### 2.2 Usage Example

**Layout File:**

```xml
<com.tencent.qcloud.tuiplayer.shortvideo.ui.view.TUIShortVideoView
    android:id="@+id/my_video_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent"/>
```

**Initialization and Playback:**

```java
// 1. Get the view
TUIShortVideoView mSuperShortVideoView = findViewById(R.id.my_video_view);

// 2. Bind lifecycle (optional, for automatic pause/resume)
mSuperShortVideoView.setActivityLifecycle(getLifecycle());

// 3. Set listener
mSuperShortVideoView.setListener(new TUIShortVideoListener() {
    @Override
    public void onPageChanged(int index, TUIVideoSource videoSource) {
        // Load next page when scrolled to the bottom
        if (index >= mSuperShortVideoView.getCurrentDataCount() - 1) {
            // mSuperShortVideoView.appendModels(nextPageData);
        }
    }

    @Override
    public void onCreateVodLayer(TUIVodLayerManager layerManager, int viewType) {
        // Add custom layers
        layerManager.addLayer(new TUICoverLayer());
        layerManager.addLayer(new TUIVideoInfoLayer());
    }
});

// 4. Populate data
List<TUIPlaySource> shortVideoData = new ArrayList<>();

// VOD data
TUIVideoSource videoSource = new TUIVideoSource();
videoSource.setVideoURL("https://example.com/video.mp4");
videoSource.setCoverPictureUrl("https://example.com/cover.jpg");
shortVideoData.add(videoSource);

// Live data
TUILiveSource liveSource = new TUILiveSource();
liveSource.setUrl("https://example.com/live.flv");
liveSource.setCoverPictureUrl("https://example.com/live_cover.jpg");
shortVideoData.add(liveSource);

// Set data
mSuperShortVideoView.setModels(shortVideoData);
```

### 2.3 Custom Layers

```java
public class TUIVideoInfoLayer extends TUIVodLayer {
    private SeekBar mSeekBar;

    @Override
    public View createView(ViewGroup parent) {
        LayoutInflater inflater = LayoutInflater.from(parent.getContext());
        View view = inflater.inflate(R.layout.player_video_info_layer, parent, false);
        mSeekBar = view.findViewById(R.id.vsb_tui_video_progress);
        return view;
    }

    @Override
    public String tag() {
        return "TUIVideoInfoLayer";
    }

    @Override
    public void onBindData(TUIVideoSource videoSource) {
        show(); // Show layer when data is bound
    }

    @Override
    public void onPlayProgress(long current, long duration, long playable) {
        if (mSeekBar != null) {
            int progress = (int) ((current * 100) / duration);
            mSeekBar.setProgress(progress);
        }
    }
}
```

### 2.4 Common APIs

| Method | Description |
|--------|-------------|
| `startPlayIndex(int index)` | Start playback from a specified position |
| `pause()` | Pause playback |
| `resume()` | Resume playback |
| `setPlayMode(mode)` | Set playback mode (list loop / single loop) |
| `appendModels(list)` | Append data (pagination) |
| `switchResolution(resolution, type)` | Switch resolution |

---

## 3. iOS Short Video Component

### 3.1 Integration Steps

**Step 1: Import Frameworks**

Add the following frameworks to the Xcode project:
- `TUIPlayerCore.xcframework`
- `TUIPlayerShortVideo.xcframework`

Set **Do Not Embed** in the "Embed" settings.

**Step 2: Integrate Dependencies**

```ruby
# Podfile
pod 'TXLiteAVSDK_Player'  # Player SDK
pod 'SDWebImage'          # Image loading
pod 'Masonry'             # Layout library
```

**Step 3: Configure License**

```objective-c
// AppDelegate.m
NSString * const licenceURL = @"<Your licenseUrl>";
NSString * const licenceKey = @"<Your key>";
[TXLiveBase setLicenceURL:licenceURL key:licenceKey];
```

### 3.2 Usage Example

```objective-c
// 1. Initialize TUIShortVideoView
- (TUIShortVideoView *)videoView {
    if (!_videoView) {
        // Set UI manager
        TUIPlayerShortVideoUIManager *uiManager = [[TUIPlayerShortVideoUIManager alloc] init];
        [uiManager setControlViewClass:TUIPlayerShortVideoControlView.class];
        [uiManager setControlViewClass:TUIPSControlLiveView.class viewType:TUI_ITEM_VIEW_TYPE_LIVE];
        
        _videoView = [[TUIShortVideoView alloc] initWithUIManager:uiManager];
        _videoView.delegate = self;
        
        // 2. Configure VOD strategy
        TUIPlayerVodStrategyModel *model = [[TUIPlayerVodStrategyModel alloc] init];
        model.mPreloadConcurrentCount = 1;  // Preload count
        model.preDownloadSize = 1;          // Pre-download size
        model.enableAutoBitrate = NO;
        model.isLastPrePlay = YES;
        [_videoView setShortVideoStrategyModel:model];
    }
    return _videoView;
}

// 3. Add view
self.videoView.frame = self.view.bounds;
[self.view addSubview:self.videoView];

// 4. Set data source
TUIPlayerVideoModel *model1 = [[TUIPlayerVideoModel alloc] init];
model1.videoUrl = @"http://xxx.mp4";
model1.coverPictureUrl = @"http://xxx.jpg";

TUIPlayerLiveModel *model2 = [[TUIPlayerLiveModel alloc] init];
model2.liveUrl = @"http://xxx.flv";

NSArray *videos = @[model1, model2];
[self.videoView setShortVideoModels:videos];
```

### 3.3 Pagination

```objective-c
// Implement delegate method
- (void)onReachLast {
    // Build next page data
    TUIPlayerVideoModel *model = [[TUIPlayerVideoModel alloc] init];
    // ... set properties
    
    NSArray *nextPage = @[model];
    [self.videoView appendShortVideoModels:nextPage];
}
```

---

## 4. Flutter Short Video Component

### 4.1 Integration Steps

**Method 1: Online Dependency**

```yaml
# pubspec.yaml
ftuiplayer_kit:
  git:
    url: https://github.com/LiteAVSDK/TUIPlayerKit_Flutter
```

**Method 2: Local Dependency**

```yaml
# pubspec.yaml
ftuiplayer_kit:
  path: ./FTUIPlayerKit
```

**Configure License:**

```dart
FTUIPlayerConfig config = FTUIPlayerConfig(
    licenseUrl: LICENSE_URL,
    licenseKey: LICENSE_KEY
);
FTUIPlayerKitPlugin.setTUIPlayerConfig(config);
```

**Android Obfuscation Rules:**

```proguard
-keep class com.tencent.**{*;}
```

### 4.2 Usage Example

```dart
// 1. Create controller
FTUIPlayerShortController _shortPlayerController = FTUIPlayerShortController();

// 2. Load data
_shortPlayerController.setModels(sources);

// 3. Set VOD strategy
_shortPlayerController.setVodStrategy(FTUIPlayerVodStrategy());

// 4. PageView integration
itemBuilder: (context, index) {
    FTUIPlayerView playerView = FTUIPlayerView();
    ShortVodItemControlView itemControlView = ShortVodItemControlView(playerView, sources[index]);
    
    // Pre-create player
    _shortPlayerController.preCreateVodPlayer(itemControlView.playerView, index);
    
    return itemControlView;
}

// 5. Bind player on page change
void onPageChanged(int index) async {
    ShortVodItemControlView itemControlView = getFTUIPlayerView(index);
    
    // Bind player
    _currentVodController = await _shortPlayerController.bindVodPlayer(
        itemControlView.playerView, index);
    
    // Start playback
    await _shortPlayerController.startCurrent();
}
```

### 4.3 Player Controls

```dart
// Play
_currentVodController.startPlay(source);

// Pause
_currentVodController.pause();

// Resume
_currentVodController.resume();

// Playback rate
_currentVodController.setRate(1.5);

// Mute
_currentVodController.setMute(true);

// Seek (seconds)
_currentVodController.seekTo(10.0);

// Get duration
double duration = await _currentVodController.getDuration();

// Get current playback time
double currentTime = await _currentVodController.getCurrentPlayTime();
```

### 4.4 Event Listeners

```dart
playerController?.addListener(FTUIVodControlListener(
    onVodPlayerEvent: (event) {
        int eventCode = event[TXVodPlayEvent.EVT_EVENT];
        // Handle playback events
    },
    onVodControllerBind: () {
        // Triggered when scrolled to the current video
    },
    onVodControllerUnBind: () {
        // Triggered when the current video is scrolled away
    }
));
```

---

## 5. Picture-in-Picture Component (TUIPIP) - iOS

### 5.1 Features

| Feature | Description |
|---------|-------------|
| **Encrypted Video PiP** | Supports PiP playback for encrypted videos |
| **Offline Playback PiP** | Supports PiP playback for local videos |
| **Instant Switch** | PiP starts immediately when entering background, no waiting required |

### 5.2 Environment Requirements

| Requirement | Version |
|-------------|---------|
| iOS Version | ≥ 14.0 (iPhone), ≥ 9.0 (iPad) |
| Device | iPhone 8 and above |
| SDK Version | ≥ 11.4 |

### 5.3 Integration Steps

**Step 1: Import Resources**

Download and import `TXVodPlayer.bundle`:
[TXVodPlayer.bundle.zip](https://mediacloud-76607.gzc.vod.tencent-cloud.com/TUIPlayerKit/pip/TXVodPlayer.bundle.zip)

> **Note**: Do not rename the bundle or its internal resources.

**Step 2: Configure Permissions**

In Xcode: Target > Signing & Capabilities > Background Modes, check **"Audio, AirPlay, and Picture in Picture"**.

**Step 3: Configure License**

```objective-c
// AppDelegate.m
NSString *const licenceURL = @"<Your licenseUrl>";
NSString *const licenceKey = @"<Your key>";
[TXLiveBase setLicenceURL:licenceURL key:licenceKey];
```

### 5.4 Usage Example

```objective-c
// 1. Enable automatic PiP
[TXVodPlayer setPictureInPictureSeamlessEnabled:YES];

// 2. Manually enter PiP
if ([TXVodPlayer isSupportPictureInPicture]) {
    [_vodPlayer enterPictureInPicture];
}

// 3. Handle entering background
if ([self.vodplayer isSupportSeamlessPictureInPicture]) {
    // Supports seamless switch, no action needed, PiP activates automatically
} else {
    // Does not support seamless switch, pause playback
    [self.vodplayer pause];
}

// 4. Exit PiP
[_vodPlayer exitPictureInPicture];
```

### 5.5 State Callbacks

```objective-c
// Implement TXVodPlayListener delegate

// PiP state callback
- (void)onPlayer:(TXVodPlayer *)player 
    pictureInPictureStateDidChange:(TX_VOD_PLAYER_PIP_STATE)pipState 
    withParam:(NSDictionary *)param;

// PiP error callback
- (void)onPlayer:(TXVodPlayer *)player 
    pictureInPictureErrorDidOccur:(TX_VOD_PLAYER_PIP_ERROR_TYPE)errorType 
    withParam:(NSDictionary *)param;
```

---

## 6. VR Playback Plugin

### 6.1 Features

| Feature | Description |
|---------|-------------|
| **Panoramic Viewing** | Supports 360-degree panoramic video viewing |
| **Interaction Methods** | Supports gyroscope or gesture-based view angle control |
| **Monocular Mode** | For viewing with the naked eye |
| **Binocular Mode** | For VR headset devices |

### 6.2 Environment Requirements

| Requirement | Version |
|-------------|---------|
| SDK Version | ≥ 11.3 (or Premium Edition) |
| License | Player SDK Premium Edition License |

### 6.3 Integration Steps

**Step 1: Download Plugin**

Download the VR plugin package for the corresponding platform (Android: `.aar`, iOS: `.framework`).

**Step 2: Configure Obfuscation Rules (Android)**

```proguard
-keep class com.tencent.** { *; }
```

**Step 3: Initialize License**

**Android:**
```java
String licenceUrl = "Enter your purchased License URL";
String licenseKey = "Enter your purchased License Key";
TXLiveBase.getInstance().setLicence(context, licenceUrl, licenseKey);
```

**iOS:**
```objective-c
NSString *licenceURL = @"Enter your purchased License URL";
NSString *licenseKey = @"Enter your purchased License Key";
[TXLiveBase setLicenceURL:licenceURL key:licenseKey];
```

### 6.4 Usage Example

#### Android

```java
// Enable VR panoramic monocular mode (set value to 12 for binocular mode)
mVodPlayer.setStringOption("PARAM_MODULE_TYPE", 11);

// Disable VR panoramic video
mVodPlayer.setStringOption("PARAM_MODULE_TYPE", 0);
```

**Advanced Configuration:**

```java
Map<String, Object> config = new HashMap<>();
// Disable sensor (gyroscope)
config.put(TXVodConstants.PLAYER_OPTION_PARAM_MODULE_VR_ENABLE_SENSOR, false);
// Set field of view (range 30.0f–110.0f)
config.put(TXVodConstants.PLAYER_OPTION_PARAM_MODULE_VR_FOV, 90.0f);
// Set gesture swipe sensitivity
config.put(TXVodConstants.PLAYER_OPTION_PARAM_MODULE_VR_ANGLE_RATE, 0.2f);

// Apply configuration
mVodPlayer.setStringOption(TXVodConstants.PLAYER_OPTION_PARAM_MODULE_CONFIG, config);
// Set to panoramic monocular
mVodPlayer.setStringOption(TXVodConstants.PLAYER_OPTION_PARAM_MODULE_TYPE, 
    TXVodConstants.PLAYER_OPTION_PARAM_MODULE_TYPE_VR_PANORAMA);
```

**Rotate View Angle:**

```java
Map<String, Object> action = new HashMap<>();
// Horizontal rotation (positive = right, negative = left)
action.put(TXVodConstants.PLAYER_OPTION_PARAM_MODULE_VR_ANGLE_X, 10.0f);
// Vertical rotation (positive = up, negative = down)
action.put(TXVodConstants.PLAYER_OPTION_PARAM_MODULE_VR_ANGLE_Y, 10.0f);
mVodPlayer.setStringOption(TXVodConstants.PLAYER_OPTION_PARAM_MODULE_VR_DO_ROTATE, action);
```

#### iOS

```objective-c
// Enable VR panoramic monocular mode (set value to 12 for binocular mode)
NSMutableDictionary *extInfoMap = [NSMutableDictionary dictionary];
[extInfoMap setObject:@"11" forKey:@"PARAM_MODULE_TYPE"];
[_txVodPlayer setExtentOptionInfo:extInfoMap];

// Disable VR panoramic video
[extInfoMap setObject:@"0" forKey:@"PARAM_MODULE_TYPE"];
[_txVodPlayer setExtentOptionInfo:extInfoMap];
```

**Advanced Configuration:**

```objective-c
NSMutableDictionary *extInfoMap = [NSMutableDictionary dictionary];
[extInfoMap setObject:@(PLAYER_OPTION_PARAM_MODULE_TYPE_VR_PANORAMA) 
               forKey:PLAYER_OPTION_PARAM_MODULE_TYPE];

NSMutableDictionary *vrconfig = [NSMutableDictionary dictionary];
// Enable sensor
[vrconfig setObject:@(YES) forKey:PLAYER_OPTION_PARAM_MODULE_VR_ENABLE_SENSOR];
// Set field of view
[vrconfig setObject:@(110.0f) forKey:PLAYER_OPTION_PARAM_MODULE_VR_FOV];
// Set rotation angle slope threshold
[vrconfig setObject:@(0.3f) forKey:PLAYER_OPTION_PARAM_MODULE_VR_ANGLE_SLOPE_THRESHOLD];

[extInfoMap setObject:vrconfig forKey:PLAYER_OPTION_PARAM_MODULE_CONFIG];
[self.vodPlayer setExtentOptionInfo:extInfoMap];
```

### 6.5 Event Listeners

**Android:**
```java
@Override
public void onPlayEvent(TXVodPlayer player, int event, Bundle param) {
    if (event == TXLiveConstants.PLAYER_OPTION_EVT_ANGLES) { // 8001
        if (param != null) {
            float angleX = param.getFloat(TXVodConstants.PLAYER_OPTION_EVT_KEY_ANGLE_X);
            float angleY = param.getFloat(TXVodConstants.PLAYER_OPTION_EVT_KEY_ANGLE_Y);
            // Handle angle data
        }
    }
}
```

**iOS:**
```objective-c
- (void)onPlayEvent:(TXVodPlayer *)player event:(int)EvtID withParam:(NSDictionary*)param {
    if (EvtID == PLAYER_OPTION_EVT_ANGLES) {
        float angleX = [[param objectForKey:PLAYER_OPTION_EVT_KEY_ANGLE_X] floatValue];
        float angleY = [[param objectForKey:PLAYER_OPTION_EVT_KEY_ANGLE_Y] floatValue];
        // Handle angle data
    }
}
```

---

## 7. TSR (Super-Resolution)

### 7.1 Features

| Feature | Description |
|---------|-------------|
| **Super-Resolution** | Upscales low-resolution video to high-resolution playback quality |
| **Cost Optimization** | Transmits lower quality from cloud, restores quality on device, saving bandwidth |
| **Low Power Consumption** | Current increase less than 100mA |
| **Multiple Modes** | Supports Standard mode, Professional mode, and color adjustment variants |

### 7.2 Environment Requirements

| Requirement | Version |
|-------------|---------|
| SDK Version | ≥ 9.5.29009 |
| License | Player License + Video Enhancement Authorization |

### 7.3 Integration Steps

**Step 1: Download SDK**

Two components are required:
- TSR Plugin SDK (`plugin_monet...` or `TXCMonetPlugin...`)
- Video Enhancement SDK (`TsrSdk...` or `tsr_client.framework`)

**Step 2: Obtain Authorization**

Get `appId` and `authId` for the Video Enhancement SDK.

### 7.4 Usage Example

#### Android

**Initialization:**

```java
// 1. Initialize License
String licenceUrl = "Enter your purchased License URL";
String licenseKey = "Enter your purchased License Key";
TXLiveBase.getInstance().setLicence(context, licenceUrl, licenseKey);

// 2. Enable plugin loading
TXVodPlayConfig playConfig = new TXVodPlayConfig();
playConfig.mEnableRenderProcess = true;
mVodPlayer.setConfig(playConfig);

// 3. Initialize TSR plugin
// srAlgorithmType: TXMonetConstant#SR_ALGORITHM_TYPE_STANDARD (Standard mode)
MonetPlugin.setAppInfo(appId, authId, srAlgorithmType);
```

**Enable/Disable:**

```java
@Override
public void onPlayEvent(TXVodPlayer player, int event, Bundle param) {
    if (event == TXLiveConstants.PLAY_EVT_VOD_PLAY_PREPARED) {
        // Enable TSR
        mVodPlayer.setStringOption("PARAM_SUPER_RESOLUTION_TYPE", 1);
    }
}

// Disable when playback ends
mVodPlayer.setStringOption("PARAM_SUPER_RESOLUTION_TYPE", 0);
```

#### iOS

**Initialization:**

```objective-c
// 1. Import header
#import <TXCMonetPlugin/TXCMonetPluginInterface.h>

// 2. Initialize License
NSString * const licenseURL = @"<Your licenseUrl>";
NSString * const licenseKey = @"<Your key>";
[TXLiveBase setLicence:licenseURL key:licenseKey];

// 3. Configure player parameters
_config = [[TXVodPlayConfig alloc] init];
_config.videoFrameFormatType = TX_VIDEO_PIXEL_FORMAT_BGRA; // Must be set to BGRA
_config.enableRenderProcess = YES;
[_txVodPlayer setConfig:_config];

// 4. Initialize TSR plugin
// algorithmType: TXCMPAlgorithmType_Standard (Standard mode)
[[TXCMonetPluginManager sharedManager] setAppInfo:@"appId"
                                           authId:authId
                                    algorithmType:algorithmType];
```

**Enable/Disable:**

```objective-c
// Enable (set before playback, or restart playback after setting)
NSMutableDictionary *extInfoMap = [NSMutableDictionary dictionary];
[extInfoMap setObject:@"1" forKey:@"PARAM_SUPER_RESOLUTION_TYPE"];
[_txVodPlayer setExtentOptionInfo:extInfoMap];

// Disable
[extInfoMap setObject:@"0" forKey:@"PARAM_SUPER_RESOLUTION_TYPE"];
[_txVodPlayer setExtentOptionInfo:extInfoMap];
```

---

## 8. Feature Comparison

| Feature | Android | iOS | Flutter | Web |
|---------|---------|-----|---------|-----|
| Short Video Component | ✓ | ✓ | ✓ | ✗ |
| Picture-in-Picture | ✓ | ✓ (Premium) | ✓ | ✗ |
| VR Playback | ✓ | ✓ | ✗ | ✓ (Premium) |
| TSR (Super-Resolution) | ✓ | ✓ | ✗ | ✗ |

---

## 9. FAQ

### 9.1 License Related

**Q: What should I do if the screen is black when using advanced features?**
A: Check whether the Player SDK Premium Edition License is correctly configured.

**Q: How to obtain a Premium Edition License?**
A: Go to the [RT-Cube Console](https://console.cloud.tencent.com/vcube/mobile) to apply.

### 9.2 Short Video Component

**Q: How to optimize slow first-frame loading?**
A: Adjust preloading strategy parameters such as `preloadCount` and `preDownloadSize`.

**Q: How to implement a custom UI?**
A: Create custom layers by extending the Layer class.

### 9.3 VR Playback

**Q: What is the log message for successful plugin loading?**
A: Android: `succeed loading pluginId=2`; iOS: `pluginId = 2, pluginName = Monet`

**Q: How to verify that VR is enabled successfully?**
A: The log contains `moduleType=11`.
