# Livery Web SDK

[![NPM package](https://img.shields.io/npm/v/@exmg/livery)](https://www.npmjs.com/package/@exmg/livery)
[![Changelog](https://img.shields.io/badge/unpkg-CHANGELOG-blue)](https://unpkg.com/browse/@exmg/livery/CHANGELOG.md)
[![License](https://img.shields.io/npm/l/@exmg/livery)](https://unpkg.com/browse/@exmg/livery/LICENSE)
[![Built with open-wc recommendations](https://img.shields.io/badge/built%20with-open--wc-blue.svg)](https://open-wc.org/)

Ex Machina Group Livery Web SDK, published as [@exmg/livery](https://www.npmjs.com/package/@exmg/livery).

?> Demo can be found at: [demo.liveryvideo.com](https://demo.liveryvideo.com) ([source](https://github.com/exmg/livery-demo-web)).

More information can be found at: [liveryvideo.com](https://liveryvideo.com).

## Installation

### Compatibility

If you want to support Firefox and Edge, begin by loading polyfills:

```html
<script src="https://unpkg.com/@webcomponents/webcomponentsjs@latest/webcomponents-loader.js"></script>
```

### CDN

Livery can be loaded from [unpkg](https://unpkg.com):

```html
<script src="https://unpkg.com/@exmg/livery"></script>
```

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

?> This example uses the Livery Demo config and source, replace them by your own.

```html
<livery-sdk
  config="https://cdn.playtotv.com/video-encoder/remoteconfigs/5ddb98f5e4b0937e6a4507f2.json"
></livery-sdk>

<livery-player autoplaymuted persistmuted>
  <source
    src="https://exmachina-ull-demo.akamaized.net/cmaf/live/664379/5ddb98f5e4b0937e6a4507f2-TESTING/out.mpd"
  />
</livery-player>

<!-- Optionally show buffer graph -->
<livery-buffer-graph></livery-buffer-graph>

<!-- Optionally show log -->
<livery-log></livery-log>

<script>
  // Connect player to graph and log elements
  const player = document.querySelector('livery-player');
  const graph = document.querySelector('livery-buffer-graph');
  const log = document.querySelector('livery-log');
  graph.player = player;
  log.player = player;
</script>
```

## Exports

When using the UMD bundle, these can be found as properties of `exmg.livery`.

### LiverySdk

Invisible element defined as `<livery-sdk>` which is used to initialize the Livery SDK.

#### Attributes

| Attribute  | Property   | Type                                                                    | Default  | Description                                                 |
| ---------- | ---------- | ----------------------------------------------------------------------- | -------- | ----------------------------------------------------------- |
| `bubbles`  | `bubbles`  | `boolean`                                                               | `false`  | If true then events dispatched by this element will bubble. |
| `config`   | `config`   | `string`                                                                | `''`     | Remote config URL.                                          |
| `loglevel` | `logLevel` | `'quiet'` \| `'error'` \| `'warn'` \| `'info'` \| `'debug'` \| `'spam'` | `'info'` | Log level.                                                  |

#### Events

| Event                                         | Description                                        |
| --------------------------------------------- | -------------------------------------------------- |
| [livery-error](#liveryerrorevent)             | Dispatched when an error occurs.                   |
| [livery-initialized](#liveryinitializedevent) | Dispatched when SDK initialization was successful. |

### LiveryPlayer

Element defined as `<livery-player>` which can be used like a `<video>` element to play a live video stream.
One or more sources will need to be specified as child `<source>` elements.
This will not start playing until a `<livery-sdk>` element has been successfully initialized within the same `window`.

#### Attributes

| Attribute       | Property        | Type               | Default | Description                                                                                                                               |
| --------------- | --------------- | ------------------ | ------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| `autoplay`      | `autoplay`      | `boolean`          | `false` | If `true` then automatically begin playback as soon as possible.<br />Note: Browser might prevent this if muted is false.                 |
| `autoplaymuted` | `autoplayMuted` | `boolean`          | `false` | If `true` then automatically begin playback as soon as possible, falling back to muted autoplay if necessary.                             |
| `bubbles`       | `bubbles`       | `boolean`          | `false` | If true then events dispatched by this element will bubble.                                                                               |
| `controls`      | `controls`      | `string` \| `null` | `null`  | Which controls to enable (space separated; e.g. to enable all controls: `'error mute fullscreen quality'`).                               |
| `loop`          | `loop`          | `boolean`          | `false` | If `true` then automatically seek back to the start upon reaching the end.                                                                |
| `muted`         | `muted`         | `boolean`          | `false` | If `true` then audio is muted.<br />Dispatches: `livery-volume-change`.                                                                   |
| `persistmuted`  | `persistMuted`  | `boolean`          | `false` | If `true` then persist muted state of player in localStorage with key `'muted'`.                                                          |
| `poster`        | `poster`        | `string` \| `null` | `null`  | A URL for an image to be shown while the video is downloading.                                                                            |
| `preload`       | `preload`       | `string` \| `null` | `null`  | Whether to preload `'none'`, just `'metadata'` (default) or `'full'` media.                                                               |
| `targetlatency` | `targetLatency` | `number` \| `null` | `null`  | Custom target live latency in seconds. If `null` then remote config or default latency is used. If `0` or `NaN` then syncing is disabled. |

#### Properties

| Property          | R/W | Type                                                                                                                    | Default     | Description                                                                                                                                                                             |
| ----------------- | --- | ----------------------------------------------------------------------------------------------------------------------- | ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `activeQuality`   | R   | `number`                                                                                                                | `-1`        | Index of active quality.<br />If `-1` then no quality or an audio and video quality pair that matches none of the qualities is active.<br />Dispatches: `livery-active-quality-change`. |
| `buffer`          | R   | `number`                                                                                                                | `NaN`       | Size of buffer, ahead of current position, in seconds.<br />Changes on: `livery-progress`, `livery-time-update`.                                                                        |
| `bufferEnd`       | R   | `number`                                                                                                                | `NaN`       | Buffer end position in seconds.<br />Dispatches: `livery-progress`.                                                                                                                     |
| `currentSrc`      | R   | `string`                                                                                                                | `''`        | Current media source URL.                                                                                                                                                               |
| `currentTime`     | R/W | `number`                                                                                                                | `0`         | Current playback time position in seconds.<br />Dispatches: `livery-time-update`.                                                                                                       |
| `decodedFrames`   | R   | `number`                                                                                                                | `NaN`       | Number of video frames which have been decoded since the last time the media was loaded.<br />If not supported this will return `NaN`.                                                  |
| `droppedFrames`   | R   | `number`                                                                                                                | `NaN`       | Number of video frames which have been dropped since the last time the media was loaded.<br />If not supported this will return `NaN`.                                                  |
| `duration`        | R   | `number`                                                                                                                | `NaN`       | Media duration in seconds.<br />Dispatches: `livery-duration-change`.                                                                                                                   |
| `engineName`      | R   | `string`                                                                                                                | `''`        | Current media engine name.                                                                                                                                                              |
| `error`           | R   | `Error` \| `undefined`                                                                                                  | `undefined` | Most recent unrecovered error.<br />Dispatches: `livery-error`, `livery-recovered`.                                                                                                     |
| `latency`         | R   | `number`                                                                                                                | `NaN`       | End to end latency in seconds.<br />Changes on: `livery-offset-change`, `livery-time-update`, `livery-zero-change`.                                                                     |
| `playbackRate`    | R/W | `number`                                                                                                                | `1`         | Playback rate (1 is normal).<br />Dispatches: `livery-rate-change`.                                                                                                                     |
| `playbackState`   | R   | `'BUFFERING'` \| `'ENDED'` \| `'FAST_FORWARD'` \| `'PAUSED'` \| `'PLAYING'` \| `'REWIND'` \| `'SEEKING'` \| `'SLOW_MO'` | `'PAUSED'`  | Playback state.<br />Dispatches: `livery-playback-change`.                                                                                                                              |
| `qualities`       | R   | `LiveryQuality[]`                                                                                                       | `[]`        | Available qualities.<br />Dispatches: `livery-qualities-change`.                                                                                                                        |
| `selectedQuality` | R/W | `number`                                                                                                                | `-1`        | Index of selected quality.<br />If `-1` then no quality is selected and automatic quality selection is used instead.<br />Dispatches: `livery-selected-quality-change`.                 |
| `streamTimestamp` | R   | `number`                                                                                                                | `NaN`       | Stream time in milliseconds since 1/1/1970 UTC.<br />Changes on: `livery-time-update`, `livery-zero-change`.                                                                            |
| `streamType`      | R   | `'UNKNOWN'` \| `'ONDEMAND'` \| `'LIVE'`                                                                                 | `'UNKNOWN'` | Stream type.<br />Changes on: `livery-duration-change`.                                                                                                                                 |
| `timeOffset`      | R   | `number`                                                                                                                | `NaN`       | Local device time offset in milliseconds.<br />To be added to local time to get synchronized (e.g: server) time.<br />Dispatches: `livery-offset-change`.                               |
| `volume`          | R/W | `number`                                                                                                                | `1`         | Audio volume, from 0.0 (silent) to 1.0 (loudest).<br />Dispatches: `livery-volume-change`.                                                                                              |
| `zeroTimestamp`   | R   | `number`                                                                                                                | `NaN`       | Time in milliseconds since 1/1/1970 UTC at which currentTime would have been 0.<br />Dispatches: `livery-zero-change`.                                                                  |

#### Methods

| Method | Type                | Description                                                                                                      |
| ------ | ------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `load` | `(): Promise<void>` | Reset the player, select the next supported source from the list of `sources` and attempt to (re)start playback. |
| `play` | `(): Promise<void>` | Attempt to start/resume media playback.                                                                          |

#### Events

| Event                                                               | Description                                                                                        |
| ------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| [livery-active-quality-change](#liveryactivequalitychangeevent)     | Dispatched when `activeQuality` has changed.                                                       |
| [livery-duration-change](#liverydurationchangeevent)                | Dispatched when `duration` has changed.                                                            |
| [livery-error](#liveryerrorevent)                                   | Dispatched when an error occurs.                                                                   |
| [livery-offset-change](#liveryoffsetchangeevent)                    | Dispatched when `timeOffset` has changed.                                                          |
| [livery-playback-change](#liveryplaybackchangeevent)                | Dispatched when `playbackState` has changed.                                                       |
| [livery-progress](#liveryprogressevent)                             | Dispatched periodically to inform of progress downloading the media.                               |
| [livery-qualities-change](#liveryqualitieschangeevent)              | Dispatched when `qualities` have changed.                                                          |
| [livery-rate-change](#liveryratechangeevent)                        | Dispatched when `playbackRate` has changed.                                                        |
| [livery-recovered](#liveryrecoveredevent)                           | Dispatched when player has recovered from an error.                                                |
| [livery-selected-quality-change](#liveryselectedqualitychangeevent) | Dispatched when `selectedQuality` has changed.                                                     |
| [livery-source-change](#liverysourcechangeevent)                    | Dispatched when `currentSrc` has changed.                                                          |
| [livery-started](#liverystartedevent)                               | Dispatched when player has started, e.g: after SDK is initialized and element is connected to DOM. |
| [livery-stopped](#liverystoppedevent)                               | Dispatched when player has stopped, e.g: after element is disconnected from DOM.                   |
| [livery-time-update](#liverytimeupdateevent)                        | Dispatched periodically when `currentTime` changes.                                                |
| [livery-volume-change](#liveryvolumechangeevent)                    | Dispatched when `volume` and/or `muted` have changed.                                              |
| [livery-zero-change](#liveryzerochangeevent)                        | Dispatched when `zeroTimestamp` has changed.                                                       |

#### CSS Custom Properties

!> Support for CSS custom properties is experimental; names etc. might change.

| Property                              | Default              | Description                                                                                                                       |
| ------------------------------------- | -------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `--livery-player-error-message-color` | `#999`               | Error message color.                                                                                                              |
| `--livery-player-fit`                 | `'cover'`            | Video [object-fit](https://developer.mozilla.org/en-US/docs/Web/CSS/object-fit) value (contain, cover, fill, none or scale-down). |
| `--livery-player-overlay-color`       | `rgba(0, 0, 0, 0.8)` | Overlay background color.                                                                                                         |
| `--livery-player-overlay-text-color`  | `#ccc`               | Overlay text color.                                                                                                               |

### LiveryBufferGraph

Element defined as `<livery-buffer-graph>` which renders a graph of the buffer size and latency of specified LiveryPlayer.

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

#### LiveryActiveQualityChangeEvent

Dispatched with event type `'livery-active-quality-change'` when `activeQuality` has changed.

| Property        | Type     | Description              |
| --------------- | -------- | ------------------------ |
| `activeQuality` | `number` | Index of active quality. |

#### LiveryDurationChangeEvent

Dispatched with event type: `'livery-duration-change'` when `duration` has changed.

| Property   | Type     | Description                |
| ---------- | -------- | -------------------------- |
| `duration` | `number` | Media duration in seconds. |

#### LiveryErrorEvent

Dispatched with event type: `'livery-error'` when an error occurs in an element.

| Property | Type    | Description                     |
| -------- | ------- | ------------------------------- |
| `error`  | `Error` | Error that occurred in element. |

#### LiveryInitializedEvent

Dispatched with event type: `'livery-initialized'` when SDK initialization was successful.

| Property | Type           | Description         |
| -------- | -------------- | ------------------- |
| `config` | `LiveryConfig` | Initialized config. |

#### LiveryOffsetChangeEvent

Dispatched with event type: `'livery-offset-change'` when `timeOffset` has changed.

| Property     | Type     | Description                               |
| ------------ | -------- | ----------------------------------------- |
| `timeOffset` | `number` | Local device time offset in milliseconds. |

#### LiveryPlaybackChangeEvent

Dispatched with event type: `'livery-playback-change'` when `playbackState` has changed.

| Property        | Type                                                                                                                    | Description     |
| --------------- | ----------------------------------------------------------------------------------------------------------------------- | --------------- |
| `playbackState` | `'BUFFERING'` \| `'ENDED'` \| `'FAST_FORWARD'` \| `'PAUSED'` \| `'PLAYING'` \| `'REWIND'` \| `'SEEKING'` \| `'SLOW_MO'` | Playback state. |

#### LiveryProgressEvent

Dispatched with event type: `'livery-progress'` periodically to inform of progress downloading the media.

| Property    | Type     | Description                     |
| ----------- | -------- | ------------------------------- |
| `bufferEnd` | `number` | Buffer end position in seconds. |

#### LiveryQualitiesChangeEvent

Dispatched with event type: `'livery-qualities-change'` when `qualities` have changed.

| Property    | Type              | Description          |
| ----------- | ----------------- | -------------------- |
| `qualities` | `LiveryQuality[]` | Available qualities. |

#### LiveryRateChangeEvent

Dispatched with event type: `'livery-rate-change'` when `playbackRate` has changed.

| Property       | Type     | Description                  |
| -------------- | -------- | ---------------------------- |
| `playbackRate` | `number` | Playback rate (1 is normal). |

#### LiveryRecoveredEvent

Dispatched with event type: `'livery-recovered'` when player has recovered from an error.

#### LiverySelectedqualityChangeEvent

Dispatched with event type: `'livery-selected-quality-change'` when `selectedQuality` has changed.

| Property          | Type     | Description                |
| ----------------- | -------- | -------------------------- |
| `selectedQuality` | `number` | Index of selected quality. |

#### LiverySourceChangeEvent

Dispatched with event type: `'livery-source-change'` when `currentSrc` has changed.

| Property     | Type     | Description               |
| ------------ | -------- | ------------------------- |
| `currentSrc` | `string` | Current media source URL. |

#### LiveryStartedEvent

Dispatched with event type: `'livery-started'` when player has started, e.g: after SDK is initialized and element is connected to DOM.

#### LiveryStoppedEvent

Dispatched with event type: `'livery-stopped'` when player has stopped, e.g: after element is disconnected from DOM.

#### LiveryTimeUpdateEvent

Dispatched with event type: `'livery-time-update'` periodically when `currentTime` changes.

| Property      | Type     | Description                                |
| ------------- | -------- | ------------------------------------------ |
| `currentTime` | `number` | Current playback time position in seconds. |

#### LiveryVolumeChangeEvent

Dispatched with event type: `'livery-volume-change'` when `volume` and/or `muted` have changed.

| Property | Type      | Description                                       |
| -------- | --------- | ------------------------------------------------- |
| `muted`  | `boolean` | If `true` then audio is muted.                    |
| `volume` | `number`  | Audio volume, from 0.0 (silent) to 1.0 (loudest). |

#### LiveryZeroChangeEvent

Dispatched with event type: `'livery-zero-change'` when `zeroTimestamp` has changed.

| Property        | Type     | Description                                                                     |
| --------------- | -------- | ------------------------------------------------------------------------------- |
| `zeroTimestamp` | `number` | Time in milliseconds since 1/1/1970 UTC at which currentTime would have been 0. |
