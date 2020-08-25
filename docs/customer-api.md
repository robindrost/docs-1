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

| Query parameter | Description                                             |
| --------------- | ------------------------------------------------------- |
| pageIndex       | The page index of the pagination. The first index is 1. |
| pageSize        | The number of streams on a page                         |

Response:

```json
[
  {
    "streamId": "5f2022df27b35d1ebfe17b78",
    "customerId": "5c5468158f087748c233194f",
    "name": "streamName",
    "posterUrl": "streamName.com/poster.png",
    "interactiveLayerUrl": "interactive.example/5f2022df27b35d1ebfe17b78",

    "encoderIds": ["5f2807dc4fd690da7238fd56", "5f2807e50dc1518c86e9cbf5"],

    "streamingState": "STARTED",
    "streamUrlType": "STATIC",
    "encoderPlatform": "CLOUD",

    "streamUrls": [
      "https://example.com/cmaf/5f27c847e4b0716a97225f47/out.mpd",
      "https://example.com/cmaf/5f27c847e4b0716a97225f47/master.m3u8"
    ],

    "remoteConfigUrl": "cdn.liveryvideo.com/remoteconfigs/5f2022df27b35d1ebfe17b78"
  }
]
```

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

```json
{
  "streamId": "5f2022df27b35d1ebfe17b78",
  "customerId": "5c5468158f087748c233194f",
  "name": "streamName",
  "posterUrl": "streamName.com/poster.png",
  "interactiveLayerUrl": "interactive.example/5f2022df27b35d1ebfe17b78",

  "encoderIds": ["5f2807dc4fd690da7238fd56", "5f2807e50dc1518c86e9cbf5"],

  "streamingState": "STARTED",
  "streamUrlType": "STATIC",
  "encoderPlatform": "CLOUD",

  "streamUrls": [
    "https://example.com/cmaf/5f27c847e4b0716a97225f47/out.mpd",
    "https://example.com/cmaf/5f27c847e4b0716a97225f47/master.m3u8"
  ],

  "remoteConfigUrl": "cdn.liveryvideo.com/remoteconfigs/5f2022df27b35d1ebfe17b78"
}
```

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

```json
{
  "streamId": "5e7a10bee4b046f00ad17245",
  "customerId": "5c5468158f087748c233194f",
  "name": "streamName",
  "posterUrl": "streamName.com/poster.png",
  "interactiveLayerUrl": "interactive.example/5f2022df27b35d1ebfe17b78",

  "encoderIds": ["5f2807dc4fd690da7238fd56", "5f2807e50dc1518c86e9cbf5"],

  "streamingState": "STOPPED",
  "streamUrlType": "STATIC",
  "encoderPlatform": "CLOUD",

  "streamUrls": [
    "https://exmachina-ull-demo.akamaized.net/cmaf/live/5e7a10bee4b046f00ad17245/out.mpd",
    "https://exmachina-ull-demo.akamaized.net/cmaf/live/5e7a10bee4b046f00ad17245/master.m3u8"
  ],

  "remoteConfigUrl": "cdn.liveryvideo.com/remoteconfigs/5e7a10bee4b046f00ad17245"
}
```
