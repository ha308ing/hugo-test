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
# Hello!

## {{ .title }}

Glad 2 c u here

```js
document.addEventListener( "DOMContentLoaded" , ()=>{} )
```

```ps
Get-ChildItem -Path ./ | foreach { $_.Name }
```

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
{{ $image := .Resources.GetMatch "pic.png" }}
![alt text pic]("{{ $image.RelPermalink }}" 'pic title')
![alt text pic]({{.Resources.pic.Permalink}} 'pic title')

{{ with .Resources.GetMatch "pic" }}
  [!{{.RelPermalink}}]()
{{end}}
