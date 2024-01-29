# Livery Video Web Player

[![npm package](https://img.shields.io/npm/v/@liveryvideo/player.svg?logo=npm)](https://www.npmjs.com/package/@liveryvideo/player)
[![conventional CHANGELOG](https://img.shields.io/badge/conventional-CHANGELOG-FE5196.svg?logo=conventionalcommits)](web-player-changelog.md)
[![tsdocs API](https://img.shields.io/badge/tsdocs-API-3178C6.svg?logo=typescript)](https://tsdocs.dev/docs/@liveryvideo/player/modules.html)
[![Lit built with](https://img.shields.io/badge/Lit-Elements-324FFF.svg?logo=lit)](https://lit.dev/)
[![license MIT](https://img.shields.io/npm/l/@liveryvideo/player.svg?color=808080&logo=unlicense)](https://cdn.jsdelivr.net/npm/@liveryvideo/player/LICENSE)

Livery video player for use in web browsers.

?> Demo can be found at: [demo.liveryvideo.com](https://demo.liveryvideo.com) ([source](https://github.com/liveryvideo/demo-web)).

Please refer to the [Livery Management Portal](https://video-encoder-director.playtotv.com/) for stream configuration options.

## Basic Usage

If you're not bundling your own JS you can just use an HTML snippet like this:

```html
<script src="https://cdn.jsdelivr.net/npm/@liveryvideo/player@x.y.z"></script>
<livery-player streamid="5ddb98f5e4b0937e6a4507f2"></livery-player>
```

?> Replace the player version and stream id by your own, see: [CDN](#cdn), [HTML](#html) and [CSS](#css) for details below.

?> If this does not work you can fall back to embedding the player in an iframe instead, see: [Livery Video Embed](embed.md).

## Support

This package exports two bundles.

The ES `module` bundle is targetted at modern browsers and tools supporting `ES2019`.

The UMD `main` bundle (as used by jsdelivr [CDN](#cdn)) supports the last 2 major versions of: Chrome, Safari, Edge, Firefox, Android Chrome, iOS Safari and Samsung Internet.

## Installation

### CDN

The player can be loaded from [jsdelivr](https://jsdelivr.com):

```html
<script src="https://cdn.jsdelivr.net/npm/@liveryvideo/player@x.y.z"></script>
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

Please refer to our [tsdocs API](https://tsdocs.dev/docs/@liveryvideo/player/modules.html) for documentation of the exports of current and past versions of the web player package.

**Note:** When using the UMD bundle, the documented exports can be found as properties of `livery` in the global namespace, e.g: `livery.version`.

## Integrations

### Next.js

Next.js makes use of server side rendering, which the Livery Web SDK does not currently support. However, users of Next.js can still use the SDK by making use of Next.js’s support for [dynamic imports](https://nextjs.org/docs/advanced-features/dynamic-import) inside a [React Effect Hook](https://reactjs.org/docs/hooks-effect.html) or in a [React class component's `componentDidMount` lifecycle method](https://reactjs.org/docs/react-component.html#componentdidmount). When used in combination, this ensures the module being imported is evaluated at runtime on the client.

Note that many of the examples that Next.js provides use a statement of the form `dynamic( import(...) )`. The `dynamic()` function is used for importing React components and should not be used for importing `@liveryvideo/player`. The code below illustrates basic usage with Next.js.

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

## Q&A

**Why does the stream start muted on some devices?**

_With chrome://media-engagement/ you can check the ranking of the page and which can determine whether it is allowed to start unmuted. When “is high” is YES that site is allowed to autoplay unmuted._

**Autoplay does not work on mobile?**

_The autoplay feature can be blocked by the browser when the device is in low-power mode. Check if the device is in low-power mode or if you've blocked autoplay for this page or altogether._
