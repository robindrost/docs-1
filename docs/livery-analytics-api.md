# Livery analytics API

The Livery analytics API allows customers to get insights in the performance of their streams.
The API consist of a single REST call with a number of query parameters.
This document explains how this API can be used.

```groovy
GET /api/2/analytics/{streamId}/dimensions
```

Where {streamId} is the unique ID of your stream as found in the dashboard.
This call accepts a couple different parameters:

- from (**mandatory**: start date in millis)
- until (**mandatory**: end date in millis)
- metric (_optional_: currently only accepts "viewers". Defaults to viewers.)
- dimension (_optional_: must be one of the following: PLATFORM, CONNECTION_TYPE, VIDEO_QUALITY, ENCODER_TYPE . Defaults to PLATFORM.)
- minutesInterval (**mandatory (1/2)**: integer (1-1440). Minute intervals to split the data into. **Cannot be used along with the points parameter**)
- points (**mandatory (2/2)**: positive integer (1+). The amount of points to split the data into. **Cannot be used along with the minutesInterval parameter**)

The minimum range to fetch data for is one minute, the maximum range is currently one week.

## Examples

For example, to get viewer data per platform in a single point:

```groovy
GET /api/2/analytics/{streamId}/dimensions?from=1606246100000&until=1606247300000&points=1
```

Which would return something like the following:

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
          "dimension": "UNKNOWN",
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

```groovy
GET /api/2/analytics/{streamId}/dimensions?from=1606246100000&until=1606247300000&dimension=ENCODER_TYPE&minutesInterval=10
```

Which would come back with something like:

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
