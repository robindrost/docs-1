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

The `<livery-interactive>` element can similarly be embedded from URL: `https://embed.liveryvideo.com/interactive/`.

This similarly supports query parameter `v` to specify the version of `@liveryvideo/interactive` to load (defaults to `latest`).

And query parameter `mockBridge` to specify that a `MockPlayerBridge` should be specified as `playerBridge` to the `<livery-interactive>` element.
The value of this parameter specifies the version of `@liveryvideo/interactive-bridge` to load (defaults to `latest`).

E.g: `https://embed.liveryvideo.com/interactive/?v=foo&mockBridge=bar`
