# Flutter App Tracking

This Flutter plugin allows you to display ios 14+ tracking authorization dialog and request permission to collect data. Collected data is crucial for ad networks (ie admob) to work efficiently on ios 14+ devices.

## Introduction

Starting in iOS 14.0 App Tracking Transparency framework is available so we can present the app-tracking authorization request to the end user.

Starting in iOS 14.5, IDFA will be unavailable until an app calls the App Tracking Transparency framework to present the app-tracking authorization request to the end user. If an app does not present this request, the IDFA will automatically be zeroed out which may lead to a significant loss in ad revenue.

On iOS >=14.0 <14.5, tracking authorization request dialog isn't required in order to get IDFA. Keep in mind that, in those iOS versions, if you ask for it and the user rejects it you will lose access to IDFA (see tests).

This plugin lets you display App Tracking Transparency authorization request and ask for permission.

## Usage

Step 1:

Make sure you update Info.plist file located in ios/Runner directory and add the NSUserTrackingUsageDescription key with a custom message describing your usage.

   ```
   <key>NSUserTrackingUsageDescription</key>
<string>This identifier will be used to deliver personalized ads to you.</string>
```

Step 2:

Google recommends that you should be using Google Mobile Ads SDK 7.64.0 or higher. The Google Mobile Ads SDK supports conversion tracking using Apple's SKAdNetwork, which means Google is able to attribute an app install even when IDFA is unavailable. This is a crucial step if you want to maximize your ad revenue when IDFA is not available. To enable this functionality, you will need to update the SKAdNetworkItems key with an additional dictionary in your Info.plist, check them here.

## Example


   ```
   // Import package
import 'package:app_tracking_transparency/app_tracking_transparency.dart';

// Show tracking authorization dialog and ask for permission
final status = await AppTrackingTransparency.requestTrackingAuthorization();

// Now you can safely initialize admob and start to show ads. Admob should use
// advertising identifier automatically.
// FirebaseAdMob.instance.initialize(...)
```

Google (admob) recommends implementing an explainer message that appears to users immediately before the consent dialogue. For additional info on this topic please refer to this article: https://support.google.com/admob/answer/9997589?hl=en

   ```
   // If the system can show an authorization request dialog
if (await AppTrackingTransparency.trackingAuthorizationStatus ==
    TrackingStatus.notDetermined) {
  // Show a custom explainer dialog before the system dialog
  await showCustomTrackingDialog(context);
  // Wait for dialog popping animation
  await Future.delayed(const Duration(milliseconds: 200));
  // Request system's tracking authorization dialog
  await AppTrackingTransparency.requestTrackingAuthorization();
}
```

The explainer dialog must not contain a "Decide later" button. It should contain only a "Continue" button and the system dialog must be shown after the explainer dialog. See this issue for details.

You can also get advertising identifier after authorization. Until a user grants authorization, the UUID returned will be all zeros: 00000000-0000-0000-0000-000000000000. Also note, the advertisingIdentifier will be all zeros in the Simulator, regardless of the tracking authorization status.

   ```
   final uuid = await AppTrackingTransparency.getAdvertisingIdentifier();
   ```
