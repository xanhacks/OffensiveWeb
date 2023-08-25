---
title: "VueJS"
description: "Cheatsheet on VueJS"
lead: "Cheatsheet on VueJS"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "framework"
weight: 620
toc: true
---

## VueJS

[VueJS](https://vuejs.org/) is an approachable, performant and versatile framework for building web user interfaces. 

## Client-Side Injection

```js
// XSS Injection
Vue.createApp({
  template: `<div>` + userProvidedString + `</div>`
}).mount('#app')

<div v-html="userProvidedHtml"></div>

h('div', { innerHTML: this.userProvidedHtml })

<div innerHTML={this.userProvidedHtml}></div>

// Javascript protocol injection
<a :href="userProvidedUrl">click me</a>

// CSS Injection
<h1 :style="userProvidedStyles">Title</h1>
```

> Source: [VueJS - Security](https://vuejs.org/guide/best-practices/security.html)