# Livery Video Interactive API

This document describes the API calls that are available to customers. These calls can be used by application administrators to retrieve restricted data.

For more information regarding domain & paths, see: **Livery Video Customer API**

## Pull restricted broadcast data

Authorized customers are allowed to access Livery interactive restricted API calls.
Authorization will be done via an API key that can be retrieved from the Livery interactive portal.

The value of the added key must be included in header `x-livery-api-key`

Unless an API key is added, the customer will not be able to retrieve broadcast data.

## Push restricted broadcast data

On broadcast status change, it is possible to be notified of the changes. You have to add a value to `PUSH_URL`
server setting, representing your API endpoint which will receive the data with HTTP POST.

##### Note: Add your API endpoint URL to receive broadcast status changes. This is done via server settings update endpoint

## API Calls

Page numbers start at 1 on paginated API calls

### Get broadcast data (pull)

This API call can be used to retrieve information about the broadcast that is currently live.
A use case is to poll this API to react on the broadcast being closed.

```
curl -H "x-livery-api-key: value" https://www.example.com/services/broadcasts/{broadcastId}
```

Response example:

[filename](_customer-interactive-api/_example-VideoExtensionBroadcast.md ':include')

### Get broadcast data (push)

This http request will push data to your API endpoint when a broadcast is updated.

```
curl -d 'jsonExample' -H "Content-Type: application/json" -X POST http://yourdomain:3000/endpoint
```

Request body:

[filename](_customer-interactive-api/_example-VideoExtensionBroadcast.md ':include')

### Get user data

This API call can be used to retrieve a list of users in a broadcast.

```
curl -H "x-livery-api-key: value" https://www.example.com/services/broadcasts/{broadcastId}/users
```

Response example:

[filename](_customer-interactive-api/_example-Users.md ':include')

### Get leaderboard data

This API call can be used to retrieve game data on a broadcast.

```
curl -H "x-livery-api-key: value" https://www.example.com/services/broadcasts/{broadcastId}/leaderboard/pagenumbers/{pageNumber}
```

Response example:

[filename](_customer-interactive-api/_example-Leaderboard.md ':include')