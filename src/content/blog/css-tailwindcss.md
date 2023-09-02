---
author: Maloong🐎🐲
pubDatetime: 2023-09-02T20:48:00Z
title: The Tailwind CSS
postSlug: css-tailwindcss
featured: false
draft: false
tags:
  - CSS
  - Tailwindcss
ogImage: ""
description: A utility-first CSS framework packed with classes like flex, pt-4, text-center and rotate-90 that can be composed to build any design, directly in your markup.
---

Rapidly build modern websites without ever leaving your HTML.A utility-first CSS
framework packed with classes like flex, pt-4, text-center and rotate-90 that
can be composed to build any design, directly in your markup.

## Table of contents

## tailwindcss

[Play](https://play.tailwindcss.com/)

### CSS Property

#### Position

Position determines how an HTML element is positioned within its containing
element or overall website.

1. relative
2. absolute
3. fixed
4. sticky

- The `absolute` and `fixed` are absolute Property to the whole page, they can
  use relative Property `top, bottom,right,left` to the whole page, and also can
  use absolute Property `margin`.
- The `sticky` and `relative` can only use the absolute Property `margin` to the
  relative element.

#### Display

Controls how elements are displayed

1. block
2. inline
3. inline block
4. none
5. flex
6. grid

##### flex

add elements to a `flex` div, the elements displayed as a row default, not like
the normal div displayed as a whole row.

`items-*` property is related to CSS layout. It effects how elements are
aligned both in Flexbox and Grid layouts. [doc](https://css-tricks.com/almanac/properties/a/align-items/)

`justify-*` defines the alignment along the **main axis**( It depend on `flex-row` or
`flex-col`. It helps distribute extra free space leftover when either all the
flex items on a line are inflexible, or are flexible but have reached their
maximum size. It also exerts some control over the alignment of items when they
overflow the line. [doc](https://css-tricks.com/almanac/properties/j/justify-content/)

##### grid

use `grid-col-*` defined how many columns and `gap-*` defined how much spaces
between the elements.

### Responsive

Every utility class in Tailwind can be applied conditionally at different
breakpoints, which makes it a piece of cake to build complex responsive
interfaces without ever leaving your HTML.

There are five breakpoints by default, inspired by common device resolutions:

| Breakpoint prefix | Minimum width | CSS                                |
| ----------------- | ------------- | ---------------------------------- |
| sm                | 640px         | @media (min-width: 640px) { ... }  |
| md                | 768px         | @media (min-width: 768px) { ... }  |
| lg                | 1024px        | @media (min-width: 1024px) { ... } |
| xl                | 1280px        | @media (min-width: 1280px) { ... } |
| 2xl               | 1536px        | @media (min-width: 1536px) { ... } |

To add a utility but only have it take effect at a certain breakpoint, all you
need to do is prefix the utility with the breakpoint name, followed by
the : character:

If you’d like to apply a utility only when a specific breakpoint range is active,
stack a responsive modifier like md with a max-_ modifier to limit that style to
a specific range,Tailwind generates a corresponding max-_ modifier for each
breakpoint, so out of the box the following modifiers are available:

| Modifier | Media query                                    |
| -------- | ---------------------------------------------- |
| max-sm   | @media not all and (min-width: 640px) { ... }  |
| max-md   | @media not all and (min-width: 768px) { ... }  |
| max-lg   | @media not all and (min-width: 1024px) { ... } |
| max-xl   | @media not all and (min-width: 1280px) { ... } |
| max-2xl  | @media not all and (min-width: 1536px) { ... } |

Eg.

```html
<div class="md:block hidden">
  <p>I will appear for device resultion > 768px</p>
</div>

<div class="max-md:block hidden">
  <p>I will appear for device resultion < 768px</p>
</div>
```

### [Handling Hover, Focus, and Other States](https://tailwindcss.com/docs/hover-focus-and-other-states)

### [Dark Mode](https://tailwindcss.com/docs/dark-mode)

### [Custom Styles](https://tailwindcss.com/docs/adding-custom-styles)

1. Break your layouts into specific components
2. Use directives
3. use component libraries

- [Shadcn](https://ui.shadcn.com/)
- [tailwindui](https://tailwindui.com/)
- [headless](https://headlessui.com/)
