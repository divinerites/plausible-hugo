# plausible-huogo theme

According to their web site, *"Plausible Analytics, is a simple, open-source, lightweight (< 1 KB) and privacy-friendly web analytics alternative to Google Analytics"*

This Hugo theme is a dead simple integration between plausible.io and gohugo.io

## Usage

- Add this `plausible-hugo` theme component in your theme section in `config.toml` : `theme = ["theme1", ..., "plausible-hugo"]`
- Add a [params.plausible] section in your `config.toml` file.
- Call the partial `plausible_head.html` in your own `<head>` section : `{{ partial "plausible_head.html" . }}`

That's it.

### File `config.toml`

```toml
[params.plausible]
   enable      = true
   subdomain   = false
   analytics   = "put-here-your-plausible-analytics-code"
```

## Custom goals

If you want to use some custom goals, for each goal, you just have to add a snipplet in a new created partial named `plausible_js.html`

Then, add a straightforward call to plausible function/goal where you want in your gohugo code.

### Snipplet example for `plausible_js.html`

```js
function ClickOnTelephoneNumber() {
   plausible('ClickOnTelephoneNumber');
}
```

### Example of integration in your html code

```html
<a href="tel:+331234567890}"
   onclick="ClickOnTelephoneNumber()">
+331234567890
</a>
```

### plausible custom subdomain domain

If you use your own subdomain for plausible.io, you just have to enable the `subdomain = true` parameter.

## Mode server

When you're in `hugo mode server`, the call to plausible.io javascript is disable, so you can dev without bloating your statistics.

## Credits

- Plausible : https://www.plausible.io/
- Plausible documentation : https://docs.plausible.io/
- Plausible community forum : https://plausible.discourse.group
