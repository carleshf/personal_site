---
title: "Change key for toggle comment in Sublime Text 3"
date: 2016-04-06T00:00:00+02:00
draft: false
tags:
  - IDE
  - Sublime Text 3
---

I love Sublime Text 3 but I'm sued to `Ctrl + Shift + C` to _toggle comment_ the current line or block of lines. The default behaviour of Sublime Text for _commenting_ is:

```
{ "keys": ["ctrl+/"], "command": "toggle_comment", "args": { "block": false } }
{ "keys": ["ctrl+shift+/"], "command": "toggle_comment", "args": { "block": true } }
```

I like it to behave like this:

```
{ "keys": ["ctrl+shift+c"], "command": "toggle_comment", "args": { "block": false } }
```

So, this lines means that the comments are done by line (not by block) and with the `Ctrl + Shift + C` key-binding.
