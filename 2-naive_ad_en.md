# 2. Native Ads


* [Origin Native Ads](#start/native_ad_origin)
  * [Loading Ads](#start/native_ad_origin_load)
  * [Determining load events](#start/native_ad_origin_loadevent)
  * [Displaying Ads](#start/native_ad_origin_display)
* [Template Native Ads](#start/native_ad_template)
  * [Loading Ads](#start/native_ad_template_load)
  * [Determining load events](#start/native_ad_template_loadevent)
  * [Displaying Ads](#start/native_ad_template_display)

* [Sending advertisements events](#start/ad_event)

This chapter will explain the procedure for displaying the native ads in the application and measuring the effectiveness of the advertisement.

Please [integrate Pangle SDK](1-integrate_en.md) before load ads.




<a name="start/native_ad_origin"></a>
## Origin Native Ads

<a name="start/native_ad_origin_load"></a>
### Loading Ads

Create an **Origin** ad in the app, you will get a **placement ID** for ad's loading.

<img src="native_origin.png" alt="drawing" width="200"/>



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

        // for BUNativeAdsManagerDelegate
        adManager.delegate = self

        adManager.loadAdData(withCount: count)
    }

    ...

```

<a name="start/native_ad_origin_loadevent"></a>
### Determining load events

`BUNativeAdsManagerDelegate` indicates the result of ad's load.
**Must set `rootViewController` of nativeAd for ad's displaying.**

```swift
extension YourNativeAdsViewController: BUNativeAdsManagerDelegate {
    func nativeAdsManagerSuccess(toLoad adsManager: BUNativeAdsManager, nativeAds nativeAdDataArray: [BUNativeAd]?) {

        nativeAdDataArray?.forEach { nativeAd in
            //each BUNativeAd object has datas for displaying
            nativeAd.rootViewController = self
        }
    }

    func nativeAdsManager(_ adsManager: BUNativeAdsManager, didFailWithError error: Error?) {
        print("\(#function)  failed with error: \(String(describing: error?.localizedDescription))")
    }
}
```

<a name="start/native_ad_origin_display"></a>
### Displaying Ads

`nativeAd`'s parameter `data` has parts like ad's title, description, images for displaying.

if the parameter`imageMode` in the `data` is **BUFeedVideoAdModeImage** or **BUFeedADModeSquareVideo**, please init a `BUNativeAdRelatedView` and call `- (void)refreshData:(BUNativeAd *)nativeAd;` to get videoAdView parts for the ad.


```swift
var nativeAdRelatedView = BUNativeAdRelatedView.init()

func setup(nativeAd: BUNativeAd) {
    title.text = nativeAd.data?.adTitle
    desc.text = nativeAd.data?.adDescription
    actionBtn.setTitle(nativeAd.data?.buttonText, for: .normal)
    nativeAd.delegate = self
    nativeAdRelatedView.refreshData(nativeAd)
    adLabel.text = nativeAdRelatedView.adLabel?.text

    if (nativeAd.data?.imageMode == BUFeedADMode.videoAdModeImage || nativeAd.data?.imageMode == BUFeedADMode.videoAdModePortrait) {
        //This is a video ad
        if let videoView = nativeAdRelatedView.videoAdView {
            let videoFrame = CGRect(x: 0, y: 0, width: containerView.frame.width, height: containerView.frame.height)
            videoView.frame = videoFrame
            addPangleLogo(parentView: videoView, nativeAdRelatedView: nativeAdRelatedView)
            containerView.addSubview(videoView)
        }
    } else {
        //This is an image ad
        if let url = URL(string: nativeAd.data?.imageAry.first?.imageURL ?? "") {
            if let data = try? Data(contentsOf: url) {
                let image = UIImage(data: data)
                let imageFrame = CGRect(x: 0, y: 0, width: containerView.frame.width, height: containerView.frame.height)
                let imageView = UIImageView.init(frame: imageFrame)
                imageView.contentMode = .scaleAspectFit
                imageView.image = image
                addPangleLogo(parentView: imageView, nativeAdRelatedView: nativeAdRelatedView)
                containerView.addSubview(imageView)
            }
        }
    }

    // register the button to be clickable    
    nativeAd.registerContainer(containerView, withClickableViews: [actionBtn])
}

func addPangleLogo(parentView: UIView, nativeAdRelatedView: BUNativeAdRelatedView) {
    //Pangle Logo
    if let pangleLogoView = nativeAdRelatedView.logoImageView {
        let logoSize:CGFloat = 15.0
        pangleLogoView.frame = CGRect(x:(parentView.frame.width - logoSize) , y:(parentView.frame.height - logoSize), width: logoSize, height: logoSize)
        parentView.addSubview(pangleLogoView)
    }
}

```



<a name="start/native_ad_template"></a>
## Template Native Ads

Create an **Template** ad in the app, you will get a **placement ID** for ad's loading.

<img src="native_template.png" alt="drawing" width="200"/>

<a name="start/native_ad_template"></a>
## Loading Ads

<a name="start/native_ad_template_loadevent"></a>
## Determining load events

<a name="start/native_ad_template_display"></a>
## Displaying Ads