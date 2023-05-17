# Livery Video Customer API

This document describes the API calls that are available to customers. These calls can be used by application developers that are using the Livery streaming solution and want to control the stream from within their own software instead of relying on the Livery producer console.

The paths in this document should be concatenated to the domain name of a environment to make the full URL for a request.
The domain name for the production environment is `backend.video-encoder.playtotv.com`.
So if the streamId is 1234 and the documentation mentions this API call:

```
GET api/1/services/analytics/{streamId}/dimensions
```

The URL for the API call would be:

```
https://backend.video-encoder.playtotv.com/api/1/services/analytics/1234/dimensions`
```

## Authorization

Access to the API calls is restricted to authorized users only. Authorization is done with API Keys.

The API key should be added as a X-PlayToTV-ApiKey header to requests.

There are two types of API Keys: Customer API keys and Stream API keys.

| API Key Type | Description                                                                                                                            |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| Customer     | authorized to do all documented customer requests & all stream request for streams owned by the customer which the API key belongs to. |
| Stream       | authorized to do all documented stream requests that involve a single stream.                                                          |

## Rate limits

The API calls are rate limited to 30 calls per minute per customer.
This means that it combines the request count for all streams of a customer.
If this limit is exceded the server will return a response with a 429 HTTP status code.

## API Calls

### GetAnalytics

The Livery analytics API allows customers to get insights in the performance of their streams.
The API consist of a single REST call with a number of query parameters.
This section explains how this API can be used.

```
GET api/1/services/analytics/{streamId}/dimensions
```

| Parameter       | Type  | Mandatory | Description                                                                                                                       |
| --------------- | ----- | --------- | --------------------------------------------------------------------------------------------------------------------------------- |
| streamId        | Path  | Yes       | The stream identifier                                                                                                             |
| from            | Query | Yes       | Start date in milliseconds                                                                                                        |
| until           | Query | Yes       | End date in milliseconds                                                                                                          |
| metric          | Query | No        | Currently only accepts "viewers". Defaults to viewers                                                                             |
| dimension       | Query | No        | Which type of data to query, see info below                                                                                       |
| minutesInterval | Query | No        | Minute intervals to split the data into as integer (1-1440). **Cannot be used along with the points parameter**                   |
| points          | Query | No        | The amount of points to split the data into as positive integer (1+). **Cannot be used along with the minutesInterval parameter** |

The minimum range to fetch data for is one minute, the maximum range is currently one week.

These are the dimensions that are supported:

| Dimension       | Description                                                                                                        |
| --------------- | ------------------------------------------------------------------------------------------------------------------ |
| PLATFORM        | The type of SDK that is being used. This will be either android, iOS or web                                        |
| CONNECTION_TYPE | The type of internet connection that viewers used, only available with the Android or iOS SDK                      |
| VIDEO_QUALITY   | The video quality that viewers watched                                                                             |
| ENCODER_TYPE    | Shows if viewers were watching the stream of the MAIN or the BACKUP encoder                                        |
| PACKAGE_NAME    | This is the package name of the native app or the domain name of the website that viewers used to watch the stream |
| DEVICE_MODEL    | The type of device that viewers were using to watch the stream                                                     |

For example, to get viewer data per platform in a single point:

```
GET api/1/services/analytics/{streamId}/dimensions?from=1606246100000&until=1606247300000&dimension=PLATFORM&points=1
```

Response example:

```json
{
  "data": [
    {
      "timestamp": 1606246100000,
      "values": [
        {
          "dimension": "android",
          "value": 4699
        },
        {
          "dimension": "web",
          "value": 707
        },
        {
          "dimension": "ios",
          "value": 12
        }
      ]
    }
  ]
}
```

Or to retrieve the ENCODER_TYPE dimension in 10 minute intervals:

```
GET api/1/services/analytics/{streamId}/dimensions?from=1606246100000&until=1606247300000&dimension=ENCODER_TYPE&minutesInterval=10
```

Response example:

```json
{
  "data": [
    {
      "timestamp": 1606242000000,
      "values": [
        {
          "dimension": "main",
          "value": 1600
        },
        {
          "dimension": "backup",
          "value": 13
        }
      ]
    },
    {
      "timestamp": 1606242600000,
      "values": [
        {
          "dimension": "main",
          "value": 5309
        },
        {
          "dimension": "backup",
          "value": 400
        }
      ]
    },
    {
      "timestamp": 1606243200000,
      "values": [
        {
          "dimension": "main",
          "value": 4811
        },
        {
          "dimension": "backup",
          "value": 444
        }
      ]
    }
  ]
}
```

### Test data

There is a dev environment that allows you to test the analytics API calls before you have had any live streams.

| Field       | Description                                                                                                                                    |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| Domain name | backend.video-encoder-dev.playtotv.com                                                                                                         |
| streamId    | 5e7a10bee4b046f00ad17248                                                                                                                       |
| API key     | 1568285122117_c0fa8dc13a4c104b673b4b2aa664341200ef81fbde330b03404621f178f9e158eae47d1e9b071d20f7ddd8ae9ba4f1df858081f31b43b809bbef920c04459951 |

For example this API call can be used to retrieve a mocked version of the viewers per platform:

```
curl -H "X-PlayToTV-ApiKey: 1568285122117_c0fa8dc13a4c104b673b4b2aa664341200ef81fbde330b03404621f178f9e158eae47d1e9b071d20f7ddd8ae9ba4f1df858081f31b43b809bbef920c04459951" https://backend.video-encoder-dev.playtotv.com/api/1/services/analytics/5e7a10bee4b046f00ad17248/dimensions?dimension=PLATFORM&from=1606246100000&until=1606247300000&points=1
```

### GetCustomer

Returns the customer data that is available for a given customerId.

```
GET api/1/services/customers
```

Response example:

```json
[
  {
    "customerId": "5c5468158f087748c233194f",

    "name": "ExampleCustomer",
    "email": "info@example.com",

    "encoderIds": ["5f438c8d2e8037d91adf780a", "5f438c9b09ab5a068b9f87e7"],

    "pushUrl": "https://customerserveraddress"
  }
]
```

### GetStreams

Returns a list of streams. This call can be used to retrieve streamIds which are needed to start and stop a stream.

This could be used to monitor the streams by polling this API call reading the `warnings` field of the stream object.
When the system detects a problem with the stream or the attached encoders a new line with a description will be added to that field.
Check the [Rate limits](#rate-limits) section for some considerations around the polling frequency.

```
GET api/1/services/streams?pageIndex=1&pageSize=100
```

| Query parameter | Description                                                        |
| --------------- | ------------------------------------------------------------------ |
| pageIndex       | The page index of the pagination. The first index is 1.            |
| pageSize        | The number of streams on a page                                    |
| streamIds       | List of streamIds to be found, ignored when using a stream API key |

Response:

[filename](_customer-video-api/_example-multiple-StreamResponseDTO.md ':include')

### StartStream

Starts the video stream. Once the response of this call is available the application can start the video player with the video urls from the response.

The urls are defined in the streamUrls field in the output.\
The value of streamingState should be _STARTED_

```
PUT api/1/services/streams/start?id=5f2022df27b35d1ebfe17b78
```

| Query parameter | Description                                                     |
| --------------- | --------------------------------------------------------------- |
| id              | The stream identifier. Required when not using a Stream API Key |

Response example:

[filename](_customer-video-api/_example-StreamResponseDTO.md ':include')

### StopStream

Stops the video stream.

The value of streamingState should be _STOPPED_

```
PUT api/1/services/streams/stop?id=5f2022df27b35d1ebfe17b78
```

| Query parameter | Description                                                     |
| --------------- | --------------------------------------------------------------- |
| id              | The stream identifier. Required when not using a Stream API Key |

Response example:

[filename](_customer-video-api/_example-stopped-StreamResponseDTO.md ':include')

### CreateStream

Create a new stream

```
POST api/1/services/streams
```

Request body:

```json
{
  "name": "streamName",

  "cdnRegionId": "6062e72ca1b0d8e93af985f9",

  "posterUrl": "streamName.com/poster.png",
  "prePosterUrl": "streamName.com/poster-pre.png",
  "postPosterUrl": "streamName.com/poster-post.png",
  "interactiveLayerUrl": "streamName.com/poster.png",

  "videoStreamQualities": [
    "6062e81716bcd84ab3562cc1",
    "6062e81716bcd84ab3562cc1"
  ],
  "audioStreamQualities": [
    "6062e823f8cea49a7e2036f7",
    "6062e82e40b0aaf95d6c634f"
  ],

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
    "domainWhiteList": [
      "https://streamName.com",
      "https://video.streamName.com"
    ]
  }
}
```

Response example:

[filename](_customer-video-api/_example-StreamResponseDTO.md ':include')

### UpdateStream

Update the settings of a stream.

```
PUT api/1/services/streams?id=5f2022df27b35d1ebfe17b78
```

| Query parameter | Description                                                     |
| --------------- | --------------------------------------------------------------- |
| id              | The stream identifier. Required when not using a Stream API Key |

Request body:

```json
{
  "name": "streamName",

  "posterUrl": "streamName.com/poster.png",
  "prePosterUrl": "streamName.com/poster-pre.png",
  "postPosterUrl": "streamName.com/poster-post.png",
  "interactiveLayerUrl": "streamName.com/poster.png",

  "streamPhase": "PRE",

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
    "domainWhiteList": [
      "https://streamName.com",
      "https://video.streamName.com"
    ]
  }
}
```

Response example:

[filename](_customer-video-api/_example-StreamResponseDTO.md ':include')

### RemoveStream

Remove a stream.

```
DELETE api/1/services/streams?id=5f2022df27b35d1ebfe17b78
```

| Query parameter | Description                                                     |
| --------------- | --------------------------------------------------------------- |
| id              | The stream identifier. Required when not using a Stream API Key |

### Re-generate Stream Key

Re-generate stream key for the specified customerId and streamId

```
PUT api/2/customers/6267d661e4b0420e3acced5e/streams/626fa59fe4b018f4ecdc092c/streamkey
```

| Parameter  | Type | Mandatory | Description             |
| ---------- | ---- | --------- | ----------------------- |
| customerId | Path | Yes       | The customer identifier |
| streamId   | Path | Yes       | The stream identifier   |

Response example:

[filename](_customer-video-api/_example-key-re-generate-StreamResponseDTO.md ':include')

## Push API

Livery has a push API available which can directly inform the customer server when the streaming state of a stream has changed.
For this the customer needs to configure a push url.
The Livery server will do an HTTP POST request to that url with the streamId and the streaming state.
The state can be either `STARTED` or `STOPPED`.

Request body:

```json
{
  "streamId" : "5e7a10bee4b046f00ad17248",
  "streamingState" : "STARTED"
}
```
