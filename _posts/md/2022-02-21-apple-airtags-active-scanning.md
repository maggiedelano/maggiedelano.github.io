---
layout: post
title: "Enough with the beeping: Apple needs to enable active Bluetooth scanning on iOS to prevent stalking using Apple AirTags"
---

*Update 2022-02-21: After posting about this on [twitter](https://twitter.com/maggied/status/1495863458654568454), Glenn Fleishman [pointed out](https://twitter.com/maggied/status/1495885817671294978) that Apple has implemented this feature in a iOS 15.2 Beta, but it has not made it to production. This feature would enable active tracking on iOS using built-in features, but still prevent third parties from implementing similar services on iOS.*

[Apple AirTags](https://www.apple.com/airtag/) are small Bluetooth Low Energy tracking devices that can fit in a pocket or wallet. While AirTags are intended to be used to find misplaced and lost items, they also provide a cheap and incredibly effective way to track someone without their consent. AirTags use Apple’s [Find My Network](https://www.howtogeek.com/725842/what-is-apples-find-my-network/), enabled by default on Apple products like iPhones, iPads, and Mac computers, to update the network as to the device’s current location. However, unlike previous devices such as [Tile](https://www.thetileapp.com/en-us/), which had sporadic coverage even in urban areas, the widespread coverage of the Find My Network (i.e. any Apple device that has not actively opted out), means that these devices are perfect tools for [stalking](https://www.nytimes.com/2022/02/11/technology/airtags-gps-surveillance.html). 

While Apple included some protections [at launch](https://www.apple.com/newsroom/2021/04/apple-introduces-airtag/), these safety features could take days to trigger, and left Android users especially vulnerable as they were not able to receive notifications of AirTags traveling with them. Apple has since updated AirTag safety features, including [shortening](https://www.cnet.com/news/apple-updating-airtags-with-new-privacy-warnings-better-warning-sounds-smarter-find-my-tracking/) the length of time before an AirTag will make a sound, and releasing an active scanning app for Android called [Tracker Detect](https://play.google.com/store/apps/details?id=com.apple.trackerdetect). They have also [pledged](https://www.apple.com/newsroom/2022/02/an-update-on-airtag-and-unwanted-tracking/) a variety of improvements such as coupling an alert on iOS with a sound, and refining the unwanted tracker logic.

While pledged improvements should help in cases where someone is slipped an AirTag (for example in their purse or car), especially if unwanted tracker logic is improved, active scanning for Apple AirTags is important for anyone who suspects they are being tracked without their consent. This can help someone secure their immediate area as needed, rather than waiting around for a notification or innocuous set of beeps that an AirTag is traveling with them. Ironically, Apple’s emphasis on passive scanning for AirTags on iOS means that this is much easier to do using an Android phone right now than on iOS. My research shows that vital information about Apple AirTags is missing from software available to third party application developers on iOS, and Apple provides no active way to scan using their existing iOS safety features. In this post, I’ll cover the technical details behind why iOS users cannot actively scan for AirTags as Android users can, and discuss what Apple can do about it.

# Technical Details
Apple's existing safety features on iOS involve passive notifications that potentially suspicious Bluetooth devices are traveling with you. This means that there is currently no way to actively scan the area for AirTags without downloading additional software. One easy way to search for nearby BTLE devices is to use a freely available third party bluetooth scanning app like the [nRF Connect](https://www.nordicsemi.com/Products/Development-tools/nRF-Connect-for-mobile) app. The app displays all BTLE devices that are advertising nearby, and displays information such as the advertised name and information about who manufactured the device. A quick scan from my Philadelphia apartment showed over 100 devices, many of which could be clearly identified based on advertised name and manufacturer data, such as BTLE enabled speakers or other electronic devices owned by myself or neighbors, or even location trackers from other manufacturers like [Tile](https://www.thetileapp.com/en-us/) devices. AirTags are harder to find in scanning apps: they do not have an advertised name, but they do advertise they are Apple products. For some reason though, this advertisement data is not available on iOS. Here's what an unregistered AirTag looks like on the nRF Connect BTLE scanner, with a Sonos device from the neighborhood for comparison:

<img src="/images/AirTag_nRF.png" width="60%" class="center-image">

You can tell it's the AirTag due to the advertising frequency (about 33 ms as discussed in [this teardown](https://adamcatley.com/AirTag)) and the signal strength (-30 dBm is about as strong as you can get, suggesting it is in immediate proximity). Contrast this with the manufacturer data packet that is present for the Sonos device, but missing for the AirTag. (I have confirmed this with multiple AirTags, and it also holds for other Apple products such as my iPad, iPhone, and laptop.) AirTag advertisement data can be read on both Android and Mac and there are many dedicated Android AirTag scanner [apps](https://phonemantra.com/android-apps-to-find-airtags-and-ble-scanners/) available, including one from Apple called [Tracker Detect](https://play.google.com/store/apps/details?id=com.apple.trackerdetect). Something different is happening on iOS.

So what's going on? On iOS, advertisement data is received through the CBCentralManagerDelegate, in particular the didDiscoverPeripheral [function](https://developer.apple.com/documentation/corebluetooth/cbcentralmanagerdelegate/1518937-centralmanager). This function has an argument for the advertising data, which should theoretically include the Apple manufacturer information as well as the rest of the advertising packet. However, here's what we get for an AirTag compared with a Bose SoundLink (focus on the bolded advertisementData):

AirTag:
```
2021-08-27 14:36:49.372856-0400 BTLEViewer[371:11269] didDiscoverPeripheral success: <CBPeripheral: 0x280e99fe0, identifier = 90119C9F-BF45-6D3D-FB03-484D7400E5F1, name = (null), mtu = 0, state = disconnected>
2021-08-27 14:36:49.373259-0400 BTLEViewer[371:11269] advertisementData: {
 kCBAdvDataIsConnectable = 1;
 kCBAdvDataRxPrimaryPHY = 0;
 kCBAdvDataRxSecondaryPHY = 0;
 kCBAdvDataTimestamp = "651782209.370573";
}
2021-08-27 14:36:49.373396-0400 BTLEViewer[371:11269] RSSI: -16
```

Bose SoundLink:
```
2021-08-27 14:36:49.614290-0400 BTLEViewer[371:11269] didDiscoverPeripheral success: <CBPeripheral: 0x280e9dea0, identifier = 64AA7D5C-E332-13C7-A86F-57366CFA4146, name = LE-Bose Revolve SoundLink, mtu = 0, state = disconnected>
2021-08-27 14:36:49.614497-0400 BTLEViewer[371:11269] advertisementData: {
 kCBAdvDataIsConnectable = 1;
 kCBAdvDataLocalName = "LE-Bose Revolve SoundLink";
 kCBAdvDataManufacturerData = {length = 9, bytes = 0x01060206c575479a41};
 kCBAdvDataRxPrimaryPHY = 0;
 kCBAdvDataRxSecondaryPHY = 0;
 kCBAdvDataServiceUUIDs =     (
 FEBE
 );
 kCBAdvDataTimestamp = "651782209.612992";
 kCBAdvDataTxPowerLevel = 10;
}
2021-08-27 14:36:49.614562-0400 BTLEViewer[371:11269] RSSI: -82
```

Notice that the `kCBAdvDataManufacturerData`, `kCBAdvDataServiceUUIDs`, `kCBAdvDataTxPowerLevel` keys are not present for the AirTag, but are present for the SoundLink. I found a similar pattern regardless of which type of Apple product I scanned for (iPhone, iPad, Mac laptop). What's even more surprising is that this data is available on Mac (I used an app called [Bluetility](https://github.com/jnross/Bluetility)). Note the first four digits for both Apple devices are 0x4C00 which [identifies](https://www.bluetooth.com/specifications/assigned-numbers/company-identifiers/) these devices as Apple products. I cannot find any information about why these data would not be available on iOS.

AirTag:
```
identifier:     48146FE1-AEE9-4D11-BF84-B99459651C38
MAC:        F3-93-1C-96-6C-E8
Mfg Data:       0x4C000719050055100000011FC5DD0B37275463BD57B4E5F61EBB2C0A9C
```


My phone:
```
identifier:     6793236B-B9AC-4E4E-A5E2-22620D4CB84E
MAC:        A4-F1-E8-29-05-C2
Mfg Data:       0x4C001006161E38D32B41
Local Name: Margaret's iPhone
Tx Power:       12
```

Bose SoundLink:
```
identifier:     F45A2236-0E91-4571-A2EE-C60EE3123A61
MAC:        -
Mfg Data:       0x01060206C575479A41
Local Name: LE-Bose Revolve SoundLink
Service UUIDs:  FEBE
Tx Power:       10
```

# Potential Explanation
The most likely explanation for this is that Apple does not make this information available on iOS because it does not expect and/or want third parties interfacing with their devices. I did find a [blog post](https://developer.radiusnetworks.com/2013/10/21/corebluetooth-doesnt-let-you-see-ibeacons.html) from 2013 that stated that Apple does not allow you to see BTLE advertising data from iBeacons. So maybe this has changed to include all Apple devices. It’s also possible that the advertising data is used by other services running on the phone and then does not make its way to Core Bluetooth to be exposed to end users. This lack of manufacturer data does not prevent connection to AirTags or other Apple products using third party apps, though; it just makes it more difficult to find AirTags (phones and computers appear to be identifiable by their names).

# Implications
Apple’s anti-stalking measures for AirTags on iOS focus on background scanning for unwanted AirTags traveling with you. However, even with perfect detection logic (and it is certainly not perfect yet), there are scenarios where one would like to actively search the environment for the presence of unwanted tracking devices. Apple’s implementation of Core Bluetooth on iOS makes this very difficult to do. Scanning for BTLE devices and looking for devices with an Apple Manufacturer ID would be the easiest way to detect the presence of an unwanted AirTag traveling with you, allowing you to then physically search your area. However, without manufacturer data or an advertised name, AirTags can only be identified on iOS (without connecting to the device) based either on their proximity to the user or their long advertisement interval (2000 ms compared with a typical interval of 150-200 ms). While this is possible, it certainly isn't as reliable or unlikely to change as the manufacturer data is. One might be able to determine if a device is an AirTag by connecting to the suspected device and examining the discovered services and characteristics. In my limited testing these appear to be the same across two different AirTags. But as the workflow gets more complex, it gets less and less useful for people in crisis situations who may not have the needed technical expertise to navigate these systems. This means people have no choice but to rely on Apple’s built in security features and wait for them to improve them, rather than designing trauma-informed apps that meet the needs of end users today. 

Apple should take steps to make it easier to actively search for devices that might be used for stalking, rather than harder. This could include adding built-in location tracker scanning like they did with the Tracker Detect app (available only on Android), and/or updating the Core Bluetooth functionality for Apple devices to keep them in line with the advertisement data available for non-Apple devices. Apple should also consider securing the Find My Network in other ways to prevent misuse by third-party stealth AirTag clones, as discussed in [this writeup](https://positive.security/blog/find-you) from Positive Security. 

# Resources
If you are concerned about being stalked, you can contact the National Domestic Violence Hotline for help: 1-800-799-SAFE (7233), 1-800-787-3224 (TTY). There are also organizations working to combat tech abuse and support anyone being stalked or experiencing other forms of intimate and/or gender-based violence including:

- [National Network to End Domestic Violence](https://nnedv.org/) (NNEDV, US based)
- [Technology Enable Coercive Control Clinic](https://newbegin.org/find-help/staying-safe/technology-safety/) (TECC, based in King County, WA)
- [Clinic to End Tech Abuse](https://www.ceta.tech.cornell.edu/) (CETA, based in New York City)
- [Refuge Against Domestic Violence](https://www.refuge.org.uk/) (UK based)
- [Chayn](https://www.chayn.co/) (UK based, global focus)
- [Coalition Against Stalkerware](https://stopstalkerware.org/) 

 I’ve also written a [guide](/Bluetooth_Scanner_HowTo_v1.pdf) that you can use to scan your local environment for AirTags on iOS or Android.

# Acknowledgements
 
 Thank you to Toby Shulruff and Kendra Albert for their help with the research and writing for this article. 


<style>
.center-image
{
    margin: 0 auto;
    display: block;
}
</style>