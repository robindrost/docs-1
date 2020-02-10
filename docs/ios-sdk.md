# Livery iOS SDK

Ex Machina Group Livery iOS SDK, published as CocoaPods spec on Github: [livery-sdk-ios-podspec](https://github.com/exmg/livery-sdk-ios-podspec).

Documentation can be found at: [docs.liveryvideo.com/ios-sdk](https://docs.liveryvideo.com/ios-sdk).

More information can be found at: [liveryvideo.com](https://liveryvideo.com).

## Installation

### Compatibility

The Livery iOS SDK is compatible with iOS 10 of higher.

## Usage

For basic usage of the SDK following minimal steps are needed:

- Initialize the SDK (See SDK `initialize()` method below)
- Create playerOptions at least with a source URL of a DASH manifest.
- Create a player object with this playerOptions (See SDK `createPlayer()` method below and playerOptions for a full list of supported options)
- Set a UIView for the player to render into. (See Player Method `setView()`  method below)
- `Player.play()`

At this point, the player will fetch the DASH manifest, start rendering by automatically synching to the targetLatency set in playerOptions (or 3 seconds default.)
For more information about options, properties and methods please see relevant sections below.
For a sample application code utilizing these minimal steps see the LiveryExample sample application section.

### LiverySdk

#### Methods

| Name  | Returns   | Description                                                   |
|---|----|----|
|`createPlayer(playerOptions)` |`Player`|Create a Live Player by combining specified options with the initialize() config.|
|`initialize(configUrl)` |`boolean`|Livery SDK initialization method.|

#### SDK Initialize

The SDK `initialize()` method has to be called on an sdk instance before Live Players are created.

```swift
let liveSDK = Livery()
liveSDK.initialize(configUrl: nil, onSuccess: () -> (), onError: (String?) -> ())
```

There are basically two ways to initialize the SDK. With or Without a remote configuration url.
If a remote configuration url `configUrl` is set and remote configuration fails to be fetched, the SDK will fail to initialize.

Second option is to initialize without a `configUrl` and give the necessary options to the player via `playerOptions`.

The values `onSuccess` and `onError` are functions called when the player successfully initialised or failed respectively. In case of an error, `onError` returns an optional localized error string.
For more info about the supported `playerOptions` see relevant sections below.

#### SDK Create Player

The SDK `createPlayer()` method depends on the `initialize()` method having been called . If no options are given `createPlayer()` will throw an error.

`createPlayer()` in IOS SDK expects a `playerOptions` class and returns a Player instance

```swift
let options = playerOptions()
 options.autoplay = false
 /* Giving a source is mandatory */
 options.sources = ["https://akamaibroadcasteruseast.akamaized.net/cmaf/live/657078/akasource/out.mpd"]
 options.targetLatency = 3

 /* Create the player */
 player = liveSDK.createPlayer(options: options)
```

## Remote Config

Customer project environment specific remote configs in json format with properties described below can be used by the SDK to facilitate experiments and tweaking by Ex Machina.

Below are sample config files with player sources property listed.

- [https://media-player-demo.playtotv.com/live/exmg-testbox1.json](https://media-player-demo.playtotv.com/live/exmg-testbox1.json)

- [https://media-player-demo.playtotv.com/live/exmg-testbox2.json](https://media-player-demo.playtotv.com/live/exmg-testbox2.json)

### Advanced Config

These are the options that can be specified under `advanced`:

#### Analytics Config

Amazon AWS Pinpoint Analytics options should be specified under `advanced.analytics`:

|Name|Type|Default|Description|
|--|--|--|--|
|`customerId`|`String`|`<required>`|Ex Machina Customer ID.|

#### Stall Config

Stall recovery options can be specified under `advanced.stall`:

|Name|Type|Default|Description|
|--|--|--|--|
|`minRecoveryDelay`|`Number`|`10`|Minimum delay in seconds before starting automatic recovery.|
|`maxRecoveryDelay`|`Number`|`90`|The maximum delay in seconds between subsequent recovery attempts.|

#### Sync Config

Sync options can be specified under `advanced.sync`:

|Name|Type|Default|Description|
|--|--|--|--|
|`targetLatency`|`Number`|`3`|Target live latency in seconds. If 0 then syncing is disabled.|

## Player Options

These are the options to be passed to Players:

|Name|Type|Default|Description|
|--|--|--|--|
|`autoplay`|`Boolean`|`false`|If true then automatically begin playback as soon as possible. Note: In browsers this will not work unless muted is true.|
|`muted`|`Boolean`|`false`|If true then the audio will initially be silenced.|
|`targetLatency`|`Integer`|`3`|Target live latency in seconds. If 0 then syncing is disabled.|
|`sources`|`String[]`|`<required>`|Array of media source URLs from which the first that can be played will be selected.|
|`fit`|`enum playerFit`|`.contain`|Determines how the video will be scaled and cropped. See Section Player Fitting.|
|`minRecoveryDelay`|`Integer`|`10`|Minimum delay in seconds before starting automatic recovery.|
|`maxRecoveryDelay`|`Integer`|`90`|Maximum delay in seconds between subsequent recovery attempts.|

### Source Protocols

The source protocol must be DASH for IOS SDK

|Extension|Protocol|
|--|--|
|.mpd|DASH|

## Player Properties

The following properties are exposed by Live Player instances:

|Name|Type|Write|Description|
|--|--|--|--|
|`activeQuality`|`Number`|No|Index of active quality.| Emits: `activeQualityChange`.|
|`advanced`|`AdvancedProperties`|No|Advanced properties.|
|`currentSrc`|`String`|No|Current media source URL.|
|`currentTime`|`Number`|Yes|Current playback time position in seconds.Emits: `timeUpdate`.|
|`duration`|`Number`|No|Media duration in seconds. Used in `onDemand`.Emits: `durationChange`.|
|`error`|`Error`|No|Most recent unrecovered error.Emits: error, recovered.|
|`muted`|`Boolean`|Yes|If true then audio is muted. Emits: `volumeChange`.|
|`playbackRate`|`Number`|Yes|Playback rate (1 is normal).Emits: `rateChange`.|
|`playbackState`|`String`|No|Playback state. BUFFERING / ENDED / FAST_FORWARD / PAUSED / PLAYING / REWIND / SEEKING / SLOW_MO Emits: `playbackChange`.|
|`selectedQuality`|`Number`|Yes|Index of selected quality. Emits: selectedQualityChange.|
|`streamType`|`String`|No|Stream type. LIVE / ONDEMAND / UNKNOWN Computed: duration == Infinite => LIVE, Finite => ONDEMAND and NaN => UNKNOWN Changes on: `durationChange`.|
|`volume`|`Number`|Yes|Audio volume, from 0.0 (silent) to 1.0 (loudest).Emits: `volumeChange`.|

## Player Methods

The following methods are exposed by Player instances:

|Name|Description|
|--|--|
|`constructor(options)`|Construct player with specified options.|
|`dispose()`|Cleanup all resources and stop playback.|
|`load()`|Resets the media and selects the best available source again.|
|`setView(view: UIView)`|Set the view for player to render.|
|`stop()`|Stop media playback.|
|`play()`|Start/resume media playback.|
|`updateOptions(playerOptions)|Update player options|
|`onApplicationDidBecomeActive()`|Call in your IOS Application Lifecycle callback`applicationDidBecomeActive()`|
|`onApplicationWillResignActive()`|Call in your IOS Application Lifecycle callback`applicationWillResignActive()`|
|`onApplicationDidEnterBackground()`\|Call in your IOS Application Lifecycle callback`applicationDidEnterBackground()`|
|`onApplicationWillEnterForeground()`|Call in your IOS Application Lifecycle callback`applicationWillEnterForeground()`|

## Player Events

The following events can be emitted by Player instances via Notification center:

|Name|Description|Properties|
|--|--|--|
|`activeQualityChange`|Emitted when activeQuality has changed.|`activeQuality`|
|`error`|Emitted when an error occurs with a reference to the platform specific Error instance.|`error`|
|`playbackChange`|Emitted when playbackState has changed.|`playbackState`|
|`qualitiesChange`|Emitted when qualities have changed.|`qualities`|
|`selectedQualityChange`|Emitted when selectedQuality has changed.|`selectedQuality`|
|volumeChange`|Emitted when volume or muted has changed.|`muted`|
|`volume`|See MediaPlayerLiveExample app to see how playback state change events are received via Notification center and check the below list for Notification names|

Below are the names for the Notifications to listen to

```swift
Notification.Name {
 static  var activeQualityChange: Notification.Name {
  return .init(rawValue: "Livery.activeQualityChange")
  }
 static  var error: Notification.Name {
  return .init(rawValue: "Livery.error")
  }
 static  var networkChange: Notification.Name {
  return .init(rawValue: "Livery.networkChange")
  }
 static  var playbackChange: Notification.Name {
  return .init(rawValue: "Livery.playbackChange")
  }
 static  var progress: Notification.Name {
  return .init(rawValue: "Livery.progress")
  }
 static  var qualitiesChange: Notification.Name {
  return .init(rawValue: "Livery.qualitiesChange")
  }
 static  var selectedQualityChange: Notification.Name {
  return .init(rawValue: "Livery.selectedQualityChange")
  }
 static  var volumeChange: Notification.Name {
  return .init(rawValue: "Livery.volumeChange")
  }
}
```

## Playback Synchronization

The player will receive the millisecond precision server time from the time urls (Default is <https://time.akamai.com?ms> ) and start the Audio and Video decoder clocks timebase as

`currentTime - targetLatency`

In other words, IOS SDK players behave always in sync once initialized, meaning if audio packages or video frames are received later then their presentation timestamps due the network connections, they will be ignored or played back in a faster rate by the player.

## ABR (Adaptive BitRate)

Current version players just select the highest quality video for ABR

## Error Handling

To be updated with recovery options implementation in v 1.0.0

## Life Cycle

Your application needs to call the corresponding player methods for each IOS Application LifeCycle callback

```swift
public  func onApplicationDidBecomeActive()
public  func onApplicationWillResignActive()
public  func onApplicationDidEnterBackground()
public  func onApplicationWillEnterForeground()
```

## Player Stall Recovery

When playback stalls (e.g: playbackState BUFFERING) for longer than calculated ​timeoutSeconds automatic recovery is attempted through calling the Player `load()` method.

The timeoutSeconds calculation is based on an exponential backoff algorithm. You can determine the maximum and minimum thresholds via player options `minRecoveryDelay` and `maxRecoveryDelay` respectively.

## Player Fitting

The SDK provides fitting methods to scale and crop the video according to the specified ​fit​ option value.

```swift
public  enum playerFit {
 case contain
 case cover
 case fill
 case scaledown
}
```

|Name|Description|
|--|--|
|`.contain`|The video will be scaled to fit in the view while maintaining the aspect ratio.(equivalent to ```swift videoGravity = .resizeAspect```)
|`.cover`|The video will be scaled to fill the entire view while maintaining the aspect ratio. If the object's aspect ratio does not match the aspect ratio of its box, then the object will be cropped to fit. (equivalent to ```swift videoGravity = .resizeAspectFill```)|
|`.fill`|T​he video will be scaled to fill the entire view. The aspect ratio is ignored so the video will be stretched to the size of the view.(equivalent to ```swift videoGravity = .resize``` )|
|`.scaledown`|Same as .contain for now|

## Player Skin

### Overlays

There are two types of overlays for the default player skin.

![Stall overlay](https://lh3.googleusercontent.com/MCPb2cLtFyhzDx56gg9iS4qQzt_HPf9CIiFyQL7d6BYDWG9ncFY3g8vXKoGaxYcjieCNu7v686sveLVK41bwhRdV5ULaEF1Jz0ECN1X9gStg5SwiDy8_PzOlGI_6-2ShuHx1S4FD)

This overlay will become active on Player stall error and will be inactive when automatic recovery is attempted (or when Reload button is pressed by the user)

![Buffering overlay](https://lh4.googleusercontent.com/OQ1n2rMp4QtOF0Ym10IlSOilb3VshRf9_1lkLVcZu2wJzaySg-M5cbtzj813Ni8NL4vJPQxLjeqEhnLySGyFS1vf8nu8d2xeHSFlMg32SlfLIBt40l4Depbeu3kOraM034UK1XEF)

This overlay is active as long as ​playbackState​ is ​BUFFERING

## Analytics

Our SDK makes use of [Amazon Pinpoint](https://aws.amazon.com/pinpoint/).

For now, our SDK reports events to preset Exmachina AWS backend (i.e. appId , region and identityPoolId cannot be changed)

Only the customerId property of `advanced.analytics` configuration are used for the necessary Pinpoint configuration.

The `customerId` will be added to all pinpoint events as a property.

In future releases, the appId, region and `identityPoolId` properties of `advanced.analytics` configuration will be used for the necessary Pinpoint configuration.
Notice dependency to external AWSPinpoint SDK changes the build instructions for your custom app.

Please check MediaPlayerLiveExample App and custom player build instructions for managing AWS Pinpoint dependency via the sample CocoaPod Podfile

If you are using AWSPinpoint in your own application analytics, AWSPinpoint version must be the same with the SDK

![Pinpoint version](https://lh3.googleusercontent.com/BPTm7yTMeRQy0CBBg09B1B_y5l_te-DayCdM1AT8DTl8xZvL2D1vscFNhoYb548Z9jmiLhFT7N8B1Ckjuqp0gIuCGBRn9ihnJYcB6Koo1KRogrUYJtGN1qI2XPBcK9xBBn1A2_Iu)

## Sample MediaPlayerLiveExample App

Notes:

- The release contains a sample application to demonstrate basic SDK usage.
- The Application requires Xcode Version 10.2.1 and Swift Version 5 (Since the Livery SDK is built with Swift5 )
- You will need to reset the bundle identifier and signage settings with your own Team in order to build and run the app

Please check the framework and project setup within the sample app if you are developing your own application from scratch.

## Developing Your Own Application From Scratch

If you are developing your own application from scratch please make sure

- You use Xcode Version 10.2.1 with Swift5 support . SDK binary is built with Swift5
- You set minimum IOS DEPLOYMEN_TARGET at least 12.0
- You remove existing copies of framework and libdash_ios.framework from your dev environment
- You add new versions of both Livery SDK as well as libdash_ios.framework (released seperately now) to your Xcode project
- You link with both Livery SDK as well as libdash_ios.framework (released seperately now) and add both to Embedded binaries section

libdash_ios.framework is an internal dependency of the framework but you will still need to add it to Embedded Binaries section with Signing enabled on Copy to be able to deploy on a device (to avoid getting signing related errors)

In the upcoming cocoapod SDK release, you will just add it as a cocoapod dependency

See below how this is done in the sample application

![libdash as Cocoapod dependency](https://lh5.googleusercontent.com/Z9sxhp9n2F8zUeEL1WRnXc1ZsEzpUuBmVzwPHnUfLGNB582Nk0Reh-fUdPn-6VazohEKIiJMngaEsxZYzgbo5VHK6EQYrtRFGgsFwtn4DKH9MREMl8e66Nv7ODEYsRSu6Vi6yBkt)

### AWSPinpoint Analytics dependencies

- The `Podfile` that you configure to install the AWS Pinpoint SDK must contain:
![Pinpoint in podfile](https://lh3.googleusercontent.com/BPTm7yTMeRQy0CBBg09B1B_y5l_te-DayCdM1AT8DTl8xZvL2D1vscFNhoYb548Z9jmiLhFT7N8B1Ckjuqp0gIuCGBRn9ihnJYcB6Koo1KRogrUYJtGN1qI2XPBcK9xBBn1A2_Iu)

- Run `pod install --repo-update` before you continue
- From now on use YOUR_PROJECT_NAME. xcworkspace file in Xcode to open your project
- Finally import Livery to use the Framework in your codebase

## App Store binary validation

SDK release includes different builds of two binaries under 2 folders.

universal/

- libdash_ios.framework
- Livery.framework

iphoneos/

- libdash_ios.framework
- Livery.framework

Universal binaries include both x86 and arm builds (i.e. fatlibs). This is for easy development setup and testing with the simulator.

These binaries will not pass App Store validation since they include x86 architecture.
You must use binaries under iphoneos/ folder, test your app once more on the device and then submit your product archive to the app store

### Too many symbols warning in AppStore validation

Too many symbols error is reported when CocoaPod does not remove arm7 architecture from dependency Frameworks (i.e. Currently AWSPinpoint and AWSCore) . Apple reports this as a warning because for targets >=12.0 only needed architecture for IOS is arm64

This is not a blocker for distribution, however if you want to solve the warning please add the following lines to your App’s Podfile before pod install.

```swift
post_install do |installer|
 installer.pods_project.build_configuration_list.build_configurations.each do |configuration|
 configuration.build_settings['ARCHS'] = 'arm64'
 end
end
```

Please see example MediaPlayerLive example app’s Podfile and check [https://github.com/CocoaPods/CocoaPods/issues/7111](https://github.com/CocoaPods/CocoaPods/issues/7111) for more information.

## Known Issues

Also universal SDK release is a fat binary (including x86 simulator an arm64 architectures) for easy development with simulator and on device.
Check section above for app store validation

## Revission History

|Version|Description|
|--|--|
|0.9.6|Fixed issues with having to use a device or simulator version. You can now use the universal build. Made initialize call asynchronous, so it doesn’t block the main thread. The onSuccess and onError functions are called when initialisation is completed. Library only works with Xcode 11 now, since it is compiled in Swift 5.1.|
