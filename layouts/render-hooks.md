Only with Glodmark rendered (default)
Output-Format and language-specific templates are supported.

# Hooks Naming
`render-[kind]`
in:
- `layouts/_default/_markup/` - override default markdown rendering
- `layouts/[type/section]/_markup` - type/section specific hooks

# Supported Hook Kinds
- image
- link
- heading
- codeblock

# Context Passed to `render-link`, `render-image`
- Page - page being rendered
- Destination - url
- Title - title attribute
- Text - rendered (html) link text
- PlainText - plain variant of the Text

# Context Passed to `render-heading`
- Page
- Level - the header level (1-6)
- Anchor - auto-generated html id unique to the header within the page
- Text - rendered text
- PlainText
- Attributes (map) - e.g. id, class (empty for links)

## additionally to `render-image`
- isBlock - true if
  1) this is a standalone image
  2) config option `markup.goldmark.parser.wrapStandAloneImageWithinParagraph` is disabled
- Ordinal - zero-based ordinal for all the images in the current document

# Examples

## Link with title
- md: `[Text](https://ex.ex "Title")`
- `_markup/render-link.html`:
  ```
  <a href="{{ .Destination | safeURL }}"
    {{ with .Title }}
      title="{{ . }}"
    {{ end }}
    {{ if stings.HasPrefix .Destination "http" }}
      target="_blank"
      rel="noopener"
    {{ end }}
  >
    {{ .Text | safeHTML }}
  </a>
  ```

## Image
- md: `![Text](https://image.url "Title")`
- `_markup/render-image.html`:
  ```
  <p class="md__image">
    <img
      src="{{ .Destination | safeURL }}"
      alt="{{ .Text }}"
      {{ with .Title }}
        title="{{ . }}"
      {{ end }}
    />
  </p>
  ```

## Heading Link
- md: `### Section A`
- `_markup/render-heading.html`:
  ```
  <h{{ .Level }} id="{{ .Anchor | safeURL }}">
    {{ .Text | safeHTML }}
    <a href="#{{ .Anchor | safeURL }}">&#182;</a>
  </h{{ .Level }}>
  ```
- rendered html:
  ```
  <h3 id="section-a">
    Section A
    <a href="#section-a">&#182;</a>
  </h3>
  ```

# Render Hooks for Code Blocks
- `layouts/[_default|type/section]/_markup/render-codeblock.html` - hook template for all code blocks
- `layouts/[_default|type/section]/_markup/render-codeblock-[chroma lexer].html` - hook template for specific code type/language [Known lexers and aliases](https://gohugo.io/content-management/syntax-highlighting/#list-of-chroma-highlighting-languages)

## Context passed to a code block template
- Type (string) - the type of code block. This will be the programming languate, e.g. `bash`
- Attributes (map) - attributes passed in from Markdown (e.g. `{ attrName1=attrValue2 attrName2="attr Value 2" }`)
- Options (map) - chroma highlighting processing options (filled if type is known)
- Inner (string) - text between the code fences
- Ordinal (integer) - zero-based ordinal for all code blocks in the current document
- Page - the owning `Page`
- Position - useful in error logging as it prints the filename and position (linenumber, column), e.g.
  `{{ errorf "error in code block: %s" .Position }}`

