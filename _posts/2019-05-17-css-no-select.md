---
layout: post
title: CSS NO SELECT!!!!!!!
permalink: /css-no-select/
categories:
  - css
---


I just **HATE** the highlighting effect on `<a></a>` tag or any button whenever clicked!!


```css
a {
  outline: 0 !important;
  border: none !important;
  box-shadow: none !important;
  -webkit-touch-callout: none !important; /* iOS Safari */
  -webkit-user-select: none !important; /* Safari */
  -khtml-user-select: none !important; /* Konqueror HTML */
  -moz-user-select: none !important;  /* Firefox */
  -ms-user-select: none !important; /* Internet Explorer/Edge */
  user-select: none !important; /* Non-prefixed version, currently supported by Chrome and Opera */
  -webkit-tap-highlight-color: transparent !important;
}
```
