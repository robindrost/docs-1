# Add streaming support (SRT)
Third-party tools are required to ingest a live stream via SRT or RTMP into the Livery platform. While Livery does not provide a mobile streaming library to enable live transmission from mobile devices via RTMP or SRT, there are multiple alternatives available. The following guide explains how to add a transmission library to your application, enabling your customers to stream directly from their native application.

## Streaming support for iOS
To add a SRT ingest to your app you can use SRTHaishinKit library, to do so:

Start by adding the library through Carthage or manually into your project (CocoaPods and Swift Package Manager are not available)
```groovy
github "shogo4405/SRTHaishinKit.swift" "main"
```
Install library prerequisites

```groovy
brew install cmake
```
Add camera and microphone permissions in your project’s Info.plist

<key>NSCameraUsageDescription</key>
<string>The app uses the camera to Live Stream </string>
<key>NSMicrophoneUsageDescription</key>
<string>The app uses the microphone to Live Stream </string>
Setup AVAudioSession

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
Add a HKView or a MTHKView to your View Controller

var hkView = HKView(frame: view.bounds)
view.addSubview(hkView)
Create and setup the SRTConnection and the SRTStream

let srtConnection = SRTConnection()
let srtStream = SRTStream(srtConnection)

// attach Camera and Audio sources in the stream
srtStream.attachCamera(DeviceUtil.device(withPosition: .back))
srtStream.attachAudio(AVCaptureDevice.default(for: .audio))
Start streaming

srtStream.publish("iOS app's stream")
srtConnection.connect("srt://host:port?option=foo") // yours srt ingest URL
Stop streaming

srtStream.close()
srtConnection.close()
For more details please check the SRTHaishinKit installation and usage guide.
