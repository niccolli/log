# niccolli/log

ページを作るときのメモ

## 個別のページのMarkdown

markdownのファイル先頭には下記の行を設ける。ページタイトルは第1見出しと同じにする。

```
---
layout: default
title:  "2021年をふりかえって"
date:   2021-12-29 15:00:00 +0900
categories: make
---

# 2021年をふりかえって
…本文…
```

## レイアウト

minimal等のGitHubのテーマは個別ページのみしかないので、このリポジトリの_layouts以下のファイルは[minima](https://github.com/jekyll/minima)のものを使っています。
