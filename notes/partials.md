# Partial Template Lookup Order

1. `layouts/partials/*<PARTIALNAME>.html`
2. `themes/<THEME>/layouts/partials/*<PARTIALNAME>.html`

Multiple subdirectories within partials:
`layouts/partials/head/[favicons, metadata, prerender, twitter].html`


# Call Partial

`{{ partial "<PATH>/<PARTIAL>.html" . }}`


# Variable Scoping

Parials are isolated and has no access to the outer scope.
In partials `$.Var` is equivalent to `.Var`.

Only passed context is accessible.


# Returning a Value from a Partial

include `return` statement at the end

## Example: Get Featured
`layouts/partials/GetFeatured.html`:
```hugo
  {{ return first . (where site.RegularPages "Params.featured" true) }}
```

`layouts/index.html`:
```hugo
  {{ range partial "GetFeatured.html" 5 }}
    {{/* ... */}}
  {{ end }}
```

## Example: Get Image
`layouts/partials/GetImage.html`:
```hugo
  {{ $image := $false }}
  {{ with .Params.gallery }}
    {{ $image = index . 0 }}
  {{ end }}
  {{ with .Params.image }}
    {{ $image = . }}
  {{ end }}
  {{ return $image }}
```

`layouts/_default/single.html`:
```hugo
  {{ with partial "GetImage.html" . }}
    {{/* ... */}}
  {{ end }}
```


# Inline Partials

Define partials inline in template.
Template namespace is global,
so ensure that the names are unique to avoid conflicts.

```hugo
Value: {{ partial "my-inline-partial.html" }}

{{ define "partials/my-inline-partial.html" }}
  {{ $value := 32 }}
  {{ return $value }}
{{ end }}
```


# Cached Partials

`{{ partialCached "footer.html" . .Section }} - only render partial once per section
`{{ partialCached "footer.html" . .Params.country .Params.province }}` - additional parameters for cached variant (paramters are not passed to partial - used to create a unique cache key)


# Example `header.html`

see base templates and blocks

`layouts/partials/header.html`:
```hugo
<!DOCTYPE html>
<html
  class="no-js"
  lang="en-US"
  prefix="og: http://ogp.me/ns# fb: http://ogp.me/ns/fb#"
>
  <head>
    <meta charset="utf-8">

    {{ partial "meta.html" }}

    <base href="{{ .SiteBaseURL }}">
    <title> {{ .Title }}</title>
    <link rel="canonical" href="{{ .Permalink }}">
    {{ if .RSSLink }}
      <link
        href="{{ .RSSLink }}"
        rel="alternate"
        type="application/rss+xml"
        title="{{ .Title }}"
      />
    {{ end }}

    {{ partial "head_includes.html" . }}
  </head>
```


# Example `footer.html`

`layouts/partials/footer.html`:
```hugo
  <footer>
    <div>
      <p>
        &copy;
        <a href="#">Copyright</a>
      </p>
    </div>
  </footer>
```
