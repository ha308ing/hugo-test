# Data Templates

To store additional data.
Located in `data` directory: yaml, json, xml, toml.
The data will be accessible as a `map` in the `.Site.Data` variable.
`site.Data.filename` - filename start from letter, underscore, followed by unicode underscores, letters, digits.
filename is irrelevant when `index` function is used:
- `{{ index .Site.Data "_123" }}`
- `{{ index .Site.Data "x-123" }}`


# Data Files in Themes

Data files in root project dir `data`
takes precedence over files with the same name located in
`themes/<THEME>/data` fir keys that are duplicated.

For theme-specific data, that shouldn't be overridden,
use dir structure with a namespace:
`mytheme/data/<THEME>/somekey/...`.
To check for duplicated keys, run hugo with `-v` flag

Access data with dot-chained set of
`path`, `filename`, `key`

# Example: Discography

**files in data dir**:
- `data/jazz/bass/jacopastorius.toml`
- `data/jazz/bass/johnpatitucci.toml`

**data files conent**:
- `data/jazz/bass/jacopastorius.toml`:
  ```toml
  discography = ['1974 - Modern American Music … Period! The Criteria Sessions', '1974 - Jaco', '1976 - Jaco Pastorius', "1986 - Heavy'n Jazz (released in 1992)", '1991 - Live In New York City, Volumes 1-7.', '1999 - Rare Collection (compilation)', '2003 - Punk Jazz: The Jaco Pastorius Anthology (compilation)', '2007 - The Essential Jaco Pastorius (compilation)']
  ```
- `data/jazz/bass/jacopastorius.json`:
  ```json
  {
     "discography": [
        "1974 - Modern American Music … Period! The Criteria Sessions",
        "1974 - Jaco",
        "1976 - Jaco Pastorius",
        "1986 - Heavy'n Jazz (released in 1992)",
        "1991 - Live In New York City, Volumes 1-7.",
        "1999 - Rare Collection (compilation)",
        "2003 - Punk Jazz: The Jaco Pastorius Anthology (compilation)",
        "2007 - The Essential Jaco Pastorius (compilation)"
     ]
  }
  ```
- `data/jazz/bass/jacopastorius.yml`:
  ```yaml
  discography:
  - 1974 - Modern American Music … Period! The Criteria Sessions
  - 1974 - Jaco
  - 1976 - Jaco Pastorius
  - 1986 - Heavy'n Jazz (released in 1992)
  - 1991 - Live In New York City, Volumes 1-7.
  - 1999 - Rare Collection (compilation)
  - '2003 - Punk Jazz: The Jaco Pastorius Anthology (compilation)'
  - 2007 - The Essential Jaco Pastorius (compilation)
  ```

To list of bass players: `.Site.Data.jazz.bass`
Data about one bass player: `.Site.Data.jazz.bass.jacopastorius` (without suffix)

**Render the list of recordings for all the bass players in a template**:
```hugo
{{ range $.Site.Data.jazz.bass }}
  {{ partial "artist.html" }}
{{ end }}
```

**Artist partial:**
`partials/artist.html`:
```hugo
<ul>
  {{ range .discograpy }}
    <li>{{ . }}</li>
  {{ end }}
</ul>
```


# Example: Accessing Names Values in a Data File

`data/User0123.yml`:
```yaml
Achivements:
- one
- two
Name: name
Short Description: He is a **good** fellow
```

Render:
```hugo
<div>
  Short Description of {{ .Site.Data.User0123.Name }}:
  <p>
    {{ index .Site.Data.User0123. "Short Description" | markdownify }}
  </p>
</div>
```


# Get Data

Local `.csv` files must be located **outside** the `data` dir.

To get remote data:
- `getJSON`:
  - `{{ $dataJ := getJSON "url" }}`
  - `{{ $dataJ := getJSON "url" "prefix" "arg1" "arg2" "arg n" }}`
  - `{{ $dataJ := getJSON "url" "prefix" "arg1" "arg2" "arg n" (dict "HTTP-HEADER" "Value" "X-List" (slice "a" "b" "c")) }}` - last argument is a map of HTTP headers
- `getCSV`
  - `{{ $dataC := getCSV "url" }}`
  - `{{ $dataC := getCSV "separator" "url prefix" "arg1" "arg2" "arg n" }}`
  separator first and one character long

All passed arguments will be joined to the final URL:
```hugo
{{ $urlPre := "https://api.github.com" }}
{{ $gistJ := getJSON $urlPre "/users/GITHUB_USERNAME/gists" }}

{{/*
  internally resolves to:
*/}}
{{ $gistJ := getJSON "https://api.github.com/users/GITHUB_USERNAME/gists" }}
```


# Example for CSV files: Create an HTML table in a partial template

`layouts/partials/get-csv.html`:
```hugo
<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Position</th>
      <th>Salary</th>
    </tr>
  </thead>
  <tbody>
    {{ $url := "https://ex.com/finance/salaries.csv" }}
    {{ $sep := "," }}
    {{ range $i, $r := getCSV $sep $url }}
      <tr>
        <td>{{ index $r 0 }}</td>
        <td>{{ index $r 1 }}</td>
        <td>{{ index $r 2 }}</td>
      </tr>
    {{ end }}
  </tbody>
</table>
```


# Cache URL

Each downloaded URL will be cached in the default dir:
`$TMPDIR/hugo_cache` ($TMPDIR - system-dependent temp dir)

Change cahe location:
- `--cacheDir` flag
- `cacheDir` in the main config file

To fully disable caching `--ignoreCache`
without cache LiveReload triggers re-download remote content,
which cause traffic and API limits.
