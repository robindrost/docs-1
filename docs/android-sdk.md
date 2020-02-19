# Livery Android SDK

Ex Machina Group Livery Android SDK.

Documentation can be found at: [docs.liveryvideo.com/android-sdk](https://docs.liveryvideo.com/android-sdk).

More information can be found at: [liveryvideo.com](https://liveryvideo.com).

## Installation

### Compatibility

Livery Android SDK is compatible with Android 5.0 (API level 21) or higher.

### Download & Configure

#### Step 1: Resolve Repositories

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
               username ‘received_username’
               password 'bintray_API_key’
           }
       }
   }
}
```

#### Step 2: Add Implementations

Add these implementations inside **dependencies** to your app-level build.gradle.

```groovy
dependencies {
   //...
   implementation 'tv.exmg.livery:livery:1.6.3'
   implementation 'android.arch.core:runtime:1.1.1'
}
```

#### Step 3: Set Java Version of Project

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

## Quick Implementation

For basic usage of the SDK following minimal steps are needed:

### Step 1: Define Remote Config URL

Add ‘livery_remote_config_url’ to your strings and pass the remote config URL as the value.

```xml
<resources>
   <!--...-->
   <string name="livery_remote_config_url">insert_remote_config_url_here</string>
</resources>
```

> You can also change remote config url on run-time. To do so, check [here](#Update-Remote-Config)

### Step 2: Add LiveryPlayerView

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

### Step 3: Configure LiveryPlayerView

Bind LiveryPlayerView to your layout file, then create the livery player with passing LiveryPlayerOptions object after activity or fragment is created.

> Please check [LiveryPlayerOptions](#Analytics) properties in order to have better control over the player.

```java
LiveryPlayerView playerView;

@Override
public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
   super.onViewCreated(view, savedInstanceState);

   playerView = view.findViewById(R.id.liveryPlayer);
   LiveryPlayerOptions playerOptions = new LiveryPlayerOptions();
   playerOptions.autoPlay = true;
   playerOptions.controls.error = true;
   ArrayList<String> src = new ArrayList<>();
   src.add("target dash stream manifest url");
   playerOptions.sources = src;
   playerView.createPlayer(playerOptions);
}
```

### Step 4: Add Life-cycle methods

It is critical to implement the lifecycle methods of Livery Player. Otherwise, you can notice the playback is still going on when an activity is paused, stopped or destroyed. Therefore add these lifecycle methods on your activity or fragment’s onDestroy, onStop, onPause and onResume blocks.

```java
@Override
public void onResume() {
   super.onResume();
   playerView.onResume();
}

@Override
public void onPause() {
   playerView.onPause();
   super.onPause();
}

@Override
public void onStop() {
   playerView.onStop();
   super.onStop();
}

@Override
public void onDestroy() {
   playerView.onDestroy();
   super.onDestroy();
}
```

#### 🎉 Done

You have implemented Livery SDK successfully. By default, the target latency is three seconds.

To have more control over Livery SDK, next sections should provide you more knowledge.

## SDK Methods

> To access the SDK class, ‘LiverySDK’ should be used.

### SDK Instance

In order to call some methods, instance of the SDK must be used.
You can get the instance with the line below:

```java
LiverySDK.getInstance();
```

### Generic Player Options

To generate player options to pass to the player quickly, use this method below:

```java
LiverySDK.getPlayerOptions("MPD Url");
//Or
LiverySDK.getPlayerOptions(mpdUrlsList);
```

### Pinpoint User ID

In some cases, getting the pinpoint user ID can be useful, i.e.: You would like to show the user their pinpoint user ID for analysis based on a specific user. To do so, here is how to get the pinpoint user ID:

```java
String pinpointUserId = LiverySDK.getInstance().getPinpointUserId();
```

### Update Remote Config

Initial remote config is passed to the SDK via strings.xml, however there might be situations that remote config URL needs to be changed later on during run-time. To achieve this, the code below can be used:

```java
LiverySDK.getInstance().updateRemoteConfig("Remote Config URL");
```

## Player Methods

> To access these methods, use the reference to your LiveryPlayerView from your fragment or activity.

### Create Player

By calling `createPlayer(LiveryPlayerOptions)` on LiveryPlayerView reference, a player instance will be created and initialized.

### Current Time

#### Get Current Time of the Media

```java
long currentTime = playerView.getCurrentTime();
```

#### Set Current Time of the Media

```java
long positionInMs = 30 * 1000;
playerView.setCurrentTime(positionInMs);
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

### Playback Rate

#### Set Playback Rate

```java
float playBackRate = 1.5f; //Must be between 0.1 and 3.0
playerView.setPlaybackRate(playBackRate);
```

#### Get Playback Rate

```java
float playBackRate = playerView.getPlaybackRate();
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
playerView.setQuality("auto");

//Sets the quality to audio only.
playerView.setQuality("audio");
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

### Debug Mode

#### Set Debug Mode Status

This will create an info dialog on the top right corner of the player and output all logs of SDK.

```java
boolean isDebugMode = true;
playerView.setDebugModeEnabled(isDebugMode);
```

#### Get Debug Mode Status

```java
boolean isDebugModeEnabled = playerView.getIsDebugModeEnabled();
```

## Player Options

LiveryPlayerOptions has these properties listed on the table below. These options take effect only after [createPlayer](#creating-player) is called.

**_LiveryPlayerOptions_**

| Name       | Type                    | Default                                                                                                          | Description                                                                                                                                                       |
| ---------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `autoPlay` | `Boolean`               | `false`                                                                                                          | Determines whether video shall play immediately after [createPlayer](#creating-player).                                                                           |
| `controls` | `LiveryControlsOptions` | `new LiveryControlsOptions()`                                                                                    | Provides access to [LiveryControlsOptions](#livery-controls-options). Player controls can be tweaked by this object.                                              |
| `fit`      | `LiveryResizeMode`      | `CONTAIN`                                                                                                        | Provides access to LiveryResizeMode. Video size inside the player view can be changed from here.                                                                  |
| `loop`     | `Boolean`               | `false`                                                                                                          | Determines whether the video should restart after it ends.                                                                                                        |
| `muted`    | `Boolean`               | `false`                                                                                                          | Determines whether media should be muted or not.                                                                                                                  |
| `poster`   | `String`                | `` | Represents URL to poster image. When this property is not empty, the player will show a poster on creation. |
| `sources`  | `List<String>`          | `new ArrayList<String>()`                                                                                        | List of DASH manifest URLs to play. The player starts from the first element of this list, then iterates through it one by one if the current index doesn’t play. |

## Player Resize Mode

Player resize mode is passed on [Player Options](#player-options) to indicate video size.

| Name      | Description                                                                                                       |
| --------- | ----------------------------------------------------------------------------------------------------------------- |
| `CONTAIN` | Shows the full video in the view box no matter what, without breaking the aspect ratio.                           |
| `COVER`   | Fills the view box, however, zooms the video if video dimensions are too big for the view. Keep the aspect ratio. |
| `FILL`    | Fits the video into the view box, by ignoring aspect ratio rules.                                                 |

## Player Controls

You can edit LiveryControlsOptions from controls property of LiveryPlayerOptions. Available controls are listed on the table below.

**_LiveryPlayerControls_**

| Name         | Type      | Default | Description                                   |
| ------------ | --------- | ------- | --------------------------------------------- |
| `fullscreen` | `Boolean` | `false` | Sets the visibility of the fullscreen button. |
| `mute`       | `Boolean` | `false` | Sets the visibility of the mute button.       |
| `quality`    | `Boolean` | `false` | Sets the visibility of the quality button.    |
| `error`      | `Boolean` | `false` | Sets the visibility of the error overlay.     |

### Fullscreen Button

Default behavior of fullscreen button can be changed like shown below:

```java
playerView.overrideFullscreenButton(new View.OnClickListener() {
   @Override
   public void onClick(View v) {

   }
});
```

### Quality Button

Default behavior of quality button can be changed like shown below:

```java
playerView.overrideQualityButton(new View.OnClickListener() {
   @Override
   public void onClick(View v) {

   }
});
```

## Player Events

It might be important in some cases to receive player events and process the data for your needs. In order to receive player events, a LiveryPlayerListener must be registered to the player.

**_LiveryPlayerListener_**

| Name                                                           | Description                                                                                                                                |
| -------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `onActiveQualityChanged(LiveryQuality quality)`                | Called when the active quality of the media being played has changed.                                                                      |
| `onDurationChanged(long duration)`                             | Called when the duration of media has changed. (Not applicable to live streams.)                                                           |
| `onPlayerError(Exception e)`                                   | Called when the player encounters an error.                                                                                                |
| `onPlayerStateChanged(LiveryPlayerState playerState)`          | Called when the playback state of the player has changed.                                                                                  |
| `onProgressChanged(long buffer, long bufferEnd, long latency)` | Called in short intervals while playing, buffer, buffered position and latency values in milliseconds can be actively retrieved from here. |
| `onQualitiesChanged(List<LiveryQuality> qualities)`            | Called when available qualities of media have changed.                                                                                     |
| `onPlaybackRateChanged(float rate)`                            | Called when the playback rate of current media has changed.                                                                                |
| `onRecovered()`                                                | Called when an error is recovered.                                                                                                         |
| `onTimeUpdate(long currentTime)`                               | Called when the current time of media has changed.                                                                                         |
| `onVolumeChanged(float volume)`                                | Called when the volume of the player has changed.                                                                                          |
| `onSourceChanged(String source)`                               | Called when the media source URL of the player has changed.                                                                                |

### Registering Player Event Listener

By calling `registerListener(LiveryPlayerListener)` method on LiveryPlayerView, a player event listener can be registered.

```java
playerView.registerListener(new LiveryPlayerListener() {
   //...
});
```

## Player Media Quality

**_LiveryQuality_**

| Name        | Type      | Description                      |
| ----------- | --------- | -------------------------------- |
| `bitrate`   | `Integer` | Bitrate of the quality.          |
| `codecs`    | `String`  | Codec of the media.              |
| `frameRate` | `Float`   | Frame rate of the media.         |
| `height`    | `Integer` | Height of the media.             |
| `label`     | `String`  | Human readable name of quality.  |
| `qualityId` | `String`  | ID of quality.                   |
| `track`     | `String`  | Track type of media. i.e.: video |
| `width`     | `Integer` | Width of the media.              |

## Screen Orientation

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

### Error Overlay

To customize error overlay, create a layout named `livery_player_error_view.xml`.

Available IDs and their corresponding view types are listed below,
ID names are self-explanatory about what that item does.

| Name          | ID                            | Type           |
| ------------- | ----------------------------- | -------------- |
| Error Title   | `livery_error_header`         | `TextView`     |
| Error Text    | `livery_error_overlay_text`   | `TextView`     |
| Reload Button | `livery_error_overlay_button` | `LinearLayout` |

### Player Controls

To customize player controls, create a layout named `livery_player_control_view.xml`.

Available IDs and their corresponding view types are listed below, ID names are self-explanatory about what that item does. Each view is optional.

| Name                 | ID                          | Type        |
| -------------------- | --------------------------- | ----------- |
| Previous Button      | `livery_prev`               | `View`      |
| Next Button          | `livery_next`               | `View`      |
| Play Button          | `livery_play`               | `View`      |
| Pause Button         | `livery_pause`              | `View`      |
| Fast Forward Button  | `livery_ffwd`               | `View`      |
| Rewind Button        | `livery_rew`                | `View`      |
| Mute Button          | `livery_mute`               | `View`      |
| Unmute Button        | `livery_unmute`             | `View`      |
| Quality Button       | `livery_quality`            | `View`      |
| Fullscreen Button    | `livery_fullscreen`         | `View`      |
| Time bar Layout      | `controller_timebar_layout` | `View`      |
| Time bar             | `livery_progress`           | `TimeBar`   |
| Repeat Toggle Button | `livery_repeat_toggle`      | `ImageView` |
| Shuffle Button       | `livery_shuffle`            | `View`      |
| Duration View        | `livery_duration`           | `TextView`  |
| Position View        | `livery_position`           | `TextView`  |

### Audio Only Stream Indicator

To customize the loading indicator, create a layout named `livery_player_audio_only_view.xml`.

For this layout, there are no IDs available. This means that whenever the player is playing only audio, this layout will be shown.

## Change log

### 1.6.4

Fixed a crash occured when player.dispose() method was called due to uiElementsHandler not being able to find the player instance.