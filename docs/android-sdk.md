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

## Usage

### Quick Implementation

For basic usage of the SDK following minimal steps are needed:

#### Step 1: Define Remote Config URL

Add ‘livery_remote_config_url’ to your strings and pass the remote config URL as the value.

```xml
<resources>
   <!--...-->
   <string name="livery_remote_config_url">insert_remote_config_url_here</string>
</resources>
```

> You can change remote config url later on. To do so, check [here](#Analytics)
> TODO: Edit the link to change remote config

#### Step 2: Add LiveryPlayerView

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

#### Step 3: Configure LiveryPlayerView

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

#### Step 3: Add Life-cycle methods

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
For a detailed explanation of implementation please follow the rest of this document.

### Advanced Implementation

#### SDK Methods

> To access the SDK class, ‘LiverySDK’ class should be used.
