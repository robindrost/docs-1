# Livery Video iOS SDK

[![Revision History](https://img.shields.io/badge/doc-Revision%20History-blue)](ios-sdk-changelog.md)

Livery Video iOS SDK, published via CocoaPods and Swift Package Manager. For CocoaPods see [livery-sdk-ios-podspec](https://github.com/exmg/livery-sdk-ios-podspec) and for Swift Package Manager see [livery-sdk-ios-spm](https://github.com/liveryvideo/livery-sdk-ios-spm).

Documentation can be found at: [docs.liveryvideo.com/ios-sdk](https://docs.liveryvideo.com/ios-sdk).

More information can be found at: [liveryvideo.com](https://liveryvideo.com).

## Installation

### Compatibility

The Livery iOS SDK is compatible with iOS 14 or higher.

### Configuration

To install Livery SDK into your project, follow these steps below.

You should request credentials from Livery, then place them in your ~/.netrc as follows: (create if necessary)

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
  pod "Livery", "3.0.0"
end
```

Then run

```
$ pod install
```

**Note:** you may need to run `pod install --repo-update` to update the cocoapods repository.

#### Swift Package Manager

Swift Package Manager is distributed with Xcode. To add the Livery SDK to your iOS project, do as follows:

1. Open your project in Xcode and select File > Add Packages
2. Enter the URL for the Swift Package Manager Livery SDK repo (<https://github.com/liveryvideo/livery-sdk-ios-spm>) into the search bar and click "Add Package"
3. Then on the Dependency Rule menu select the "Exact Version" - 3.0.0 or "Branch" - main to have the latest release

## Usage

For basic usage of the SDK use the following minimal steps:

```swift
import Livery

let playerView: LiveryPlayerView

func initializeLiverySDK(streamId: String = "yourStreamId") async {
  // 1. Initialize the SDK
  // (See LiverySDK initialize() method below for more options)
  do {
    try await LiverySDK.initialize(streamId: streamId)
  } catch {
    // deal with the initialization error here
    return
  }

  // 2. Create a player object
  // (See LiveryPlayerView createPlayer() method below for a full list of supported options)
  do {
    try playerView.createPlayer()
  } catch {
    // deal with the initialization error here
    return
  }

  // 3. The player is now ready to play
  playerView.play()
}
```

At this point, the player will fetch the DASH manifest, start rendering by automatically synching to the targetLatency set in the remote config or the LiveryPlayerOptions (3 seconds default).
For more information about options, properties and methods please see relevant sections below.
For a sample application code utilizing these minimal steps see the LiveryExample sample application section.

## Livery SDK

### Methods

| Name                                                            | Returns  | Description                                                                                                        |
| --------------------------------------------------------------- | -------- | ------------------------------------------------------------------------------------------------------------------ |
| `initialize(streamId, configType, completionQueue, completion)` | `void`   | Livery SDK initialization method.                                                                                  |
| `initialize(completionQueue, completion)` | `void`   | Livery SDK initialization method that uses `LIVERY_STREAM_ID` and `LIVERY_CONFIG_TYPE` values from your app's Info.plist.                |
| `initialize(streamId, configType) async throws` | `void`   | Livery SDK async initialization method.                                                                                            |
| `initialize() async throws` | `void`   | Livery SDK async initialization method that uses `LIVERY_STREAM_ID` and `LIVERY_CONFIG_TYPE` values from your app's Info.plist.                        |

### SDK Initialize

The SDK `initialize()` method must be called before calling `LiveryPlayerView.createPlayer()`.

```swift
LiverySDK.initialize(streamId: String, configType: String = LiverySDK.defaultConfigType, completionQueue: DispatchQueue = .main, completion: (LiveryErrors) -> Void)
// or
try await LiverySDK.initialize(streamId: String, configType: String = LiverySDK.defaultConfigType)
```

It is also possible to define `LIVERY_STREAM_ID` and `LIVERY_CONFIG_TYPE` in your's app `Info.plist` file and use `LiverySDK.initialize()` without passing the `streamId` parameter.

```swift
LiverySDK.initialize(completionQueue: DispatchQueue = .main, completion: (LiveryErrors) -> Void)
// or
try await LiverySDK.initialize()
```

----

The `streamId` is used to determine the remote configuration url and is fetched from it. If the remote configuration fails to be fetched the SDK will fail to initialize.

The `configType` defines the type of the remote configuration that would be requested. The `LiverySDK.defaultConfigType` is `IOS`.

The `completionQueue` defines the `DispatchQueue` in which the `completion` callback is called.

The `completion` callback is a block that receives a `LiveryErrors?` with the SDK initialization eror in case of any.


#### SDK Initialization Errors

```swift
enum LiveryErrors: LocalizedError {
  case sdkNotInitialized
  case invalidStreamId
  case invalidURL
  case invalidServerTime
  case downloadError(_ error: Error)
  case missingData
  case missingConfig
  case invalidConfig(_ error: Error)
  case notEnoughSpace
}
```

### SDK Properties

| Name                   | Type                                           | Default                        | Description                                                          |
| ---------------------- | ---------------------------------------------- | ------------------------------ | -------------------------------------------------------------------- |
| `sdkVersion`           | `String`                                       |                                | A getter to retrieve the SDK version.                                |
| `enableSentrySDK`      | `Bool`                                         | `true`                         | A Boolean whose enables the use of the `SentrySDK` instance.         |
| `audioSessionSettings` | [`LivertAudioSessionSettings`](#audio-session) | `LivertAudioSessionSettings()` | Property to store the `AVAudioSession` settings.                     |
| `minimumDeviceSpace`   | `Int`                                          | `20000000`                     | Minimum device space in bytes required for the SDK to work properly. |

#### enableSentrySDK

If set to **true** Livery will use the `SentrySDK` instance to track events, handled errors and crashes.

**Warning**: If your application uses Sentry too it will override its configuration.

If set to **false** Livery will setup a custom `SentryHub` object that will only track events and handled errors.
This will not override your Sentry configuration.

#### audioSessionSettings

Property used to configure `AVAudioSession` by `LiverySDK` in its initialization process.

Default settings are:

```swift
category = .playback
mode = .spokenAudio
options = []
```

**Important:**<br>If `audioSessionSettings` is **nil** `LiverySDK` won't setup `AVAudioSession` and your application is responsible to call `AVAudioSession.sharedInstance().setCategory`.

`LiverySDK.audioSessionSettings` can be set after the `LiverySDK.initialize()` method is called, however `LiverySDK.audioSessionSettings` has no effect on `AVAudioSession` is set to **nil**.

If your application wants to play another audio, besides the one from the live stream, without stopping the live stream, you should set the `options` property to `[.mixWithOthers]`, as below:

```swift
LiverySDK.audioSessionSettings?.options = [.mixWithOthers]
```

## LiveryPlayerView

| `controls`               | [`ControlsOptions`](#controls-options) | `ControlsOptions()` | Provides access to [ControlsOptions](#controls-options). Player's controls can be changed by this object.             |


### Create Player

| Name                   | Description                                                                                                                   |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `createPlayer()`       | Create a Livery internal Player. This method can receive specified options if you need to override the remote configurations. |


The LiveryPlayerView `createPlayer()` method must be called after calling `LiverySDK.initialize()`.

```swift
let playerView = LiveryPlayerView()
....

/* Create the player */
player = playerView.createPlayer()
```

If you need to override the remote configurations of the player you can call `createPlayer(options: LiveryPlayerOptions)` with a `LiveryPlayerOptions` object.<br>
For more information about [`LiveryPlayerOptions`](#livery-player-options) properties please see the corresponding section.

### Player Options

These are the options to be passed to wehen creating the LiveryPlayerView [internal player](#create-player):

| Name                     | Type                                   | Default             | Description                                                                                                           |
| ------------------------ | -------------------------------------- | ------------------- | --------------------------------------------------------------------------------------------------------------------- |
| `akamaiLongToken`        | `String`                               | `nil`                | Access token to use to be able to see a stream that uses Akamai’s Token Auth feature                                 |
| `autoplay`               | `Boolean`                              | `false`             | Determines whether video shall play immediately after [`createPlayer`](#sdk-create-player).                           |
| `enablePictureInPicture` | `Boolean`                              | `false`             | Determines whether the Picture in Picture feature should be enabled or not. It is available only on iOS 15 or higher. |
| `fit`                    | [`LiveryPlayerFit`](#livery-player-fit)| `.contain`          | Determines how the video will be scaled and cropped. See: [`LiveryPlayerFit`](#livery-player-fit)                     |
| `prePoster`              | `String`                               | `nil`               | URL for an image to be shown while the video is loading.                                                              |
| `poster`                 | `String`                               | `nil`               | URL for an image to be shown while the video is loading.                                                              |
| `postPoster`             | `String`                               | `nil`               | URL for an image to be shown while the video is loading.                                                              |
| `sources`                | `String[]`                             | `[]`                | Array of media source URLs from which the first that can be played will be selected.                                  |
| `targetLatency`          | `Integer`                              | `3`                 | Target live latency in seconds. If 0 then syncing is disabled.                                                        |
| `audioOnly`               | `Boolean`                             | `false`             | Enabled [Audio Only Mode](#audio-only-mode).                                                                                              |

### Controls Options

These are the controls visibility options to be passed to the LiveryPlayerView:

| Name         | Type      | Default | Description                                                                                                                                                     |
| ------------ | --------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `cast`       | `Boolean` | `false` | Determines whether cast button should be displayed or not on the [`Player Controls`](#player-controls).                                                         |
| `contact`    | `Boolean` | `false` | Determines whether contact button should be displayed or not on the [`Player Controls`](#player-controls).                                                      |
| `error`      | `Boolean` | `false` | Determines whether Error Overlay should be displayed or not.                                                                                |
| `fullscreen` | `Boolean` | `false` | Determines whether full screen button should be displayed or not on the [`Player Controls`](#player-controls).                                                  |
| `mute`       | `Boolean` | `false` | Determines whether mute button should be displayed or not on the [`Player Controls`](#player-controls).                                                         |
| `pip`        | `Boolean` | `false` | Determines whether Picture in Picture button should be displayed or not on the [`Player Controls`](#player-controls). It is available only on iOS 15 or higher. |
| `play`       | `Boolean` | `false` | Determines whether play button should be displayed or not on the [`Player Controls`](#player-controls).                                                         |
| `quality`    | `Boolean` | `false` | Determines whether select quality button should be displayed or not on the [`Player Controls`](#player-controls).                                               |
| `scrubber`   | `Boolean` | `false` | Determines whether scrubber / progress bar view should be displayed or not on the [`Player Controls`](#player-controls).                                        |

### Properties :id=player-properties

The following properties are exposed by LiveryPlayerView:

| Name                        | Type                                                                                            | Write | Description                                                                                     |
| --------------------------- | ----------------------------------------------------------------------------------------------- | ----- | ----------------------------------------------------------------------------------------------- |
| `activeQuality`             | [`LiveryQuality`](#player-media-quality)                                                        | No    | Active Media Quality. Emits: `activeQualityDidChange`                                           |
| `akamaiLongToken`           | `String`                                                                                        | Yes   | Defines an access token to use to be able to see a stream that uses Akamai’s Token Auth feature |
| `buffer`                    | `Int64`                                                                                         | No    | Size of buffer, ahead of current position, in milliseconds. Emits: `progressDidChange`          |
| `currentSource`             | `URL`                                                                                           | No    | Current media source. Emits: `sourceDidChange` URL.                                             |
| `currentTime`               | `Int64`                                                                                         | No    | Current playback time position in milliseconds. Emits: `timeDidUpdate`                               |
| `delegate`                  | [`LiveryPlayerDelegate`](#player-events)                                                        | Yes   | Protocol to implement to get player events                                                      |
| `error`                     | `Error`                                                                                         | No    | Most recent unrecovered error. Emits: `playerDidFail`, `playerDidRecover`                       |
| `interactiveBridgeDelegate` | [`LiveryInteractiveBridgeCustomCommandDelegate`](#liveryinteractivebridgecustomcommanddelegate) | Yes   | Protocol to implement to get custom messages from the interactive layer                         |
| `interactiveURL`            | `URL`                                                                                           | Yes   | The interactive layer URL                                                                       |
| `latency`                   | `Int64`                                                                                         | No    | End to end latency in milliseconds. Emits: `progressDidChange`                                  |
| `muted`                     | `Bool`                                                                                          | Yes   | If true then audio is muted. Emits: `volumeDidChange`.                                          |
| `playbackState`             | [`LiveryPlaybackState`](#player-types)                                                          | No    | Playback state: .none / .buffering / .playing / .paused / .ended Emits: `playbackStateDidChange`.           |
| `qualities`                 | `[LiveryQuality]`                                                                               | No    | Array of Available Media Qualities. Emits: `qualitiesChange`.                                   |
| `selectedQuality`           | [`LiveryQuality`](#player-media-quality)                                                        | Yes   | Selected Media Quality. Emits: `selectedQualityDidChange`                                       |
| `timeOffset`                | `Int64`                                                                                         | No    | Local device time offset in milliseconds.                                                       |

**Note**: LiveryPlayerView properties are optional and will return `nil` if called before `createPlayer`.

### Methods :id=player-methods

The following methods are exposed by LiveryPlayerView:

| Name                                                              | Description                                                                                     |
| ----------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `play()`                                                          | Start/resume media playback.                                                                    |
| `pause()`                                                         | Pause media                                                                                     |
| `stop()`                                                          | Stop media playback.                                                                            |
| `reload()`                                                        | Reloads the player by stopping it, selecting the best available source and start playing again. |
| `sendInteractiveBridgeCustomCommand(name:arg:completionHandler:)` | Sends a custom message to the interactive bridge.                                               |

### Events :id=player-events

It might be important in some cases to receive player events and process the data for your needs.
In order to receive player events, the LiveryPlayerDelegate can be implemented.
All methods have an empty default implementation so only the ones desired need to be implemented.

```swift
public protocol LiveryPlayerDelegate: class {
    // Emitted when activeQuality has changed.
    // Auto is 'true' when it was changed through the ABR and 'false' when it was changed manually
    func activeQualityDidChange(activeQuality: LiveryQuality?, auto: Bool)

    // Emitted when playbackState has changed
    func playbackStateDidChange(playbackState: LiveryPlaybackState)

    // Emitted when an error occurs
    func playerDidFail(error: Error)

    // Emitted when player has recovered from an error
    func playerDidRecover()

    // Emitted periodically to inform of progress downloading the media
    func progressDidChange(buffer: Int64, latency: Int64)

    // Emitted when qualities have changed
    func qualitiesDidChange(qualities: [LiveryQuality])

    // Emitted when currentSource has changed
    func sourceDidChange(currentSource: URL?)

    // Emitted periodically to inform of current playback time changing
    func timeDidUpdate(currentTime: Int64)

    // Emitted when volume or muted has changed
    func volumeDidChange()
}
```

To implement the `LiveryPlayerDelegate` you should do as follows:

```swift
playerView.delegate = // your class that implements the LiveryPlayerDelegate protocol
```

See [Livery iOS SDK Example App](#example-app) to see how playback state change events are received via the `LiveryPlayerDelegate` implementation.

## Types :id=player-types

```swift

enum LiveryPlaybackState : String {
  case none = ""
  case buffering = "Buffering"
  case playing = "Playing"
  case paused = "Paused"
  case ended = "Ended"
}
```

### LiveryQuality :id=player-media-quality

```swift
struct LiveryQuality {
  let qualityId: String
  let label: String
  let shortLabel: String
  let bitrate: UInt32
  let width: UInt32
  let height: UInt32
  let codecs: String
  let frameRate: Float
}
```

| Name         | Type     | Description                                     |
| -----------  | -------- | ----------------------------------------------- |
| `qualityId`  | `String` | ID of quality.                                  |
| `label`      | `String` | Human readable name of quality.                 |
| `shortLabel` | `String` | Human readable shortname of quality: SD/HD/UHD. |
| `bitrate`    | `UInt32` | Bitrate of the quality.                         |
| `width`      | `UInt32` | Width of the media.                             |
| `height`     | `UInt32` | Height of the media.                            |
| `codecs`     | `String` | Codec of the media.                             |
| `frameRate`  | `Float`  | Frame rate of the media.                        |

### LiveryPlayerFit

The SDK provides fitting methods to scale and crop the video according to the specified ​fit​ option value.

```swift
public enum PlayerFit {
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

## Playback Synchronization

The player will receive the millisecond precision server time from the time urls (Default is <https://time.akamai.com?ms> ) and start the Audio and Video decoder clocks timebase as

`currentTime - targetLatency`

In other words, IOS SDK players behave always in sync once initialized, meaning if audio packages or video frames are received later then their presentation timestamps due the network connections, they will be ignored or played back in a faster rate by the player.


## Player Skin

### Player Controls :id=player-controls

### Error Overlay


## Audio Session

class **_AudioSessionSettings_**

| Name       | Type                             | Default       | Description                                                                        |
| ---------- | -------------------------------- | ------------- | ---------------------------------------------------------------------------------- |
| `category` | `AVAudioSession.Category`        | `playback`    | Audio session category identifiers.                                                |
| `mode`     | `AVAudioSession.Mode`            | `spokenAudio` | An object that communicates to the system how you intend to use audio in your app. |
| `options`  | `AVAudioSession.CategoryOptions` | `[]`          | Constants that specify optional audio behaviors.                                   |

## Audio Only Mode

When you want users to not watch the video but hear it you can allow them to select audio-only mode.

To enable Audio Only mode you need to set the `audioOnly` property to `true` on [`PlayerOptions`](#player-options). With this option enabled a new "Audio Only" quality appears on quality list and ABR will switch to Audio Only when the network conditions are not good enough to play the video.

## Cast

Our SDK is able to cast to AirPlay and Google Chromecast.

### Google Chromecast

**Note:** Google Chromecast does not have support for Swift Package Manager or the simulator on the M1, so as a consequence, Livery SDK does not include Google Chromecast in any of those cases.

To cast to Google Chromecast you need to add two items in your app's `Info.plist`:

- Add `NSBonjourServices`

Specify `NSBonjourServices`, like is shown below, in your `Info.plist` to allow local network discovery to succeed.

```
<key>NSBonjourServices</key>
<array>
  <string>_googlecast._tcp</string>
  <string>_93EB8FEA._googlecast._tcp</string>
</array>
```

- Add `NSLocalNetworkUsageDescription`

We recommend that you customize the message shown in the Local Network prompt by adding an app-specific permission string in your app's `Info.plist` file for the `NSLocalNetworkUsageDescription`, as show below.

```
<key>NSLocalNetworkUsageDescription</key>
<string>${PRODUCT_NAME} uses the local network to discover Cast-enabled devices on your WiFi network.</string>
```

This message will appear as part of the iOS Local Network Access dialog when the user taps on the cast button.

## Interactive Bridge

On the interactive layer you can use the [Interactive Bridge](/interactive-bridge) to communicate with the Livery Player. Please see the [Interactive Bridge](/interactive-bridge) documentation for more details on how to use it on the interactive layer side.

Besides the [methods](/interactive-bridge?id=methods) already definied on the Interactive Bridge to get values like the player **latency**, device **orientation**, etc. you can get and send custom messages.

### Custom Messages

- To **GET** custom messages you should implement the `LiveryInteractiveBridgeCustomCommandDelegate` by calling:

```swift
playerView.interactiveBridgeDelegate = self //being self the class that conforms to the LiveryInteractiveBridgeCustomCommandDelegate protocol
```

#### LiveryInteractiveBridgeCustomCommandDelegate

```swift
public protocol LiveryInteractiveBridgeCustomCommandDelegate: class {
    func getCustomMessageValue(message name: String, arg: Any?, completionHandler: @escaping (_ value: Any?) -> Void)
}
```

There you will get the custom messages you sent through the bridge on the interactive layer. You can use the name and the arg paramenters to better recognize the message itself and act on it by calling the `completionHandler` with a value that will be send back to the interactive layer.

- To **SEND** custom messages you should call:

```swift
playerView.sendInteractiveBridgeCustomCommand(name: String, arg: Any?, completionHandler: @escaping (LiveryInteractiveBridgeCustomCommandResult) -> Void)
```

Those messages will be send to the interactive layer through the bridge. To get them on the interactive layer side you should first call the `registerCustomCommand(name, handler)` with the `name` matching the one used on the player. Please see Interactive Bridge [methods](/interactive-bridge?id=methods) section for more details.

```swift
public enum LiveryInteractiveBridgeCustomCommandResult {
    case success(value: Any?)
    case failure(error: String)
}
```

## Picture in Picture

Please note that this feature is only available on iOS 15 or higher.

To enable the Picture in Picture feature you need to set the `enablePictureInPicture` property to `true` on [`PlayerOptions`](#player-options) and enable the “Audio, AirPlay, and Picture in Picture” option under background modes in Xcode.

The simplest way to configure it is by selecting your app’s target in Xcode and select the Signing & Capabilities tab. Under the Capabilities tab, add the Background Modes capability and select the “Audio, AirPlay, and Picture in Picture” option under its list of background modes, as exemplified on the image below:

<p align="center">
<img src="iOS/xcode_background_modes.png" width="500"/>
</p>

## Analytics

Our SDK makes use of [Amazon Pinpoint](https://aws.amazon.com/pinpoint/).

If you are not using the AWSPinpoint in your own application there's no need to include this on your Podfile since this dependency is included in our SDK.

Note:

- If your application already contains AWSPinpoint make sure its version matches the one used in the SDK, which is:

```
  pod 'AWSPinpoint', '~> 2.33'
```

## Migration Guide

#### [Migration from 1.4.1 to 2.0.0](/ios-migration-guide-2#migration-from-141-to-200)

#### [Migration from 2.1.0 to 3.0.0](/ios-migration-guide-3#migration-from-210-to-300)

## Example App

Example iOS application which demonstrates how to use the Livery iOS SDK.<br>
You can find it on the following git repositories:

- Cocoapods: [Livery iOS Example App](https://github.com/liveryvideo/player-ios-example-cocoapods)
- Swift Package Manager: [Livery iOS Example App](https://github.com/liveryvideo/player-ios-example-spm)

Notes:

- Built using XCode 15.1 (15C65) (Swift 5.9)

### How to run the app

1. Clone this repo
2. Go to the repo directory on the terminal and run `pod install`
3. Open **livery-sdk-ios-example.xcworkspace**
4. You will need to change the **Bundle Identifier** and the **Team** to your own team in order to build and run the app
5. Run the project on selected device or simulator

## Q&A
