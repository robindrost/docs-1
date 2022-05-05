# Livery Video Web Player

[![NPM package](https://img.shields.io/npm/v/@liveryvideo/player)](https://www.npmjs.com/package/@liveryvideo/player)
[![Changelog](https://img.shields.io/badge/docs-CHANGELOG-blue)](web-player-changelog.md)
[![License](https://img.shields.io/npm/l/@liveryvideo/player)](https://unpkg.com/browse/@liveryvideo/player/LICENSE)
[![Build with Lit](https://img.shields.io/badge/build%20with-Lit-blue.svg)](https://lit.dev/)

Livery video player for use in web browsers.

?> Demo can be found at: [demo.liveryvideo.com](https://demo.liveryvideo.com) ([source](https://github.com/liveryvideo/demo-web)).

Please refer to the [Livery Management Portal](https://video-encoder-director.playtotv.com/) for stream configuration options.

## Basic Usage

If you're not bundling your own JS you can just use an HTML snippet like this:

```html
<script src="https://unpkg.com/@liveryvideo/player@x.y.z"></script>
<livery-player streamid="5ddb98f5e4b0937e6a4507f2"></livery-player>
```

?> Replace the player version and stream id by your own, see: [CDN](#cdn), [HTML](#html) and [CSS](#css) for details below.

?> If this does not work you can fall back to embedding the player in an iframe instead, see: [Livery Video Embed](embed.md).

## Support

This package exports two bundles.

The ES `module` bundle is targetted at modern browsers and tools supporting `ES2019`.

The UMD `main` bundle (as used by unpkg [CDN](#cdn)) supports iOS/iPadOS Safari v12 and up and the last 2 major versions of: Chrome, Safari, Edge, Firefox, Android Chrome and Samsung Internet.

## Installation

### CDN

The player can be loaded from [unpkg](https://unpkg.com):

```html
<script src="https://unpkg.com/@liveryvideo/player@x.y.z"></script>
```

?> Replace the version (`x.y.z`) above by the player version that you wish to use. E.g: update to latest stable release periodically. Please see the [CHANGELOG](web-player-changelog.md) for details.

### NPM

Or the player can be installed using NPM:

```bash
npm install @liveryvideo/player
```

And then loaded in your JavaScript and bundled as you like:

```js
import '@liveryvideo/player';
```

## Usage

### HTML

```html
<livery-player streamid="5ddb98f5e4b0937e6a4507f2"></livery-player>
```

?> Replace the Livery Demo stream id (`5ddb98f5e4b0937e6a4507f2`) above by your own.

Please see the [LiveryPlayer](#liveryplayer) API documentation below for additional attributes etc.

### CSS

The livery-player element is displayed as a `block`, i.e: using the full width available by default.

If no height is specified it will use the video aspect ratio (default `16:9`) to determine the height intrinsically.

Alternatively you can specify the size extrinsically, to prevent it from changing as the stream is loaded, e.g:

```css
livery-player {
  height: 50vh;
}
```

See also: [MDN Sizing items in CSS](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Sizing_items_in_CSS)

When you use a fixed height like this the video contents of the player will be fitted inside the available space based on the `Fit` mode configured in the Livery Portal for this stream (e.g: `CONTAIN` or `COVER` or ..).

See also: [MDN object-fit](https://developer.mozilla.org/en-US/docs/Web/CSS/object-fit)

## Exports

When using the UMD bundle, these can be found as properties of `livery` in the global namespace, e.g: `livery.version`.

### endpointId

String property specifying the Livery endpoint id used by this player (e.g: for analytics).

### version

String property specifying the Livery video web player version.

### LiveryPlayer

Element defined as `<livery-player>` which can be used to play a Livery video stream.

By default the player will attempt to start playback unmuted and fall back to muted auto play if necessary. When the muted attribute is specified to the player element on creation it will start muted. When the user mutes the audio by clicking on the mute control that is persisted to local storage and on subsequent player loads it will then remain muted.

#### Usage

See the general [Usage](#usage) section above.

#### Attributes

| Attribute     | Property      | Type      | Default | Description                                                                                                                                         |
| ------------- | ------------- | --------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `akamaitoken` | `akamaiToken` | `string`  | `null`  | Access token to use to be able to see a stream that uses Akamai’s Token Auth feature.                                                               |
| `bubbles`     | `bubbles`     | `boolean` | `false` | If true then events dispatched by this element will bubble.                                                                                         |
| `configtype`  | `configType`  | `string`  | `null`  | Type of stream config to use. Defaults to SDK platform (WEB) when value is null (attribute is absent).                                              |
| `fullscreen`  | `fullscreen`  | `boolean` | `false` | If true then player is fullscreen.<br />Dispatches: `livery-fullscreen-change`                                                                      |
| `loglevel`    | `logLevel`    | `string`  | `null`  | Log level (`'quiet'` \| `'error'` \| `'warn'` \| `'info'` \| `'debug'` \| `'spam'`). Defaults to `'info'` when value is null (attribute is absent). |
| `muted`       | `muted`       | `boolean` | `false` | If `true` then audio is muted.<br />Dispatches: `livery-volume-change`                                                                              |
| `streamid`    | `streamId`    | `string`  | `null`  | Livery stream ID (required).                                                                                                                        |
| `vumeter`     | `vuMeter`     | `boolean` | `false` | Enable volume unit meter.                                                                                                                           |

#### Properties

| Property        | R/W | Type                                                                                                                    | Default     | Description                                                                        |
| --------------- | --- | ----------------------------------------------------------------------------------------------------------------------- | ----------- | ---------------------------------------------------------------------------------- |
| `customerId`    | R   | `string`                                                                                                                | `null`      | Livery customer ID.                                                                |
| `error`         | R   | `Error` \| `undefined`                                                                                                  | `undefined` | Most recent unrecovered error.<br />Dispatches: `livery-error`, `livery-recovered` |
| `latency`       | R   | `number`                                                                                                                | `NaN`       | End to end latency in seconds.                                                     |
| `playbackState` | R   | `'BUFFERING'` \| `'ENDED'` \| `'FAST_FORWARD'` \| `'PAUSED'` \| `'PLAYING'` \| `'REWIND'` \| `'SEEKING'` \| `'SLOW_MO'` | `'PAUSED'`  | Playback state.<br />Dispatches: `livery-playback-change`                          |
| `quality`       | R   | `string`                                                                                                                | `null`      | Active quality label.<br />Dispatches: `livery-quality-change`                     |
| `streamPhase`   | R   | `'PRE'` \| `'LIVE'` \| `'POST'`                                                                                         | `'PRE'`     | Stream phase.<br />Dispatches: `livery-phase-change`                               |

In addition there is an `engine` property but that is meant for debugging purposes only. It's use is not supported.

#### Methods

| Method                                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ----------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `registerPlayerCommand(name, handler)`          | Register custom player bridge command `handler` by `name`. The `handler` will be called with `arg` and `listener` when `sendPlayerCommand()` is called from the interactive layer bridge side with matching `name`. The value or Promise returned by `handler` will be passed back and returned by that interactive bridge call. Any `listener` calls here will similarly result in the interactive `listener` being called with those values. |
| `sendInteractiveCommand(name, arg?, listener?)` | Send command to interactive layer and return promise of value returned by the interactive bridge's custom command handler with matching `name` that is passed `arg`. Any `handler` `listener` calls will subsequently also be bridged to this `listener` callback.                                                                                                                                                                             |
| `unregisterPlayerCommand(name)`                 | Unregister custom player command by `name`.                                                                                                                                                                                                                                                                                                                                                                                                    |

#### Events

| Event                                                    | Description                                           |
| -------------------------------------------------------- | ----------------------------------------------------- |
| [livery-error](#liveryerrorevent)                        | Dispatched when an error occurs.                      |
| [livery-fullscreen-change](#liveryfullscreenchangeevent) | Dispatched when `fullscreen` has changed.             |
| [livery-phase-change](#liveryphasechangeevent)           | Dispatched when `streamPhase` has changed.            |
| [livery-playback-change](#liveryplaybackchangeevent)     | Dispatched when `playbackState` has changed.          |
| [livery-quality-change](#liveryqualitychangeevent)       | Dispatched when `quality` has changed.                |
| [livery-recovered](#liveryrecoveredevent)                | Dispatched when player has recovered from an error.   |
| [livery-volume-change](#liveryvolumechangeevent)         | Dispatched when `volume` and/or `muted` have changed. |

In addition there is a `livery-engine-change` event but that is meant for debugging purposes only. It’s use is not supported.

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
| `audiocolor`      | `audioColor`      | `string` \| `null` | `null`  | Audio buffer line color, defaults to `bufferColor` or '#3366cc'.                                                                        |
| `backgroundcolor` | `backgroundColor` | `string` \| `null` | `null`  | Chart background color.                                                                                                                 |
| `bubbles`         | `bubbles`         | `boolean`          | `false` | If true then events dispatched by this element will bubble.                                                                             |
| `buffercolor`     | `bufferColor`     | `string` \| `null` | `null`  | Buffer line color used as default for `audioColor` and `videoColor`.                                                                    |
| `latencycolor`    | `latencyColor`    | `string` \| `null` | `null`  | Live latency line color, defaults to '#ffa500'.                                                                                         |
| `maxrows`         | `maxRows`         | `number`           | `60`    | Maximum number of rows to store in data table. E.g: With default value: 60 and 500ms updateInterval this will roughly equal 30 seconds. |
| `textcolor`       | `textColor`       | `string` \| `null` | `null`  | Chart text color.                                                                                                                       |
| `updateinterval`  | `updateInterval`  | `number`           | `500`   | Interval in milliseconds at which to add a row to the data table and draw the chart.                                                    |
| `videocolor`      | `videoColor`      | `string` \| `null` | `null`  | Video buffer line color, defaults to `bufferColor` or '#dc3912'.                                                                        |

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

| Attribute       | Property        | Type     | Default | Description                                                     |
| --------------- | --------------- | -------- | ------- | --------------------------------------------------------------- |
| `maxlinelength` | `maxLineLength` | `number` | `100`   | Number of characters after which to truncate lines for display. |
| `maxlines`      | `maxLines`      | `number` | `50`    | Maximum number of lines to display.                             |
| `maxloglines`   | `maxLogLines`   | `number` | `10000` | Maximum number of log lines to retain in memory.                |

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

#### LiveryFullscreenChangeEvent

Dispatched with event type: `livery-fullscreen-change` when `fullscreen` has changed.

| Property     | Type      | Description                                        |
| ------------ | --------- | -------------------------------------------------- |
| `fullscreen` | `boolean` | True if player became fullscreen, false otherwise. |

#### LiveryPhaseChangeEvent

Dispatched with event type: `livery-phase-change` when `streamPhase` has changed.

| Property | Type                            | Description   |
| -------- | ------------------------------- | ------------- |
| `phase`  | `'PRE'` \| `'LIVE'` \| `'POST'` | Stream phase. |

#### LiveryPlaybackChangeEvent

Dispatched with event type: `'livery-playback-change'` when `playbackState` has changed.

| Property        | Type                                                                                                                    | Description     |
| --------------- | ----------------------------------------------------------------------------------------------------------------------- | --------------- |
| `playbackState` | `'BUFFERING'` \| `'ENDED'` \| `'FAST_FORWARD'` \| `'PAUSED'` \| `'PLAYING'` \| `'REWIND'` \| `'SEEKING'` \| `'SLOW_MO'` | Playback state. |

#### LiveryQualityChangeEvent

Dispatched with event type: `livery-quality-change` when `quality` has changed.

| Property  | Type     | Description                          |
| --------- | -------- | ------------------------------------ |
| `quality` | `string` | Label of quality that became active. |

#### LiveryRecoveredEvent

Dispatched with event type: `'livery-recovered'` when player has recovered from an error.

#### LiveryVolumeChangeEvent

Dispatched with event type: `'livery-volume-change'` when `volume` and/or `muted` have changed.

| Property | Type      | Description                                       |
| -------- | --------- | ------------------------------------------------- |
| `muted`  | `boolean` | If `true` then audio is muted.                    |
| `volume` | `number`  | Audio volume, from 0.0 (silent) to 1.0 (loudest). |

## Integrations

### Next.js

Next.js makes use of server side rendering, which the Livery Web SDK does not currently support. However, users of Next.js can still use the SDK by making use of Next.js’s support for [dynamic imports](https://nextjs.org/docs/advanced-features/dynamic-import) inside a [React Effect Hook](https://reactjs.org/docs/hooks-effect.html) or in a [React class component's `componentDidMount` lifecycle method](https://reactjs.org/docs/react-component.html#componentdidmount). When used in combination, this ensures the module being imported is evaluated at runtime on the client.

Note that many of the examples that Next.js provides use a statement of the form `dynamic( import(...) )`. The `dynamic()` function is used for importing React components and is not used for importing `@liveryvideo/player`. The code below illustrates basic usage with Next.js.

```javascript
// do not import('@liveryvideo/player') here

function MyComponent() {
  useEffect(
    () => {
      import('@liveryvideo/player');
    },
    [], // tell react to only use this effect on mount
  );
  return <livery-player streamid="5ddb98f5e4b0937e6a4507f2"></livery-player>;
}
```

## Livery Web Player Q&A

**Why do stream started muted on some devices?**

_With chrome://media-engagement/ you can check the ranking of the page and if it is allowed to start unmuted. When “is high” is YES that site is allowed to autoplay unmuted._

**Autoplay does not work on mobile?**

_The autoplay feature is blocked by the browser when the device in low-power mode. Check if the device is in low-power mode._
