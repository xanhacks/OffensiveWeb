---
title: "Drupal"
description: "Cheatsheet on Drupal library"
lead: "Cheatsheet on Drupal library"
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

## Drupal

[Drupal](https://git.drupalcode.org/project/drupal) is an open source content management platform supporting a variety of websites ranging from personal weblogs to large community-driven websites. 
 
### Discovery

**Content:**

- **Users** are located under `/user/<user-id>`.
- **Nodes** are located under `/node/<node-id>`.
- **Plugins** are located under `/modules/<module-name>/`.
- **Themes** are located under `/themes/<theme-name>/`.

[drupal_modules_path.txt](https://github.com/xanhacks/web-wordlists/): List of all Drupal modules from www.drupal.org, sort by *Most installed*.

**Authentication:**

- `/user/register`
- `/user/login` or `/user`
- `/user/password`

**Version:**

- `/CHANGELOG.txt`
- `/INSTALL.txt`
- `/UPGRADE.txt`

```html
<meta name="generator" content="Drupal 7 (https://www.drupal.org)" />
```

### Admin account into RCE

 - [Services module unserialize to rce](https://github.com/p0dalirius/Awesome-RCE-techniques/blob/master/Content-Management-Systems-(CMS)/Drupal/techniques/Services-module-unserialize-to-rce/README.md)
 - [Upload a plugin](https://github.com/p0dalirius/Awesome-RCE-techniques/blob/master/Content-Management-Systems-(CMS)/Drupal/techniques/Upload-a-plugin/README.md)
 - [Upload a Theme](https://github.com/p0dalirius/Awesome-RCE-techniques/blob/master/Content-Management-Systems-(CMS)/Drupal/techniques/Upload-a-theme/README.md)

### Others

- Drupal frequently utilizes `$_REQUEST` over `$_GET` or `$_POST`, allowing you to alternate between POST and GET requests.
- Drupal commonly caches web requests based on the `User-Agent`. Additionally, some URLs on HTML pages include the `Host` HTTP header. This can occasionally lead to cache poisoning and the generation of harmful URLs on the intended site.