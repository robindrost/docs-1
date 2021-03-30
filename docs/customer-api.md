# Customer API

This document describes the API calls that are available to customers. These calls can be used by application developers that are using the Livery streaming solution and want to control the stream from within their own software instead of relying on the Livery producer console.

## Authorization

Access to the API calls is restricted to authorized users only. Authorization is done with API Keys.

The API key should be added as a X-PlayToTV-ApiKey header to requests.

There are two types of API Keys: Customer API keys and Stream API keys.

| API Key Type | Description                                                                                                                            |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| Customer     | authorized to do all documented customer requests & all stream request for streams owned by the customer which the API key belongs to. |
| Stream       | authorized to do all documented stream requests that involve a single stream.                                                          |

## API Calls

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

    "encoderIds": ["5f438c8d2e8037d91adf780a", "5f438c9b09ab5a068b9f87e7"]
  }
]
```

### GetStreams

Returns a list of streams. This call can be used to retrieve streamIds which are needed to start and stop a stream.

```
GET api/1/services/streams?pageIndex=1&pageSize=100
```

| Query parameter | Description                                                        |
| --------------- | ------------------------------------------------------------------ |
| pageIndex       | The page index of the pagination. The first index is 1.            |
| pageSize        | The number of streams on a page                                    |
| streamIds       | List of streamIds to be found, ignored when using a stream API key |

Response:

[filename](_customer-api/example-multiple-StreamResponseDTO.md ':include')

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

[filename](_customer-api/example-StreamResponseDTO.md ':include')

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

[filename](_customer-api/example-stopped-StreamResponseDTO.md ':include')

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
    "domainWhiteList": ["streamName.com", "video.streamName.com"]
  }
}
```

Response example:

[filename](_customer-api/example-StreamResponseDTO.md ':include')

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
  }
}
```

Response example:

[filename](_customer-api/example-StreamResponseDTO.md ':include')
