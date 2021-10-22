# Livery Video Ingest

## Livery Encoder
<!--Network
Image Settings-->

### Cropping

The livery encoder can crop the video that it is receiving before it encodes the video. This is useful when the video stream is used in a portrait mode app. If we would not crop the image the video player will receive the full landscape image and has to crop it in order to not show black bars above and below the video. While this will also work it means that a lot of the streamed pixels are discarded by the video player. This is a waste of bandwidth.

Cropping on the encoder works as follows. The encoder uses the output resolution aspect ratio to determine the size of the cropping area. We can provide a position from where to do the cropping. Usually this will be either at the complete left or exactly centered. This is an example for a portrait stream with a 9:16 aspect ratio. The input resolution is 1920x1080 and we have set the encoder to do centered cropping.

![Cropping](encoder/cropping.png)

The yellow plane is the input image and the blue plane is the part of that image that the encoder will use. The encoder will use a plane of 603 pixels wide and 1072 pixels in height. It takes this weird nr of pixels to get a perfect aspect ratio of 9/16.

### Resolution and refresh rate

If a computer is used as the video source there are usually 2 places where we have to set the resolution and refresh rate. In order to achieve the best possible image quality these settings must match the encoder settings (or the other way around). The first place is the monitor settings and the second place is the mixing software settings. Let’s say we want to stream a landscape video in 16/9 format where the highest quality we want to stream is 1080P/30FPS. If possible the framerate should be 30FPS or exactly double the target framerate. So 60FPS in this case. On Windows we can change the display settings like this:

![Display Settings](encoder/windows-display-settings-1.png)

Make sure to also check the Advanced display settings to make sure Active signal resolution is set correctly.

![Display Settings](encoder/windows-display-settings-2.png)

On macOS open Displays to alter the settings. Use the Gather Windows button to see the settings window of both monitor outputs. For the 1080P/30FPS we could set it up like this: 

![Display Settings](encoder/mac-display-ssettings.png)

<!--- Mixer soft- & hardware resolution and FPS 
NEED MORE DETAILS --->

## Mixer Soft- and Hardware
A soft- and or hardware mixer is often used to output one or more broadcast streams. All video sources are connected with the Mixer, from which the broadcast stream is ingested in either the Cloud or On-premis encoder.  

The following overview contains an overview of the most common video mixing tools and the configuration needed live stream using Livery Video.

### OBS

?> https://obsproject.com/nl

<!-- tabs:start -->
#### RTMP
|Menu|Item Property|Value|
|-----|-----|-----------|
|Stream|Service|Custom|
| |Server|Ingest URL for example rtmp://ingest.liveryvideo.com/app/12a34b56c78d90e|
| |Stream Key|Stream Key optional value for example vYm5X669xxdk|
|Output|Output Mode|Advanced|
| |Encoder|x264|
| |Rate Control|CBR|
| |Bitrate|A higher value as the max bitrate configured in the Livery Encoder settings|
| |Keyframe interval|Recommended setting is 2. The value should match the segment size set in the Livery Encoder settings|
| |CPU Usages Preset|Veryfast|
| |Tune|Zerolatency|
|Video|Output Resolution|1920x1080px Please reachout to us when an alternative resolution is requested.|
| |Common FPS Values|25 PAL It is strongly recommended to use the same FPS throughout the complete workflow, to prevent audio/video sync issues.|

#### SRT
|Menu|Item Property|Value|
|-----|-----|-----------|
|Stream|Service|Custom|
| |Server|Ingest URL for example srt://ingest.liveryvideo.com:9998?pkt_size=1316&streamid=12a34b56c78d90e|
| |Stream Key|Stream Key optional value for example vYm5X669xxdk|
|Output|Output Mode|Advanced|
| |Encoder|x264|
| |Rate Control|CBR|
| |Bitrate|A higher value as the max bitrate configured in the Livery Encoder settings|
| |Keyframe interval|Recommended setting is 2. The value should match the segment size set in the Livery Encoder settings|
| |CPU Usages Preset|Veryfast|
| |Tune|Zerolatency|
|Video|Output Resolution|1920x1080px Please reachout to us when an alternative resolution is requested.|
| |Common FPS Values|25 PAL It is strongly recommended to use the same FPS throughout the complete workflow, to prevent audio/video sync issues.|

### On-prem
Assign the on-prem encoder as an external monitor of the computer. Set OBS to Studio Mode, and select the encoder as the “Fullscreen Projector”. 

![Stream Settings](encoder/obs-stream-settings.png)
![Output Settings](encoder/obs-output-settings.png)
![Video Settings](encoder/obs-video-settings.png)
![Onprem Settings](encoder/obs-onprem-fullscreen.png)
<!-- tabs:end -->

### vMixer

?> https://www.vmix.com/

<!-- tabs:start -->
#### RTMP

|Menu Item|Property|Value|
|-----|-----|-----------|
|Destination|Service|Custom|
| |URL|Ingest URL for example rtmp://ingest.liveryvideo.com/app/12a34b56c78d90e|
| |Stream Key|Stream Key optional value for example vYm5X669xxdk|
| |Quality|Custom via the gear icon|
|Streaming Quality|Encode Size|1920x1080|
| |Profile|Main|
| |Level|3.1|
| |Preset|Ultrafast| 
| |Aspect Ratio/Crop|Original|
| |Keyframe Frequency|2 seconds|
| |Network Buffer|5 Seconds|
| |Strict CBR| Checked|

#### SRT
SRT is supported via a workaround

|Menu Item|Property|Value|
|-----|-----|-----------|
|Destination|Service|Custom|
| |URL|Ingest URL for example rtmp://ingest.liveryvideo.com/app/12a34b56c78d90e|
| |Stream Key|Stream Key optional value for example vYm5X669xxdk|
| |Quality|Custom via the gear icon|
|Streaming Quality|Encode Size|1920x1080|
| |Profile|Main|
| |Level|3.1|
| |Preset|Ultrafast| 
| |Aspect Ratio/Crop|Original|
| |Keyframe Frequency|2 seconds|
| |Network Buffer|5 Seconds|
| |Strict CBR| Checked|

### On-prem

|Destination|Service|Custom|
|Windows Display settings|Multiple displays|Extend these displays|
|vMix Display settings|Output Size|1920x1080|
| |Display| 2 (The display port used by the encoder)|
|vMix Main screen|Fullscreen|Press Fullscreen to start outputting the video to the selected display port.|
<!-- tabs:end -->

### StreamYard
?> https://streamyard.com/
?> A Professional Streamyard account is required to make the required changes to stream using the Livery platform. 
?> SRT is not supported

#### RTMP

|Menu Item|Property|Value|
|-----|-----|-----------|
|Destination|Service|Custom|
| |RTMP Server URL|Streamyard requers the ingest URL and stream key to be separated. The stream ID should be removed from the Ingest URL rtmp://ingest.liveryvideo.com/app/|
| |Stream Key|The Stream Key is a mandatory value in Streamyard, the last part if the stream ID can be added as Stream Key; 12a34b56c78d90e|
|Settings|Broadcast Quality|1080p|

### Re-stream.IO

?> https://try.restream.io/
?> SRT is not supported
?> On-Prem is not supported

#### RTMP

|Menu Item|Property|Value|
|-----|-----|-----------|
|Channel|Service|Custom|
| |RTMP URL|Ingest URL for example rtmp://ingest.liveryvideo.com/app/12a34b56c78d90e|
| |Stream Key|Stream Key optional value for example vYm5X669xxdk|
|Settings|Broadcast Quality|1080p|

### Softvelum (iOS and Android SDK)

?> https://softvelum.com/larix/
?> On-Prem is not supported

<!-- tabs:start -->
#### RTMP

|Menu Item|Property|Value|
|-----|-----|-----------|
|Destination|URL|Larix requires the ingest URL and stream key to be separated. The stream ID should be removed from the Ingest URL rtmp://ingest.liveryvideo.com/app/|
| |Stream Key|The Stream Key is a mandatory value in Larix the last part if the stream ID can be added as Stream Key; 12a34b56c78d90e
Video|
| |Resolution1920x1080|
| |Keyframe frequency|2 seconds|
| |Codec|H264|

#### SRT

|Menu Item|Property|Value|
|-----|-----|-----------|
|Destination|URL|Larix requires the ingest URL and stream key to be separated. The stream ID should as well as the 'pkt_size=1316&streamid=' be removed from the Ingest URL srt://ingest.liveryvideo.com:9998
| |Stream Key|The Stream Key is a mandatory value in Larix the last part if the stream ID can be added as Stream Key; 12a34b56c78d90e
Video|
| |Resolution1920x1080|
| |Keyframe frequency|2 seconds|
| |Codec|H264|
<!-- tabs:end -->

### Streamaxia (iOS and Android SDK)

?> https://www.streamaxia.com/
?> SRT is not supported
?> On-Prem is not supported

#### RTMP 

|Menu Item|Property|Value|
|-----|-----|-----------|
|Settings|Server URL|Ingest URL for example rtmp://ingest.liveryvideo.com/app/12a34b56c78d90e|
| |Server Authentication|Stream Key optional value for example vYm5X669xxdk|
| |Resolution|1920x1080|

