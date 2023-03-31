# Livery Video Android SDK

More information can be found at: [liveryvideo.com](https://liveryvideo.com).

## Installation

### Compatibility

Livery Android SDK is compatible with Android 5.0 (API level 21) or higher.

### Configure Gradle

From version 2.0.0 onwards Livery SDK is published via [jitpack.io](https://jitpack.io).

You can still use bintray repository and credentials, please follow the steps in [here](#installation-from-bintray).

To install Livery SDK into your project, follow these steps below.

#### Resolve Repositories

For new projects created with Android Studio Arctic Fox 2020.3.1 go to settings.gradle and add:

```groovy
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        .....
        maven {
            url "https://jitpack.io"
            credentials { username authToken }
        }
    }
}
```

For existing projects go to your project level build.gradle file, add these closures to **allprojects** > **repositories**.

```groovy
allprojects {
   repositories {
      //...
      google()
      jcenter()
      maven {
         url 'https://jitpack.io'
         credentials { username '<your access token>' }
      }
   }
}
```

To retrieve your access token log into <http://jitpack.io> with your GitHub account and go to <https://jitpack.io/w/user>.

Your GitHub account has to be granted access to the packages.
If you are unable to access the packages please ask your support contact to grant you access.

#### Add Implementations

Add these implementations inside **dependencies** to your app-level build.gradle.

```groovy
dependencies {
   //...
   def livery_sdk_version = '3.1.0'
   implementation "com.liveryvideo:livery-sdk-android:$livery_sdk_version"
   //...
}
```

#### Set Java Version of Project

Then add this compileOptions inside **android** closure on your app level build.gradle, like shown below.

```groovy
android {
//...
  compileOptions {
     sourceCompatibility 1.8
     targetCompatibility 1.8
  }
}
```

## Usage

For basic usage of the SDK following minimal steps are needed:

### Configure SDK streamId

Add `livery_stream_id` to your strings and pass the streamId as the value.

```xml
<resources>
   <!--...-->
   <string name="livery_stream_id">insert_stream_id_here</string>
</resources>
```

The `LiverySDK` will initialize itself using the string resource `livery_stream_id` when the application starts.

It is possible to know the LiverySDK State by calling:

```java
LiverySDK.State state = LiverySDK.getInstance().getState();
```

### Add LiveryPlayerView

Add the player view into the layout that your activity or fragment is using.

Please note that **keepScreenOn** attribute is optional and it defines whether
screen can turn off after an amount of time or not.

```xml
<tv.exmg.livery.LiveryPlayerView
   android:id="@+id/liveryPlayer"
   android:layout_width="match_parent"
   android:layout_height="wrap_content"
   android:keepScreenOn="true" />
```

Due to the Cast feature the Player's Activity Theme needs to have a `colorPrimary` defined without opacity.

### Setup LiveryPlayerView

Bind LiveryPlayerView to your layout file, then call `createPlayer` to setup the LiveryPlayerView.

```java
LiveryPlayerView playerView;

@Override
public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
   super.onViewCreated(view, savedInstanceState);

   playerView = view.findViewById(R.id.liveryPlayer);
   playerView.createPlayer();
}
```

The player might not be initializaed right after `createPlayer` call. For example, it may be defer to after `LiverySDK`
initialization finishes. [LiveryPlayerView.CreatePlayerListener and LiveryPlayerView.CreatePlayerErrorListener](#createplayer-feedback)
provide more information if the player was initialized or there was an error during initialization.

### Register in a LifecycleOwner

`LiveryPlayerView` implements `DefaultLifecycleObserver` and is must be registered in a `LifecycleOwner`, for example when using a `Fragment`:

```java
public void onViewCreated(@NonNull View view, Bundle savedInstanceState) {
   super.onViewCreated(view, savedInstanceState);
   ...
   LiveryPlayerView playerView = view.findViewById(R.id.livery_player_view);
   getLifecycle().addObserver(playerView);
   ...
}
```

#### 🎉 Done

Congratulations! You have implemented Livery SDK successfully.

To have more control over Livery SDK, next sections should provide you more knowledge.

## Advanced configuration

### Manual SDK Configuration

Defining the `streamId` via the resource string `livery_stream_id` as it is in [Configure SDK streamId](#configure-sdk-streamid)
automatically initializes the SDK when the application starts.

It is possible defer this initialization to a later point by doing it manually by defining the boolean
resource `livery_sdk_auto_init` with `false` instead of `livery_stream_id`:

```xml
<resources>
   <!--...-->
    <bool name="livery_sdk_auto_init">false</bool>
</resources>
```

and then use the `LiverySDK.initialize` method that allows to define a desied `streamId` and to have some feedback
about the SDK initization process:

```java
   String streamId = ...
   LiverySDK.getInstance().initialize(streamId, new LiverySDK.StateListener() {
      @Override
      public void stateChanged(LiverySDK.State state) {

      }

      @Override
      public Lifecycle getListenerLifecycle() {
         return getLifecycle();
      }
   });
```

When choosing the manual initialization neither LiverySDK nor LiveryPlayerView will automatically retry to initialize in
case or errors. Error handling and retry needs to be done manually.

```java
void initializeSdk(String streamId) {
   LiverySDK.getInstance().initialize(streamId, new LiverySDK.StateListener() {
      @Override
      public void stateChanged(LiverySDK.State state) {
         if (LiverySDK.State.INITIALIZED.equals(state)) {
               // continue
         } else {
               // handle error
               Exception error = state.error;
               // retry
               requireView().post(() -> initializeSdk(streamId));
         }
      }

      @Override
      public Lifecycle getListenerLifecycle() {
         return getLifecycle();
      }
   });
}
```

LiveryPlayerView `createPlayer` should only be called after LiverySDK is initialized, otherwise this method will fail.

### Configure LiveryPlayerView

#### CreatePlayer feedback

It is possible to have more control of the LiveryPlayerView initialization. There are 2 interfaces in
`LiveryPlayerView` that allow to know when the player was configured or if there was an error during configuration:

```java
   LiveryPlayerView playerView = ...
   playerView.createPlayer(new LiveryPlayerView.CreatePlayerListener() {
      @Override
      public void finished() {

      }
   }, new LiveryPlayerView.CreatePlayerErrorListener() {
      @Override
      public void onError(Exception error) {

      }
   });
```

#### LiveryPlayerView options

The LiveryPlayerView can be configured with either remote or local configurations. When local configurations are set
they will override the corresponding remote configurations.

The remote configuration is automatically applied when calling createPlayer without parameters. This remote configuration can be set in the Livery Portal.

```java
   LiveryPlayerView playerView = ...

   LiveryControlsOptions controlsOptions = new LiveryControlsOptions.Builder()
            .showFullscreen(true)
            .showMute(true)
            .showQuality(true)
            .showPlay(true)
            .showScrubber(true)
            .showError(true)
            .build();

   LiveryPlayerOptions playerOptions = new LiveryPlayerOptions.Builder()
            .setAutoPlay(true)
            .setControlsOptions(controlsOptions)
            .setResizeMode(LiveryResizeMode.DEFAULT)
            .setPrePoster("prePoster url")
            .setPoster("poster url")
            .setPostPoster("postPoster url")
            .setAkamaiLongToken("akamai_token")
            .build();

   playerView.createPlayer(playerOptions);
   // or
   playerView.createPlayer(playerOptions, this::onPlayerCreated, this::onCreatePlayerError);

```

## SDK Methods

> To access the SDK class, ‘LiverySDK’ should be used.

### SDK Instance

In order to call some methods, instance of the SDK must be used.
You can get the instance with the line below:

```java
LiverySDK.getInstance();
```

### Pinpoint User ID

In some cases, getting the pinpoint user ID can be useful, i.e.: You would like to show the user their pinpoint user ID for analysis based on a specific user. To do so, here is how to get the pinpoint user ID:

```java
String pinpointUserId = LiverySDK.getInstance().getPinpointUserId();
```

## Player Methods

> To access these methods, use the reference to your LiveryPlayerView from your fragment or activity.

### Create Player

By calling `createPlayer()` on LiveryPlayerView reference, a player instance will be created and initialized.

### Current Time

#### Get Current playback time since the stream has started.

```java
long currentTime = playerView.getCurrentTime();
```

### Duration

#### Get the duration of the stream. For Live streams returns the duration since the start of the stream.

```java
long duration = playerView.getDuration();
```

### Mute/Unmute

#### Set Muted/Unmuted State

```java
boolean shouldMute = true;
playerView.setMuted(shouldMute);
```

#### Get Muted/Unmuted State

```java
boolean isMuted = playerView.isMuted();
```

### Volume

#### Set Volume

```java
float volume = 0.5f; //Must be between 0 and 1 inclusively.
playerView.setVolume(volume);
```

#### Get Volume

```java
float volume = playerView.getVolume();
```

### Latency

#### Set Target Latency

```java
int targetLatencyInMilliseconds = 5000;
playerView.setTargetLatency(targetLatencyInMilliseconds);
```

#### Get Target Latency

```java
long currentLatency = playerView.getLatency();
```

### Media Quality

#### Get Available Qualities

```java
List<LiveryQuality> qualities = playerView.getQualities();
```

#### Get Active Quality

```java
LiveryQuality activeQuality = playerView.getActiveQuality();
```

#### Set Quality

```java
playerView.setQuality(liveryQuality.qualityId);
```

When you want to set quality to **automatic** mode or **audio-only** mode,
you need to make a call with a specific id that does not return in getQuality method.

For that, you can use the methods below.

```java
//Sets the quality automatically for the rest of the media,
//until a quality id is specified.
playerView.setQuality(LiveryQuality.AUTO_ID);

//Sets the quality to audio only.
playerView.setQuality(LiveryQuality.AUDIO_ONLY_ID);
```

### Audio Only Mode

When you want users to not watch the video but hear it, you can use the method mentioned above, since that forces the player to switch to audio-only mode.

However, there can be some cases that you might want the player to switch to audio-only mode automatically to keep playing under bad network conditions. For this particular reason, you can use the method below. By default, this behavior is disabled.

```java
playerView.setAutoSwitchToAudioOnlyMode(true);
```

In the qualities list, the audio-only mode is not visible by default. If you want it to be shown, use this method:

```java
playerView.setShowAudioOnlyModeInQualitiesList(true);
```

### Akamai long token

Defines a access token to use to be able to see a stream that uses Akamai’s Token Auth feature.

```java
playerView.setAkamaiLongToken("akamai_token");
```

### Debug Mode

#### Set Debug Mode Status

This will create an info dialog on the top right corner of the player and output all logs of SDK.

```java
boolean isDebugMode = true;
playerView.setDebugModeEnabled(isDebugMode);
```

#### Get Debug Mode Status

```java
boolean isDebugModeEnabled = playerView.isDebugModeEnabled();
```

## Classes

### LiverySDK State

LiverySDK.State is used to provide LiverySDK state feedback either with [automatic initalization](#configure-sdk-streamid)
or [manual initialization](#manual-sdk-configuration).

LiverySDK.State can be one of 3 values:

- `LiverySDK.State.NOT_INITIALIZED`
- `LiverySDK.State.INITIALIZED`
- `LiverySDK.State.ERROR` where you can access the inner Exception via `LiverySDK.State.error` member.

### Player Options

LiveryPlayerOptions has these properties listed on the table below. These options take effect only after [createPlayer](#liveryplayerview-options) is called.
Each of these properties can be defined individually via LiveryPlayerOptions.Builder.

class **_LiveryPlayerOptions_**

| Name              | Type                        | Default                       | Description                                                                                                                                |
| ----------------- | --------------------------- | ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `autoPlay`        | `Boolean`                   | `true`                        | Determines whether video shall play immediately after [createPlayer](#create-player).                                                      |
| `controls`        | `LiveryControlsOptions`     | `new LiveryControlsOptions()` | Provides access to [LiveryControlsOptions](#liveryplayeroptions-and-liverycontrolsoptions). Player controls can be tweaked by this object. |
| `fit`             | `LiveryResizeMode`          | `CONTAIN`                     | Provides access to LiveryResizeMode. Video size inside the player view can be changed from here.                                           |
| `prePoster`       | `String`                    | null                          | Represents URL to poster image. When this property is not empty, the player will show a poster on creation.                                |
| `poster`          | `String`                    | null                          | Represents URL to poster image. When this property is not empty, the player will show a poster on creation.                                |
| `postPoster`      | `String`                    | null                          | Represents URL to poster image. When this property is not empty, the player will show a poster on creation.                                |
| `akamaiLongToken` | `String`                    | null                          | Access token to use to be able to see a stream that uses Akamai’s Token Auth feature.                                                      |
| `sources`         | `String` or `Array<String>` | None                          | Defines the sources URLs.                                                                                                                  |

### Player Resize Mode

Player resize mode is passed on [Player Options](#player-options) to indicate video size.

enum **_LiveryResizeMode_**

| Name      | Description                                                                                                       |
| --------- | ----------------------------------------------------------------------------------------------------------------- |
| `CONTAIN` | Shows the full video in the view box no matter what, without breaking the aspect ratio.                           |
| `COVER`   | Fills the view box, however, zooms the video if video dimensions are too big for the view. Keep the aspect ratio. |
| `FILL`    | Fits the video into the view box, by ignoring aspect ratio rules.                                                 |

### Player Controls

You can edit LiveryControlsOptions from controls property of LiveryPlayerOptions.
Each of these properties can be defined individually via LiveryControlsOptions.Builder.
Available controls are listed on the table below.

class **_LiveryControlsOptions_**

| Name         | Type      | Default | Description                                   |
| ------------ | --------- | ------- | --------------------------------------------- |
| `fullscreen` | `Boolean` | `false` | Sets the visibility of the fullscreen button. |
| `mute`       | `Boolean` | `false` | Sets the visibility of the mute button.       |
| `quality`    | `Boolean` | `false` | Sets the visibility of the quality button.    |
| `play`       | `Boolean` | `false` | Sets the visibility of the play button.       |
| `scrubber`   | `Boolean` | `false` | Sets the visibility of the scrubber button.   |
| `contact`    | `Boolean` | `false` | Sets the visibility of the contact button.    |
| `error`      | `Boolean` | `false` | Sets the visibility of the error overlay.     |

### Player Events

It might be important in some cases to receive player events and process the data for your needs. In order to receive player events, a LiveryPlayerListener must be registered to the player.

class **_LiveryPlayerListener_**

| Name                                                                   | Description                                                                                                             |
| ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `onActiveQualityChanged(@NonNull LiveryQuality quality, boolean auto)` | Called when the active quality of the media being played has changed.                                                   |
| `onPlayerError(Exception e)`                                           | Called when the player encounters an error.                                                                             |
| `onPlayerStateChanged(@NonNull LiveryPlayerState playerState)`         | Called when the playback state of the player has changed.                                                               |
| `onProgressChanged(long buffer, long latency)`                         | Called in short intervals while playing, buffer and latency values in milliseconds can be actively retrieved from here. |
| `onQualitiesChanged(@NonNull List<LiveryQuality> qualities)`           | Called when available qualities of media have changed.                                                                  |
| `onPlaybackRateChanged(float rate)`                                    | Called when the playback rate of current media has changed.                                                             |
| `onRecovered()`                                                        | Called when an error is recovered.                                                                                      |
| `onTimeUpdate(long currentTime)`                                       | Called when the current time of media has changed.                                                                      |
| `onSourceChanged(@NonNull String source)`                              | Called when the media source URL of the player has changed.                                                             |

#### Registering Player Event Listener

By calling `registerListener(LiveryPlayerListener)` method on LiveryPlayerView, a player event listener can be registered.

```java
playerView.registerListener(new LiveryPlayerListener() {
   //...
});
```

### Player Media Quality

class **_LiveryQuality_**

| Name        | Type      | Description                     |
| ----------- | --------- | ------------------------------- |
| `bitrate`   | `Integer` | Bitrate of the quality.         |
| `codecs`    | `String`  | Codec of the media.             |
| `frameRate` | `Float`   | Frame rate of the media.        |
| `height`    | `Integer` | Height of the media.            |
| `label`     | `String`  | Human readable name of quality. |
| `qualityId` | `String`  | ID of quality.                  |
| `width`     | `Integer` | Width of the media.             |

## Handle Orientation Changes

There are situations when you need to handle screen orientation changes so that the player won’t re-initialize after changing orientation. To prevent it, add this line to the manifest in the activity scopes which you want to handle orientation changes.

```xml
<activity
android:configChanges="orientation|screenSize">
<!-- ... -->
</activity>
```

## Customize

Livery Player has three layers that you can customize. They are loading indicator, error overlay and player controls. To customize each one of them, a separate layout file must be created and a specific ID should be given to the related view in the layout.

### Loading Indicator

To customize the loading indicator, create a layout named `livery_player_loading_view.xml`.

For this layout, there are no IDs available. This means that whenever the player is loading, this layout will be shown.

```xml
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <ProgressBar
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center" />

</FrameLayout>
```

### Error Overlay

To customize error overlay, create a layout named `livery_player_error_view.xml`.

Available IDs and their corresponding view types are listed below,
ID names are self-explanatory about what that item does.

| Name          | ID                    | Type           |
| ------------- | --------------------- | -------------- |
| Error Text    | `livery_error_text`   | `TextView`     |
| Reload Button | `livery_error_button` | `LinearLayout` |

### Player Controls

To customize player controls, create a layout named `livery_player_control_view.xml`.

Available IDs and their corresponding view types are listed below, ID names are self-explanatory about what that item does. Each view is optional.

| Name                   | ID                              | Type                |
| ---------------------- | ------------------------------- | ------------------- |
| Play Button            | `livery_play`                   | `View`              |
| Pause Button           | `livery_pause`                  | `View`              |
| Mute Button            | `livery_mute`                   | `View`              |
| Unmute Button          | `livery_unmute`                 | `View`              |
| Fullscreen Button      | `livery_fullscreen`             | `View`              |
| Quality Indicator Text | `livery_quality_indicator_text` | `TextView`          |
| Time bar               | `livery_progress`               | `LiveryProgressBar` |
| Live Indicator         | `livery_live_indicator`         | `View`              |
| Catchup Indicator      | `livery_catchup_indicator`      | `View`              |
| Time Indicator         | `livery_time_indicator`         | `TextView`          |
| Settings Button        | `livery_settings`               | `View`              |
| Contact Button         | `livery_contact`                | `View`              |

Note: Be aware of the system UI elements such as navigation and status bar when going to full screen mode, and using custom UI elements.

### Audio Only Stream Indicator

To customize the loading indicator, create a layout named `livery_player_audio_only_view.xml`.

For this layout, there are no IDs available. This means that whenever the player is playing only audio, this layout will be shown.

## Interactive Bridge

It is possible to interact with LiveryPlayerView and the Interactive Layer using the [Interactive Bridge](/interactive-bridge).

The interactive URL can be defined with:

```java
LiveryPlayerView playerView = ...
String url = ...
playerView.setInteractiveUrl(url);
```

Besides the [methods](/interactive-bridge?id=methods) already definied on the Interactive Bridge to get values like the player **latency**, **quality**, etc. you can get and send custom messages.

### Custom commands

From the Android LiveryPlayerView it is possible to send and receive custom command to/from the interactive layer.

A custom command with a specific `name` and `argument` can be sent with the `sendInteractiveBridgeCustomCommand` method:

```java
String name = ...
String argument = ...
playerView.sendInteractiveBridgeCustomCommand(name, argument, new LiveryInteractiveBridge.CustomCommandResponseCallback() {
   @Override
   public void result(@Nullable Object response, @Nullable String error) {

   }
});
```

To receive callbacks from the interactive layer a `LiveryInteractiveBridge.CustomCommandListener` object needs to be
register with the `setInteractiveBridgeCustomCommandListener`
method.
The commands are received on `onMessage` with the `name`, `argument` and a `callback`
that allows to sent back to the interactive layer a result Object:

```java
playerView.setInteractiveBridgeCustomCommandListener(new LiveryInteractiveBridge.CustomCommandListener() {
   @Override
   public void onMessage(@NonNull String name, @Nullable String argument, @Nullable LiveryInteractiveBridge.CustomCommandResultCallback callback) {
      ...
      Object result = ...
      callback.result(result);
   }
});
```

## Sentry Integration

Livery uses Sentry for error reporting. In order to setup sentry.io with the correct DNS LiverySDK forces its initialization to manual
by setting `false` to [io.sentry.auto-init](https://docs.sentry.io/platforms/android/configuration/manual-init/) meta-data key.

To enable sentry.io auto initialization please add the following to the Application AndroidManifest.xml

```xml
<meta-data android:name="io.sentry.auto-init" android:value="true" tools:replace="android:value" />
```

## Migration Guide

### Migration from 2.4 to 3.0

#### Livery SDK Package name

The Livery SDK package name changed to `com.liveryvideo.sdk` in the 3.0.0 release.

It should be safe to replace `tv.exmg.livery` with `com.liveryvideo.sdk` everywhere in the project.

### LiverySDK

LiverySDK now implements DefaultLifecycleObserver. The LiverySDK registers itself on the Aplication's LifecycleOwner.

The `Lifecycle getListenerLifecycle()` method was added to LiverySDK.StateListener interface to get the Lifecycle that the LiverySDK should take into account when calling the initialization listener. The getter implementation is options and the default return null. It is advised to return a valid Lifecycle via this method to prevent accessing to a destroyed Activity/Fragment. See [Manual SDK Configuration](#manual-sdk-configuration) for mode details.

### LiveryPlayerView

The methods

```java
LiveryPlayerView.onStop()
LiveryPlayerView.getTimeOffset()
LiveryPlayerView.getPlaybackRate()
```

were removed.

LiveryPlayerView now implements `DefaultLifecycleObserver` meaning that the methods

```java
   liveryPlayer.onResume()
   liveryPlayer.onPause()
   liveryPlayer.onDestroy()
```

now receive a `LifecycleOwner`

```java
   liveryPlayer.onResume(@NonNull LifecycleOwner owner)
   liveryPlayer.onPause(@NonNull LifecycleOwner owner)
   liveryPlayer.onDestroy(@NonNull LifecycleOwner owner)
```

It is recommended to register the LiveryPlayerView in a `LifecycleOwner` like an `Activity` or `Fragment`.

```java
   getLifecycle().addObserver(inlinePlayerView);
   // or
```

#### LiveryPlayerState

The state LiveryPlayerState.STATE_IDLE was removed.

#### LiveryPlayerListener

Version 3.0 removes the deprecated method from `LiveryPlayerListener`:

Replace:

```java
LiveryPlayerListener.onActiveQualityChanged(LiveryQuality quality)
```

with:

```java
LiveryPlayerListener.onActiveQualityChanged(LiveryQuality quality, Boolean auto)
```

And the method

```java
LiveryPlayerListener.onPlaybackRateChanged(float rate)
```

was also removed.

#### LiveryQuality

LiveryQuality no longer has a `track`property.
From 3.0.0 there are 2 static properties `AUTO_ID` and `AUDIO_ONLY_ID` which can be used to `setQuality` in LiveryPlayerView.

```java
   liveryPlayer.setQuality(LiveryQuality.AUTO_ID)
   // ...
   liveryPlayer.setQuality(LiveryQuality.AUDIO_ONLY_ID)
```

#### LiveryPlayerOptions

The properties of LiveryPlayerOptions are now final.

The method

```java
LiveryPlayerOptions.setAkamaiToken(String token)
```

was renamed to

```java
LiveryPlayerOptions.setAkamaiLongToken(@Nullable String token)
```

#### LiveryControlsOptions

The play and scrubber are now hidden regarding of any value defined in `LiveryControlsOptions.showPlay` and `LiveryControlsOptions.showScrubber`.
A new `showContact` setter method was added to `LiveryControlsOptions`

#### Error Overlay

The IDs used to customize the error overlay

```java
   R.id.livery_error_header
   R.id.livery_error_overlay_text
   R.id.livery_error_overlay_button
```

were replaced with

```java
   R.id.livery_error_text    -> TextView
   R.id.livery_error_button   -> View
```

#### Player Controls

The interface `TimeBar` was replaced with `LiveryProgressBar`

The IDs used to customize the player's controls

```java
   R.id.livery_quality
   R.id.livery_quality_indicator_background
   R.id.livery_time_separator
   R.id.livery_live_circle
   R.id.livery_live_text
```

were remove.

#### Nullability changes

`@NonNull` and `@Nullable` annotations were added to some methods in the 3.0.0 release:

- `LiveryPlayerListener.onActiveQualityChanged`
- `LiveryPlayerListener.onPlayerStateChanged`

#### Interactive Bridge

It is no longer possible to subscribe to orientation changes via Interactive Bridge.

### Migration from 1.7 to 2.0

#### LiverySDK initialization

With version 2.0 we provide more control over LiverySDK initialization and more feedback.

The method in LiverySDK:

```java
public void updateRemoteConfig(String streamID);
```

was replaced with

```java
public void initialize(String streamId, StateListener listener)
```

where initialization feedback is given via the `LiverySDK.StateListener` callback.

Although you can call `initialize(String, StateListener)` regardless of LiverySDK being automatic or manually initialized
it is adviced to setup LiverySDK manually by setting the boolean resource `livery_sdk_auto_init` to `false` like as it is
in [Manual SDK Configuration](#manual-sdk-configuration) section.

The methods

```java
LiverySDK.getPlayerOptions()
```

where removed. Please use LiveryControlsOptions and LiveryPlayerOptions builders as is explained in
[LiveryPlayerView options](#liveryplayerview-options)

#### LiveryPlayerView

Version 2.0 adds a few `createPlayer` overload methods. It is adviced to use the default method:

```java
LiveryPlayerView playerView = ...
playerView.createPlayer();
// or
playerView.createPlayer(this::onPlayerCreated, this::onCreatePlayerError);
```

although the `createPlayer(LiveryPlayerOptions)` still [exist](#liveryplayerview-options).

The method

```java
LiveryPlayerView.loadInteractiveLayer(String);
```

was renamed to

```java
LiveryPlayerView.setInteractiveUrl(String);
```

#### LiveryPlayerOptions and LiveryControlsOptions

`LiveryPlayerOptions` and `LiveryControlsOptions` should now be defined with the corresponding Builder class.
See [LiveryPlayerView options](#liveryplayerview-options) section as an example.

#### LiveryPlayerListener

LiveryPlayerListener now have a no-op default implementation so there is no need to implement all methods.

LiveryPlayerListener.onProgressChanged was changed from

```java
onProgressChanged(long buffer, long bufferEnd, long latency)
```

to

```java
onProgressChanged(long buffer, long latency)
```

The methods `onDurationChanged` and `onVolumeChanged` were also removed.

## Installation from bintray

It is still possible to use bintray repository/credentials for until version 2.0.0.

### Resolve Repositories

In your project level build.gradle file, add these closures to **allprojects** > **repositories**.

```groovy
allprojects {
   repositories {
       //...
       google()
       jcenter()
       maven { url 'https://jitpack.io' }
       maven {
           url "https://exmg.bintray.com/livery"
           credentials {
               username 'received_username'
               password 'bintray_API_key'
           }
       }
   }
}
```

You can get the Bintray API key by following these steps below:

1. Navigate to [Bintray Login](https://bintray.com/login) then login with the credentials given to you.
2. After logging in, go to [Edit Profile](https://bintray.com/profile/edit) from the menu on top right which is shown by clicking to your username.
3. Once you land to Edit Profile page, you should be able to see more menu items on the left. Click to 'API Key' and you will be asked to input your password. After verifying the password, API Key will be shown.

### Add Implementations

Add these implementations inside **dependencies** to your app-level build.gradle.

```groovy
dependencies {
   //...
   implementation 'tv.exmg.livery:livery:2.0.0'
   //...
}
```

## Revision History

| Version | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 3.1.0   | - VOD Support added<br>- Catchup Support added<br>- Enabled NTP                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| 3.0.0   | - New Player UI<br>- Replace AkamaiToken with AkamaiLongToken<br>- Improve Nullability<br>- Cast was support was removed<br>- Update targetSdkVersion to 32<br>- Update ExoPlayer to version 2.18.1<br>- Add OkHttp version 4.10.0<br>- Cleanup unused Views IDs<br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| 2.4.2   | - Fixed an issue that could occur while a stall happens                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| 2.4.1   | - New Livery bridge properties added<br> - Fix issue related to initializePlayer<br> - ABR improvement<br> - Improve logic around reconnects<br> - Minor fixes and improvements                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| 2.4.0   | - akamai token support<br>- add prePoster and postPoster to LiveryPlayerOptions                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| 2.3.1   | - Fixed Unexpected runtime error which could occur on a timeout.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 2.3.0   | - Update ExoPlayer to 2.16.0<br>- Update Sentry SDK<br>- Send error analytics event when unable to fetch config failed<br>- For the speed measurement, we no longer rely on the "live" segment if possible<br>- Improved error messages copy<br>- Fixed the player UI not taking the native UI into account<br>- Prevent player access outside main thread<br>- The new Android SDK 2.2.0 reported Switch bitrate analytics events while it should not                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| 2.2.0   | - implement stream phase<br>- add subscribeStreamPhase to interactive layer<br>- implement smooth bitrate downgrade<br>- use different downgrade thresholds according to current bitrate<br>- fix controls sometimes not appearing due to multiple events from interactive layer<br>Internal:<br>- disable bust requests when downloading manifest<br>- handle play/scrubber from RemoteConfig.controls<br>- disable cast<br>- add reason/description to bitrate switch analytics event<br>- improve player startup and synchronization logic                                                                                                                                                                                                                                                                                                                                                                               |
| 2.1.5   | - Update SDK version                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| 2.1.4   | - Improvements on Player stability and error handling <br>- Improvements on Analytics                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| 2.1.3   | - Fix for crash related to unavailable stream sources.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| 2.1.2   | - Analytics fix                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| 2.1.1   | - Fix crash with devices using Android API level 25 or lower                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| 2.1.0   | - Improved server error handling <br>- Fixed LiveryPlayerView.getActiveQuality() when quality was set manually <br>- LiveryPlayerView.createPlayer callback methods are now also called when SDK is not initialized <br>- Allow analytics and Sentry sampling <br>- Added remote config update interval <br>- Added subscribeOrientation to interactive layer <br>- Updated Sentry to v3.1.3                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| 2.0.0   | - Added bridge calls for interactive layer <br>- Added isPlayerInitialized property <br>- Added the ability to use ExoPlayer next to the LiveryPlayer <br>- Improved full screen experience <br>- Improved initialization process and added opportunities receive error feedback <br>- Improved main/backup switching <br>- Better IPv6 support <br>- Fixed several crashes that could occur in edge cases <br>- currentTime property is made read-only <br>- Renamed loadInteractiveLayer to setInteractiveUrl and currentSrc to currentSource <br>- Removed the need to manually set the player to unbranded <br>- Removed muted and loop player options <br>- Removed overrideFullscreenButton and overrideQualityButton methods <br>- Removed all exoplayer methods <br>- Removed bufferEnd, zeroTimestamp, streamTimestamp, decodedFrames, droppedFrames, setPlaybackRate, getStreamType, videoType, onDurationChanged |
| 1.7.16  | - Fixed initialization issues <br>- Disabled NTP                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 1.7.13  | - Improved time syncing in SDK initialization                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 1.7.12  | - Improved loading of streams without specifying sources                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 1.7.11  | - Fixed streamId in analytics                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 1.7.10  | - Fixed a crash on a number of old devices                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| 1.7.9   | - It is no longer possible to create the player with a config URL. Please use the streamId (which is part of the config URL) instead <br>- Use RemoteConfig to configure analytics <br>- Change IP lookup <br>- Improve CDN error handling <br>- Use unbranded property from config                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| 1.7.8   | - New ABR algorithm <br>- `livery_remote_config_url` was renamed to `livery_stream_id` <br>- loading source url's from remote config <br>- Send streamId to analytics                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| 1.7.7   | - Fixed an issue which could occur if NTP is not available                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| 1.7.6   | - Improved bitrate switching <br>- Add ‘Casting’ feature. <br>- Player's Activity Theme needs to have a `colorPrimary` defined without opacity. <br>- Change UI of player controls. <br>- Add new fields to Player Controls Customizing documentation. <br>- Change audio only indicator.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| 1.7.1   | - Add the possibility to initialize SDK only with customer ID. <br>- Fix a crash issue on null media source. <br>- Refactor the way loading view worked, add extra parameters for switching qualities. <br>- Added an algorithm to fetch and check the cache of remote config each minute.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| 1.7.0   | - Reset dropped frames each progress event. <br>- Fix bitrate sorting issue. <br>- Fix invalid stall durations and wrong end session event reports.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| 1.6.9   | - Fix an issue where branded version error view could be overridden. <br>- Fix an issue where min or max buffer would be reported below zero. <br>- Refactor stall reporting mechanism. <br>- Fix an issue where the player would not recover. <br>- Save muted or unmuted state to keep it after recovering.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 1.6.8   | - Remove load function. <br>- Add toggle method for play/pause actions. <br>- Fix a crash issue on playerStateChanged when playerOptions was passed null. <br>- Fix pause method so it won't skip frames on paused state.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| 1.6.7   | - Added "Error" event for analytics. <br>- Added "PinPointID" to Sentry tags. <br>- Added missing fields to "Connection Change" and "Switch Bitrate" events.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| 1.6.6   | - Fixed paid version of SDK was not using default error overlay.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 1.6.5   | - Fixed analytics values (EdgeServer, VideoURL) not being reported sometimes.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 1.6.4   | - Fixed a crash occured when player.dispose() method was called due to uiElementsHandler not being able to find the player instance.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |

## Q&A
