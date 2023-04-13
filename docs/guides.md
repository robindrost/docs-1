# Add streaming support (SRT)
Third-party tools are required to ingest a live stream via SRT or RTMP into the Livery platform. While Livery does not provide a mobile streaming library to enable live transmission from mobile devices via RTMP or SRT, there are multiple alternatives available. The following guide explains how to add a transmission library to your application, enabling your customers to stream directly from their native application.

## Streaming support for iOS
To add a SRT ingest to your app you can use [SRTHaishinKit](https://github.com/shogo4405/SRTHaishinKit.swift#-installation) library, to do so:

Start by adding the library through Carthage or manually into your project (CocoaPods and Swift Package Manager are not available)
```groovy
github "shogo4405/SRTHaishinKit.swift" "main"
```
Install library prerequisites

```groovy
brew install cmake
```
Add camera and microphone permissions in your project’s `Info.plist`

```groovy
<key>NSCameraUsageDescription</key>
<string>The app uses the camera to Live Stream </string>
<key>NSMicrophoneUsageDescription</key>
<string>The app uses the microphone to Live Stream </string>
```
- Setup `AVAudioSession`

```groovy
import AVFoundation
let session: AVAudioSession = AVAudioSession.sharedInstance()
do {
    try session.setPreferredSampleRate(44_100)
    try session.setCategory(.playAndRecord, mode: .default, options: [.allowBluetooth])
    try session.setMode(.default)
    try session.setActive(true)
} catch {
    // catch the error here
}
```
- Add a `HKView` or a `MTHKView` to your View Controller

```groovy
var hkView = HKView(frame: view.bounds)
view.addSubview(hkView)
```
- Create and setup the `SRTConnection` and the `SRTStream`

```groovy
let srtConnection = SRTConnection()
let srtStream = SRTStream(srtConnection)

// attach Camera and Audio sources in the stream
srtStream.attachCamera(DeviceUtil.device(withPosition: .back))
srtStream.attachAudio(AVCaptureDevice.default(for: .audio))
```
- Start streaming
```groovy
srtStream.publish("iOS app's stream")
srtConnection.connect("srt://host:port?option=foo") // yours srt ingest URL
```
- Stop streaming
```groovy
srtStream.close()
srtConnection.close()
```
For more details please check the [SRTHaishinKit](https://github.com/shogo4405/SRTHaishinKit.swift#-installation) installation and usage guide.

## Streaming support for Android

To add a SRT ingest to your app you can use [StreamPack](https://github.com/ThibaultBee/StreamPack) library.

Start by adding the library dependencies for SRT:
```groovy
dependencies {
    def streampack_version = ...
    implementation "io.github.thibaultbee:streampack:${streampack_version}"
    implementation "io.github.thibaultbee:streampack-extension-srt:${streampack_version}"
}
```
- You need to add permissions to access the device's camera and microphone.
- Add a AutoFitSurfaceView to preview your stream:
```groovy
    <io.github.thibaultbee.streampack.views.AutoFitSurfaceView
            android:id="@+id/preview"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
```
- Create and configure a streamer:
```groovy
    streamer = CameraSrtLiveStreamer(
        context,
        enableAudio = true
    ).apply {

        // (https://developer.android.com/reference/android/media/AudioRecord?hl=en#AudioRecord(int,%20int,%20int,%20int,%20int)): "44100Hz is currently the only rate that is guaranteed to work on all devices, but other rates such as 22050, 16000, and 11025 may work on some devices."
        val audioConfig = AudioConfig(
            startBitrate = 128000,
            sampleRate = 44100,
            channelConfig = AudioFormat.CHANNEL_IN_STEREO
        )
        configure(audioConfig)

        val videoConfig = VideoConfig(
            startBitrate = <desired bitrate>,
            resolution = <resolution>,
            fps = 30
        )
        configure(videoConfig)
    }
```
- To start the preview just call:
```groovy
    streamer.startPreview(preview)
```
- To start streaming using an URL just use:
```groovy
    streamer.connect(<srt_ingest_url>)
    streamer.startStream()
```
- At the end don't forget to stop and release the stream and preview:
```groovy
streamer.stopStream()
streamer.disconnect()
streamer.stopPreview()
streamer.release()
```
For more details check [StreamPack](https://github.com/ThibaultBee/StreamPack) quick start guide.
