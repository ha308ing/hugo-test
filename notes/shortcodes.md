# Shortcode Templates

[Hugo Built-in Shortcodes](https://gohugo.io/content-management/shortcodes/)


# Shortcode HTML Template Location

`layouts/shorcodes/<path>/<shortcode-name>.html` is accessed with relative path:
`{{% <path>/<shortcode-name> /%}}`

`layouts/shortcodes/myshortcode.html` will be called:
- `{{< myshortcode />}}`
- `{{% myshortcode /%}}`


# Shorcode Template Lookup Order

1. `/layouts/shortcode/<SCORTCODE>.html`
2. `/themes/<THEME>/layouts/shortcode/<SCORTCODE>.html`


# Shortcode Parameters

- positional
- named
- positional or named ("flexible")

While shortcode template can accept both positional and namded params,
you cannot declare shortcodes in content with a mix of parameter types:
`{{< image src="img.jpg" "alt text" >}}` will return an error


# Access parameters

To access parameters within shortcode
- `{{ .Get 1 }}`
- `{{ .Get "class" }}`

```hugo
{{ with .Get "class" }} class="{{ . }}"{{ end }}
```

`.Get` can also be used to check if a parameter has been provided:
```hugo
{{ if or (.Get "title") (.Get "alt") }}
  alt="
    {{ with .Get "alt" }}
      {{ . }}
    {{ else }}
      {{ .Get "title }}
    {{ end }}
  "
{{ end }}
```

## `.Inner`
The `.Inner` variable is populated with the cotent between the opening and closing shortcodes.

If a closing shortcode is required, you can check the length of `.Inner` as an indicator of its existence.

Any shortcode that refers to `.Inner` must be closed or self-closed.

## `.Params`
\- list parameters passed to shortcode

## `$.Params`
\- parameters passed directly into the shorcode declaration

## `$.Page.Params`
\- page's params (content file (.md) in which the shortcode is declared)

## `.IsNamedParams`
\- checks whether the shortcode declaration uses named parameters and returns boolean value

**image shortcode called**:
`{{< image src="image.png" >}}`

`layouts/shortcodes/image.html`:
```hugo
{{ if .IsNamedParams }}
  <img src="{{ .Get "src" }}" />
{{ else }}
  <img src="{{ .Get 0 }}" />
{{ end }}
```

## `.Page`
\- to access all normal page variables

## `.Parent`
\- in a nested shortcode, to access the parent shortcode context


# Checking for Existence

`.HasShortcode` - check if a specific shortcode is used on a page (in page template)


# Custom Shortcode Examples

## Single-word Example: `year`
`{{< year >}}`

`layouts/shorcodes/year.html`:
`{{ now.Format "2006" }}`

## Single Positional Example: `youtube`
`{{< youtbe abc22def >}}`

`layouts/shorcodes/youtube.html`:
```hugo
<div class="embed video-player">
  <iframe
    class="youtube-player"
    type="text/html"
    width="640"
    height="385"
    src="https://www.youtbe.com/embed/{{ index .Params 0 }}"
    allowfullscreen
    frameborder="0">
  </iframe>
</div>
```

## Single Named Example: `image`

`content-image.md`:
`{{< img src="/media/pic.jpg" title="Pic Title" >}}`

`layouts/shortcode/img.html`:
```hugo
<!-- image -->
<figure {{ with .Get "class"}}class="{{ . }}"{{ end }}>
  {{ with .Get "link" }}
    <a href="{{ . }}">
  {{ end }}
      <img
        src="{{ .Get "src" }}"
        {{ if or ( .Get "alt" ) ( .Get "caption" ) }}
          alt="
            {{ with .Get "alt" }}
              {{ . }}
            {{ else }}
              {{ .Get "caption" }}
            {{end}}
          "
        {{ end }}
      />
  {{ if .Get "link" }}
    </a>
  {{ end }}

  {{ if or (or ( .Get "title" ) ( .Get "caption" ) ) ( .Get "attr" ) }}
    <figcaption>
      {{ if isset .Params "title" }}
        <h4>{{ .Get "title" }}</h4>
      {{ end }}
      {{ if or ( .Get "caption" ) ( .Get "attr" ) }}
        <p>
          {{ .Get "caption" }}
          {{ with .Get "attrlink" }}
            <a href="{{ . }}">
          {{ end }}
            {{ .Get "attr" }}
          {{ if .Get "attrlink" }}
            </a>
          {{ end }}
        </p>
      {{ end }}
    </figcaption>
  {{ end }}
</figure>
<!-- image -->
```

would be rendered as:
`img-output.html`:
```html
<figure>
  <img src="media/pic.jpg" />
  <figcaption>
    <h4>Pic title</h4>
  <figcaption>
</figure>
```

## Single Flexible Example: `vimeo`

`{{< vimeo abc55de >}}`
`{{< vimeo id="abc55de" class="flex-video" >}}`

`layouts/shortcodes/vimeo.html`:
```hugo
{{ if .IsNamedParams }}
  <div class="
    {{ if .Get "class" }}
      {{ .Get "class" }}
    {{ else }}
      vimeo-container
    {{ end }}
  ">
    <iframe src="https://pl.vime.com/video/{{ .Get "id" }}" allowfullscreen></iframe>
  </div>
{{ else }}
  <div class="
    {{ if len .Params | eq 2 }}
      {{ .Get 1 }}
    {{ else }}
      vimeo-container
    {{ end }}
  ">
    <iframe src=".../{{ .Get 0 }}" allowfullscreen></iframe>
  </div>
{{ end }}
```

## Paired Example: `highlight`

`highlight-example.md`:
```md
{{< highlight html >}}
  <html>
    <body> This HTML </body>
  </html>
{{< /highlight >}}
```

`highlight.html`:
```hugo
{{ .Get 0 | highlight .Inner }}
```

## Nested Shortcode: Image Gallery

`layouts/shortcodes/gallery.html`:
```hugo
<div class="{{ .Get "class" }}">
  {{ .Inner }}
<.div>
```

`layouts/shortcodes/img.html`:
```hugo
{{- $src := .Get "src" -}}
{{- with .Parent -}}
  <img src="{{ $src }}" class="{{ .Get "class" }}-image">
{{- else -}}
  <img src="{{ $src }}" >
{{- end -}}
```

calling shortcodes:
```
{{< gallery class="content-gallery" >}}
  {{ img src="pic1" }}
  {{ img src="pic2" }}
{{< /gallery >}}

{{ img src="pic3" }}
```


# Error Handling in ShortCodes

Use `errorf` template func and `.Position` ariable to get useful error messages in shortcodes:
```hugo
{{ with .Get "name" }}
{{ else }}
  {{ errorf "missing value for param 'name': %s" .Position }}
{{ end }}}
```

when the above failes, `ERROR` log:
`ERROR 2006/01/02 05:06:10 missing value for param name: <content file>.md:32:1`


# Inline Shortcodes

Disabled by default, enable in config
`enableInlineShortcodes: true`

Once enabled, you can do in content files:
`{{< time.inline >}}{{ now }}{{< /time.inline >}}` - prints current date and time

Inline shortcode's inner content is parsed and executed as a Go text template
with the same context as a regular shortcode template.
This also means that the current page is accessed via `.Page`,
and there are no "nested inline shortcodes"

To reuse inline shortcode:
`{{< time.inline />}}`
