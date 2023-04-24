# Local File Templates

To traverse project's directory structure
and write file contents to templates
- `readDir`
- `readFile`


# `readDir`

Returns an array of `os.FileInfo`.
It takes the file's path as a single string argument.
for `readDir` the root of website (typically `./public`) becomes both:
- the file system root
- the current working directory


# `readFile`

Reads file from disk
and converts to string to be manipulated with other hugo functions
or added as-is.

Make sure the path is relative to hugo project's root directory:
`{{ readFile "/content/templates/local-file-templates.md" }}`

When using custom shortcodes with `readFile` for a theme,
shortcode will refer ot project root and not themes directory.
