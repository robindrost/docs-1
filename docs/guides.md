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

# Use the Frictionless login in combination with MailChimp

Livery's [Unverified Frictionless Auth](https://docs.liveryvideo.com/frictionless-login) (based on OpenId), in combination with its Query Parameter support, is a perfect tool for a smooth transition from personal marketing messages, such as emails, to active participation in an Interactive Live Broadcast. The user is automatically logged in once they navigate to the Livery interactive client via the URL, and with the use of Livery's Custom Dimensions, additional data can be passed along and stored on the user's profile. The following 5 steps explain how to set this up in combination with [MailChimp](https://mailchimp.com/).

Step 1:
Choose which extra user details you want to gather and store in the users broadcast profile. To guarantee that each user has a unique personal URL, it's crucial to add a unique `livery_sub` value parameter alongside other relevant details. A `livery_sub` value that is not known by the backend generates a new user, while a `livery_sub` value that is already known by the backend updates or extends the user's profile with the additional details provided via the parameters. These parameters can include the user's email address and name, as well as other information like their company, location, and phone number.

Step 2:
Upload your audience to Mailchimp either by uploading a file or entering them manually. When uploading your contacts, make sure to include all the parameters (contact information) that you'd like to add to the users broadcast profile. Also, make sure to match the correct labels to each column of information. These labels will be used in the unique user URLs.

Step 3:
Create the URL code that will be included in your email campaign. 

An example URL is: 
`https://embed.liveryvideo.com/?id=64382097e4b07f0e2af767a5&v=beta&livery_sub=*|EMAIL|*&livery_preferred_username=*|FNAME|*&livery_cd1=day1&livery_cd2=email`

For this example, Livery's embed page is being used, with the specific stream ID contained in the '?id=...' part of the URL. The `livery_sub` parameter is used to determine who the user is. If an unknown value is used, a new user is created. If the 'sub' value is already known, the user's profile is updated. In the example above, the email address is used from each user to make the URL unique, and the first name is used as the preferred username in Livery. Next to the parameters used for the Frictionless login, 2 Custom Dimensions (`livery_cd`) are used to store general information about the campaign. 

To ensure that Mailchimp recognizes which email address or name to include in the user URL for each specific user when you send out an email, it is important to use the correct merge tag. Mailchimp offers merge tags for various dynamic information. By inserting a merge tag into your email campaign, you can send personalized content to your subscribers. For instance, to include a user's email address, you should use the merge tag `*|EMAIL|*`. To get a comprehensive overview of Mailchimp's merge tags, please visit https://mailchimp.com/help/all-the-merge-tags-cheat-sheet/

Step 4:
Create a template email for your email campaign. You can include your URL in multiple ways; 
- Add the URL code as text to the template
- Add the URL code as a link to written text (e.g. click here to watch the stream) 
- Make a button and connect to URL code to that button 
Both option 2 and 3 have an invisible link, so when your contacts receive the email the won’t see the URL with all the parameters. 

Step 5:
Finalize your email and send out the campaign. Each contact will now receive an email with a unique and personalized URL. Clicking on this link will load the stream and automatically register the user, with a profile containing all the information included in the URL.

