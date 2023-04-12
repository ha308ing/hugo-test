---
title: Hello
date: 2023-04-03T11:57:32+03:00
draft: false
linkTitle: 'h1100 fo:'
 # tags: [ 'greetings', 'first post' ]
tags:
  - greetings
  - first post
slug: hello-psst
 # resources: {name: pic, src:pic.png}
resources:
- name: pic
  src: pic.png
toc: true
---
<!--
+++
  title = "Hello"
  date = "2023-04-03T11:57:32+03:00"
  draft = false
  tags = ["greetings", "first post"]
  categories = [ "notes", "hugo" ]
+++

{
  title: "Hello",
  date: 2023-04-03T11:57:32+03:00,
  draft: false,
  tags: ["greetings", "first post"],
}
-->
# Hello Post!

## TOC

{{ .TableOfContents }}


## Syntax Highlighting

{{< highlight js "linenos=inline,hl_lines=2,linenostart=12,anchorlinenos=true,lineanchors=2" >}}
document.addEventListener( "DOMContentLoaded", () => {
  console.warn( "qq" )
}
{{< / highlight >}}

{{</* is equivalent to code fences */>}}


```js {linenos=inline,hl_lines=[1,2],linenostart=12,anchorlinenos=true,lineanchors=2}
document.addEventListener( "DOMContentLoaded", () => {
  console.warn( "qq" )
}
```

## Title property

{{ .title }}


## Ref Link

[Link to other post]({{< ref "today-is-the-day#head_today" >}} "Link to today post")

Glad 2 c u here


## Code

```js
document.addEventListener( "DOMContentLoaded" , ()=>{} )
```

```ps
Get-ChildItem -Path ./ | foreach { $_.Name }
```


### Goat markup

```goat
      .
     /|\
    / | \
   +  |  +
  /|  |  |\
 / |  |  | \
 3 4  1  3  4
```

```goat
      .               .                .               .--- 1          .-- 1     / 1
     / \              |                |           .---+            .-+         +
    /   \         .---+---.         .--+--.        |   '--- 2      |   '-- 2   / \ 2
   +     +        |       |        |       |    ---+            ---+          +
  / \   / \     .-+-.   .-+-.     .+.     .+.      |   .--- 3      |   .-- 3   \ / 3
 /   \ /   \    |   |   |   |    |   |   |   |     '---+            '-+         +
 1   2 3   4    1   2   3   4    1   2   3   4         '--- 4          '-- 4     \ 4

```

```goat
       .--- 1          .-- 1     / 1
   .---+            .-+         +
   |   '--- 2      |   '-- 2   / \ 2
---+            ---+          +
   |   .--- 3      |   .-- 3   \ / 3
   '---+            '-+         +
       '--- 4          '-- 4     \ 4

```


## Resources

{{ $image := .Resources.GetMatch "pic.png" }}
![alt text pic]("{{ $image.RelPermalink }}" 'pic title')
![alt text pic]({{.Resources.pic.Permalink}} 'pic title')

{{ with .Resources.GetMatch "pic" }}
  [!{{.RelPermalink}}]()
{{end}}

