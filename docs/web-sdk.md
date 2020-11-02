# Livery Web SDK

[![NPM package](https://img.shields.io/npm/v/@exmg/livery)](https://www.npmjs.com/package/@exmg/livery)
[![Changelog](https://img.shields.io/badge/docs-CHANGELOG-blue)](web-sdk-changelog.md)
[![License](https://img.shields.io/npm/l/@exmg/livery)](https://unpkg.com/browse/@exmg/livery/LICENSE)
[![Built with open-wc recommendations](https://img.shields.io/badge/built%20with-open--wc-blue.svg)](https://open-wc.org/)

Ex Machina Group Livery Web SDK, published as [@exmg/livery](https://www.npmjs.com/package/@exmg/livery).

?> Demo can be found at: [demo.liveryvideo.com](https://demo.liveryvideo.com) ([source](https://github.com/exmg/livery-demo-web)).

More information can be found at: [liveryvideo.com](https://liveryvideo.com).

Please refer to the [Livery Management Portal](https://video-encoder-director.playtotv.com/) for stream configuration options.

## Support

This package exports two bundles.

The ES `module` bundle is targetted at modern browsers and tools supporting `ES2019`.

The UMD `main` bundle (as used by unpkg below) supports the following browser versions (and up):

Chrome v64, Safari v12, Edge v74, Firefox v60, Android Chrome v51, Samsung Internet v6.4 and iOS/iPadOS Safari v12.

## Installation

### CDN

Livery can be loaded from [unpkg](https://unpkg.com):

```html
<script src="https://unpkg.com/@exmg/livery@x.y.z"></script>
```

?> Replace the version (`x.y.z`) above by the version of the SDK that you wish to use. E.g: update to latest stable release periodically. Please see the [CHANGELOG](web-sdk-changelog.md) for details.

### NPM

Or Livery can be installed using NPM:

```bash
npm install @exmg/livery
```

And then loaded in your JavaScript and bundled as you like:

```js
import '@exmg/livery';
```

## Usage

### HTML

?> Replace the Livery Demo stream id (`5ddb98f5e4b0937e6a4507f2`) below by your own.

```html
<livery-player streamid="5ddb98f5e4b0937e6a4507f2"></livery-player>
```

Please see the [LiveryPlayer](#liveryplayer) API documentation below for additional attributes etc.

### CSS

To fix the size of the livery-player element for the video etc. to be fit inside, use something like:

```css
livery-player {
  width: 100%;
  height: 50vh;
}
```

## Exports

When using the UMD bundle, these can be found as properties of `livery` in the global namespace, e.g: `livery.version`.

### endpointId

String property specifying id used by this Livery endpoint.

### version

String property specifying version of Livery SDK.

### LiveryPlayer

Element defined as `<livery-player>` which can be used to play a livery video stream.

#### Usage

See the general [Usage](#usage) section above.

#### Attributes

| Attribute  | Property   | Type      | Default | Description                                                                                                                                         |
| ---------- | ---------- | --------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `bubbles`  | `bubbles`  | `boolean` | `false` | If true then events dispatched by this element will bubble.                                                                                         |
| `loglevel` | `logLevel` | `string`  | `null`  | Log level (`'quiet'` \| `'error'` \| `'warn'` \| `'info'` \| `'debug'` \| `'spam'`). Defaults to `'info'` when value is null (attribute is absent). |
| `muted`    | `muted`    | `boolean` | `false` | If `true` then audio is muted.<br />Dispatches: `livery-volume-change`.                                                                             |
| `streamid` | `streamId` | `string`  | `null`  | Livery stream ID (required).                                                                                                                        |
| `vumeter`  | `vuMeter`  | `boolean` | `false` | Enable volume unit meter. Note: Will result in anonymous CORS requests.                                                                             |

#### Properties

| Property        | R/W | Type                                                                                                                    | Default     | Description                                                                         |
| --------------- | --- | ----------------------------------------------------------------------------------------------------------------------- | ----------- | ----------------------------------------------------------------------------------- |
| `error`         | R   | `Error` \| `undefined`                                                                                                  | `undefined` | Most recent unrecovered error.<br />Dispatches: `livery-error`, `livery-recovered`. |
| `playbackState` | R   | `'BUFFERING'` \| `'ENDED'` \| `'FAST_FORWARD'` \| `'PAUSED'` \| `'PLAYING'` \| `'REWIND'` \| `'SEEKING'` \| `'SLOW_MO'` | `'PAUSED'`  | Playback state.<br />Dispatches: `livery-playback-change`.                          |

In addition there is an `engine` property but that is meant for debugging purposes only. It's use is not supported.

#### Events

| Event                                                | Description                                           |
| ---------------------------------------------------- | ----------------------------------------------------- |
| [livery-error](#liveryerrorevent)                    | Dispatched when an error occurs.                      |
| [livery-playback-change](#liveryplaybackchangeevent) | Dispatched when `playbackState` has changed.          |
| [livery-recovered](#liveryrecoveredevent)            | Dispatched when player has recovered from an error.   |
| [livery-volume-change](#liveryvolumechangeevent)     | Dispatched when `volume` and/or `muted` have changed. |

### LiveryBufferGraph

Element defined as `<livery-buffer-graph>` which renders a graph of the buffer size and latency of specified LiveryPlayer.

#### Usage

This needs to be connected to the player element like so:

```html
<livery-player streamid="5ddb98f5e4b0937e6a4507f2"></livery-player>
<livery-buffer-graph></livery-buffer-graph>

<script>
  const player = document.querySelector('livery-player');
  const graph = document.querySelector('livery-buffer-graph');
  graph.player = player;
</script>
```

#### Attributes

| Attribute         | Property          | Type               | Default | Description                                                                                                                             |
| ----------------- | ----------------- | ------------------ | ------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| `backgroundcolor` | `backgroundColor` | `string` \| `null` | `null`  | Chart background color.                                                                                                                 |
| `bubbles`         | `bubbles`         | `boolean`          | `false` | If true then events dispatched by this element will bubble.                                                                             |
| `buffercolor`     | `bufferColor`     | `string` \| `null` | `null`  | Buffer line color.                                                                                                                      |
| `latencycolor`    | `latencyColor`    | `string` \| `null` | `null`  | Latency line color.                                                                                                                     |
| `maxrows`         | `maxRows`         | `number`           | `60`    | Maximum number of rows to store in data table. E.g: With default value: 60 and 500ms updateInterval this will roughly equal 30 seconds. |
| `textcolor`       | `textColor`       | `string` \| `null` | `null`  | Chart text color.                                                                                                                       |
| `updateinterval`  | `updateInterval`  | `number`           | `500`   | Interval in milliseconds at which to add a row to the data table and draw the chart.                                                    |

#### Properties

| Property | R/W | Type           | Default | Description                                                |
| -------- | --- | -------------- | ------- | ---------------------------------------------------------- |
| `player` | R/W | `LiveryPlayer` | `null`  | Reference to LiveryPlayer element to create the graph for. |

#### Events

| Event                             | Description                      |
| --------------------------------- | -------------------------------- |
| [livery-error](#liveryerrorevent) | Dispatched when an error occurs. |

### LiveryLog

Element defined as `<livery-log>` which shows messages logged by global livery logic and specified LiveryPlayer.

Notes:

- Only one livery-log element can be used (e.g: connected to DOM) at a time.
- Previously logged messages will not be shown.

#### Usage

This needs to be connected to the player element like so:

```html
<livery-player streamid="5ddb98f5e4b0937e6a4507f2"></livery-player>
<livery-log></livery-log>

<script>
  const player = document.querySelector('livery-player');
  const log = document.querySelector('livery-log');
  log.player = player;
</script>
```

#### Attributes

| Attribute       | Property        | Type     | Default | Description                                         |
| --------------- | --------------- | -------- | ------- | --------------------------------------------------- |
| `maxlinelength` | `maxLineLength` | `number` | `100`   | Number of characters after which to truncate lines. |
| `maxlines`      | `maxLines`      | `number` | `50`    | Maximum number of lines to display.                 |

#### Properties

| Property | R/W | Type           | Default | Description                                            |
| -------- | --- | -------------- | ------- | ------------------------------------------------------ |
| `player` | R/W | `LiveryPlayer` | `null`  | Reference to LiveryPlayer element to log details from. |

### LiveryEvents

Object containing references to event classes used by the livery elements.

Each Livery event class has a static `type` property defining the `Event` instance `type` property it uses.
Any additional properties are documented below.

#### LiveryErrorEvent

Dispatched with event type: `'livery-error'` when an error occurs in an element.

| Property | Type    | Description                     |
| -------- | ------- | ------------------------------- |
| `error`  | `Error` | Error that occurred in element. |

#### LiveryPlaybackChangeEvent

Dispatched with event type: `'livery-playback-change'` when `playbackState` has changed.

| Property        | Type                                                                                                                    | Description     |
| --------------- | ----------------------------------------------------------------------------------------------------------------------- | --------------- |
| `playbackState` | `'BUFFERING'` \| `'ENDED'` \| `'FAST_FORWARD'` \| `'PAUSED'` \| `'PLAYING'` \| `'REWIND'` \| `'SEEKING'` \| `'SLOW_MO'` | Playback state. |

#### LiveryRecoveredEvent

Dispatched with event type: `'livery-recovered'` when player has recovered from an error.

#### LiveryVolumeChangeEvent

Dispatched with event type: `'livery-volume-change'` when `volume` and/or `muted` have changed.

| Property | Type      | Description                                       |
| -------- | --------- | ------------------------------------------------- |
| `muted`  | `boolean` | If `true` then audio is muted.                    |
| `volume` | `number`  | Audio volume, from 0.0 (silent) to 1.0 (loudest). |
