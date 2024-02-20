# Livery Video Embed

You can embed Livery in your website using an iframe like so:

?> Replace the Livery Demo stream `id` iframe `src` query parameter below by your own.

```html
<iframe
  width="560"
  height="315"
  src="https://embed.livery.live?id=5ddb98f5e4b0937e6a4507f2"
  frameborder="0"
  allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"
  allowfullscreen
></iframe>
```

!> Note: Embedding with an iframe like this will result in fullscreen support being unavailable in some browsers and is generally _not_ recommended.
You should be able to embed the [Livery Video Web Player](web-player.md) in your page directly just as easily (e.g: using a few lines of HTML).

In addition to the `id` query parameter you can also specify a `v` query parameter to specify the version of `@liveryvideo/player` to load (defaults to `latest`).

E.g: `https://embed.livery.live?id=5ddb98f5e4b0937e6a4507f2&v=beta`.

## Example

[embed](https://embed.livery.live?id=5ddb98f5e4b0937e6a4507f2 ':include :type=iframe width=100% height=315 frameborder=0 allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen')

## Interactive

Our Livery Interactive element can also be embedded from URL: `https://embed.livery.live/interactive/`.

This supports the following query parameters:

| Name       | Description                                                                                       |
| ---------- | ------------------------------------------------------------------------------------------------- |
| `bridge`   | Version of `@liveryvideo/interactive-bridge` to load for `test` and/or `mock`.                    |
| `mock`     | Use a `@liveryvideo/interactive-bridge` `MockPlayerBridge` as interactive element `playerBridge`. |
| `region`   | Interactive element server `region` attribute value to specify.                                   |
| `tenantid` | Interactive element server `tenantid` attribute value to specify.                                 |
| `test`     | Use `@liveryvideo/interactive-bridge` `<livery-bridge-interactive>` as interactive element.       |
| `v`        | Version of `@liveryvideo/interactive` to load and use `<livery-interactive>` element from.        |

The `bridge` and `v` query parameters are NPM package version selector strings that default to: `latest`.

Parameter examples:

- [?v=1.0.0&region=eu&tenantid=123abc](https://embed.livery.live/interactive/?v=1.0.0&region=eu&tenantid=123abc) -- E.g. for use by native players in a WebView or third party players in an iframe
- [?test&bridge=1.9.0](https://embed.livery.live/interactive/?test&bridge=1.9.0) -- For native player devs to test with specified version of interactive bridge
- [?mock&v=1.0.0](https://embed.livery.live/interactive/?mock&v=1.0.0) -- For interactive devs to test specified version of their element without a player
- [?mock&test&bridge=1.9.0&region=eu&tenantid=123abc](https://embed.livery.live/interactive/?mock&test&bridge=1.9.0&region=eu&tenantid=123abc) -- For bridge devs to test a specified version
