---
title: "CSS Layout Techniques"
date: 2024-02-15
draft: false
order: 2
description: "Modern approaches to web page layout"
---

CSS provides several powerful layout systems for arranging elements on a web page.

## Flexbox

Flexbox is a one-dimensional layout method for arranging items in rows or columns:

```css
.container {
    display: flex;
    justify-content: space-between;
    align-items: center;
}
```

## Grid

CSS Grid is a two-dimensional layout system:

```css
.container {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 20px;
}
```

These modern layout techniques make it easier to create responsive designs.
