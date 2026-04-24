# Flutter Player SDK - Integration Guide

## Environment Requirements

### Development Environment

| Item | Requirement |
|------|-------------|
| Flutter Version | 3.0 or above |
| Android Studio | 3.5 or above |
| Xcode | 11.0 or above |
| macOS | 10.11 or above |
| Minimum Android Version | API 19 (Android 4.4) |
| Minimum iOS Version | iOS 9.0+ |

> **Note**: The iOS simulator is currently not supported for debugging. Use a real device for development and testing. Ensure that the project has a valid developer signing certificate configured.

### SDK Download

The RT-Cube Flutter Player project is hosted on GitHub:
- **Repository**: https://github.com/LiteAVSDK/Player_Flutter
- When running the Demo, you need to configure your own Player License in `demo_config` and modify the package name and BundleId.

---

## Quick Integration

### Step 1: Add Dependencies

Add the dependency in your project's `pubspec.yaml`. The SDK supports integration based on the LiteAVSDK Player or Professional edition.

**Option 1: Git Integration (Recommended)**

```yaml
dependencies:
  super_player:
    git:
      url: https://github.com/LiteAVSDK/Player_Flutter
      path: Flutter
      # ref: release_v13.0.0  # Uncomment and modify the tag to specify a version
```

**Specifying SDK Edition (After version 13.0):**

```yaml
dependencies:
  super_player:
    git:
      url: https://github.com/LiteAVSDK/Player_Flutter
      path: Flutter
    sub_spec: 'professional' # Options: player, professional, premium, professional_premium
```

**Option 2: Pub Integration**

```yaml
dependencies:
  super_player: ^13.0.0
```

**Run the command to fetch dependencies:**

```bash
flutter pub get
```

### Step 2: Integrate the Player License

The License must be configured at application startup (e.g., in `main.dart`); otherwise, playback may fail (black screen).

```dart
String licenceURL = ""; // Your licence URL
String licenceKey = ""; // Your licence key

SuperPlayerPlugin.setGlobalLicense(licenceURL, licenceKey);
```

### Step 3: Android Native Configuration

#### 1. Permission Configuration

Add network and storage permissions in `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

#### 2. Network Security Configuration (Important)

If `targetSdkVersion >= 28`, cleartext HTTP traffic is disallowed by default for security reasons. The Player SDK starts a local proxy and requires permission to send HTTP requests to `127.0.0.1`; otherwise, playback will fail.

Create `res/xml/network_security_config.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="true">
        <domain includeSubdomains="true">127.0.0.1</domain>
    </domain-config>
</network-security-config>
```

Reference it in the `<application>` tag of `AndroidManifest.xml`:

```xml
<application
    android:networkSecurityConfig="@xml/network_security_config"
    ... >
```

#### 3. Gradle Configuration

- Ensure `mavenCentral()` is used
- Modify `build.gradle` to set `minSdkVersion` to **19** or above
- If Picture-in-Picture (PiP) capability is needed, set `compileSdkVersion` and `targetSdkVersion` to **31** or above

#### 4. Resolve Manifest Conflicts

If you encounter a `Manifest merger failed` error (e.g., label conflict):

- Add to the root `<manifest>` node: `xmlns:tools="http://schemas.android.com/tools"`
- Add to the `<application>` node: `tools:replace="android:label"`

### Step 4: iOS Native Configuration

#### 1. HTTP Support

If the video source uses the HTTP protocol, add the following to `Info.plist`:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

#### 2. Podfile Configuration

You can specify the SDK version in `ios/Podfile`. The Player edition is integrated by default:

```ruby
pod 'TXLiteAVSDK_Player'        # Player Edition
# pod 'TXLiteAVSDK_Professional' # Professional Edition
```

#### 3. Force Update Dependencies

To force update iOS dependencies, run:

```bash
cd ios
rm -rf Pods
rm -rf Podfile.lock
pod update
```

#### 4. Landscape Mode and Picture-in-Picture

- **Landscape Mode**: In Xcode project `General` -> `Deployment Info`, check the supported orientations
- **Picture-in-Picture**: In `Signing & Capabilities` -> `Background Modes`, check "Audio, AirPlay, and Picture in Picture"

---

## VOD Playback Guide

### Create a Player Controller

```dart
TXVodPlayerController _controller = TXVodPlayerController();
```

### Set Up Event Listeners

```dart
_controller.onPlayerEventBroadcast.listen((event) async {
  final int code = event["event"];
  if (code == TXVodPlayEvent.PLAY_EVT_CHANGE_RESOLUTION) {
    int? videoWidth = event[TXVodPlayEvent.EVT_PARAM1];
    int? videoHeight = event[TXVodPlayEvent.EVT_PARAM2];
    // Set UI aspect ratio based on width and height
  }
});
```

### Add Layout and Bindx View

Add `TXPlayerVideo` to the Widget tree, and bind the view to the Controller via `viewId`:

```dart
child: _aspectRatio > 0
    ? AspectRatio(
        aspectRatio: _aspectRatio,
        child: TXPlayerVideo(
          androidRenderType: _renderType,
          onRenderViewCreatedListener: (viewId) {
            _controller.setPlayerView(viewId); // Bind the view
          },
        ),
      )
    : Container(),
```

> **Note**: Starting from SDK 12.3.1, you no longer need to manually call `initialize()`.

### Video Playback

#### Play via URL

```dart
String _url = "http://1400329073.vod2.example.com/....m3u8";
await _controller.startVodPlay(_url);
```

#### Play via FileId (Recommended)

Suitable for scenarios using Tencent Cloud VOD media asset management. Requires passing AppId, FileId, and psign (playback signature):

```dart
TXPlayInfoParams params = TXPlayInfoParams(
    appId: 1252463788,
    fileId: "4564972819220421305",
    psign: "psignxxxxxxx" // Hotlink protection signature
);
await _controller.startVodPlayWithParams(params);
```

#### Stop Playback and Release Resources

Always release resources when playback ends to prevent memory leaks:

```dart
@override
void dispose() {
  _controller.dispose(); // Destroy the controller
  super.dispose();
}
```

### Playback Control

| Method | Description |
|--------|-------------|
| `_controller.startVodPlay(url)` | Start playback |
| `_controller.pause()` | Pause playback |
| `_controller.resume()` | Resume playback |
| `_controller.stopPlay(true)` | Stop playback |
| `_controller.seek(time)` | Seek to specified time (seconds) |
| `_controller.seekToPdtTime(timeMs)` | Seek to a specific HLS PDT time point |
| `_controller.setLoop(true)` | Enable loop playback |
| `_controller.setRate(1.2)` | Set playback speed (e.g., 1.2x) |
| `_controller.setMute(true)` | Enable mute |

### Resolution Switching and Bitrate Control

#### Get Supported Bitrate List

Retrieve after receiving the `PLAY_EVT_VOD_PLAY_PREPARED` event:

```dart
List _supportedBitrates = (await _controller.getSupportedBitrates())!;
```

#### Switch Resolution

```dart
int index = _supportedBitrates[i]; // Select resolution index
_controller.setBitrateIndex(index);
```

#### Enable Adaptive Bitrate

```dart
_controller.setBitrateIndex(-1); // -1 enables adaptive bitrate
```

#### Smooth Bitrate Switching

```dart
FTXVodPlayConfig playConfig = FTXVodPlayConfig();
playConfig.smoothSwitchBitrate = true;
_controller.setConfig(playConfig);
```

---

## Common Advanced Features

### Video Pre-play

Pre-load the next video while playing the current one for instant playback. Set `autoPlay` to `false` to load without playing:

```dart
controller.setAutoPlay(isAutoPlay: false);
controller.startVodPlay(nextVideoUrl); // Pre-load
// ... After current video finishes playing ...
await controller.resume(); // Resume pre-loaded video playback
```

### Local Video Caching

Used in short video scenarios to avoid repeated bandwidth consumption:

```dart
SuperPlayerPlugin.setGlobalMaxCacheSize(200); // Set global cache size (MB)
SuperPlayerPlugin.setGlobalCacheFolderPath("postfixPath"); // Set cache path
```

### Offline Download

Supports downloading videos online for offline playback, including encrypted downloads:

```dart
// Start download (FileId method)
TXVodDownloadMedialnfo medialnfo = TXVodDownloadMedialnfo();
medialnfo.dataSource = TXVodDownloadDataSource();
medialnfo.dataSource!.appId = 1252463788;
medialnfo.dataSource!.fileId = "your fileId";
medialnfo.dataSource!.quality = DownloadQuality.QUALITY_720P;
TXVodDownloadController.instance.startDownload(medialnfo);
```

#### Download Status Listener

```dart
TXVodDownloadController.instance.setDownloadObserver(
  FTXVodDownloadListener(
    onStateChange: (int taskId, int state, TXVodDownloadMedialnfo info) {
      // state: 0-Initial, 1-Downloading, 2-Paused, 3-Completed, 4-Failed
    },
    onDownloadProgress: (int taskId, TXVodDownloadMedialnfo info) {
      // Progress callback
    },
    onDownloadError: (int taskId, int errorCode, String errorMsg) {
      // Download error
    },
  ),
);
```

#### Get Download List and Delete

```dart
// Get all download tasks
List<TXVodDownloadMedialnfo> list = await TXVodDownloadController.instance.getDownloadList();

// Delete a downloaded video
TXVodDownloadController.instance.deleteDownloadMediaInfo(medialnfo);
```

### External Subtitles

Supports SRT and VTT formats (requires Premium Edition 11.7+):

```dart
// Add subtitles
controller.addSubtitleSource("subtitle_url.vtt", "subtitleName", TXVodPlayEvent.VOD_PLAY_MIMETYPE_TEXT_SRT);

// Select subtitle track
controller.selectTrack(trackIndex);

// Deselect subtitle track
controller.deselectTrack(trackIndex);
```

### Picture-in-Picture (PiP)

Supports Picture-in-Picture mode on Android (7.0+) and iOS:

```dart
// Enter PiP mode
_playerController.enterPictureInPictureMode(
    backIconForAndroid: backIconForAndroid,
    playIconForAndroid: playIconForAndroid,
    pauseIconForAndroid: pauseIconForAndroid,
    forwardIconForAndroid: forwardIconForAndroid,
);

// Exit PiP mode
_playerController.exitPictureInPictureMode();
```

**Platform Configuration Requirements:**
- **Android**: `targetSdkVersion` in `build.gradle` must be 31+
- **iOS**: Add `Background Modes` in `Signing & Capabilities`, and check `Audio, AirPlay, and Picture in Picture`

### Hardware Decoding Switch

```dart
// Must call stopPlay before switching between software and hardware decoding; otherwise, screen artifacts may occur
await _controller.stopPlay(true);
await _controller.enableHardwareDecode(true); // Enable hardware decoding
await _controller.startVodPlay(url);
```

---

## Player Event Listeners

### Playback Progress Listener

```dart
_controller.onPlayerEventBroadcast.listen((event) async {
  if (event["event"] == TXVodPlayEvent.PLAY_EVT_PLAY_PROGRESS) {
    double playableDuration = event[TXVodPlayEvent.EVT_PLAYABLE_DURATION_MS].toDouble();
    int progress = event[TXVodPlayEvent.EVT_PLAY_PROGRESS].toInt();
    int duration = event[TXVodPlayEvent.EVT_PLAY_DURATION].toInt();
    // Update progress bar UI
  }
});
```

### Network Status Listener

```dart
_controller.onPlayerNetStatusBroadcast.listen((event) {
  double speed = (event[TXVodNetEvent.NET_STATUS_NET_SPEED]).toDouble(); // Network speed
});
```

### Playback State Listener

```dart
_controller.onPlayerState.listen((state) {
  // state: playing, paused, buffering, failed, etc.
});
```

---

## Player Component (SuperPlayerView)

The Player Component is an advanced extension built on the Flutter Player SDK, encapsulating a complete UI interaction layer (fullscreen toggle, resolution switching, progress bar, playback speed, etc.).

### Import the Component

There are two main integration methods (Git integration recommended for the latest version):

#### Option 1: Copy Component Code

Copy the `superplayer_widget` directory from the GitHub project into your Flutter project.

#### Option 2: Configure pubspec.yaml

```yaml
dependencies:
  super_player:
    git:
      url: https://github.com/LiteAVSDK/Player_Flutter
      path: Flutter
  superplayer_widget:
    path: ../superplayer_widget # Local path
```

> **Note**: You need to modify the `pubspec.yaml` inside `superplayer_widget` to ensure its dependencies point to the correct `super_player` source.

### Internationalization Configuration

Add the internationalization delegate to the `MaterialApp` entry of your application:

```dart
MaterialApp(
  localizationsDelegates: [
    SuperPlayerWidgetLocals.delegate,
    // ...other delegates
  ],
  supportedLocales: [
    Locale.fromSubtags(languageCode: 'en'),
    Locale.fromSubtags(languageCode: 'zh'),
  ],
);
```

### Initialization and Configuration

```dart
// 1. Create Controller
SuperPlayerController _controller = SuperPlayerController(context);

// 2. Configure player parameters (optional)
FTXVodPlayConfig config = FTXVodPlayConfig();
config.preferredResolution = 720 * 1280; // Preferred playback resolution
_controller.setPlayConfig(config);

// 3. Set up event listeners
_controller.onSimplePlayerEventBroadcast.listen((event) {
  String evtName = event["event"];
  if (evtName == SuperPlayerViewEvent.onStartFullScreenPlay) {
    setState(() { _isFullScreen = true; });
  } else if (evtName == SuperPlayerViewEvent.onStopFullScreenPlay) {
    setState(() { _isFullScreen = false; });
  }
});
```

### Add Layout

```dart
Widget _getPlayArea() {
  return Container(
    height: 220,
    child: SuperPlayerView(_controller),
  );
}
```

### Start Playback

**URL Playback:**

```dart
SuperPlayerModel model = SuperPlayerModel();
model.videoURL = "http://your-video-url.m3u8";
_controller.playWithModelNeedLicence(model);
```

**FileId Playback (Recommended):**

```dart
SuperPlayerModel model = SuperPlayerModel();
model.appId = 1500005830;
model.videoId = new SuperPlayerVideoId();
model.videoId.fileId = "8602268011437356984";
model.videoId.pSign = "psignXXX"; // Playback signature
_controller.playWithModelNeedLicence(model);
```

### Lifecycle Management

**Important**: The player must be released when leaving the page; otherwise, memory leaks or screen flashing may occur.

```dart
@override
void dispose() {
  _controller.releasePlayer(); // Must be called
  super.dispose();
}
```

### Common Control Methods

| Method | Description |
|--------|-------------|
| `_controller.pause()` | Pause playback |
| `_controller.resume()` | Resume playback |
| `_controller.reStart()` | Restart playback |
| `_controller.seek(progress)` | Seek to position (seconds) |
| `_controller.switchStream(videoQuality)` | Switch resolution |
| `_controller.releasePlayer()` | Release resources |
| `_controller.onBackPress()` | Handle back press event (exits fullscreen first when in fullscreen mode) |

### Video Download Capability

```dart
// Enable download capability
SuperPlayerModel model = SuperPlayerModel();
model.isEnableDownload = true;

// Trigger download
_controller.startDownload();

// Download a specific video
DownloadHelper.instance.startDownloadBySize(videoModel, videoWidth, videoHeight);
```

### Orientation Switching

**Back Event Listener**: Ensure that pressing the back button in fullscreen mode exits fullscreen first rather than leaving the page:

```dart
Future<bool> onWillPop() async {
  return !_controller.onBackPress(); // Returning false means the back event was intercepted
}
```

**Gravity Sensor**: On Android, call the following method to enable sensor monitoring:

```dart
SuperPlayerPlugin.startVideoOrientationService();
```

### UI Customization

The player component is open-source. Since the UI component and Flutter player plugin versions must be kept consistent, the official recommendation is to directly modify the component source code for deep UI customization. If you need to track component version updates, it is recommended to fork the component code and create a private repository.

---

## Deep Customization Development Guide

- For basic playback only, use `TXVodPlayerController` (VOD) or `TXLivePlayerController` (Live)
- For a player component with UI, refer to `SuperPlayerController`
- **Custom UI**: Import the code from the `Flutter/superplayer_widget` directory into your project for modification

---

## Flutter Integration FAQ

| Issue | Solution |
|-------|----------|
| iOS error: interface not found | Run `pod update` to update the iOS SDK |
| Conflict with TRTC SDK | Integrate the Professional edition of the player and ensure TRTC and player depend on the same LiteAVSDK version |
| Blurry screen when switching between multiple players | Call the `dispose` method when destroying the player |
| Flutter dependency issues | Run `flutter doctor` and `flutter pub get` to check the environment |
| Android Manifest label conflict | Add `tools:replace="android:label"` |
| Android minSdkVersion error | Set `minSdkVersion` to 19 or above |
| Audio plays but no video | Version mismatch between `superplayer_widget` (UI component) and `super_player` (underlying plugin); check and unify version numbers |
| Crash after obfuscation during packaging | Add `-keep class com.tencent.**{*;}` to the obfuscation configuration |
| Local video cannot play | Check if the local path is correct and if storage read permission is granted |
| Flutter version compatibility (texture rendering issue) | Upgrade Flutter to version 3.7.0 or above |
| Disable automatic screen rotation | Modify the component source code or lock to portrait mode at the application entry: `SystemChrome.setPreferredOrientations([DeviceOrientation.portraitUp])` |

### Extracting Runtime Logs

- **Android**: `/sdcard/Android/data/<package-name>/files/log/tencent/liteav`
- **iOS**: Sandbox `Documents/log`
- **Disable Logging**: `SuperPlayerPlugin.setLogLevel(TXLogLevel.LOG_LEVEL_NULL)`
