# Livery Video Interactive Bridge

[![npm package](https://img.shields.io/npm/v/@liveryvideo/interactive-bridge.svg?logo=npm)](https://www.npmjs.com/package/@liveryvideo/interactive-bridge)
[![conventional CHANGELOG](https://img.shields.io/badge/conventional-CHANGELOG-FE5196.svg?logo=conventionalcommits)](interactive-bridge-changelog.md)
[![tsdocs API](https://img.shields.io/badge/tsdocs-API-3178C6.svg?logo=typescript)](https://tsdocs.dev/docs/@liveryvideo/interactive-bridge/modules.html)
[![Lit Elements](https://img.shields.io/badge/Lit-Elements-324FFF.svg?logo=lit)](https://lit.dev/)
[![license MIT](https://img.shields.io/npm/l/@liveryvideo/interactive-bridge.svg?color=808080&logo=unlicense)](https://cdn.jsdelivr.net/npm/@liveryvideo/interactive-bridge/LICENSE)

Bridge for communicating between a Livery Video Player and the interactive layer page shown within that.

?> Test page for use by Livery Video Players: [interactive-bridge.liveryvideo.com](https://interactive-bridge.liveryvideo.com)

?> Test page with mock player bridge: with [interactive element](https://interactive-bridge.liveryvideo.com/?mock) or [interactive iframe](https://interactive-bridge.liveryvideo.com/?mock=iframe)

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
