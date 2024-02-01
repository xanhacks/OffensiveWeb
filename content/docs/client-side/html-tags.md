---
title: "HTML Tags"
description: "Overview of HTML Tags"
lead: "Overview of HTML Tags"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "client-side"
weight: 620
toc: true
---

## List of tags

- [defer](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script#defer): Specifies that the `<script>` should be executed after the HTML document has been parsed, allowing the document to continue loading. Useful for optimizing page load performance.
- [async](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script#async): Indicates that the `<script>` should be executed asynchronously while the HTML document continues to load. This attribute is helpful when the script does not depend on the page's structure or other scripts.
- [autofocus](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/autofocus): Automatically focuses on an input element or textarea when the page loads, providing convenience for users.
- [tabindex](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/tabindex): Specify the tabbing order of focusable elements on a webpage. It allows you to control the order in which elements receive focus when navigating through the page using the keyboard's Tab key.
- [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/autocomplete): Controls the autocomplete behavior for an `<input>` element, allowing the browser to suggest previously entered values based on user history.
- [download](https://developer.mozilla.org/en-US/docs/Web/API/HTMLAnchorElement/download): Indicates that a hyperlink (`<a>`) should be downloaded when clicked, rather than navigating to the linked resource. It specifies the filename for the downloaded file. This value cannot be used to determine whether the download will occur.
- [integrity](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity): Enables the integrity check for external resources, such as `<script>` or `<style>`, by providing a cryptographic hash that verifies the resource's integrity and ensures it hasn't been tampered with.
- [crossorigin](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin): Indicates how the element should handle cross-origin requests, allowing or restricting access to resources on different domains.
- [rel=noreferrer](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/rel/noreferrer): Instructs the browser to omit the Referer header entirely when making requests to external sites, enhancing privacy and reducing the risk of leaking sensitive information.
- [sandbox](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe#sandbox): Specifies that an iframe should be loaded in a sandboxed environment, restricting its capabilities to mitigate potential security risks.
- [http-equiv](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta#http-equiv):  Specifies an HTTP header to be included in the server response for the webpage. Commonly used for security purposes is http-equiv="content-security-policy", which allows specifying the Content Security Policy using an HTML attribute.

