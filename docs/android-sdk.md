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

## Creating Player

By calling `createPlayer(LiveryPlayerOptions)` on LiveryPlayerView reference, a player instance will be created and initialized.

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

| Name    | Description                                                                                                       |
| ------- | ----------------------------------------------------------------------------------------------------------------- |
| CONTAIN | Shows the full video in the view box no matter what, without breaking the aspect ratio.                           |
| COVER   | Fills the view box, however, zooms the video if video dimensions are too big for the view. Keep the aspect ratio. |
| FILL    | Fits the video into the view box, by ignoring aspect ratio rules.                                                 |

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

### Registering Player Event Listener

By calling `registerListener(LiveryPlayerListener)` method on LiveryPlayerView, a player event listener can be registered.

```java
playerView.registerListener(new LiveryPlayerListener() {
   //...
});
```

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
