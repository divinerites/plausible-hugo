# plausible-hugo : A theme component & Hugo module

According to their web site, *"Plausible Analytics, is a simple, open-source, lightweight (< 1 KB) and privacy-friendly web analytics alternative to Google Analytics"*

And Hugo is *The world’s fastest framework for building websites*.

This Hugo theme component & module is a dead simple integration between [plausible.io](https://www.plausible.io?ref=github-plausible-hugo) and [gohugo.io](https://www.gohugo.io)

## 1 - Usage is simple as 1-2-3

1. Add this `plausible-hugo` component as a theme in your theme section in `config.toml`.
1. Add a `[params.plausible]` section in your `config.toml` file.
1. Call the partial `plausible_head.html` in your own `<head>` section.

**That's it !** It works. Nothing more is needed.

*You also can use it as a versioned [Hugo Module](https://gohugo.io/hugo-modules/) if you prefer and know how to do it*.

### Mininum `config.toml` file

```toml
theme = ["plausible-hugo"]  # Add this theme to your already existing other themes
[params.plausible]
  enable = true  # Whether to enable plausible tracking
  domain = "example.com"  # Plausible "domain" name/id in your dashboard
```

### Example `<head>` html section

```html
<head>
   ...
   {{ partial "plausible_head.html" . }}
   ...
</head>
```

## 2 - Do not track certain pages

You can prevent certain pages from being tracked by adding `plausible_do_not_track: true` in the page Front Matter

```yaml
---
plausible_do_not_track: true
---
```

## 3 - Simple custom goals

If you want to use some custom goals, for each goal, you just have to add a snipplet in a partial named `plausible_js.html` that you have to create in your site `/partials` directory

Then, add a straightforward call to those plausible function/goal where you need it in your gohugo code.

This is an example, if you want a plausible custom goal named `ClickOnTelephoneNumber`.

### Snipplet example for `plausible_js.html`

```js
function ClickOnTelephoneNumber() {
   plausible('ClickOnTelephoneNumber');
}
```

### Example of integration in your html code

```html
<a href="tel:+331234567890"
   onclick="ClickOnTelephoneNumber()">
+331234567890
</a>
```

## 4 - Variable custom goals

### Using variables for your Goal names

For more flexibility, do not forget that you can use any `{{ $variable }}` or `{{ .variable }}` for your Goal name.

For example, generate your goals using `/data/about.yml` or similar frontmatter, you get the idea.

Then you’ll get 2 goals : `telephoneMobileAbout` & `telephoneHomeAbout`

#### A - Example : `/data/about.yml`

```yml
about:
  enable : true
  title : My title
  about_item :
  - title : An other item title
    plausible : Mobile
    phone : "+33 1 23 45 67 89"
    content : lorem ipsum is better than nothing.
  - title : An other item title 2
    plausible : Home
    phone : "+33 9 87 65 43 21"
    content : lorem ipsum is better than nothing 2
```

#### B - Generate the calls in your `/partial/about.html` example

```html
{{- $data := index .Site.Data .Site.Language.Lang }}
{{- if $data.about.about.enable }}
   {{- range $data.about.about.about_item }}
      <!-- sanitize phone number for tel: function -->
      {{ $phone_ok := (replaceRE "(\\s)" "" .phone ) }}
      <a href="tel:{{ .phone_ok }}"
         onclick="telephone{{ .plausible | safeJS }}About()">
         {{ .phone }}
      </a>
  {{- end }}
{{- end }}
```

#### C - Sniplet for `plausible_js.html`

```js
{{- $data := index .Site.Data .Site.Language.Lang }}
{{- if $data.about.about.enable }}
   {{- range $data.about.about.about_item }}
      function telephone{{ .plausible | safeJS }}About() {
          plausible('telephone{{ .plausible | safeJS }}About');
      }
  {{- end }}
{{- end }}
```

## 5 - Plausible custom subdomain

If you [use your own subdomain](https://docs.plausible.io/custom-domain) for plausible.io, you just have to give the url in `custom_js_domain` parameter.

```toml
[params.plausible]
   custom_js_domain = "stats.example.com"  # Whether to serve the script from a custom domain (https://docs.plausible.io/custom-domain) (Optional)
```

## 6 - Write public dashboard information in Web page source

If you made your [dashboard public](https://docs.plausible.io/visibility), *you may want* to write this url in your web page source, so people can find it more easily.

Just add `public_dashboard = true` in your `config.toml` plausible section. **By default this option is set to `false`, so nothing is written by default**.

```toml
[params.plausible]
   public_dashboard = true
```

And this will be written in your HTML source code. It also works for self hosting.

```html
<!-- Plausible Analytics public dashboard URL : https://plausible.io/example.com -->
```

## 7 - Self Hosting

You can define your self hosted domain address in `config.toml`.

This is optional, and `plausible.io` is used if this parameter is unset.

```toml
[params.plausible]
   selfhosted_domain = "myplausible.example.com"  # Self-hosted plausible domain
```

## 8 - Plausible and Content-Security-Policy

Be careful if you have some CSP in your headers, do not forget to **allow plausible domains** you use.

You can add directly the requested domain(s) to your existing `Content-Security-Policy`.

Or use this `plausible_csp.html` partial in your `/layouts/index.headers`to generate for you the correct `_headers`, used by Netlify:

### CSP example for `/layouts/index.headers`

```headers
Content-Security-Policy: [... existing stuff ...] {{ partial "plausible_csp.html" . }}
```

In any case, in the HTML source code, you'll find a comment with the correct domain to add to your CSP.

## 9 - Mode server

When you're in `hugo mode server`, the call to plausible.io javascript is disable, so you can dev without bloating your statistics.

## Credits

- Hugo : [www.gohugo.io](https://www.gohugo.io)
- Plausible : [www.plausible.io](https://www.plausible.io?ref=github-plausible-hugo)
  - Plausible documentation : [docs.plausible.io](https://docs.plausible.io?ref=github-plausible-hugo)
  - Plausible community forum : [plausible.discourse.group](https://plausible.discourse.group?ref=github-plausible-hugo)
