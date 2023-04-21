# `.Site.GetPage`
Combine `Kind` with `where` function in templates to create kind-specific lists of content.
`.Site.GetPage` returns `nil` if no `_index.md` page is found.

The `.GetPage` function looks up an index page of a given `Kind` and `path`:
- `{{ .Site.GetPage <Kind> <path> }}`
- `{{ .Site.GetPage "section" "posts" }}`
- `{{ .Site.GetPage "page" "search" }}`


## Examples
```hugo
  <h1>
    {{ with .Site.GetPage "section" "blog" }}
      {{/*
        if no content/blog/_index.md`
        the template will output the section name ("blog")
        else
        title from front matter in blog/_index.md
      */}}
      {{ .Title }}
    {{ end }}
  </h1>
```
