# Livery iOS SDK

Ex Machina Group Livery iOS SDK, published as CocoaPods spec on Github: [livery-sdk-ios-podspec](https://github.com/exmg/livery-sdk-ios-podspec).

Documentation can be found at: [docs.liveryvideo.com/ios-sdk](https://docs.liveryvideo.com/ios-sdk).

More information can be found at: [liveryvideo.com](https://liveryvideo.com).

## Installation

### Compatibility

The Livery iOS SDK is compatible with iOS 12 of higher.

### Configuration

To install Livery SDK into your project, follow these steps below.

You should request credentials from Ex Machina, then place them in your ~/.netrc as follows: (create if necessary)

```
machine sdk-ios-binaries.liveryvideo.com
  login YOUR_USERNAME
  password YOUR_PASSWORD
```

#### CocoaPods

[CocoaPods](https://cocoapods.org/) is a dependency manager for Swift and Objective-C Cocoa projects. You can install it with the following command:

```
$ gem install cocoapods
```

To integrate Livery SDK into your Xcode project using CocoaPods, specify it in your Podfile, as follows:

```ruby
source 'https://github.com/CocoaPods/Specs.git'
source 'https://github.com/exmg/livery-sdk-ios-podspec.git'

target 'MyProject' do
  pod "Livery", "0.10.3"
end
```

Then run

```
$ pod install
```

## Usage

For basic usage of the SDK following minimal steps are needed:

```swift
import Livery

@IBOutlet weak var playerView: UIView!

var player: Player?
let liveSDK = LiverySDK()

// 1. Initialize the SDK (See SDK initialize() method below for more options)
liveSDK.initialize(streamId: "yourStreamId", completionQueue: .main) { result in
    switch result {
    case .success(let config):
      // 2. Create playerOptions at least with a source URL of a DASH manifest
      let options = playerOptions()
      options.autoplay = false
      options.sources = ["yourSourceUrl"]
      options.fit = .contain

      // 3. Create a player object with this playerOptions
      // (See SDK createPlayer() method below and playerOptions for a full list of supported options)
      self.player = self.liveSDK.createPlayer(options: options)
      if let player = self.player {
        // 4. Set a UIView for the player to render into. (See Player Method setView() method below)
        player.setView(view: self.playerView)
      }

      // 5. The player is now ready to play
      play()

    case .failure(let error):
      // deal with the initialization error here
    }
}

func play() {
  player?.play(completionQueue: .main) { result in
    switch result {
      case .success:
        // player is now playing

      case .failure(let error):
        // deal with the play error here
      }
  }
}
```

At this point, the player will fetch the DASH manifest, start rendering by automatically synching to the targetLatency set in playerOptions (or 3 seconds default). For more information about options, properties and methods please see relevant sections below.
For a sample application code utilizing these minimal steps see the LiveryExample sample application section.

### Livery SDK

#### Methods

| Name                                                | Returns  | Description                                                                       |
| --------------------------------------------------- | -------- | --------------------------------------------------------------------------------- |
| `initialize(streamId, completionQueue, completion)` | `void`   | Livery SDK initialization method.                                                 |
| `createPlayer(playerOptions)`                       | `Player` | Create a Live Player by combining specified options with the initialize() config. |

#### SDK Initialize

The SDK `initialize()` method has to be called on an sdk instance before Live Players are created.

```swift
let liveSDK = LiverySDK()
liveSDK.initialize(streamId: String, completionQueue: DispatchQueue = .main, completion: (LiverySDK.ResultConfig) -> Void)
```

The streamId is used to determine the remote configuration url and is fetched from it. If the remote configuration fails to be fetched the SDK will fail to initialize.

The `completionQueue` defines the `DispatchQueue` in which the `completion` callback is called.

The `completion` callback is a block that receives a `LiverySDK.ResultConfig` with the SDK initialization result.

- In case of success it contains the fetched [`LiveryConfig`](#config).
- In case of an error it contains an error of type `LiverySDK.Errors`.

##### SDK Initialization Result

```swift
extension LiverySDK {
  typealias ResultConfig = Swift.Result<LiveryConfig, Errors>
}
```

##### SDK Initialization Errors

```swift
extension LiverySDK {
  enum Errors : Error {
    case invalidURL
    case invalidServerTime
    case downloadError(_ error: Error)
    case missingData
    case invalidConfig(_ error: Error)
  }
}
```

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

| Name         | Type     | Default      | Description             |
| ------------ | -------- | ------------ | ----------------------- |
| `customerId` | `String` | `<required>` | Ex Machina Customer ID. |

#### Stall Config

Stall recovery options can be specified under `advanced.stall`:

| Name               | Type     | Default | Description                                                        |
| ------------------ | -------- | ------- | ------------------------------------------------------------------ |
| `minRecoveryDelay` | `Number` | `10`    | Minimum delay in seconds before starting automatic recovery.       |
| `maxRecoveryDelay` | `Number` | `90`    | The maximum delay in seconds between subsequent recovery attempts. |

#### Sync Config

Sync options can be specified under `advanced.sync`:

| Name            | Type     | Default | Description                                                    |
| --------------- | -------- | ------- | -------------------------------------------------------------- |
| `targetLatency` | `Number` | `3`     | Target live latency in seconds. If 0 then syncing is disabled. |

### Interactive Config

Interactive options can be specified under `advanced.interactive`:

| Name  | Type   | Default | Description                |
| ----- | ------ | ------- | -------------------------- |
| `url` | `URL?` | `nil`   | The interactive layer URL. |

### Config

class **_LiveryConfig_**

| Name       | Type             | Description                                                                                   |
| ---------- | ---------------- | --------------------------------------------------------------------------------------------- |
| `sources`  | `String[]`       | Array of media source URLs from which the first that can be played will be selected.          |
| `advanced` | `advancedConfig` | Advanced options.                                                                             |
| `autoplay` | `Bool`           | Determines whether video shall play immediately after [`createPlayer`](#sdk-create-player).   |
| `fit`      | `String`         | Determines how the video will be scaled and cropped. See: [`Player Fitting`](#player-fitting) |
| `muted`    | `Bool`           | Determines whether media should be muted or not.                                              |

## Player Options

These are the options to be passed to Players:

| Name               | Type             | Default      | Description                                                                          |
| ------------------ | ---------------- | ------------ | ------------------------------------------------------------------------------------ |
| `autoplay`         | `Boolean`        | `false`      | Determines whether video shall play immediately after createPlayer.                  |
| `muted`            | `Boolean`        | `false`      | Determines whether media should be muted or not.                                     |
| `targetLatency`    | `Integer`        | `3`          | Target live latency in seconds. If 0 then syncing is disabled.                       |
| `sources`          | `String[]`       | `<required>` | Array of media source URLs from which the first that can be played will be selected. |
| `fit`              | `enum playerFit` | `.contain`   | Determines how the video will be scaled and cropped. See Section Player Fitting.     |
| `minRecoveryDelay` | `Integer`        | `10`         | Minimum delay in seconds before starting automatic recovery.                         |
| `maxRecoveryDelay` | `Integer`        | `90`         | Maximum delay in seconds between subsequent recovery attempts.                       |

### Source Protocols

The source protocol must be DASH for IOS SDK

| Extension | Protocol |
| --------- | -------- |
| .mpd      | DASH     |

## Player Properties

The following properties are exposed by Live Player instances:

| Name                | Type                                          | Write | Description                                                                                                                                        |
| ------------------- | --------------------------------------------- | ----- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `activeQuality`     | [`Quality`](#player-media-quality)            | No    | Active Media Quality. Emits: `activeQualityChange`.                                                                                                |
| `advanced`          | `AdvancedProperties`                          | No    | Advanced properties.                                                                                                                               |
| `currentSrc`        | `String`                                      | No    | Current media source URL.                                                                                                                          |
| `currentTime`       | `Number`                                      | Yes   | Current playback time position in seconds. Emits: `timeUpdate`.                                                                                    |
| `duration`          | `Number`                                      | No    | Media duration in seconds. Used in `onDemand`. Emits: `durationChange`.                                                                            |
| `error`             | `Error`                                       | No    | Most recent unrecovered error. Emits: error, recovered.                                                                                            |
| `muted`             | `Boolean`                                     | Yes   | If true then audio is muted. Emits: `volumeChange`.                                                                                                |
| `latency`           | `Number`                                      | No    | End to end latency in milliseconds.                                                                                                                |
| `buffer`            | `Number`                                      | No    | Size of buffer, ahead of current position, in milliseconds.                                                                                        |
| `playbackRate`      | `Number`                                      | Yes   | Playback rate (1 is normal).Emits: `rateChange`.                                                                                                   |
| `playbackState`     | `String`                                      | No    | Playback state. BUFFERING / ENDED / FAST_FORWARD / PAUSED / PLAYING / REWIND / SEEKING / SLOW_MO Emits: `playbackChange`.                          |
| `selectedQuality`   | [`Quality`](#player-media-quality)            | Yes   | Selected Media Quality. Emits: `selectedQualityChange`.                                                                                            |
| `volume`            | `Number`                                      | Yes   | Audio volume, from 0.0 (silent) to 1.0 (loudest). Emits: `volumeChange`.                                                                           |
| `streamType`        | `String`                                      | No    | Stream type. LIVE / ONDEMAND / UNKNOWN Computed: duration == Infinite => LIVE, Finite => ONDEMAND and NaN => UNKNOWN Changes on: `durationChange`. |
| `timeOffset`        | `Number`                                      | No    | Local device time offset in milliseconds.                                                                                                          |
| `qualities`         | `[Quality]`                                   | No    | Array of Available Media Qualities. Emits: `qualitiesChange`.                                                                                      |
| `branded`           | `Boolean`                                     | Yes   | If true the Livery branding is visible (loading indicator, info dialog, etc).                                                                      |
| `customLoadingView` | `UIView`                                      | Yes   | Custom loading indicator view                                                                                                                      |
| `customControlView` | [`LiveryPlayerControlView`](#player-controls) | Yes   | Custom player controls view                                                                                                                        |
| `customErrorView`   | [`LiveryPlayerErrorView`](#error-overlay)     | Yes   | Custom error view                                                                                                                                  |

## Player Methods

The following methods are exposed by Player instances:

| Name                                                                                | Description                                                                        |
| ----------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| `setView(view: UIView)`                                                             | Set the view for player to render.                                                 |
| `play(completionQueue: DispatchQueue = .main, completion: (Player.Result) -> Void)` | Async call to start/resume media playback.                                         |
| `play() -> Player.Result`                                                           | Sync call to start/resume media playback.                                          |
| `pause(completionQueue: DispatchQueue = .main, completion: @escaping () -> Void)`   | Async call to pause media                                                          |
| `stop(completionQueue: DispatchQueue = .main, completion: @escaping () -> Void)`    | Async call to stop media playback.                                                 |
| `stop()`                                                                            | Sync call to stop media playback.                                                  |
| `load()`                                                                            | Resets the media and selects the best available source again.                      |
| `updateOptions(playerOptions)`                                                      | Update player options                                                              |
| `onApplicationDidBecomeActive()`                                                    | Call in your IOS Application Lifecycle callback `applicationDidBecomeActive()`     |
| `onApplicationWillResignActive()`                                                   | Call in your IOS Application Lifecycle callback `applicationWillResignActive()`    |
| `onApplicationDidEnterBackground()`                                                 | Call in your IOS Application Lifecycle callback `applicationDidEnterBackground()`  |
| `onApplicationWillEnterForeground()`                                                | Call in your IOS Application Lifecycle callback `applicationWillEnterForeground()` |
| `dispose()`                                                                         | Cleanup all resources and stop playback.                                           |

## Player Events

The following events can be emitted by Player instances via Notification center:

| Name                    | Description                                                                            | Properties         |
| ----------------------- | -------------------------------------------------------------------------------------- | ------------------ |
| `activeQualityChange`   | Emitted when activeQuality has changed.                                                | `activeQuality`    |
| `error`                 | Emitted when an error occurs with a reference to the platform specific Error instance. | `error`            |
| `playbackChange`        | Emitted when playbackState has changed.                                                | `playbackState`    |
| `qualitiesChange`       | Emitted when qualities have changed.                                                   | `qualities`        |
| `selectedQualityChange` | Emitted when selectedQuality has changed.                                              | `selectedQuality`  |
| `volumeChange`          | Emitted when volume or muted has changed.                                              | `muted` , `volume` |
| `bandwidthTest`         | Emitted when trying to upgrade video bandwidth.                                        |
| `didTapFullscreen`      | Emitted when user tapped full screen button.                                           | `player`           |

See MediaPlayerLiveExample app to see how playback state change events are received via Notification center and check the below list for Notification names

Below are the names for the Notifications to listen to

```swift
Notification.Name {
  static var activeQualityChange: Notification.Name {
    return .init(rawValue: "Livery.activeQualityChange")
    }
  static var error: Notification.Name {
    return .init(rawValue: "Livery.error")
    }
  static var networkChange: Notification.Name {
    return .init(rawValue: "Livery.networkChange")
    }
  static var playbackChange: Notification.Name {
    return .init(rawValue: "Livery.playbackChange")
    }
  static var progress: Notification.Name {
    return .init(rawValue: "Livery.progress")
    }
  static var qualitiesChange: Notification.Name {
    return .init(rawValue: "Livery.qualitiesChange")
    }
  static var selectedQualityChange: Notification.Name {
    return .init(rawValue: "Livery.selectedQualityChange")
    }
  static var volumeChange: Notification.Name {
    return .init(rawValue: "Livery.volumeChange")
    }
  static var bandwidthTest: Notification.Name {
    return Notification.Name("Livery.bandwidthTest")
  }
  static var didTapFullscreen: Notification.Name {
    return Notification.Name("Livery.didTapFullscreen")
  }
}
```

### Player Types

```swift
extension Player {
  typealias Result = Swift.Result<Void, Player.Errors>
}

extension Player {
  public enum Errors : Error {
    case invalidRenderView
    case alreadyPlaying
    case invalidPlayerOptions
    case invalidPlayerOptionsSources
    case invalidAudioSource
    case invalidVideoSources
  }
}

extension Player {
  enum PlaybackState : String {
    case none = ""
    case buffering = "Buffering"
    case playing = "Playing"
    case paused = "Paused"
    case ended = "Ended"
  }
}
```

### Player Media Quality

class **_Quality_**

| Name        | Type      | Description                      |
| ----------- | --------- | -------------------------------- |
| `bitrate`   | `Integer` | Bitrate of the quality.          |
| `codecs`    | `String`  | Codec of the media.              |
| `frameRate` | `Float`   | Frame rate of the media.         |
| `label`     | `String`  | Human readable name of quality.  |
| `qualityId` | `String`  | ID of quality.                   |
| `track`     | `String`  | Track type of media. i.e.: video |
| `width`     | `Integer` | Width of the media.              |
| `height`    | `Integer` | Height of the media.             |

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
public func onApplicationDidBecomeActive()
public func onApplicationWillResignActive()
public func onApplicationDidEnterBackground()
public func onApplicationWillEnterForeground()
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

| Name         | Description                                                                                                                                                                                                                                                    |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `.contain`   | The video will be scaled to fit in the view while maintaining the aspect ratio.(equivalent to `swift videoGravity = .resizeAspect`)                                                                                                                            |
| `.cover`     | The video will be scaled to fill the entire view while maintaining the aspect ratio. If the object's aspect ratio does not match the aspect ratio of its box, then the object will be cropped to fit. (equivalent to `swift videoGravity = .resizeAspectFill`) |
| `.fill`      | T​he video will be scaled to fill the entire view. The aspect ratio is ignored so the video will be stretched to the size of the view.(equivalent to `swift videoGravity = .resize` )                                                                          |
| `.scaledown` | Same as .contain for now                                                                                                                                                                                                                                       |

## Player Skin

### Overlays

There are two types of overlays for the default player skin.

![Stall overlay](https://lh3.googleusercontent.com/MCPb2cLtFyhzDx56gg9iS4qQzt_HPf9CIiFyQL7d6BYDWG9ncFY3g8vXKoGaxYcjieCNu7v686sveLVK41bwhRdV5ULaEF1Jz0ECN1X9gStg5SwiDy8_PzOlGI_6-2ShuHx1S4FD)

This overlay will become active on Player stall error and will be inactive when automatic recovery is attempted (or when Reload button is pressed by the user)

![Buffering overlay](https://lh4.googleusercontent.com/OQ1n2rMp4QtOF0Ym10IlSOilb3VshRf9_1lkLVcZu2wJzaySg-M5cbtzj813Ni8NL4vJPQxLjeqEhnLySGyFS1vf8nu8d2xeHSFlMg32SlfLIBt40l4Depbeu3kOraM034UK1XEF)

This overlay is active as long as ​playbackState​ is ​BUFFERING

## Customize

Livery Player has three layers that you can customize. They are loading indicator, error overlay and player controls.

### Loading Indicator

To customize the loading indicator set the `customLoadingView` property with your own `UIView` implementation of it. This means that whenever the player is loading, this view will be shown.

### Error Overlay

To customize error overlay create an `UIView` that conforms to the `LiveryPlayerErrorView` protocol below and set the `customErrorView` property with it.

```swift
public protocol LiveryPlayerErrorView where Self: UIView {
    var titleLabel: UILabel! { get set }
    var messageLabel: UILabel! { get set }
    var reloadButton: UIButton! { get set }
}
```

### Player Controls

To customize player controls create an `UIControl` that conforms to the `LiveryPlayerControlView` protocol below and set the `customControlView` property with it.

```swift
public protocol LiveryPlayerControlView where Self: UIControl {
    var settingsButton: UIButton { get set }
    var cameraButton: UIButton { get set }
    var infoButton: UIButton { get set }
    var airplayButton: UIButton { get set }
    var fullscreenButton: UIButton { get set }
    var muteButton: UIButton { get set }
    var playButton: UIButton { get set }
    var progressView: UIProgressView { get set }
    var currentTimeLabel: UILabel { get set }
    var totalTimeLabel: UILabel { get set }
    var titleLabel: UILabel { get set }
    var qualityLabel: UILabel { get set }
    //Called on the initialization of the Control View
    func setupUI()
    //Called whenever it needs to update the Control View UI like when the device rotates
    func updateUI()
}
```

## Analytics

Our SDK makes use of [Amazon Pinpoint](https://aws.amazon.com/pinpoint/).

For now, our SDK reports events to preset Exmachina AWS backend (i.e. appId , region and identityPoolId cannot be changed)

Only the customerId property of `advanced.analytics` configuration are used for the necessary Pinpoint configuration.

The `customerId` will be added to all pinpoint events as a property.

In future releases, the appId, region and `identityPoolId` properties of `advanced.analytics` configuration will be used for the necessary Pinpoint configuration.
Notice dependency to external AWSPinpoint SDK changes the build instructions for your custom app.

If you are not using the AWSPinpoint in your own application there's no need to include this on your Podfile since this dependency is included in our SDK.

Note:

- If your application already contains AWSPinpoint make sure its version matches the one used in the SDK, which is:

```
  pod 'AWSPinpoint', '~> 2.12.1'
```

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

### Livery SDK dependencies

- The `Podfile` that you configure to install the Livery SDK must contain:

```
  pod 'AWSPinpoint', '~> 2.12.1'
  pod 'TrueTime', '~> 5.0.3'
```

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

| Version | Description                                                                                                                                                                                                                                                                                                                           |
| ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0.9.6   | Fixed issues with having to use a device or simulator version. You can now use the universal build. Made initialize call asynchronous, so it doesn’t block the main thread. The onSuccess and onError functions are called when initialization is completed. Library only works with Xcode 11 now, since it is compiled in Swift 5.1. |
| 0.9.9   | Methods are now async.<br> Added interactive layer feature.<br> Using NTP as time source.<br> Fixed volume control.                                                                                                                                                                                                                   |
| 0.10.0  | Livery class was renamed to LiverySDK to avoid build errors with `BUILD_LIBRARIES_FOR_DISTRIBUTION=YES`. This is a [known issue](https://developer.apple.com/documentation/xcode_release_notes/xcode_11_2_release_notes) present from Xcode 10.2.                                                                                     |
| 0.10.1  | Battery life improvements.<br> New ABR algorithm.<br> Backup/main stream switching support.<br> Synchronization improvements for restricted networks.<br> Analytics improvements                                                                                                                                                      |
| 0.10.2  | Fixed recovery timeout logic                                                                                                                                                                                                                                                                                                          |
| 0.10.3  | SDK initialize with configURL is deprecated. Use the new initialize with streamId instead.<br> Remote config is fetched and updated each minute, if it was modified.<br> Added pause method.<br> Fixed sound hiccups.                                                                                                                 |
| 0.10.4  | Fixed stalling issue when the player has zero latency.<br> Fixed issue regarding player not showing the video on iOS 13 simulator.<br> Added Poster property to the player options.<br> Added Buffer property to the player.<br> Improvements on ABR algorithm.<br> Analytics improvements.                                           |
