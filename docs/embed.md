# Livery Embed

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
You should be able to embed the [Livery Web SDK](web-sdk.md 'Livery Web SDK') in your page directly just as easily (e.g: using a few lines of HTML).

## Example

[embed](https://embed.liveryvideo.com?id=5ddb98f5e4b0937e6a4507f2 ':include :type=iframe width=100% height=315 frameborder=0 allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen')
