# Livery Video iOS SDK

Livery Video iOS SDK, published as CocoaPods spec on Github: [livery-sdk-ios-podspec](https://github.com/exmg/livery-sdk-ios-podspec).

Documentation can be found at: [docs.liveryvideo.com/ios-sdk](https://docs.liveryvideo.com/ios-sdk).

More information can be found at: [liveryvideo.com](https://liveryvideo.com).

## Installation

### Compatibility

The Livery iOS SDK is compatible with iOS 12 or higher.

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
  pod "Livery", "1.4.0"
end
```

Then run

```
$ pod install
```

#### Swift Package Manager

Swift Package Manager is distributed with Xcode. To add the Livery SDK to your iOS project, do as follows:

1. Open your project in Xcode and select File > Add Packages
2. Enter the URL for the Swift Package Manager Livery SDK repo (<https://github.com/liveryvideo/livery-sdk-ios-spm>) into the search bar and click "Add Package"
3. Then on the Dependency Rule menu select the "Exact Version" - 1.3.0 or "Branch" - main to have the latest release

## Usage

For basic usage of the SDK following minimal steps are needed:

```swift
import Livery

@IBOutlet weak var playerView: UIView!

var player: Player?
let liveSDK = LiverySDK()

func initializeLiverySDK(streamId: "yourStreamId") {
  // 1. Initialize the SDK (See SDK initialize() method below for more options)
  liveSDK.initialize(streamId: streamId, completionQueue: .main) { result in
      switch result {
      case .success(let config):
        // 2. Create a player object
        // (See SDK createPlayer() method below for a full list of supported options)
        self.player = self.liveSDK.createPlayer()
        if let player = self.player {
          // 3. Set a UIView for the player to render into. (See Player Method setView() method below)
          player.setView(view: self.playerView)
        }

        // 4. The player is now ready to play
        play()

      case .failure(let error):
        // deal with the initialization error here
        // for example, by calling the 'initializeLiverySDK' method again to retry the initialization
        initializeLiverySDK(streamId: streamId)
      }
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

At this point, the player will fetch the DASH manifest, start rendering by automatically synching to the targetLatency set in the remote config or the PlayerOptions (3 seconds default).
For more information about options, properties and methods please see relevant sections below.
For a sample application code utilizing these minimal steps see the LiveryExample sample application section.

## Livery SDK

### Methods

| Name                                                            | Returns  | Description                                                                                                        |
| --------------------------------------------------------------- | -------- | ------------------------------------------------------------------------------------------------------------------ |
| `initialize(streamId, configType, completionQueue, completion)` | `void`   | Livery SDK initialization method.                                                                                  |
| `createPlayer()`                                                | `Player` | Create a Live Player. This method can receive specified options if you need to override the remote configurations. |

### SDK Initialize

The SDK `initialize()` method has to be called on an sdk instance before Live Players are created.

```swift
let liveSDK = LiverySDK()
liveSDK.initialize(streamId: String, configType: String = LiverySDK.defaultConfigType, completionQueue: DispatchQueue = .main, completion: (LiverySDK.ResultConfig) -> Void)
```

The `streamId` is used to determine the remote configuration url and is fetched from it. If the remote configuration fails to be fetched the SDK will fail to initialize.

The `configType` defines the type of the remote conconfiguration that would be requested on the initialization. The `LiverySDK.defaultConfigType` is `IOS`.

The `completionQueue` defines the `DispatchQueue` in which the `completion` callback is called.

The `completion` callback is a block that receives a `LiverySDK.ResultConfig` with the SDK initialization result.

- In case of success it contains the fetched [`LiveryConfig`](#config).
- In case of an error it contains an error of type `LiverySDK.Errors`.

#### SDK Initialization Result

```swift
extension LiverySDK {
  typealias ResultConfig = Swift.Result<LiveryConfig, Errors>
}
```

#### SDK Initialization Errors

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

### SDK Create Player

The SDK `createPlayer()` method depends on the `initialize()` method having been called.

```swift
/* Create the player */
player = liveSDK.createPlayer()
```

If you need to override the remote configurations of the player you can call `createPlayer(options: PlayerOptions)` with a `PlayerOptions` object.<br>
For more information about [`PlayerOptions`](#player-options) properties please see the corresponding section.

### SDK Properties

| Name              | Type   | Default | Description                                                  |
| ----------------- | ------ | ------- | ------------------------------------------------------------ |
| `enableSentrySDK` | `Bool` | `true`  | A Boolean whose enables the use of the `SentrySDK` instance. |

If set to **true** Livery will use the `SentrySDK` instance to track events, handled errors and crashes.

**Warning**: If your application uses Sentry too it will override its configuration.

If set to **false** Livery will setup a custom `SentryHub` object that will only track events and handled errors.
This will not override your Sentry configuration.

| Name                   | Type                                     | Description                                      |
| ---------------------- | ---------------------------------------- | ------------------------------------------------ |
| `audioSessionSettings` | [`AudioSessionSettings`](#audio-session) | Property to store the `AVAudioSession` settings. |

If it is not **nil** the `AVAudioSession.sharedInstance().setCategory` will be called when the `LiverySDK.initialize()` completes successfully.

**Important:**<br>If it is **nil** your application is responsible to call `AVAudioSession.sharedInstance().setCategory`.

Default settings are:

```swift
category = .playback
mode = .spokenAudio
options = []
```

If your application wants to play another audio, besides the one from the live stream, without stopping the live stream, you should set the `options` property to `[.mixWithOthers]`, as below:

```swift
liveSDK.audioSessionSettings?.options = [.mixWithOthers]
```

## Remote Config

The stream and the player should be configured in the Livery Management Portal. However, if needed, it's possible to override the remote config properties throught the [`PlayerOptions`](#player-options) object that can be passed when creating the player.

## Player Options

These are the options to be passed to Players:

| Name               | Type                           | Default    | Description                                                                                                              |
| ------------------ | ------------------------------ | ---------- | ------------------------------------------------------------------------------------------------------------------------ |
| `autoplay`         | `Boolean`                      | `false`    | Determines whether video shall play immediately after [`createPlayer`](#sdk-create-player).                              |
| `akamaiToken`      | `String`                       | `""`       | Access token to use to be able to see a stream that uses Akamai’s Token Auth feature                                     |
| `cast`             | `Boolean`                      | `false`    | Determines whether cast button should be displayed or not on the [`Player Controls`](#player-controls).                  |
| `error`            | `Boolean`                      | `false`    | Determines whether [`Error Overlay`](#error-overlay) should be displayed or not.                                         |
| `fit`              | [`playerFit`](#player-fitting) | `.contain` | Determines how the video will be scaled and cropped. See: [`Player Fitting`](#player-fitting)                            |
| `fullscreen`       | `Boolean`                      | `false`    | Determines whether full screen button should be displayed or not on the [`Player Controls`](#player-controls).           |
| `maxRecoveryDelay` | `Integer`                      | `90`       | Maximum delay in seconds between subsequent recovery attempts.                                                           |
| `minRecoveryDelay` | `Integer`                      | `10`       | Minimum delay in seconds before starting automatic recovery.                                                             |
| `mute`             | `Boolean`                      | `false`    | Determines whether mute button should be displayed or not on the [`Player Controls`](#player-controls).                  |
| `muted`            | `Boolean`                      | `false`    | Determines whether media should be muted or not.                                                                         |
| `play`             | `Boolean`                      | `false`    | Determines whether play button should be displayed or not on the [`Player Controls`](#player-controls).                  |
| `poster`           | `String`                       | `""`       | URL for an image to be shown while the video is loading.                                                                 |
| `quality`          | `Boolean`                      | `false`    | Determines whether select quality button should be displayed or not on the [`Player Controls`](#player-controls).        |
| `scrubber`         | `Boolean`                      | `false`    | Determines whether scrubber / progress bar view should be displayed or not on the [`Player Controls`](#player-controls). |
| `sources`          | `String[]`                     | `[]`       | Array of media source URLs from which the first that can be played will be selected.                                     |
| `targetLatency`    | `Integer`                      | `3`        | Target live latency in seconds. If 0 then syncing is disabled.                                                           |

## Player Properties

The following properties are exposed by Live Player instances:

| Name                        | Type                                                                  | Write | Description                                                                                     |
| --------------------------- | --------------------------------------------------------------------- | ----- | ----------------------------------------------------------------------------------------------- |
| `activeQuality`             | [`Quality`](#player-media-quality)                                    | No    | Active Media Quality. Emits: `activeQualityDidChange`                                           |
| `akamaiToken`               | `String`                                                              | Yes   | Defines an access token to use to be able to see a stream that uses Akamai’s Token Auth feature |
| `buffer`                    | `Int`                                                                 | No    | Size of buffer, ahead of current position, in milliseconds. Emits: `progressDidChange`          |
| `currentSource`             | `URL`                                                                 | No    | Current media source. Emits: `sourceDidChange` URL.                                             |
| `currentTime`               | `TimeInterval`                                                        | No    | Current playback time position in seconds. Emits: `timeDidUpdate`                               |
| `customControlView`         | [`LiveryPlayerControlView`](#player-controls)                         | Yes   | Custom player controls view                                                                     |
| `customErrorView`           | [`LiveryPlayerErrorView`](#error-overlay)                             | Yes   | Custom error view                                                                               |
| `customLoadingView`         | `UIView`                                                              | Yes   | Custom loading indicator view                                                                   |
| `delegate`                  | [`PlayerDelegate`](#player-events)                                    | Yes   | Protocol to implement to get player events                                                      |
| `error`                     | `Error`                                                               | No    | Most recent unrecovered error. Emits: `playerDidFail`, `playerDidRecover`                       |
| `interactiveBridgeDelegate` | [`PlayerInteractiveBridgeDelegate`](#playerInteractiveBridgeDelegate) | Yes   | Protocol to implement to get custom messages from the interactive layer                         |
| `interactiveURL`            | `URL`                                                                 | Yes   | The interactive layer URL                                                                       |
| `latency`                   | `Int`                                                                 | No    | End to end latency in milliseconds. Emits: `progressDidChange`                                  |
| `muted`                     | `Boolean`                                                             | Yes   | If true then audio is muted. Emits: `volumeDidChange`.                                          |
| `playbackState`             | [`PlaybackState`](#player-types)                                      | No    | Playback state. BUFFERING / ENDED / PAUSED / PLAYING Emits: `playbackStateDidChange`.           |
| `qualities`                 | `[Quality]`                                                           | No    | Array of Available Media Qualities. Emits: `qualitiesChange`.                                   |
| `selectedQuality`           | [`Quality`](#player-media-quality)                                    | Yes   | Selected Media Quality. Emits: `selectedQualityDidChange`                                       |
| `timeOffset`                | `Int`                                                                 | No    | Local device time offset in milliseconds.                                                       |
| `volume`                    | `Float`                                                               | Yes   | Audio volume, from 0.0 (silent) to 1.0 (loudest). Emits: `volumeDidChange`                      |

## Player Methods

The following methods are exposed by Player instances:

| Name                                                                                                                      | Description                                                                                     |
| ------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `pause(completionQueue: DispatchQueue = .main, completion: @escaping () -> Void)`                                         | Async call to pause media                                                                       |
| `play(completionQueue: DispatchQueue = .main, completion: (Player.Result) -> Void)`                                       | Async call to start/resume media playback.                                                      |
| `reload(completionQueue: DispatchQueue = .main, completion: @escaping (Result) -> Void)`                                  | Reloads the player by stopping it, selecting the best available source and start playing again. |
| `sendInteractiveBridgeCustomCommand(name: String, arg: Any?, completionHandler: @escaping (CustomCommandResult) -> Void)` | Sends a custom message to the interactive bridge.                                               |
| `setView(view: UIView)`                                                                                                   | Set the view for player to render.                                                              |
| `stop(completionQueue: DispatchQueue = .main, completion: @escaping () -> Void)`                                          | Async call to stop media playback.                                                              |
| `updateOptions(PlayerOptions)`                                                                                            | Update player options                                                                           |

## Player Events

It might be important in some cases to receive player events and process the data for your needs. In order to receive player events, the PlayerDelegate must be implemented:

protocol **_PlayerDelegate_**

```swift
public protocol PlayerDelegate: class {
    func activeQualityDidChange(activeQuality: Quality?) // This method is deprecated and will be removed on the next version
    func activeQualityDidChange(activeQuality: Quality?, auto: Bool) // Emitted when activeQuality has changed.
                                                                    // Auto is 'true' when it was changed through the ABR and 'false' when it was changed manually
    func bandwidthTest(result: Result<UInt32, Error>) // Emitted when trying to upgrade video bandwidth
    func playbackStateDidChange(playbackState: Player.PlaybackState) // Emitted when playbackState has changed
    func playerDidFail(error: Error) // Emitted when an error occurs
    func playerDidRecover() // Emitted when player has recovered from an error
    func progressDidChange(buffer: Int, latency: Int) // Emitted periodically to inform of progress downloading the media
    func qualitiesDidChange(qualities: [Quality]) // Emitted when qualities have changed
    func selectedQualityDidChange(selectedQuality: Quality?) // This method is deprecated and will be removed on the next version.
                                                            // Use the new 'activeQualityDidChange(activeQuality: Quality?, auto: Bool)'
    func sourceDidChange(currentSource: URL?) // Emitted when currentSource has changed
    func timeDidUpdate(currentTime: TimeInterval) // Emitted periodically to inform of current playback time changing
    func volumeDidChange() // Emitted when volume or muted has changed
    func playbackRateDidChange(playbackRate: Float) // Emitted when playbackRate has changed
}
```

To implement the `PlayerDelegate` you should do as follows:

```swift
player.delegate = //your class that implements the PlayerDelegate protocol
```

See [Livery iOS SDK Example App](https://github.com/exmg/livery-sdk-ios-example) to see how playback state change events are received via the `PlayerDelegate` implementation.

## Player Types

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

## Player Media Quality

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

## Player Fitting

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

## Audio Session

class **_AudioSessionSettings_**

| Name       | Type                             | Default       | Description                                                                        |
| ---------- | -------------------------------- | ------------- | ---------------------------------------------------------------------------------- |
| `category` | `AVAudioSession.Category`        | `playback`    | Audio session category identifiers.                                                |
| `mode`     | `AVAudioSession.Mode`            | `spokenAudio` | An object that communicates to the system how you intend to use audio in your app. |
| `options`  | `AVAudioSession.CategoryOptions` | `[]`          | Constants that specify optional audio behaviors.                                   |

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
  <string>_CC1AD845._googlecast._tcp</string>
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

- To **GET** custom messages you should implement the `PlayerInteractiveBridgeDelegate` by calling:

```swift
player.interactiveBridgeDelegate = self //being self the class that conforms to the PlayerInteractiveBridgeDelegate protocol
```

#### PlayerInteractiveBridgeDelegate

```swift
public protocol PlayerInteractiveBridgeDelegate: class {
    func getCustomMessageValue(message name: String, arg: Any?, completionHandler: @escaping (_ value: Any?) -> Void)
}
```

There you will get the custom messages you sent through the bridge on the interactive layer. You can use the name and the arg paramenters to better recognize the message itself and act on it by calling the `completionHandler` with a value that will be send back to the interactive layer.

- To **SEND** custom messages you should call:

```swift
player.sendInteractiveBridgeCustomCommand(name: String, arg: Any?, completionHandler: @escaping (CustomCommandResult) -> Void)
```

Those messages will be send to the interactive layer through the bridge. To get them on the interactive layer side you should first call the `registerCustomCommand(name, handler)` with the `name` matching the one used on the player. Please see Interactive Bridge [methods](/interactive-bridge?id=methods) section for more details.

```swift
public enum CustomCommandResult {
    case success(value: Any?)
    case failure(error: String)
}
```

## Analytics

Our SDK makes use of [Amazon Pinpoint](https://aws.amazon.com/pinpoint/).

If you are not using the AWSPinpoint in your own application there's no need to include this on your Podfile since this dependency is included in our SDK.

Note:

- If your application already contains AWSPinpoint make sure its version matches the one used in the SDK, which is:

```
  pod 'AWSPinpoint', '~> 2.24'
```

## Example App

Example iOS application which demonstrates how to use the Livery iOS SDK.<br>
You can find it on the following git repositories:

- Cocoapods: [Livery iOS Example App](https://github.com/liveryvideo/player-ios-example-cocoapods)
- Swift Package Manager: [Livery iOS Example App](https://github.com/liveryvideo/player-ios-example-spm)

Notes:

- Built using XCode 12.4 (12D4e) (Swift 5)

### How to run the app

1. Clone this repo
2. Go to the repo directory on the terminal and run `pod install`
3. Open **livery-sdk-ios-example.xcworkspace**
4. You will need to change the **Bundle Identifier** and the **Team** to your own team in order to build and run the app
5. Run the project on selected device or simulator

## Livery SDK dependencies

- The `Podfile` that you configure to install the Livery SDK must contain:

```
  pod 'AWSPinpoint', '~> 2.24'
  pod 'Kronos', '~> 4.2'
  pod 'Sentry', '~> 6.2'
  pod 'google-cast-sdk-no-bluetooth-dynamic-ios', '~> 4.4'

```

- Run `pod install --repo-update` before you continue
- From now on use YOUR_PROJECT_NAME. xcworkspace file in Xcode to open your project
- Finally import Livery to use the Framework in your codebase

## Too many symbols warning in AppStore validation

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

## Revision History

| Version | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.4.0   | Add support for Akamai Token.<br> Improvements on Player load time.<br> Improvements on stall recovery.<br> Improvements on the connection speed measurement.<br> Analytics improvements.<br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| 1.3.2   | Analytics improvements.<br> Improvements on `NtpClient`.<br> Memory leak fixes.<br> Fix Interactive Bridge handshake.<br> Fix pending messages not being sent on Interactive Bridge.<br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| 1.3.0   | Analytics improvements.<br> High latency sync improvements.<br> Player deinitialization improvements.<br> Swift Package Manager SDK.<br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| 1.2.5   | Improvements on Player stability and error handling.<br> Improvements on Analytics.<br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| 1.2.4   | Improvements on media download error handling.<br> Improvements on Analytics.<br> Improvements on ABR logic for devices with low battery levels.<br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 1.2.3   | Fixed crash caused by the `BandwidthTester`.<br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| 1.2.2   | Fixed crash caused by the `BandwidthTester`.<br> Fixed crash on `dataReceived` method.<br> Fixed `PingService` not re-starting when app returns from the background.<br> Fixed crash on `NtpClient`.<br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| 1.2.1   | Fixed crash caused by the `BandwidthTester`.<br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| 1.2.0   | Improvements on file download speed measurement.<br> Improvements on how the player goes to full screen mode.<br> Added `scrubber` and `play` controls options.<br> Added `configType` to the LiverySDK initialize method to setup the type of the remote configuration file.<br> Added `playbackRateDidChange(playbackRate: Float)` to PlayerDelegate.<br> Fixed `Quality` and `FullScreen` controls showing up when options set them to not show.<br> Fixed crash caused by the `PingService`.<br> Deprecate PlayerDelegate's `activeQualityDidChange(activeQuality: Quality?)` and `selectedQualityDidChange(selectedQuality: Quality?)`.<br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| 1.1.0   | Improvements on ABR algorithm.<br> Implemented sampling of analytics events.<br> Implemented remote config update interval.<br> Fixed bitcode archiving error.<br> Fixed crash on segment data processor.<br> Fixed crash on source switching.<br> Fixed crash on audio render.<br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| 1.0.0   | Improvements on media download.<br> Improvements on Player UI.<br> Improved touch detection on Interactive Layer.<br> Added full screen implementation.<br> Added `error` and `currentSource` properties to the Player.<br> Added response callback to `sendInteractiveBridgeCustomCommand`.<br> Added `timeDidUpdate`, `sourceDidChange`, `progressDidChange`, `playerDidRecover` and `playerDidFail` to Player Events (aka `PlayerDelegate`).<br> Changed `sendCustomMessage` method to `sendInteractiveBridgeCustomCommand`.<br> Changed `airplayButton` to `castButton` on `LiveryPlayerControlView` protocol.<br> Replaced `load()` to `reload()`.<br> Replaced `duration` with `currentTime`.<br> Replaced Player Events through Notification Center with `PlayerDelegate` protocol.<br> Removed deprecated synchronous player methods.<br> Removed Life Cycle methods.<br> Removed `muted` and `loop` player options.<br> Removed `dispose`, `addListener`, `renderStatusDidChange`, `durationChange`, `playbackRate`, `streamType` and `videoType`.<br> Fixed a crash in segment data processor.<br> Fixed a crash when a device has no space left.<br> |
| 0.12.0  | Added Cast.<br> Added Interactive Bridge.<br> Added Stream Phases.<br> Life Cycle methods are no longer needed.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| 0.11.6  | Changed interactive layer to allow automatic media playback and inline media playback.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| 0.11.5  | Improvements on Analytics.<br> Improvements on DASH manifest parser.<br> Added enableSentrySDK and audioSessionSettings.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| 0.11.4  | Improvements on HTTP error handling.<br> Improvements on Analytics.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| 0.11.3  | Added interactiveURL.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 0.11.2  | Fixed video stream stopping after receiving a call through FaceTime on an iPhone without SIM card.<br> Improvements on Analytics.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 0.11.1  | Fixed an issue regarding the player not recovering when the stream was stopped and started on the encoder side.<br> Improvements on the player recovery logic.<br> Improvements on Analytics.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| 0.11.0  | Changed ip lookup.<br> Improvements on ZAP Time.<br> Improvements on HTTP error handling.<br> Improvements on NTP error handling.<br> Improvements on the SDK initialization.<br> Fixed volume HUD taking too long to hide.<br> Fixed video stream stoping after receiving a call on iOS 14.<br> Fixed mute button not being in sync with the remote config.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

## Q&A
