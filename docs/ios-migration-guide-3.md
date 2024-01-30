# [Livery Video iOS SDK](/ios-sdk#migration-guide)

## Migration from 2.1.0 to 3.0.0

### LiverySDK changes

It is no longer necessary to hold an instance of `LiverySDK`, `LiverySDK` have static methods for initialization:

```swift
LiverySDK.initialize(streamId: streamId) { error in
    // error is a LiveryErrors?
}
```

together with `async/await` alternative:

```swift
try await LiverySDK.initialize(streamId: streamId)
```

`LiverySDK.Errors` was renamed to `LiveryErrors`

`LiverySDK.ResultConfig` was removed and now `initialize` completion block receive a `LiveryErrors` in case of an error and `nil` otherwise.

In case of the `async/await` alternative, `initialize` throws a `LiveryErrors` in case of an error.

## Player and LiveryPlayerView

`Player` no longer exists and it was replaced by `LiveryPlayerView`.

`LiveryPlayerView` is a UIView subclass that can be used to play a stream and can be placed in the view hierarchy.

The method `LiveSDK.createPlayer` was moved to `LiveryPlayerView`:

```swift
let playerView = LiveryPlayerView()
try playerView.createPlayer(with: options)
```

**Note**: `LiverySDK` should be initialized before calling `createPlayer` method.

`Player` methods and properties were moved to `LiveryPlayerView`.

`LiveryPlayerView` returns `nil` in case of `createPlayer` has not being called yet for these properties: `playbackState`, `latency`, `buffer`, `currentTime`

`play`, `pause` and `stop` methods were moved to `LiveryPlayerView` and no longer accept a `completionQueue` nor a `completion` block.

### UI customization

It is no longer possible to customize the Player UI via protocols: `Player.customLoadingView`, `Player.customErrorView` and `Player.customControlView` were removed.

The `RenderProtocol` and `AudioRenderProtocol` along with `setRenderProtocol` method were removed.

### Properties type changes

- `currentTime` and `duration` properties are now of type `Int64` instead of `TimeInterval` representing milliseconds instead of seconds.
- `buffer` and `latency` properties are now of type `Int64` instead of `Int`.

### Removals

- `Player.volume` property was removed
- `Player.Errors` was removed

## Names renaming

Class/structs/enums have been renamed with `Livery` prefix to avoid name colisions.

- `AudioSessionSettings` has been renamed to `LiveryAudioSessionSettings`
- `PlayerDelegate` has been renamed to `LiveryPlayerDelegate`
- `PlayerOptions` has been renamed to `LiveryPlayerOptions`
- `PlayerFit` has been renamed to `LiveryPlayerFit`
- `PlayerPosition` has been renamed to `LiveryPlayerPosition`
- `Quality` has been renamed to `LiveryQuality`
- `Player.PlaybackState` has been renamed to `LiveryPlaybackState`
- `StreamPhase` has been renamed to `LiveryStreamPhase`
- `ControlsOptions` has been renamed to `LiveryControlsOptions`
- `LiverySDK.Errors` has been renamed to `LiveryErrors`
- `PlayerInteractiveBridgeDelegate` has been renamed to `LiveryInteractiveBridgeCustomCommandDelegate`
- `CustomCommandResult` has been renamed to `LiveryInteractiveBridgeCustomCommandResult`
