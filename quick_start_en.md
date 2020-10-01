# Quick Start for Pangle iOS SDK

* [About iOS 14](#start/ios14)
* [Prerequisites](#start/env)
* [Integrate](#start/integrate)
* [Initializing the SDK](#start/init)
* [Native Ads](#start/native_ad)
  * [Origin Native Ads](#start/native_ad_origin)
  * [Template Native Ads](#start/native_ad_template)
* [Displaying advertisements](#start/ad_display)
* [Sending advertisements events](#start/ad_event)

This chapter will explain the procedure for displaying the advertisement in the application and measuring the effectiveness of the advertisement.

<a name="start/ios14"></a>
## About iOS 14
Please follow [iOS 14 actions](https://www.pangleglobal.com/help/doc/5f4dc4271de305000ece82aa) to enable SKAdNetwork and include App Tracking Transparency.


<a name="start/env"></a>
## Prerequisites

* Use Xcode 12.0 or later
* Target iOS 9.0 or later
* Create a Pangle account [here](https://www.pangleglobal.com/)(If you do not have one), and add your app and placements.


<a name="start/integrate"></a>
## Integrate
### Using CocoaPods
Add the information as follows in Podfile, and using `pod install`.

```
pod 'Bytedance-UnionAD'
```
### Add frameworks

1. Select the project file

2. Select the build target

3. Select the Build Phase tab

4. Click the + button on the Link Binary with Libraries section

5. Add following frameworks

    -   StoreKit.framework
    -   MobileCoreServices.framework
    -   WebKit.framework
    -   MediaPlayer.framework
    -   CoreMedia.framework
    -   CoreLocation.framework
    -   AVFoundation.framework
    -   CoreTelephony.framework
    -   SystemConfiguration.framework
    -   AdSupport.framework
    -   CoreMotion.framework
    -   libresolv.9.tbd
    -   libc++.tbd
    -   **libbz2.tbd**
    -   **libxml2.tbd**
    -   libz.tbd Detailed Steps:
    -   Add the imageio. framework if the above dependency library is still reporting errors.


### Other build settings

Perform the `Other Linker Flag` settings according to the following procedures.

1. Select the project file

2. Select the build target

3. Select the Build Settings tab

4. Search and select Other Linker Flags

5. Add the  `-ObjC` flag


This completes the installation.

<a name="start/init"></a>
## Initializing the SDK

Initialize Pangle with the APP ID as the argument. Unless there is a particular reason, stipulate as

**UIApplicationDelegate application(_:didFinishLaunchingWithOptions:)**

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {

    // Coppa 0: adult, 1: child
    //BUAdSDKManager.setCoppa(1)
    // GDPR 0: close privacy protection, 1: open privacy protection
    //BUAdSDKManager.setGDPR(1)

    BUAdSDKManager.setAppID("your_app_id")

    return true
}
```

:warning: **If you need to obtain consent from users in the European Economic Area (EEA) or users under age, please ensure you set `setCoppa:(NSUInteger)Coppa` or `setGDPR:(NSInteger)GDPR`**



<a name="start/native_ad"></a>
## Native Ads

<a name="start/native_ad_origin"></a>
### Origin Native Ads

#### Creating an Origin Native Ad placement
After you created one ad, you will get a **placement ID** for ads loading.

<img src="native_origin.png" alt="drawing" width="200"/>

#### Loading Ads

```swift
class YourNativeAdsViewController: UIViewController, BUNativeAdsManagerDelegate {

    var adManager: BUNativeAdsManager!

    //placementID : the ID when you created a placement
    //count: the counts you want to download,DO NOT set more than 3
    func requestNativeAds(placementID:String, count:Int) {
        let slot = BUAdSlot.init()
        slot.id = placementID
        slot.adType = BUAdSlotAdType.feed
        slot.position = BUAdSlotPosition.feed
        let size = BUSize.init()
        size.width = 1280
        size.height = 720
        slot.imgSize = size
        slot.isSupportDeepLink = true
        adManager = BUNativeAdsManager.init(slot: slot)
        adManager.delegate = self

        adManager.loadAdData(withCount: count)
    }

    ...

```
#### Determining load events

`BUNativeAdsManagerDelegate` indicates the result of ad's load.

```swift
extension YourNativeAdsViewController: BUNativeAdsManagerDelegate {
    func nativeAdsManagerSuccess(toLoad adsManager: BUNativeAdsManager, nativeAds nativeAdDataArray: [BUNativeAd]?) {

        nativeAdDataArray?.forEach { nativeAd in
            //show each nativeAd
        }
    }

    func nativeAdsManager(_ adsManager: BUNativeAdsManager, didFailWithError error: Error?) {
        print("\(#function)  failed with error: \(String(describing: error?.localizedDescription))")
    }
}
```


<a name="start/native_ad_template"></a>
### Template Native Ads

#### Creating an Origin Native Ad placement


#### Loading Ads
#### Determining load events


## Others

`App ID` and `Placement ID` for advertisements

| Argument | Usage |
|:-------------|:------------|
| App ID | the ID when create an app |
| Placement ID | the ID when create an placement for an app |
