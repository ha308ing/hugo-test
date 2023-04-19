# Lists Template

Hugo lists render the content based on metadata provided in front matter.

```hugo
  {{ define "main" }}
  <main>
    <article>
      <header>
        {{/* since no _index.md .Title is section name */}}
        <h1>{{ .Title }}</h1>
      </header>
      {{/* .Content pulls from markdown content of corresponding _index.md */}}
      {{/* if no _index.md - no content */}}
      {{ .Content }}
    </article>
    <ul>
      {{/* Ranges through content/quote/*.md */}}
      {{ range .Pages }}
        <li>
          <a href="{{ .Permalink }}">{{ .Date.Format "2006-01-02" }} | {{ .Title }}</a>
        </li>
      {{ end }}
    </ul>
  </main>
  {{ end }}
```

## Examples

```hugo
{{/*

  {{/*
    Section Template
    layouts/section/posts.html
  */}}

  {{ partial "header.html" . }}
  {{ partial ""subheader.html" . }}

  <main>
    <div>
      <h1>{{ .Title }}</h1>
      <ul>
        {{/* Renders the li.html content view for each content/posts/*.md */}}
        {{ range .Pages }}
          {{ .Render "li" }}
        {{ end }}
      </ul>
    </div>
  </main>

  {{ partial "footer.html" }}

*/}}
```

```hugo
{{/*

  {{/*
    Taxonomy Template
    layouts/_default/taxonomy.html
  */}}

  {{ define "main" }}
    <main>
      <div>
        <h1>{{ .Title }}</h1>
        {{/*
          ranges through each of the content files associated with a particular taxonomy term
          and renders the summary.html content view
        */}}
        {{ range .Pages }}
          {{ .Render "summary" }}
        {{ end }}
      </div>
    </main>
  {{ end }}

*/}}
```


# Order Content

- default: Weight > Date > LinkTitle > FilePath `{{ range .Pages }}`
- by weight `{{ range .Pages.ByWeight }}`
- by date (according to the "date" field" in front matter:
  `{{ range .Pages.ByDate }}`
- by publish date `{{ range .Pages.ByPublishDate }}`
- by expiration date `{{ range .Pages.ByExpiryDate }}`
- by last modified date `{{ range .Pages.ByLastmod }}`
- by length `{{ range .Pages.ByLength }}`
- by title `{{ range .Pages.ByTitle }}`
- by link title `{{ range .Pages.ByLinkTitle }}`
- by parameter (if no param - render in the end)
  - `{{ range (.Pages.ByParam "rating") }}`
  - `{{ range (.Pages.ByParam "author.last_name") }}` - for nested field

## Reverse Order

Add `.Reverse` method:
`{{ range .Pages.ByDate.Reverse }}`


# Group Content

Group listed pages by Section, Type, Date, etc.
Grouping returns a `{{ .Key }}` and a slice of pages.

- by page field (*layouts/partials/by-page-field.html*)
  ```hugo
  {{/*
    Groups content according to content section.
    The ".Key" in this instance will be the section's title.

    {{ range .Pages.GroupBy "Section" }}

      {{/*

        to access section-page-title from _index.md use .GetPage function

        checks for existence of _index.md for a section;
        if availabel, pulls from "title" in front matter
        {{ with $.Site.GetPage "section" .Key }}
          <h3>{{ .Title }}</h3>
        {{ else }}
          <h3>{{ .Key | title }}</h3>
        {{ end }}

      */}}

      <h3>{{ .Key }}</h3>

      <ul>
        {{ range .Pages }}
          <li>
            <a href="{{ .Permalink }}">{{ .Title }}</a>

            {{/*
            */}}

            <div class="meta">{{ .Date.Format "Mon, Jan 2, 2006" }}</div>
          </li>
        {{ end }}
      </ul>
    {{ end }}
  */}}
  ```
- by date (*layouts/partials/by-page-date.html*)
  `GroupByDate` accepts the same time layouts as in `time.Format`
  and the `.Key` in the result will be localized for the current language

  `{{ range .Pages.GroupByDate "2006-01" }}`
- by publish date (*layouts/partials/by-page-publish-date.html*)
  `{{ range .Pages.GroupByPublishDate "2006-01" }}`
- by lastmod (*layouts/partials/by-page-lastmod.html*)
  `{{ range .Pages.GroupByLastmod "2006-01" }}`
- by expiry date (*layouts/partials/by-page-expiry-date.html*)
  `{{ range .Pages.GroupByExpiryDate "2006-01" }}`
- by page parameter
  `{{ range .Pages.GroupByParam "param_key" }}`
- by page parameter in date format
  `{{ range .Pages.GroupByParamDate "param_key" "2006-01" }}`

## Reverse Key Order
1. Adding the Reverse Method:
  - `{{ range (.Pages.GroupBy "Section").Reverse }}`
  - `{{ range (.Pages.GroupByDate "2006-01").Reverse }}`
2. Providing the Alternate Direction
  - `{{ range .Pages.groupBy "Section" "desc" }}`
  - `{{ range .Pages.GroupByDate "2006-01" "asc" }}`

## Order Within Groups
Example:
1. Content is grouped by month according to the `date` field in front matter
2. Groups are listed in ascending order
3. Pages within each respective group are ordered alphabetically according to the `title`

*layouts/partials/by-group-by-page.html*:
```hugo
{{ range .Pages.GroupByDate "2006-01" "asc" }}
  <h3>{{ .Key }}</h3>
  <ul>
    {{ range .Pages.ByTitle }}
      <li>
        <a href="{{ .Permalink }}">{{ .Title }}</a>
        <div class="meta">{{ .Date.Format "Mon, Jan 2, 2006" }}</div>
      </li>
    {{ end }}
  </ul>
{{ end }}
```

# Filtering and Limiting Lists
To list a subset of the available content
use `where` and `first` functions.
