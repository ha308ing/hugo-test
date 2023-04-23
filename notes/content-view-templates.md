# Content View Templates

To render alternative views of content.
useful in list templates:
- render content as summary
- render content as bulleted list


# Create a Content View

create template file ("\<alternative-view-name>.html")
in content type layout directories (e.g. post, project):
`layouts/posts/[single,li,summary].html`:
- single view template (can be in \_default)
- alternative "li" template
- alternative "summary" template

\_default alternative content views are supported:
`layouts/_default/[li,single,summary].html`


# Lookup Order for Content Views

1. `/layouts/<TYPE>/<VIEW>.html`
2. `/layouts/_default/<VIEW>.html`
3. `/themes/<THEME>/layouts/<TYPE>/<VIEW>.html`
4. `/themes/<THEME>/layouts/_default/<VIEW>.html`


# Example: Content View Inside a List

list view template to render summary template
`layouts/_default/list.html`:
```hugo
<main id="main">
  <div>
    <h1 id="title">{{ .Title }}</h1>
    {{ range .Pages }}

      {{/*
        render passed content (from .Pages range)
        with the provided view template ("summary")
      */}}
      {{ .Render "summary" }}

      {{/*
        to change to li template for dotted list
        {{ .Render "li" }}
      */}}
    {{ end }}
  </div>
</main>
```

summary template has entire page object passed
`layouts/_default/summary.html`:
```hugo
<article class="post">
  <header>
    <h2><a href='{{ .Permalink }}'> {{ .Title }}</a> </h2>
    <div class="post-meta">{{ .Date.Format "Mon, Jan 2, 2006" }} - {{ .FuzzyWordCount }} Words</div>
  </header>
  {{ .Summary }}
  <footer>
    <a href='{{ .Permalink }}'><nobr>Read more...</nobr></a>
  </footer>
</article>
```

li template for dotted list:
`layouts/_default/li.html`:
```hugo
<li>
  <a href="{{ .Permalink }}">{{ .Title }}</a>
  <div class="meta">{{ .Date.Format "Mon, Jan 2, 2006" }}</div>
</li>
```
