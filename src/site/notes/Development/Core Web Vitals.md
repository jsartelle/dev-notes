---
{"dg-publish":true,"permalink":"/development/core-web-vitals/"}
---


# First Contentful Paint (FCP)

- time until any part of the page's content is rendered
- target ==<1.8s==

# Largest Contentful Paint (LCP)

- moment at which the largest DOM object in the viewport is rendered
- measures loading performance
- target ==<2.5s==

> [!tip]
> Minimize client-side requests to close the gap between FCP and LCP.

# First Input Delay (FID)

- time between user's first interaction and browser's response
- measures interactivity
- target ==<100ms==

# Cumulative Layout Shift (CLS)

- visual stability of a page
- affected by dynamically loaded content (like third party ads)
- starts at 0, target ==<0.1==

# Testing

- use the Lighthouse tool in Chromium to measure
