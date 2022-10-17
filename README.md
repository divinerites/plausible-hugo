# plausible-hugo : A theme component & Hugo module

According to their web site, *"Plausible Analytics, is a simple, open-source, lightweight (< 1 KB) and privacy-friendly web analytics alternative to Google Analytics"*

And Hugo is *The world’s fastest framework for building websites*.

This Hugo theme component & module is a dead simple integration between [plausible.io](https://www.plausible.io?ref=github-plausible-hugo) and [gohugo.io](https://www.gohugo.io)

---

![GitHub version](https://img.shields.io/github/v/tag/divinerites/plausible-hugo?label=Version)
![License](https://img.shields.io/github/license/divinerites/plausible-hugo)
![Last commit](https://img.shields.io/github/last-commit/divinerites/plausible-hugo)

Please, consider **leaving a star on Github if you like it**. ![Github Stars](https://img.shields.io/github/stars/divinerites/plausible-hugo?style=social)

---

## Usage is simple as 1-2-3

1. Add this `plausible-hugo` component as a theme in your theme section in `config.toml`.
1. Add a `[params.plausible]` section in your `config.toml` file.
1. Call the partial `plausible_head.html` in your own `<head>` section.

> ### ***That's it ! It works***. Nothing more is needed

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

You can have a look at [the end of this file](#configuration-example-hugo-module-option) for a minimal working example as a hugo module.

## Hugo Module

You also can use it as a versioned [Hugo Module](https://gohugo.io/hugo-modules/) if you prefer *and know how to do it*.

**We strongly advise for this method**, as it will be simple and transparent to keep up with our version updates.

In this case, instead of declaring this as a theme, just add it as a hugo module.

```toml
[module]
    [[module.imports]]
        path = "github.com/divinerites/plausible-hugo"
```

# Using Plausible goals

## 1 - Simple custom goals

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

## 2 -  Custom goals when entering a page

You can have a use case where you want a custom goal setup when you enter a certain page. So you can have more granularity than the classic "page goals".

Just add a front matter parameter `plausible_custom_goal` in this page.

Each time you enter the page, your custom goal is reached.

```yaml
---
plausible_custom_goal : "MySpecialCustomGoal"
---
```

## 3 - Variable custom goals

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

## 4 - Outbound Link custom goal

If you want to use the [Outbound Link](https://docs.plausible.io/outbound-link-click-tracking/) custom goal,
just add the parameter `outbound_link` to your `params.plausible` section.

```toml
[params.plausible]
  outbound_link = true
```

## 5 - File downloads tracking custom goal

If you want to use the [File downloads tracking](https://plausible.io/docs/file-downloads-tracking/) custom goal,
just add the parameter `file_downloads` to your `params.plausible` section.

```toml
[params.plausible]
  file_downloads = true
```

### 5.1 - Track a different file type

If you want to track different file types like `.sh` and `.run`, just add the parameter `file_downloads_types` to your `params.plausible` section.

```toml
[params.plausible]
  file_downloads_types = "sh,run"
```

**Using the `file_downloads_types` parameter will override the default list and only your custom file type downloads will be tracked.**

## 6 - 404 custom goal

We take care of the 404 pages for you.

You only have to [configure the goal in your Plausible settings](https://plausible.io/blog/track-404-errors) for them to show up on your dashboard : *Select `Custom event` as the goal trigger and enter this exact name: `404`*.

# Other options

## 1 - Do not track certain pages

You can prevent certain pages from being tracked by adding `plausible_do_not_track: true` in the page Front Matter

```yaml
---
plausible_do_not_track: true
---
```

## 2 - Proxy for analytics and manage Adblockers

Some adblockers/browsers block every tracking script, even privacy-first analytics like plausible.io

You can deal with this by [proxying the script](https://plausible.io/docs/proxy/introduction).

If you are hosting on Netlify, we have done the [main setup](https://plausible.io/docs/proxy/guides/netlify) for you.

1 - Set `proxy_netlify` on your `config.toml`.

```toml
[params.plausible]
   proxy_netlify = true
```

2 - Add the partial `plausible_redirects_netlify.html` in your `index.redir` file. So your final generated `_redirects` will take care of this proxying.

```html
# Netlify redirects from aliases
{{ range $p := site.Pages -}}
{{ range .Aliases }}
{{  . | printf "%-35s" }} {{ $p.RelPermalink }} 301!
{{ end -}}
{{- end -}}

{{ partial "plausible_redirects_netlify.html" . }}

```

3 - If you do not already use the `index.redir` system, you just can create a `_redirects` file in your `/static` folder and add those 2 lines below in this file.

```txt
/misc/js/script.js https://plausible.io/js/plausible.js 200
/misc/api/event https://plausible.io/api/event 200
```

If you want outbound-links, use this line instead:

```txt
/misc/js/script.js https://plausible.io/js/plausible.outbound-links.js 200
```

## 2b - [DEPRECATED] Plausible custom subdomain

Using CNAME custom domains are now in legacy mode. **Please use the proxy method instead**.

*If you [use your own subdomain](https://docs.plausible.io/custom-domain) for plausible.io, you just have to give the url in `custom_js_domain` parameter. This is optional.*

```toml
[params.plausible]
   custom_js_domain = "stats.example.com"
```

Nota : Since v1.10.0 you will receive a console warning when running hugo if you have this parameter.

## 3 - Embed your Plausible.io dashboard in your Hugo site

You can embed your Plausible stats dashboard into on your hugo website using an iFrame. This is useful in case you want to showcase your stats.

First :

- you need to generate a [**shared link** in your own Plausible dashboard](https://plausible.io/docs/embed-dashboard).
- Do not give a theme or background color. You can do this with Hugo params.
- For example it will generate `https://plausible.io/share/yourdomain?auth=AZE1234RTYUOP67`.

Then, just put this value in a `.plausible.dash_link` parameter.

```toml
[params.plausible]
  dash_link = "https://plausible.io/share/yourdomain?auth=AZE1234RTYUOP67"
```

You also can add 2 parameter if you want, but this is optional  :

- the theme you want (`light` / `dark` / `system`). Default is `light`.
- the custom background color.
  - Tip : You can set the background color as `transparent`.

```toml
[params.plausible]
  dash_theme = "dark"
  dash_bgcolor = "#880011"
```

Then just add this partial where you want your embeded Plausible dashboard.

```html
   ...
   {{ partial "plausible_dashboard.html" .}}
   ...
```

### Activating the embed dashboard

This embed dashboard feature **do not rely on `.plausible.enable`** variable.

So it will be shown if you simply add the `plausible_dashboard.html` template. Whatever is `plausible.enable`

In this case, for manually disabling the dashboard you can set `dash_disable` variable to `true`.

```toml
[params.plausible]
  dash_disable = true
```

## 4 - Write public dashboard information in Web page source

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

## 5 - Self Hosting

You can define your self hosted domain address in `config.toml`.

This is optional, and `plausible.io` is used if this parameter is unset.

```toml
[params.plausible]
   selfhosted_domain = "myplausible.example.com"  # Self-hosted plausible domain
```

## 6 - Plausible and Content-Security-Policy

Be careful if you have some CSP in your headers, do not forget to **allow plausible domains** you use.

You can add directly the requested domain(s) to your existing `Content-Security-Policy`.

Or use this `plausible_csp.html` partial in your `/layouts/index.headers`to generate for you the correct `_headers`, used by Netlify:

### CSP example for `/layouts/index.headers`

```headers
Content-Security-Policy: [... existing stuff ...] {{ partial "plausible_csp.html" . }}
```

In any case, in the HTML source code, you'll find a comment with the correct domain to add to your CSP.

# Developping locally with plausible-hugo

## 1 - Mode server

When you're in `hugo mode server`, the call to plausible.io javascript is disable, so you can dev without bloating your statistics.

## 2 - Debug mode

When in mode server you could really need to test your code in real situation.

In this case, just add `debug = true` in your `[params.plausible]`.
The call to plausible.io javascript will be enable, so you can have everything working like in production.

```toml
[params.plausible]
   debug = true  # debug mode
```

# Various

## 1 - Check variables & module versions

We perform an automatic check if the right mandatory variables are fitting the right version.

If we found a problem, we abort your build with a clear message.

You will also receive warning message if you are using deprecated features.

So it helps you updating your `config.toml` in case we introduced some breaking changes in variables names between versions. *It is rare, but it happened already*.

## 2 - Giving a star on github.com

By default we put a console friendly reminder to consider giving a star on github if you like this module.

Obviously you can disable this message, just by adding `gitstar = false` in your `params.plausible`.

```toml
[params.plausible]
   gitstar = false
```

## 3 - Credits

- Hugo : [www.gohugo.io](https://www.gohugo.io)
- Plausible : [www.plausible.io](https://www.plausible.io?ref=github-plausible-hugo)
  - Plausible documentation : [docs.plausible.io](https://docs.plausible.io?ref=github-plausible-hugo)

# Configuration example *(hugo module option)*

## config.toml

```toml
[module]
   [[module.imports]]
   path = "github.com/divinerites/plausible-hugo"

[plausible]
enable = true
domain = "my-domain-id"
outbound_link = true
file_downloads = true
debug = false
gitstar = false
```

## index.html

```html
<!DOCTYPE html>
<html>
<head>
   ...
   {{ partial "plausible_head.html" . }}
   ...
</head>
</html>
```
