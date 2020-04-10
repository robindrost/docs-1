# Encoder

## Hardware encoder

### Input signal

Livery provides hardware encoders with two types of input connectors.
The input specifications for both encoders are as follows.

SDI

- Integrated cable equalizer supporting cable lengths up to 230M for HD signals
- Support for SD/HD/3Ga/3Gb/3Gb-DL/3Gb-DS standards
- Support for RGB 4:4:4, YCbCr 4:4:4, YCbCr 4:2:2 color sampling
- Support resolutions up to 1080P. Please contact us for information about higher resolutions on SDI.
- Support for capture frame rates up to 144fps (Actual capture frame rate can be limited by the image resolution)

HDMI

- 297MHz HDMI receiver
- Adaptive HDMI equalizer
- Support for RGB/YUV 4: 4: 4 8-bit format signals with a pixel frequency of no more than 297MHz
- Support for YUV 4:2:2 12-bit format signals with a pixel frequency of no more than 297MHz
- Support for RGB/YUV 4:4:4 10/12-bit format signals with a pixel frequency of no more than 170MHz
- Support resulutions up to 4K (4096x2160).
- Support for capture frame rates up to 144fps (Actual capture frame rate can be limited by the image resolution)

### Mixing software

There are some things to consider when using a hardware encoder in combination with video mixing software on a computer. The encoder will be assigned as an external monitor for the computer. The mixing software has to be setup in such a way that it outputs the video and audio to the external monitor. In OBS this can be done like this:

![OBS extend screen](encoder/obs-extend-screen.png)

Make sure you do this on the scene and not on one of the sources within your scene. Otherwise you will only see the selected source being sent to the encoder.

## Cloud encoder

### Input signal

The cloud encoder currently expects a resolution of 1920x1080. It can receive this input signal over RTMP. The URL that is used for RTMP can be found in the Livery CMS. The encoder does not support a stream key or authenticaiton. For example in OBS you can setup the RTPM URL like this:

![OBS rtmp](encoder/obs-rtmp.png)

Sending the video to the cloud encoder adds a bit of latency. In OBS there is a setting for x264 called Tune=zerolatency that helps lowering this latency:

![OBS zerolatency](encoder/obs-zerolatency.png)

## Image settings

### Cropping

The livery encoder can crop the video that it is receiving before it encodes the video. This is usefull when the video stream is used in a portrait mode app. If we would not crop the image the video player will receive the full landscape image and has to crop it in order to not show black bars above and below the video. While this will also work it means that a lot of the streamed pixels are discarded by the video player. This is a waiste of bandwidth.

Cropping on the encoder works as follows. The encoder uses the output resolution aspect ratio to determine the size of the cropping area. We can provide a position from where to do the cropping. Usually this wille be eitehr at the complete left or exactly centered. This is an example for a portrait stream with a 9:16 aspect ratio. The input resolution is 1920x1080 and we have set the encoder to do centered cropping.

![OBS zerolatency](encoder/cropping.png)

The yellow plane is the input image and the blue plane is the part of that image that the encoder will use. The encoder will use a plane of 603 pixels wide and 1072 pixels in height. It takes this weird nr of pixels to get a perfect aspect ratio of 9/16.

### Resolution and refresh rate

If a computer is used as the video source there are usually 2 places where we have to set the resolution and refresh rate. In order to achieve the best possible image quality these settings must match the encoder settings (or the other way around). The first place is the monitor settings and the second place is the mixing software settings. Let's say we want to stream a landscape video in 16/9 format where the highest quality we want to stream is 1080P/30FPS. On Windows we can change the display settings like this:

![Windows display settings](encoder/windows-display-settings-1.png)

Make sure to also check the `Advanced display settings` to make sure Active signal resolution is set correctly.

![Windows advanced display settings](encoder/windows-display-settings-2.png)

Next is the mixing software. For example in OBS it's possible to set the rendering resolution in the video settings page:
![OBS video settings](encoder/obs-video-settings.png)
