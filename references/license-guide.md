# Player SDK License Guide

## Overview

The Player SDK is divided into **Mobile** and **Web** editions, each with independent authorization and billing. Before use, you need to purchase a License and bind it to a specific application (by package name for Mobile) or domain (for Web). After binding, the console issues the core credentials **License URL** and **License Key**, which must be passed in during SDK initialization. One Tencent Cloud account generates only one unique License URL + Key pair, which does not change when Licenses are added, renewed or expired.

> **Important**: Starting from version 10.1, you must purchase a Player License to unlock the video playback module for VOD and live playback capabilities. Starting from version 10.7, playback will fail (black screen) unless a License is configured.

> **Note**: Starting from version 10.1, Live License and UGSV (User-Generated Short Video) License also include the Standard Edition capabilities of the Player Mobile SDK.

---

## Mobile License

The Mobile License is divided into **Standard Edition** and **Premium Edition**. The authorization unit is the application: provide any one of `Package Name`, `Bundle ID` or `Bundle Name` when creating the License.

### License Edition Comparison

| Edition | Standard | Premium |
|---------|----------|---------|
| Price | 12 CNY/year (or free with traffic package purchase) | 899 CNY/month |
| Features | Basic playback capabilities | DRM, short video component, HEVC downgrade playback, and other advanced features |
| Traffic Package Gift | Free with CSS/VOD traffic packages (100GB/500GB/1TB/5TB) | Not available as gift, must be purchased separately |

### Purchasing an Official License

**Standard Edition:**
- **Direct Purchase**: 12 CNY/year
- **Traffic Package Gift**: CSS traffic packages 100GB / 500GB / 1TB / 5TB cost 26 / 128 / 248 / 1200 CNY; VOD traffic packages 100GB / 500GB / 1TB / 5TB cost 19 / 88 / 175 / 869 CNY. Each grants one year of the Mobile Standard Edition License

**Premium Edition:**
- **Direct Purchase**: 899 CNY/month

### Binding an Official License

Go to [RT-Cube Console](https://console.cloud.tencent.com/vcube/mobile):

**Method 1: Create a new official application**
1. Click "Create Official License"
2. Enter `App Name` and provide any one of `Package Name`, `Bundle ID` or `Bundle Name`; confirm they match the package information submitted to the app store, because a formal License cannot be modified once submitted
3. Select the Player License type (Standard / Premium) and click Next
4. Bind the purchased License resource and create. After creation, missing package-name fields can still be added via "Add Package Name" on the application card

**Method 2: Unlock features for an existing application**
1. Select an existing official application and click "Unlock New Feature"
2. Select Player License, bind the resource and confirm

### Updating Validity (Renewal)

**Standard Edition (yearly renewal):**
- **Obtained via traffic package**: Can only update validity by "Select another License resource to replace"
- **Obtained via direct purchase**: Supports "Renew current License" or "Select another License resource to replace"
- **Renewal rule**: If renewed more than 7 days after expiration, validity becomes "payment date + renewal period"

**Premium Edition (monthly renewal):**
- Supports "Renew current License" or "Select another License resource to replace"

### Upgrade (Standard → Premium)

1. In the console, select the **Mobile Standard Edition License** to upgrade
2. Click **Upgrade** within the "Player Standard Edition" feature
3. Select an unbound Player Premium Edition License and confirm
4. The original Standard Edition License is released and can be re-bound to other applications

---

## Web License

The Web edition authorization unit is the domain name.

### License Edition Comparison

| Edition | Standard | Premium |
|---------|----------|---------|
| Price | Free (0 CNY/year) | 399 CNY/month |
| Domain Type | Exact domain | Wildcard domain (requires SDK 5.1.0+) |
| Domain Count | 1 License supports up to 10 domains | 1 License binds to 1 wildcard domain |

### Binding an Official License

Go to [RT-Cube Console > Web License](https://console.cloud.tencent.com/vcube/web?tab=player):

- **Standard Edition**: Enter project name and domains (up to 10), automatically bound to free resources
- **Premium Edition**: Enter project name and wildcard domain, select purchased License resource to bind

### Updating Validity (Renewal)

- **Standard Edition**: Free to use; can click "Renew" for free extension when validity remaining is within 30 days. Auto-renewal is not supported
- **Premium Edition**: Supports "Renew current License" or "Select another License resource to replace". Renewal rules are the same as Mobile (within 7 days after expiration: stacked; beyond 7 days: recalculated)

---

## Auto-Renewal Management

- **Applicable scope**: Only Licenses obtained via "Direct Purchase" support auto-renewal (traffic package gifts do not)
- **Trigger timing**: Auto-deduction 3 days before expiration
- **Renewal cycle**:
  - Mobile Standard Edition: Yearly
  - Mobile Premium Edition & Web Premium Edition: Monthly
- **Management entry**:
  1. **Console**: Enable/disable auto-renewal on the corresponding resource in the License management page
  2. **Billing Center**: Go to "Renewal Management" page and search "Player" to configure

---

## License Configuration Methods

License must be configured before calling any other SDK interfaces.

### iOS Platform

It is recommended to add in `AppDelegate`'s `application:didFinishLaunchingWithOptions:` method:

```objective-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    NSString * const licenseURL = @"<Your licenseUrl>";
    NSString * const licenseKey = @"<Your key>";

    // TXLiveBase is in the "TXLiveBase.h" header
    [TXLiveBase setLicence:licenseURL key:licenseKey];
    [TXLiveBase sharedInstance].delegate = self;
    NSLog(@"SDK Version = %@", [TXLiveBase getSDKVersionStr]);
    return YES;
}

#pragma mark - TXLiveBaseDelegate
- (void)onLicenceLoaded:(int)result Reason:(NSString *)reason {
    NSLog(@"onLicenceLoaded: result:%d reason:%@", result, reason);
}
```

### Android Platform

It is recommended to add in `Application`'s `onCreate` method:

```java
public class MApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        String licenseURL = ""; // Your license url
        String licenseKey = ""; // Your license key
        TXLiveBase.getInstance().setLicence(this, licenseURL, licenseKey);
        TXLiveBase.setListener(new TXLiveBaseListener() {
            @Override
            public void onLicenceLoaded(int result, String reason) {
                Log.i(TAG, "onLicenceLoaded: result:" + result + ", reason:" + reason);
            }
        });
    }
}
```

### Flutter Platform

Add in `main.dart`:

```dart
String licenceURL = ""; // Your licence url
String licenceKey = ""; // Your licence key

SuperPlayerPlugin.setGlobalLicense(licenceURL, licenceKey);
```

### HarmonyOS Platform

Import `liteavsdk` and configure the License before creating the player:

```typescript
import { getV2TXLivePremierShareInstance, V2TXLivePremierObserver } from 'liteavsdk';

const observer: V2TXLivePremierObserver = {
  onLicenceLoaded: (result: number, msg: string) => {
    console.info(`onLicenceLoaded: result=${result}, msg=${msg}`);
  }
};

getV2TXLivePremierShareInstance(getContext()).addObserver(observer);
getV2TXLivePremierShareInstance(getContext()).setLicence('<Your licenseUrl>', '<Your key>');
```

### Viewing License Information

After successful License configuration, you can view License information using the following methods:

- **iOS**: `NSLog(@"%@", [TXLiveBase getLicenceInfo]);`
- **Android**: `TXLiveBase.getInstance().getLicenceInfo();`

---

## License Configuration Notes

### Network Requirements
License uses strict online verification. The network must be available when `setLicence` is called for the first time during app launch. If network permission has not been granted at first launch, call the method again after permission is granted.

### Result Monitoring
You must listen to the `onLicenceLoaded` callback. If loading fails, retry based on the actual situation or guide the user to check the network. After multiple failures, implement rate limiting and display product-level prompts.

### Multiple Calls
`setLicence` can be called multiple times. It is recommended to call it again when entering the app's main screen to ensure successful loading.

### Multi-Process Support
For multi-process apps (e.g., Android apps using an independent process for video playback), ensure that `setLicence` is called at startup in every process that uses the player.

### First Launch Optimization (Flexible Verification)
For scenarios where network verification cannot complete in time for the first video playback (supported from SDK version 11.6), you can enable flexible verification mode:

- **iOS**: `[TXPlayerGlobalSetting setLicenseFlexibleValid:YES];`
- **Android**: `TXPlayerGlobalSetting.setLicenseFlexibleValid(true);`
- **Flutter**: `SuperPlayerPlugin.setLicenseFlexibleValid(true);`

When enabled, the first 2 playback verification checks will pass by default.

---

## Important Notes

1. **Information cannot be modified**: Once an official License is created, the package name or domain information **cannot be modified**. To change, you must create a new License
2. **Unique credentials**: Only one set of License URL and Key is generated per Tencent Cloud account, which does not change with new License additions or renewals
3. **Activation time**: After purchase or renewal, the app will auto-update when connected to the network; if not activated, try restarting the app to clear local cache
4. **Existing License reuse**: The following Licenses can all authorize the Player SDK Mobile Standard Edition video playback feature (only one is needed):
   - **Live License**: Purchased with CSS traffic packages (10TB/50TB/200TB/1PB) or standalone Live License
   - **UGSV License**: Purchased with VOD traffic packages (10TB/50TB/200TB/1PB) or standalone UGSV License
   - **Player License**: Purchased with CSS/VOD traffic packages or standalone Player License
