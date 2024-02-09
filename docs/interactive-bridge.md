# Livery Video Interactive Bridge

[![npm package](https://img.shields.io/npm/v/@liveryvideo/interactive-bridge.svg?logo=npm)](https://www.npmjs.com/package/@liveryvideo/interactive-bridge)
[![conventional CHANGELOG](https://img.shields.io/badge/conventional-CHANGELOG-FE5196.svg?logo=conventionalcommits)](interactive-bridge-changelog.md)
[![tsdocs API](https://img.shields.io/badge/tsdocs-API-3178C6.svg?logo=typescript)](https://tsdocs.dev/docs/@liveryvideo/interactive-bridge/modules.html)
[![Lit Elements](https://img.shields.io/badge/Lit-Elements-324FFF.svg?logo=lit)](https://lit.dev/)
[![license MIT](https://img.shields.io/npm/l/@liveryvideo/interactive-bridge.svg?color=808080&logo=unlicense)](https://cdn.jsdelivr.net/npm/@liveryvideo/interactive-bridge/LICENSE)

Bridge for communicating between a Livery Video Player and the interactive layer page shown within that.

?> Test page for use by Livery Video Players: [interactive-bridge.liveryvideo.com](https://interactive-bridge.liveryvideo.com)

?> Test page with mock player bridge: with [interactive element](https://interactive-bridge.liveryvideo.com/?mock) or [interactive iframe](https://interactive-bridge.liveryvideo.com/?mock=iframe)

# Command Overview
The Livery Bridge facilitates various types of commands for interaction between the Interactive Client and the underlying platform. These commands are categorized into GET, SUBSCRIBE, SET, and ACTION types, supporting a wide array of functionalities.

| Name                | Type       | Description                                                                                   |
|---------------------|------------|-----------------------------------------------------------------------------------------------|
| getAppName          | GET        | Returns the base domain name on web and the package's name on native.                         |
| getCustomerId       | GET        | Returns the customer id as used by the video backend.                                         |
| getEndpointId       | GET        | Returns the end point id of the video backend.                                                |
| getFeatures         | GET        | Returns promise of a registry of features supported by the player under given circumstances.  |
| getLatency          | GET        | Returns the glass-to-glass latency of the video.                                              |
| getLiveryParams     | GET        | Returns the query parameters added to the URL.                                                |
| getPlayback         | GET        | Returns promise of current playback details.                                                  |
| getPlayerVersion    | GET        | Returns the version of the player SDK.                                                        |
| getStreamId         | GET        | Returns the stream id used in the player.                                                     |
| pause               | ACTION     | Pauses playback.                                                                              |
| play                | ACTION     | Attempt to start or resume playback.                                                          |
| reload              | ACTION     | Reloads player.                                                                               |
| seek                | ACTION     | Seek to a specified position in seconds since the start of the video.                         |
| selectQuality       | SET        | Select quality at specified index of subscribeQualities list. -1 sets the player to use ABR.  |
| setControlsDisabled | SET        | Change value to TRUE to disable all default player controls and implement your own instead.   |
| setDisplay          | SET        | Attempt to change display mode to specified value.                                            |
| setMuted            | SET        | Attempt to change muted state to specified value.                                             |
| submitUserFeedback  | SET        | Uses the player’s Sentry API to submit user feedback.                                         |
| subscribeConfig     | SUBSCRIBE  | Returns promise of Livery stream config, can change with server side updates or when streamId is changed. |
| subscribeDisplay    | SUBSCRIBE  | Returns current display mode and future changes to the value.                                 |
| subscribeError      | SUBSCRIBE  | Returns current player error message or undefined and future changes to the value.            |
| subscribeFullscreen | SUBSCRIBE  | Returns TRUE or FALSE indicating if the player is in full screen mode and updates when the value changes. |
| subscribeMode       | SUBSCRIBE  | Returns the type of PlaybackMode and future updates to the value.                             |
| subscribeMuted      | SUBSCRIBE  | Returns TRUE or FALSE indicating if the audio is muted and updates when the value changes.    |
| subscribeOrientation| SUBSCRIBE  | Returns the orientation of the device and updates when the value changes.                     |
| subscribePaused     | SUBSCRIBE  | Returns true if playbackState equals 'ENDED' | 'PAUSED'.                                      |
| subscribePlaybackState | SUBSCRIBE | Returns the PlaybackState of the player and future updates to the value.                      |
| subscribePlaying    | SUBSCRIBE  | Returns TRUE if playbackState equals: FAST_FORWARD, PLAYING, REWIND, SLOW_MO and future updates to the value. |
| subscribeQualities  | SUBSCRIBE  | Returns the available qualities of the video and future updates to the value.                 |
| subscribeQuality    | SUBSCRIBE  | Returns the current quality of the video and future updates to the value.                     |
| subscribeStalled    | SUBSCRIBE  | Returns true if playbackState equals BUFFERING or SEEKING and future updates to the value.    |
| subscribeStreamPhase| SUBSCRIBE  | Returns the stream phase (PRE, LIVE, POST) and future updates to the value.                   |

## Support

This package exports two bundles.

The ES `module` bundle is targetted at modern browsers and tools supporting `ES2019`.

The UMD `main` bundle (as used by jsdelivr [CDN](#cdn)) supports the last 2 major versions of: Chrome, Safari, Edge, Firefox, Android Chrome, iOS Safari and Samsung Internet.

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

Please refer to our [tsdocs API](https://tsdocs.dev/docs/@liveryvideo/interactive-bridge/modules.html) for documentation of the exports of current and past versions of the interactive-bridge package.

**Note:** When using the UMD bundle, the documented exports can be found as properties of `livery` in the global namespace, e.g: `livery.version`.
