# Livery Video Embed

You can embed Livery in your website using an iframe like so:

?> Replace the Livery Demo stream `id` iframe `src` query parameter below by your own.

```html
<iframe
  width="560"
  height="315"
  src="https://embed.liveryvideo.com?id=5ddb98f5e4b0937e6a4507f2"
  frameborder="0"
  allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"
  allowfullscreen
></iframe>
```

!> Note: Embedding with an iframe like this will result in fullscreen support being unavailable in some browsers and is generally _not_ recommended.
You should be able to embed the [Livery Video Web Player](web-player.md) in your page directly just as easily (e.g: using a few lines of HTML).

In addition to the `id` query parameter you can also specify a `v` query parameter to specify the version of `@liveryvideo/player` to load (defaults to `latest`).

E.g: `https://embed.liveryvideo.com?id=5ddb98f5e4b0937e6a4507f2&v=beta`.

## Example

[embed](https://embed.liveryvideo.com?id=5ddb98f5e4b0937e6a4507f2 ':include :type=iframe width=100% height=315 frameborder=0 allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen')

## Interactive

Our Livery Interactive element can also be embedded from URL: `https://embed.liveryvideo.com/interactive/`.

This similarly supports query parameter `v` to specify the version of `@liveryvideo/interactive` to load (defaults to `latest`).

Or query parameter `test` to specify that a `interactive-bridge` `<livery-bridge-interactive>` element should be used as the interactive element instead to test the bridge.

And query parameter `mock` to specify that a `interactive-bridge` `MockPlayerBridge` should be specified as `playerBridge` to the interactive element.

And finally query parametr `bridge` to specify the version (defaults to `latest`) of `@liveryvideo/interactive-bridge` to load for `test` and/or `mock`.

E.g: `https://embed.liveryvideo.com/interactive/?v=1.2.3&bridge=4.5.6&test&mock`
