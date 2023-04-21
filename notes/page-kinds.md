# Page Kinds
Every `Page` in Hugo has a `.Kind` attribute.

Kind | Description | Example
-- | -- | --
`home` | the landing page for the home page | `/index.html`
`page` | the landing page for a given page | `my-post` page (`/posts/my-post/index.html`)
`section` | the landing page of a given section | `posts` section (`/posts/index.html`)
`taxonomy` | the landing page for a taxonomy | `tags` taxonomy (`/tags/index.html`)
`term` | the landing page for one taxonomy's term | term `awesome` in `tags` taxonomy (`/tags/awesome/index.html`)
