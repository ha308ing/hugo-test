# Taxonomy Templates
Taxonomy - user-defined grouping of content.
Taxonomies demonstrates logical relationships between content.

includes:
- taxonomy list pages
- taxonomy terms pages
- taxonomies in single page templates

# Taxonomy Methods
A Taxonomy is a `map[string]WeightedPages`

- `.Get(term)` - returns the WeightedPages for a term
- `.Count(term)` - number of pieces of content assigned to this term
- `.Alphabetical` - returns an OrderedTaxonomy (slice) ordered by Term
- `.ByCount` - returns an OrderedTaxonomy (slice) ordered by number of entries
- `.Reverse` - returns an OrderedTaxonomy in reverse order. Must be used with an OrderedTaxonomy.

## OrderedTaxonomy
Since Maps are unordered, and OrderedTaxonomy is a special structure that has a defined order.
Each element of the slice has:

- `.Term`
- `.WeightedPages` - a slice of WeightedPages
- `.Count`
- `.Pages` - all pages assigned to this term (all lists methods are available to this)

## WeightedPages
is a slice of WeightedPage
- `.Count(term)`
- `.Pages`

# Custom metadata in Taxonomy Terms Templates
to display custom metadata for taxonomy term,
create a page for that term:
`/content/<TAXONOMY>/<TERM>/_index.md`
and add metadata in its front matter

# Examples

## taxonomy content template:
```hugo
<ul>
  {{ range .Pages }}
    <li>
      <a href="{{ .Permalink }}}">{{ .Title }}</a>
      {{ .Params.wiki }}
    </li>
  {{ end }}
</ul>
```

## Order Taxonomies
```hugo
{{/* list of terms and associated content counts */}}
<ul>
  {{ range .Data.Terms.Alphabetical }}
  {{/* range Data.Terms.ByCount */}}
    <li>
      <a href="{{ .Page.Permalink }}">{{ .Page.Title }}</a>
      {{ .Count }}
    </li>
  {{ end }}
</ul>
```

# Order Content within Taxonomies
1) `weight`
2) `date`

Default weight is `0` - does not have weight
Zero-weighted content appers after negative-weighted

# Assign Weight
Content can be assigned weight for each taxonomy that it's assign to
The convention is `<TAXONOMY-NAME>_weight`

`content/example.md`:
```yaml
---
categories:
- d
categories_weight: 44
tags:
- a
- b
- c
tags_weight: 22
---
```

When rendering category d page, weight is 44
When rendering tag pages a,b,c - 22

# Required Tamplates to Use Taxonomies
- list template - used to generate all the **automatically created taxonomy pages**
- taxonomy template - used to generate the **list of terms** for a given template (taxonomy?)

# Common Ways to Display the Data in Taxonomies
in addition to the automatic taxomoy pages created by Hugo using the list templates

1. for a given piece of content, you can list the terms attached
2. for a given piece of content, you can list other content with the same term
3. you can list all terms for a taxonomy
4. you can list all taxonomies (with their terms)

# Display a Singe Piece of Content's Taxonomies
Display all the taxonomies that piece of content is assigned to

taxonomies are deined in front matter: `<TAXONOMYNAME>: a,b,c`
to get taxonomies assigned to content: `.Params.<TAXONOMYPLURAL>` (`.Params.<TAXONOMYNAME>`?)

## List Tags in a Single Page Template
```hugo
<ul>
{{ range (.GetTerms "tags" ) }}
  <li><a href="{{ .Permalink }}">{{ .LinkTitle }}</a></li>
{{ end }}
</ul>
```

if you want to list taxonomies inline,
you'll have to take care of optional plural endings
in the title (if multiple taxonomies),
as well as commas.
e.g. whe have a taxonomy "directors":
`directors: ["Joel Coen", "Ethan Coen"]`
To list such taxonomies, use:

## Example: Comma-delimit Tags in a Single Page Template
```hugo
{{ $taxo := "directors" }}                                       {{/* use the plural form */}}
{{ with .Param $taxo }}                                          {{/* do not render if no "directors" in front matter */}
  <strong>Director{{ if gt (len .) 1 }}s{{ end }}:</strong>      {{/* if "directors more than 1, add 's' */}}
  {{ range $index, $director := . }}                             {{/* range through directors by index and value */}}
    {{- if gt $index 0 }}, {{ end }}                             {{/* add ", " before director value */}}
    {{ with $.Site.GetPage (printf "/%s/%s" $taxo $director) -}} {{/* get page in "directors/<DIRECTOR-NAME>" */}}
      <a href="{{ .Permalink }}">{{ $director }}</a>             {{/* render link to director page */}}
    {{- end -}}
  {{- end -}}
{{ end }}
```

alternatively see `delimit` function `delimit COLLECTION DELIMETER [LAST]`
```
{{ $s := slice "b" "a" "c" }}
{{ delimit $s ", " }} -> "b, a, c"
{{ delimit $s ", " " and " }} -> "b, a and c "

{{/* delimit function sorts maps by key, returning the values */}}
{{ $m := dict "b" 2 "a" 1 "c" 3 }}
{{ delimit $m ", " }} -> "1, 2, 3"
{{ delimit $m ", " " and " }} -> "1, 2 and 3"
```

# List Content with the Same Taxonomy Term
If taxonomy is used for series of post, list individual pages associated with the same taxonomy.
This is also a quick and dirty method for showing related content.

## Example: Showing Content in Same Series
```hugo
<ul>
  {{ range .Site.Taxonomies.series.golang }}
    <li><a href="{{ .Page.RelPermalink }}">{{ .Page.Title }}</a></li>
  {{ end }}
</ul>
```

# List All Content in a Given Taxonomy
Could be useful in a sidebar as "featured content".
You could have different sections of "featured content"
by assigning different terms to the content

## Example: Grouping "Featured" Content
```hugo
<section id="menu">
  <ul>
    {{ range $key, $taxonomy := .Site.Taxonomies.featured }}
      <li>{{ $key }}</li>
      <ul>
        {{ range $taxonomy.Pages }}
          <li hugo-nav="{{ .RelPermalink }}">
            <a href="{{ .Permalink }}">{{ .LinkTitle }}</a>
          </li>
        {{ end }}
      </ul>
    {{ end }}
  </ul>
</section>
```

# Render a Site's Taxonomies

## Example: List All Site Tags
```hugo
<ul>
  {{ range .Site.Taxonomies.tags }}
    <li>
      <a href="{{ .Page.Permalink }}">{{ .Page.Title }}</a>
      {{ .Count }}
    </li>
  {{ end }}
</ul>
```

## Example: List All Taxonomies, Terms, and Assigned Content
This example will list all taxonomies and their terms,
as well as all the content
assigned to each of the terms.

`layouts/partials/all-taxonomies.html`:
```hugo
<section>
  <ul id="all-taxonomies">
    {{ range $taxonomy_term, $taxonomy := .Site.Taxonomies }}
      {{ with $.Site.GetPage (printf "/%s" $taxonomy_term) }}
        <li>
          <a href="{{ .Permalink }}">{{ $taxonomy_term }}</a>
          <ul>
            {{ range $key, $value := $taxonomy }}
              <li>{{ $key }}</li>
              <ul>
                {{ range $value.Pages }}
                  <li hugo-nav="{{ .RelPermalink }}">
                    <a href="{{ .Permalink }}">{{ .LinkTitle }}</a>
                  </li>
                {{ end }}
              </ul>
            {{ end }}
          </ul>
        </li>
      {{ end }}
    {{ end }}
  </ul>
</section>
```

# `.Site.GetPage` for Taxonomies
Because taxonomies are lists, `.GetPage` function can be used to
get all the pages associated with a particular taxonomy term.

Example ranges over the full list of tags and
links to each of the individual taxonomy pages for each term

`links-to-all-tags.html`:
```hugo
{{ $taxo := "tags" }}
<ul class="{{ $taxo }}">
  {{ with ($.Site.GetPage (printf "/%s" $taxo)) }}
    {{ range .Pages }}
      <li>
        <a href="{{ .Permalink }}">{{ .Title }}</a>
      </li>
    {{ end }}
  {{ end }}
</ul>
```
