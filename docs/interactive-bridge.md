# Livery Video Interactive Bridge

[![npm package](https://img.shields.io/npm/v/@liveryvideo/interactive-bridge.svg?logo=npm)](https://www.npmjs.com/package/@liveryvideo/interactive-bridge)
[![conventional CHANGELOG](https://img.shields.io/badge/conventional-CHANGELOG-FE5196.svg?logo=conventionalcommits)](/npm/interactive-bridge/CHANGELOG)
[![tsdoc API](https://img.shields.io/badge/tsdoc-API-3178C6.svg?logo=typescript)](/npm/interactive-bridge/dist/)
[![Lit Elements](https://img.shields.io/badge/Lit-Elements-324FFF.svg?logo=lit)](https://lit.dev/)
[![license MIT](https://img.shields.io/npm/l/@liveryvideo/interactive-bridge.svg?color=808080&logo=unlicense)](https://cdn.jsdelivr.net/npm/@liveryvideo/interactive-bridge/LICENSE)

Bridge for communicating between a Livery Video Player and the interactive layer page shown within that.

?> Test page for use by Livery Video Players: [interactive-bridge.liveryvideo.com](https://interactive-bridge.liveryvideo.com). Or you can test with a mock player bridge and [interactive element](https://interactive-bridge.liveryvideo.com/?mock) or [interactive iframe](https://interactive-bridge.liveryvideo.com/?mock=iframe).

?> Detailed documentation of the exported API is included in the NPM package and can be viewed here: [API](/npm/interactive-bridge/dist/).

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

?> Replace the version (`x.y.z`) above by the version of the SDK that you wish to use. E.g: update to latest stable release periodically. Please see the [CHANGELOG](/npm/interactive-bridge/CHANGELOG) for details.
