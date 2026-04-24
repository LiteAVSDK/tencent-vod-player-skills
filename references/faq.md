# Player SDK FAQ

## License FAQ

### License Types & Purchase

**Q: What Licenses does the Player SDK include?**

The Player SDK includes the **Player License**, which unlocks the video playback feature of the Player SDK. After purchasing and unlocking the License, you can use Player SDK version 10.1 and above normally. The Player SDK Mobile and Web editions are authorized separately, and both offer Standard and Premium versions.

**Q: Is the Player License mandatory?**

Yes. If you need to use Player SDK version 10.1 or later, you must purchase a Player License to unlock the video playback module for VOD and live playback capabilities.

**Q: Is there a standalone purchase option for the Player License?**

Yes. You can purchase 100GB, 500GB, 1TB, or 5TB CSS/VOD traffic packages, which typically include a one-year Player Mobile Standard Edition License. You can also directly purchase a standalone Player License.

**Q: Can existing Licenses of other types unlock the Player SDK video playback feature?**

Yes. The following Licenses can all authorize the Player SDK Mobile Standard Edition video playback feature (only one is needed):
- **Live License**: Included with CSS traffic packages (10TB/50TB/200TB/1PB) or standalone Live License
- **UGSV License**: Included with VOD traffic packages (10TB/50TB/200TB/1PB) or standalone UGSV License
- **Player License**: Included with CSS/VOD traffic packages or standalone Player License

---

### License Application, Validity & Renewal

**Q: Can the trial License be extended after expiration?**

- The trial License has a maximum trial period of 28 days
- You can extend once after 14 days from initial application; a second extension is not supported
- Please purchase an official License as soon as possible after expiration
- **Renewal rule**: If renewal is requested within the 14-day trial period, the expiration time is calculated from the initial application time; if requested after the trial ends, the expiration time is calculated from the renewal request time

**Q: Can the trial License change the Android Package Name and iOS Bundle ID?**

Yes. Go to the RT-Cube Console > Mobile License / Web License, find the target trial License, and click Edit to modify.

**Q: Can the official License change the Android Package Name and iOS Bundle ID?**

No. The Package Name and Bundle ID of an official License cannot be modified. Please verify carefully before adding.

**Q: Can the License be modified?**

The Player License validity can be extended through renewal, but the package name information (Package Name / Bundle ID) cannot be modified.

---

### Usage Limits & Configuration Issues

**Q: Can a License support multiple apps simultaneously?**

No. One License can only correspond to one Package Name (Android) and one Bundle ID (iOS). If multiple apps need to use it, you need to purchase multiple resource packages to create multiple Licenses.

**Q: Can multiple Licenses be created under one account?**

There is no limit. For management convenience, it is recommended to extend the validity of Licenses with the same package name through renewal rather than creating new ones.

**Q: Can multiple Licenses be created with the same package name?**

Yes. Multiple Licenses with the same package name will not affect usage, but their validity periods are calculated independently. This is generally not recommended; use the renewal feature instead.

**Q: Why do all my Licenses have the same licenseUrl and Key?**

This is normal behavior. Under the same account, the Player SDK's LicenseUrl and Key are the same by default. This design ensures that trial, official, and Licenses with different package names can all reuse the same interface information without code changes when switching Licenses.

---

### Common Errors & Troubleshooting

**Q: Why am I not receiving License expiration notifications?**

Player License notifications need to be configured separately in "Message Subscription". Subscribe to "RT-Cube SDK" in the console's [Message Subscription](https://console.cloud.tencent.com/message/subscription), and configure receiving channels such as in-app messages, email, SMS, WeChat, or WeCom. The system will send reminders 30, 15, 7, and 1 day(s) before expiration.

**Q: I just renewed my License and the console shows it's within the validity period, but the app shows "license invalid"?**

This is usually caused by local cache preventing the License from auto-updating. Try the following steps:
1. Ensure the device is connected to the network
2. Restart the application
3. Wait for the License to auto-update

> **Recommendation**: Renew the License in advance to avoid business impact.

---

## Android & iOS Common Playback Issues

**Q: Playback fails with "no v4 play info" exception?**

**Cause**: When playing via FileId, the Adaptive-HLS(10) transcoding template was not used, or the player signature was not configured.

**Solution**:
1. Transcode the video using the Adaptive-HLS(10) template
2. Or obtain the source video playback URL and use URL-based playback instead

---

**Q: How to extract player logs for error reporting?**

The SDK outputs logs to local files by default:
- **Android**: `/sdcard/Android/data/<package_name>/files/log/tencent/liteav`
- **iOS**: Sandbox `Documents/log`
- **Flutter**: Same as above, depends on the running platform

Provide the log files to Tencent Cloud technical support for analysis.

---

**Q: How can the app pull media from Tencent Cloud for playback?**

For security reasons, there is no interface for the app to directly pull media. You need to call the Cloud API (Search Media Info interface) through the **App backend server** to get the list, then return it to the app.

---

**Q: When coexisting with TRTC (Tencent Real-Time Communication), the video playback volume becomes low?**

SDK version 10.0 and above provides the `attachTRTC` method. Call this method to bindproj before creating the player for video playback:

- **iOS**: `[_txVodPlayer attachTRTC:trtcCloud];`
- **Android**: `mVodPlayer.attachTRTC(trtcCloud);`

---

**Q: Video playback fails when a network proxy or packet capture tool is enabled on the device?**

You need to set `localhost` as bypass proxy in the device's HTTP proxy settings:
- **Android path**: Settings > WLAN > Tap current network > Advanced > Proxy > Bypass proxy (enter localhost)

---

## Android SDK Specific Issues

**Q: Black screen (no video) during playback?**

Check whether the `SurfaceView` or `TextureView` has been successfully bindproj to the `TXVodPlayer` object.

---

**Q: How to reduce APK size?**

1. Remove old cache SO files (e.g., `libijkhlscache-master.so`) unless you need to play files cached by older versions
2. Only package `armeabi-v7a` and `arm64-v8a` architectures
3. Configure `packagingOptions` in `build.gradle` to exclude unnecessary architectures

---

**Q: How to reduce console log output?**

Set LogLevel in version 10.2 and above:

```java
TXLiveBase.setLogLevel(TXLiveConstants.LOG_LEVEL_DEBUG);
```

---

**Q: The player gets killed by the system when the user locks the screen or the app goes to background?**

It is recommended to use a **Foreground Service** in the app to prevent the system from reclaiming resources.

---

**Q: On Android P, playback reports "java.io.IOException: Cleartext HTTP traffic to 127.0.0.1 not permitted"?**

**Cause**: Android P prohibits cleartext HTTP traffic, but the Player SDK starts a local server proxy.

**Solution**:

1. Create `res/xml/network_security_config.xml`:

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
```

---

## iOS SDK Specific Issues

**Q: Crash after integration: "[TXCThumbPlayer thumbPlayerBundleId] unrecognized selector"?**

**Cause**: The SDK contains Categories, and the linker flag was not set.

**Solution**: In Xcode Build Settings, search for `Other Link Flag` and add `-ObjC`.

---

**Q: Crash after integration: "dyld[34620]: Library not loaded: @rpath/TXFFmpeg.framework/TXFFmpeg"?**

**Cause**: The dynamic library `TXFFmpeg.framework` was not embedded or signed.

**Solution**: Xcode -> Target -> General -> Frameworks, Libraries, and Embedded Content -> Select `TXFFmpeg.framework` -> Choose `Embed & Sign` on the right.

---

**Q: Playback control panel not showing?**

Check whether you have updated the title, artwork, and other information via `MPNowPlayingInfoCenter`'s `nowPlayingInfo` property.

---

**Q: AppId + FileId playback fails with error 14010020?**

**Cause**: File download failed.

**Solution**: Please upgrade to SDK version 10.6 or above; this issue has been fixed in newer versions.

---

**Q: No size and downloadSize values in the callback when downloading M3U8 files?**

**Cause**: The M3U8 standard protocol does not include total size, and TS segment sizes can only be obtained upon request. This is a protocol characteristic; the total size cannot be accurately returned before download.

---

**Q: "Playback failed" when selecting a photo album video on iOS 13+ devices?**

**Cause**: iOS 13+ changed the photo library plugin access path.

**Solution**: Copy the photo library data to a temporary directory under the sandbox (e.g., `tmp` directory) before playback.

---

**Q: What is the difference between .framework and .xcframework versions of the SDK?**

`.xcframework` supports M1 and later devices with ARM64 simulator architecture. Projects managed by Pod can switch directly; manual integration requires updating header file search paths.

---

**Q: Has the SDK adapted to Apple's latest privacy manifest?**

Starting from version 11.7.15343:
- **Pod integration**: Depend on version 11.7.15343
- **Manual integration**: Refer to the manual integration section in the SDK integration guide

---

## Flutter SDK FAQ

**Q: iOS reports interface not found?**

Run `pod update` to update the iOS SDK.

---

**Q: Conflict with TRTC SDK?**

Integrate the Professional edition of the player to ensure TRTC and the player depend on the same LiteAVSDK version.

---

**Q: Blurry display when switching between multiple players?**

Call the `dispose` method when destroying the player.

---

**Q: Flutter dependency issues?**

Run `flutter doctor` and `flutter pub get` to check the environment.

---

**Q: Android Manifest label conflict?**

Add `tools:replace="android:label"` in `AndroidManifest.xml`.

---

**Q: Android minSdkVersion error?**

Increase `minSdkVersion` to 19.

---

**Q: Audio but no video?**

**Cause**: `superplayer_widget` (UI component) and `super_player` (underlying plugin) version mismatch.

**Solution**: Check and unify both version numbers.

---

**Q: Crash due to obfuscation?**

Add `-keep class com.tencent.**{*;}` to the obfuscation configuration.

---

**Q: Local video cannot be played?**

Check whether the local path is correct and whether storage read permission is granted.

---

**Q: Flutter version compatibility issues (texture rendering)?**

Upgrade Flutter to version 3.7.0 or above to avoid texture rendering issues.

---

**Q: How to disable automatic screen rotation?**

Modify the component source code, or call `SystemChrome.setPreferredOrientations([DeviceOrientation.portraitUp])` at the app entry to lock to portrait mode.

---

**Q: How to reduce log output?**

```dart
SuperPlayerPlugin.setLogLevel(TXLogLevel.LOG_LEVEL_NULL);
```

---

## Extracting Player Runtime Logs

When encountering issues that require technical support analysis, extract log files from the following paths:

| Platform | Log Path |
|----------|----------|
| Android | `/sdcard/Android/data/<package_name>/files/log/tencent/liteav` |
| iOS | Sandbox `Documents/log` |
| Flutter (Android) | Same as Android path |
| Flutter (iOS) | Same as iOS path |
