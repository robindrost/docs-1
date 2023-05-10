```json
[
  {
    "streamId": "5f2022df27b35d1ebfe17b78",
    "customerId": "5c5468158f087748c233194f",
    "cdnRegionId": "60631f3c7596e463afd30917",

    "name": "streamName",

    "streamingState": "STARTED",
    "streamPhase": "LIVE",
    "streamUrlType": "STATIC",
    "encoderPlatform": "CLOUD",

    "lastStreamingStateChangeTimestamp": 1599749054566,

    "posterUrl": "streamName.com/poster.png",
    "prePosterUrl": "streamName.com/pre-poster.png",
    "postPosterUrl": "streamName.com/post-poster.png",
    "interactiveLayerUrl": "interactive.example/5f2022df27b35d1ebfe17b78",

    "settings": {
      "targetLatency": 3.0,
      "segmentSizeInSeconds": 2.0
    },

    "player": {
      "fit": "COVER",
      "preRoll": "streamName.com/preroll.mp4"
    },

    "controls": {
      "cast": false,
      "error": false,
      "fullscreen": false,
      "mute": false,
      "quality": false,
      "pip": false
    },

    "cdn": {
      "domainWhiteList": ["streamName.com", "video.streamName.com"]
    },

    "encoders": [
      {
        "encoderId": "5f2807dc4fd690da7238fd56",
        "encoderType": "MAIN",
        "state": {
          "encoderStatus": "ONLINE",
          "streamingState": "STARTED",
          "videoDevice": {
            "id": "5f5614aefd9fb4ec7fb24d10",
            "resolution": "1920x1080",
            "frameRate": "59.97",
            "signalState": "Locked",
            "signalStatus": "Valid",
            "type": "0x84",
            "scanningType": "Progressive",
            "colorDepth": "8 bits",
            "colorEncoding": "Y/U/V 4:4:4"
          },
          "audioDevice": {
            "id": "5f5614e848b6ed205b282380",
            "format": "48000 Hz, 24 bit, LPCM"
          }
        }
      }
    ],

    "streamUrls": [
      "https://example.com/cmaf/5f27c847e4b0716a97225f47/out.mpd",
      "https://example.com/cmaf/5f27c847e4b0716a97225f47/master.m3u8"
    ],

    "ingestUrls": ["rtmp://some-rtmp-url", "srt://some-srt-url:9998"],

    "videoStreamQualities": [
      "6062e81716bcd84ab3562cc1",
      "60631f61219c684b0e8e3cd9"
    ],
    "audioStreamQualities": [
      "6062e823f8cea49a7e2036f7",
      "6062e82e40b0aaf95d6c634f"
    ],

    "remoteConfigUrl": "cdn.liveryvideo.com/remoteconfigs/5f2022df27b35d1ebfe17b78",
    "warnings": ["No encoder online"]
  }
]
```
