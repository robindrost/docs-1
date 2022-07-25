# Livery Video Interactive Bridge

[![GitHub last commit](https://img.shields.io/github/last-commit/liveryvideo/interactive-bridge)](https://github.com/liveryvideo/interactive-bridge)
[![NPM package](https://img.shields.io/npm/v/@liveryvideo/interactive-bridge)](https://www.npmjs.com/package/@liveryvideo/interactive-bridge)
[![Changelog](https://img.shields.io/badge/docs-CHANGELOG-blue)](interactive-bridge-changelog.md)
[![License](https://img.shields.io/npm/l/@liveryvideo/interactive-bridge)](https://cdn.jsdelivr.net/npm/@liveryvideo/interactive-bridge/LICENSE)
[![Build with Lit](https://img.shields.io/badge/build%20with-Lit-blue.svg)](https://lit.dev/)

Bridge for communicating between a Livery Video Player and the interactive layer page shown within that.

?> Demo page for use by Livery Video Player: [interactive-bridge.liveryvideo.com](https://interactive-bridge.liveryvideo.com)

?> Demo page with mock player bridge: [interactive-bridge.liveryvideo.com/mock.html](https://interactive-bridge.liveryvideo.com/mock.html)

## Support

This package exports two bundles.

The ES `module` bundle is targetted at modern browsers and tools supporting `ES2019`.

The UMD `main` bundle (as used by jsdelivr [CDN](#cdn)) supports iOS/iPadOS Safari v12 and up and the last 2 major versions of: Chrome, Safari, Edge, Firefox, Android Chrome and Samsung Internet.

## Installation

### NPM

Install using NPM:

```bash
npm install @liveryvideo/interactive-bridge
```

### CDN

Or load from [jsdelivr](https://jsdelivr.com):

```html
<script src="https://cdn.jsdelivr.net/npm/@liveryvideo/interactive-bridge@x.y.z"></script>
```

?> Replace the version (`x.y.z`) above by the version of the SDK that you wish to use. E.g: update to latest stable release periodically. Please see the [CHANGELOG](interactive-bridge-changelog.md) for details.

## Exports

When using the UMD bundle, these can be found as properties of `livery` in the global namespace, e.g: `livery.version`.

### version

String property specifying version of Livery Video Interactive Bridge.

### InteractiveBridge

Can be used on Livery interactive layer pages to communicate with the surrounding Livery Player.

#### Usage

```JS
import { InteractiveBridge } from '@liveryvideo/interactive-bridge';

const bridge = new InteractiveBridge('*');

bridge.getLatency().then(latency => window.alert(`latency: ${latency}`));
```

**Note:** To prevent [cross site security issues](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage#security_concerns) replace the `'*'` origin above with the origin of the page that the Livery Player containing this interactive layer page will be on.

#### Methods

| Method                                      | Description                                                                                                                                                                                                       |
| ------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `constructor(targetOrigin)`                 | Constructs InteractiveBridge with `window.parent` as target window and with specified target origin.                                                                                                              |
| `getAppName()`                              | Returns promise of LiveryPlayer application name.                                                                                                                                                                 |
| `getCustomerId()`                           | Returns promise of LiveryPlayer customer id.                                                                                                                                                                      |
| `getEndpointId()`                           | Returns promise of LiveryPlayer Pinpoint analytics endpoint id.                                                                                                                                                   |
| `getLatency()`                              | Returns promise of current LiveryPlayer latency in seconds.                                                                                                                                                       |
| `getPlayerVersion()`                        | Returns promise of LiveryPlayer version.                                                                                                                                                                          |
| `getStreamId()`                             | Returns promise of LiveryPlayer stream id.                                                                                                                                                                        |
| `registerInteractiveCommand(name, handler)` | Register `handler` function to be called with `arg` and `listener` when `sendInteractiveCommand()` is called on LiveryPlayer with matching `name`.                                                                |
| `sendPlayerCommand(name, arg?, listener?)`  | Returns promise of value returned by LiveryPlayer custom command handler with matching `name` that is passed `arg`. Any `handler` `listener` calls will subsequently also be bridged to this `listener` callback. |
| `subscribeFullscreen(listener)`             | Returns promise of current LiveryPlayer fullscreen state and calls back `listener` with any subsequent state changes.                                                                                             |
| `subscribeOrientation(listener)`            | Returns promise of current LiveryPlayer window orientation (`'landscape' \| 'portrait'`) and calls back `listener` with any subsequent orientations.                                                              |
| `subscribeQuality(listener)`                | Returns promise of current LiveryPlayer playback quality and calls back `listener` with any subsequent quality changes.                                                                                           |
| `subscribeStreamPhase(listener)`            | Returns promise of current LiveryPlayer stream phase (`'PRE' \| 'LIVE' \| 'POST'`) and calls back `listener` with any subsequent phases.                                                                          |
| `unregisterInteractiveCommand(name)`        | Unregister custom interactive command by name.                                                                                                                                                                    |

### LiveryBridgeLog

Element defined as `<livery-bridge-log>` which logs LiveryBridge and other window messages posted to this window.

#### Usage

```html
<livery-bridge-log></livery-bridge-log>
```

#### Attributes

| Attribute     | Property      | Type     | Default | Description                            |
| ------------- | ------------- | -------- | ------- | -------------------------------------- |
| `maxmessages` | `maxMessages` | `number` | `10`    | Maximum number of messages to display. |
